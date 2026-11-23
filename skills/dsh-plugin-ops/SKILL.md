---
name: dsh-plugin-ops
description: 在本机 DeepSeek Harness（DSH）上开发、验证、部署、排查插件的可复用流程。当任务涉及 DSH/cordis 插件、profile 目录、cordis.patch.yml、插件测试桩、审计日志落盘、会话轨迹/session 存档读取时使用。触发词：DSH、deepseek-harness、cordis.patch.yml、profile 插件、审计日志、轨迹、session.jsonl.zstd、exporter。
agent_created: true
---

# DSH 插件开发与运维

## 本机关键路径（先确认再动手）

| 用途 | 路径 |
| --- | --- |
| DSH 源码 checkout（SDK 类型、tsc） | `E:/deepseek-harness` |
| DSH_HOME | `C:/Users/ws/.dsh`（环境变量常为空，代码里按 `~/.dsh` 兜底） |
| profile | `C:/Users/ws/.dsh/profiles/web/` |
| **实际被加载的插件副本** | `C:/Users/ws/.dsh/profiles/web/<plugin>/` |
| 插件注册表 | `C:/Users/ws/.dsh/profiles/web/cordis.patch.yml` |
| 主机审计日志 | `C:/Users/ws/.dsh/logs/<plugin>-YYYY-MM-DD.jsonl` |
| 会话存档 | `C:/Users/ws/.dsh/sessions/--<项目路径转义>--/<sessionId>/session.jsonl.zstd` |

**最重要的一条**：`cordis.patch.yml` 里的 `name: ./<plugin>/index.ts` 相对 **profile 目录**解析，
所以改源码目录不会生效——必须把文件**复制到 profile 下**。生效方式分两种，见下节。

同步前先备份：`cp -r api_script_log_skill api_script_log_skill.bak-YYYYMMDD-HHMM`（该目录已有此惯例）。

## 跑插件测试（原生 TS，需 Node ≥ 23.6）

托管版 Node 22 不满足；用系统版 `C:\Program Files\nodejs\node.exe`（v24）。

```sh
node --test test/audit-core.test.ts                                     # 纯逻辑，无需桩
node --import ./test/register-stubs.mjs --test test/plugin.test.ts      # 真 index.ts + SDK 桩
node --test test/audit-log.test.ts                                      # 纯 JS CLI
```

桩机制：`test/register-stubs.mjs` → `stub-loader.mjs` 用 `node:module` 的 resolve hook
把 `@deepseek-ai/dsh-tools` / `schemastery` / `cordis` 重定向到 `test/stubs/*`，
这样真实的 `index.ts` 能在没有 DSH 安装的环境里跑。

类型检查（对着真实 SDK 类型）：
```sh
node E:/deepseek-harness/node_modules/typescript/bin/tsc -p tsconfig.typecheck.json
# 只看本项目文件的错误（vendor/cordis 源码的 strict 报错是既有的、与本插件无关）：
node E:/deepseek-harness/node_modules/typescript/bin/tsc -p tsconfig.typecheck.json 2>&1 | grep "^D:/dsh-log"
```
**不要用 `npx tsc`**：会去装一个同名的废弃包 `tsc@2.0.4`，然后告诉你"这不是你要的 tsc"。
`tsconfig.typecheck.json` 用 `paths` 映射到 checkout；`files` 只列业务文件，避免
vendor/cordis 源码在更严的 strict 下报无关错误。

## 改 profile 的 cordis.patch.yml（配置文件面）

1. **先备份**：`cp cordis.patch.yml cordis.patch.yml.bak-YYYYMMDD-HHMM`。改坏这一行会让下次重启直接起不来。
2. 改完**必须用 js-yaml 校验**（它是 checkout 里的 CJS 包，注意 ESM 的 `import` 解析不到它）：
```sh
cd /c/Users/ws/.dsh/profiles/web
NODE_PATH="E:/deepseek-harness/node_modules" node -e "
const y=require('js-yaml'),fs=require('fs');
const d=y.load(fs.readFileSync('cordis.patch.yml','utf8'));
for(const e of d) console.log(e.id||('insert x'+e.insert.length));
console.log(Object.keys(d.find(e=>e.insert).insert[0].config).join(', '))"
```
3. **配置面要与插件源码对齐**：插件新增的加固键（超时上限、各类字节上限、保留天数）
   不会自动出现在 profile 里，会走 schemastery 默认值——能跑，但配置文件读起来像上一版。
   补齐时按插件里的真实默认值写，并加行内注释说明单位/约束。
4. 改完做一次**真实配置的启动演练**（不需要 DSH、不需要重启服务）：先用上面的 js-yaml
   把 `config` 段导出成 JSON，再用测试桩跑 `Config()` + `apply()`，断言启动记录里的
   `pluginVersion` 与 `config` 生效值。
   ```sh
   node -e "...;fs.writeFileSync('.tmp-cfg.json',JSON.stringify(d.find(e=>e.insert).insert[0].config))"
   node --import ./test/register-stubs.mjs .tmp-boot-check.ts   # 读 JSON → apply → 打印启动记录
   ```
   **坑：`NODE_PATH` 对 ESM `import` 无效**，所以桩脚本里要读 JSON 文件，不能直接 `import 'js-yaml'`。

## 让改动生效：热重载 vs 重启

**启动方式**（本机是源码 checkout，不是 npm 全局安装；`dsh` 不在 PATH 上）：
```sh
cd E:/deepseek-harness
DSH_HOME=C:/Users/ws/.dsh node --import tsx/esm apps/cli/src/bin.ts web   # 等价 pnpm dsh web
```
监听 `http://127.0.0.1:3080`（CLI 版本 0.1.0-rc.7）。进程特征：`node` + cwd `E:/deepseek-harness`。

**两条生效路径**（由 `apps/cli/src/profile-boot.ts` 的 `watchUserPatches` 决定）：

| 改了什么 | 要重启吗 | 依据 |
| --- | --- | --- |
| `cordis.patch.yml` 的 `config` / 插入行 | **不用**，保存即 live recomposition | `profile-boot.ts:285-294` 同时 watch `profiles/<p>/cordis.patch.yml` 与 `~/.dsh/cordis.patch.yml` |
| 插件 `index.ts` / `audit-core.ts` 代码 | **要** | web bundle 关掉了共享的 module-reload `hmr` 行，回退为 `config: { root: [] }` 的 **config-only** HMR（`profile-boot.ts:272-284`），模块不会重新 import |

⇒ "改代码 → 复制到 profile → 重启"这条链仍然必需；但**只改配置时别重启**，直接改 profile 的
`cordis.patch.yml` 就能热生效（这也意味着配置写坏了会立刻打到运行中的实例上，改前先备份）。

**重启三步**：
1. 查监听进程：`netstat -ano | grep ":3080"`（无输出 = 根本没在跑）
2. 杀掉：`taskkill /F /PID <pid>`
3. 按上面的命令重新启动，然后 `curl --noproxy "*" http://127.0.0.1:3080` 自检

**仓里已有的辅助脚本**（都在 `~/.dsh/`，可直接读来抄）：
- `killer.ps1` — 睡 8s（先让当前回复流出去）再 taskkill 监听 3080 的 PID；
- `starter-v7.ps1` — 等 3080 释放（≤45s，拒绝起第二个实例）→ 启动新实例 → 25s 后 HTTP 自检 → 追加 `restart-status.txt`；
- 历史做法是把 starter 放进**一次性计划任务**，为的是脱离 agent 的 job object（agent 退出也能把服务拉起来）。

**本机安全策略注意**：`wmic.exe`、`schtasks.exe` 都在命令黑名单里（会被直接拒绝，别重试）；
`powershell.exe` 不能从 bash 里调（会以"绕过 PowerShell 安全检查"被拦），要用 PowerShell 工具。
另外本会话里 **PowerShell 工具不回显输出**（ConPTY 未开），取证改用 bash 的 `netstat -ano` /
`tasklist` / `find ... -newermt`，或用 Node 直接读文件。

**判断服务是否在跑的最快办法**：`netstat -ano | grep ":3080"`。注意带 `HTTP_PROXY` 的 curl 会
经代理返回 502（看起来像"服务活着但报错"），必须加 `--noproxy "*"`——直连失败才是真的没监听。

## 读 DSH 会话存档（session.jsonl.zstd）

格式：**逐行独立的 zstd 帧首尾相接**，每帧一行 JSON，帧尾 4 字节校验和，无分隔符。
- Node 的 `zlib.zstdDecompressSync` 只解第一帧；`createZstdDecompress` 会报 `Unknown frame descriptor`。
- 可靠做法：扫描 magic `28 b5 2f fd` 拿到所有帧起始偏移，第 k 帧 = `buf.subarray(offs[k], offs[k+1])`，逐帧解压。

```js
const buf = readFileSync(file)
const offs = []; let i = 0
while (true) { const p = buf.indexOf(Buffer.from([0x28,0xb5,0x2f,0xfd]), i); if (p < 0) break; offs.push(p); i = p + 4 }
// 第 k 帧：zstdDecompressSync(buf.subarray(offs[k], offs[k+1] ?? buf.length))
```
事件类型（`o.type`）：`session`、`tool/call`、`tool/result`、`assistant/message`、`step/start|end`、`turn/start|end` 等。
工具结果在 `tool/result.data.message.content[].text`；`toolCallId` 可与审计记录的 `dshCallId` 关联。

## 读插件审计日志

```sh
node tools/audit-log.mjs --stats        # 汇总
node tools/audit-log.mjs --call <前缀>   # 单次调用
node tools/audit-log.mjs --incomplete   # 记录丢失检测（轨迹做不到）
```

## 踩坑清单

- 工具结果必须是无损 JSON：**值为 `undefined` 的键要整个省略**，否则 DSH 结果校验失败（报 invalid output）。
- 不要自定义 `audit/log` 之类的会话事件类型：该 build 的持久化会拒绝未知的非 ignorable 事件，包含它的会话将无法重载。
- 平台 `tool/result` 的 `isError` 对"工具正常返回但业务失败"的情况是 `false`，判失败要看结果体里的 `ok`/`errorType`。
- 持久化日志依赖 `ctx.logger.exporter` 存在（特性探测式）；没有该 API 时必须显式告警，否则会静默退化成内存缓冲。
- Windows 上 `.bat/.cmd` 不能直接 spawn（`EINVAL`），要经 `cmd.exe /d /s /c`，且保持 argv 数组、不做字符串拼接。
- **Windows 上 `.py/.js/.sh/.ps1` 根本无法直接 spawn**（CreateProcess 不查文件关联，只有 shell 才会）。要按扩展名分发解释器：`.py→python/python3/py`、`.js→node`、`.sh→bash/sh`、`.ps1→pwsh/powershell -File`，候选逐个试、`ENOENT` 才换下一个。解释器名必须来自内置表，不能来自模型。
- **Windows 上 `spawn` 对无法启动的目标会「同步抛出」而不是发 `error` 事件**（实测 `EFTYPE`，errno -4028）。只监听 `error` 事件会漏掉：Promise 被 reject、约定被破坏，且已创建的 `setTimeout` 没人清、事件循环被拖住。必须把 `spawn(...)` 包在 try/catch 里，与 `error` 事件走同一套分类逻辑。
- 测试里若要让子进程能按**名字**找到 `node`，在 `before()` 里把 `path.dirname(process.execPath)` 前置到 `process.env.PATH`——测试运行器是用绝对路径启动的，node 自身目录不一定在 PATH 上。
- 读取/分析日志时用**帧边界**或**行**处理，不要假设整个文件能被一次性解压/解析。
- **脱敏词表必须折叠分隔符**：`key.toLowerCase().includes(kw)` 会漏掉 `api_key` / `api-key` / `x-api-key`（关键词被分隔符切断，`api_key` 里根本没有连续的 `ak`）。先把名字折叠成「小写 + 只留字母数字」再比对关键词。
- **`error` 字段是独立的泄漏出口**：错误文本通常"贴心地"回显调用方输入（拒绝理由会引用错的文件名、URL），因此 error 与 request 段必须走**同一条**脱敏通路；`fetch` 失败和内部异常的 message 不受你控制，也要过一遍。脱敏函数应写成总函数（收任意值、返回字符串、永不抛），才能安全地包住每一个写入记录的字段。
- **入站限制要与出站对称**：有 `maxResponseBytes` 就该有 `maxRequestBytes`（body 只序列化一次、量一次、**在任何 I/O 之前**拒绝）。本地预检式拒绝沿用既有 errorType（如 `PERMISSION_DENIED`），不要往 spec 枚举外新增分类，否则下游消费者要跟着学。
- **凭据类响应头在"源头"脱敏**（如 `set-cookie`）：放在采集响应头那一层，日志与返回给模型的结果同源，两边不会各自漂移。
- **日志 sink 运行期失败必须可见**：告警写 `process.stderr`，**不要走 logger**——该 sink 正是 logger 唯一的持久输出，走 logger 会自我递归；恢复后再追加一条 `event: <plugin>-sink-recovered`（带丢失条数），否则磁盘满只会留下一个悄悄停止增长的文件，读起来像"没有调用发生过"。
- 日志落盘用同步 `appendFileSync`（保证顺序性）。高频调用会阻塞事件循环——量小可接受，量大时需要重新评估。
- 改共享测试夹具里的配置/路由会波及既有用例：曾把 `maxRequestBytes: 1024` 写进共享 config，直接打挂了既有的大 body 用例（20 KB）。**优先用被测代码的默认值**构造新用例，而不是收紧共享配置。
- **审计字段名不能含 `ak` / `sk` 子串**：脱敏关键词按「折叠后的名字」做**子串**匹配，所以 `task`/`disk`/`mask` 都会被误脱敏成 `***`。这个方向是安全的（宁可多脱敏），但它是一条命名规则——新增字段时先自查，否则新字段的值会被静默吃掉。实测踩到：把地址类别字段命名为 `hostRiskTier`，折叠成 `hostri[sk]tier` 后被脱敏，改成 `addressClass` 才正常。该行为应有测试固定，避免后人"顺手修正"。
- **写分类/白名单判断时，别用"没有点号就是本地名"这类规则直接判定**：IPv6 字面量也没有点号，`2606:4700::1111` 会被误判成私网。地址字面量必须先按「能否解析成 IP」分流，再进入域名规则；判断特殊地址应基于**数值段（octet）**而不是字符串相等，这样 IPv4-mapped 形式（`::ffff:169.254.169.254`）才不会漏判。
- **白名单/静态检查类告警要分级**：把「本地验证常用的回环地址」和「元数据服务/内网地址」分成 INFO 与 WARN 两档。全量告警会让运维学会忽略告警，比不告警更糟。告警只写日志、不拒绝启动——访问内网 API 可能是合法部署。
- 静态检查的能力边界要**写在记录里**（例如「不做 DNS，因此解析到私网的公网域名检测不到」），否则一行"看起来干净"会被过度解读成安全保证。
