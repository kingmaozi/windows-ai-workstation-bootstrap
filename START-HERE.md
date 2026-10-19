# START HERE：复制给Windows新电脑ZCode

我的电脑是 **Windows 11 专业工作站版（Pro for Workstations）x64 + i9-13900KS + ASUS Z790 APEX + RTX4090 24GB水冷 + 32GB（16GB×2）DDR5-8000 CL36**。这是新机，代理客户端等还没安装；先读现状，不假定全空。以此32GB与已选工作站版为准，不从其他旧资料推导机器配置。

## 目标

给这台机器安装必要的Windows软件、选择一个合法联网代理客户端、做有证据且能回退的系统配置，再实施本仓库 `VIDEO-PIPELINE.md` 的本地视频方案。**不删除我的文件，不卸载现有软件，不重装/降级系统，不改磁盘分区，不自动发布视频。**

先完整读本公开仓库README、docs01–03、config和VIDEO-PIPELINE.md。所有任务所需技术规格均在本仓库，无需访问私有项目或旧ZIP。

## A. 不可越过的边界

1. 不执行删除/清理/卸载/重置/格式化/重命名用户文件，不覆盖工程、素材、已有模型、代理配置或Python环境。下载和日志用版本化新目录，不猜D盘一定存在。发现既有安装则保留并核实，不先删再装。
2. 不跑WinUtil/Win11Debloat/Sophia的默认/推荐/全量预设，不做Tiny11/MicroWin，不下载执行未知远程脚本，不以“开源”免除审核。
3. 不关Defender/防火墙/SmartScreen/更新/VBS/内存完整性，不加安全排除、不改TDRDelay/HPET、不RAM cleaner、不禁pagefile、不批量禁服务/任务/启动项。
4. 固件/BIOS/XMP/电压/GPU功率、OS edition变更/授权、重启、TUN服务/UAC、第三方软件/模型协议、账户登录、付款和新增远程公网入口需对应人工授权。**除此之外，按本任务推进必要可逆安装/配置/开发/验证，不为每一步重复问“要不要做”。** 工具权限拒绝时不绕过。
5. 订阅URL/token/密码/UUID、Windows密钥、BitLocker恢复密钥不进聊天、issue、仓库或公开日志。用户只在目标机受信任界面输入；不要让我粘到聊天里。不购买/推荐未知机场或免费节点，不把联网代理用于店铺身份/平台准入/地域许可规避。
6. 安装器和系统更新可能写自己的文件，不能承诺“任何文件都不改变”或零风险。先备份关键数据与变更前设置；还原点不是文件备份。设置回滚恢复原值，不默认删除新增文件/软件。

## B. 明确选型

- 系统保留 **Pro for Workstations x64、官方仍受支持正式版、非N/KN优先**。已安装就不为了名称重装。若实际是Pro/Home，只报告偏差与微软支持的合法升级路径，不自动切版；可先继续与edition无关工作。
- 代理默认 **Clash Verge Rev**，只装一个；若用户订阅协议或组织政策不适配，再用v2rayN，不同时开两套代理。Clash源 https://github.com/clash-verge-rev/clash-verge-rev ，v2rayN源 https://github.com/2dust/v2rayN 。release具体资产/sha参见附录，安装日复核正确x64稳定版，不用搜索广告或中转下载器。
- 软件底座 **微软WinGet**。缺包才装，精确ID/官方源，用`--no-upgrade`避免自动替换既有环境，逐项show/install/verify，不`upgrade --all`。清单不是自动授权接受所有EULA/开自启动/换默认浏览器。
- GPU推理 **Windows原生ComfyUI源码managed venv**，不因本补充新增WSL/Docker；只一个活跃生产实例。新环境和离线回滚副本分开，所有节点/权重有来源和许可。
- 最新硬件是32GB，8000 CL36先只读核实当前运行值。现有两槽已满，后续有读数再提出64/96GB替换套装建议，不擅自购买或调XMP。

## C. 依赖顺序：没有代理时怎么开始

1. **只读盘点**：OS edition/build、当前应用/代理/环境变量、网卡/DNS/系统代理/WinHTTP状态、磁盘真实路径和空间、硬件/BIOS/显卡驱动。不要输出密钥或整个用户profile。
2. 先测普通网络/系统时间/DNS；若GitHub和WinGet不可达，**不要循环要求winget先装代理**。由用户用手机/另一可信设备从官方release取x64安装资产及必要运行时，核官方SHA后带入。你可提前整理准确文件名/URL/hash给用户，但不改当前mac网络或装客户端。
3. 安装客户端后，由用户在本机GUI导入合法订阅/自建服务器配置。没有订阅就标“已安装、待配置”，停止该lane的真实联网验收，其他离线步骤继续。
4. 默认系统代理先行，**先用系统自带浏览器/curl.exe测试下载源、监听与退出恢复，完成引导网络验收即可安装缺失工具**。Git/Python/pip/ZCode尚未安装时标deferred，不算网络失败；工具装好后再逐一测真实业务，ComfyUI启动后才测8188/MCP。未覆盖的程序再尝试**该进程**HTTP_PROXY/HTTPS_PROXY及NO_PROXY，不永久写全机，不覆盖已有Git全局配置。
5. 明确需要TUN再装服务并申请UAC；保留DNS/路由/代理before，单机loopback监听/关闭LAN分享，控制API不公网。NO_PROXY至少localhost/127.0.0.1/::1；LAN语法按程序真实支持，测试ComfyUI8188及局域网，不用一串CIDR冒充生效。
6. 客户端退出或重启后网络恢复要按**原值**，不是一概清空代理。没有原设置快照不做网络reset兜底。

**首次网络仍依赖人工导入凭据，这是合理阻塞，不以“全自动”伪造。** 网络可用之后再安装开发工具。ZCode若尚未安装/登录，用官方 https://zcode.z.ai/en/docs/install 和合法账户；网页会话已有就先保存工作再按需要重启继承网络，不强杀。

## D. 仅补缺失软件的最小清单

先`winget --info`；WinGet缺失时按微软App Installer官方途径安装，依赖不齐先准确列阻塞。先对每个ID执行`winget show --id ID --exact --source winget`、核发布者/资产/当前版本/EULA，安装前核已有软件：

| 软件 | 候选精确ID | 默认处理 |
|---|---|---|
| Clash Verge Rev | `ClashVergeRev.ClashVergeRev` | 首次联网可先官方release离线引导；已有则不再装 |
| Git | `Git.Git` | 缺失才装 |
| GitHub CLI | `GitHub.cli` | 读私有issue/仓库需要；缺失才装，OAuth用户本机完成 |
| Python3.12 | `Python.Python.3.12` | 本仓库VIDEO-PIPELINE.md兼容起点，核当前受维护补丁/模型依赖；已有Python不乱改PATH，用独立venv |
| FFmpeg | `Gyan.FFmpeg` | 候选构建源；核build/license/ffprobe存在，已有则留原版 |
| VC++ x64 Runtime | `Microsoft.VCRedist.2015+.x64` | 非开源依赖；按需要和合法条款安装，不运行库大全 |
| ZCode | 官方下载页 | 不编造winget包ID，不改变用户模型/档位 |
| NVIDIA/ASUS驱动 | 各自官方型号页 | 记录现状后核，人工重启窗口，不强清洁卸载 |

单个包安装示例（不是让你跳过检查批跑）：

```powershell
winget install --id Git.Git --exact --source winget --no-upgrade
```

不加`--force`/忽略hash/忽略恶意扫描/`--uninstall-previous`/`--allow-reboot`；首次需同意源/EULA时让用户在目标机明确处理，不把自动接受参数藏进总脚本。命令退出码非0或版本未实际存在即未验收。命令行缺失可能是PATH会话没刷新，不先重复安装。

## E. 系统优化，只做有用的小改动

按docs02执行。重点：

- 系统和驱动正常更新，保留维护窗口；不无验证自动升级已验收torch/节点/驱动。
- 先导出`powercfg /getactivescheme`/`/query`/原方案，复制新GUID后才调整**AC自动睡眠0、显示器10–15分钟**。只激活副本，不改原方案。保持原处理器策略，不开Ultimate Performance/CPU最小100%；不关休眠文件。回滚切原GUID并复测，副本不删除。
- pagefile默认保留系统管理，有自定义先记录，不把页文件当显存。磁盘不足只告警/暂停写盘，不自动clean/迁移Downloads/素材/OneDrive。
- NVIDIA Studio是候选稳定路径，不预填未知driver版本；13900KS BIOS/微码按APEX准确型号官方说明人工。内存先不改，实测异常不能立刻认CPU坏。
- 每项设置都有old/new、原因、授权范围、验证、回滚命令。回滚预览不执行，不能假定所有命令都支持`-WhatIf`。

## F. 全天运行要区分本地生成与联网

ComfyUI/worker用受限后台账户和AtStartup，不能依赖用户打开画布；服务或任务凭据不写脚本。代理GUI的“开机启动”可能只是**登录后启动**：必须测GUI关闭/注销/登录页状态，TUN服务安装完成也不自动等于未登录时网络可用。

- 已缓存权重、无需远程LLM的本地生成可离线继续。
- 网络任务等代理/核心/凭据就绪才启动，失败退避，不重复外部任务、不改直连泄漏。
- 若未能验证后台网络，明确“重启后需用户登录恢复联网”，不配置明文自动登录伪装无人值守。
- 本地8188/MCP/局域网始终可用；不开allow-lan、公网端口、入站远程桌面；Tailscale等远控是另项授权不是本次必须。

## G. 调度、文件边界与交付

本项目需目标Windows本机，默认无需PR；不修改/覆盖现有代码和用户文件；当前仓库已公开，不擅自公开任何其他仓库。新文件路径先探测再选，例如用户认可的`PODVideo-bootstrap-20261019-v1`；没有D盘不造D盘。

| 任务 | 负责人 | 依赖/时机 | 文件/接口边界 | 验收 | 分支/PR |
|---|---|---|---|---|---|
| 盘点/无删除契约/来源与版本 | 主会话 | 立即，只读 | inventory、change-plan、schema | 硬件32GB/OS现状、旧设置/空间、阻塞清楚 | 无 |
| 网络客户端配置 | 本地code：联网lane | 盘点后；导入凭据/UAC用户在场 | network/；系统代理/DNS/TUN唯一负责人 | 先浏览器/curl引导验收与退出恢复；CLI/ZCode/8188待工具安装后补验 | 无 |
| 软件环境安装 | 本地code：软件lane | 引导网络通过后，不等未安装工具的联网测试；安装器串行 | apps/、engines/venv，包清单 | show/install/实际version；随后CLI/ZCode，ComfyUI启动后loopback/最小CUDA | 无 |
| 系统可逆设置 | 主会话 | 旧值备份后；不可与联网lane并写网络设置 | power/、changed-ledger | 前后值+回滚抽检，不删文件 | 无 |
| 独立只读审查 | code-review | 改动/记录完成后 | 只读代码/脚本/日志 | 无删除、无secret、权限/恢复边界 | 无 |
| GPU/后台/长稳 | 主会话+人工商品审核 | 环境及关键审查通过后 | validation/、人工待审 | 1条金标→2/8/24/72h，恢复/电耗/峰值 | 无 |

代理不再派生，同文件唯一写负责人。没有可用子代理时主会话可按上述依赖串行，不假称并行或已部署。尚未明确接口的code先共同确认最小契约再写。

## H. 验收与必须交付的回执

1. 客户端正确x64/版本/hash与必需运行时，合法订阅本机已配或明确pending；浏览器、git、Python包源、HF小文件、ZCode实际请求分别验证，不能仅看HTTP200。
2. 回环8188、MCP及实际局域网可达；退出恢复原代理值；TUN/DNS无环/无未授权共享；未改TLS验证。
3. 软件已装/版本已证、已有软件跳过；没有自动upgrade all或uninstall；32GB硬件事实与模型实测分列。
4. 电源/更新/Defender/防火墙/SmartScreen/pagefile前后记录，回滚计划有准确旧值；抽一项安全设置验证回滚，不删副本。
5. 选定用户目录/关键文件清单＋抽样hash（不外发秘密）前后比较，异常立即停止。不声称抽样检查能证明所有文件绝对不变。
6. 生成与后台/注销/重启、72h长稳逐项not-run/通过/阻塞；重启人工窗口。未测不写“全好了”。
7. 输出：`inventory.json`、`package-install-receipt.json`、`changed-ledger.jsonl`、`rollback-plan.md`、`network-verification.md`、`blockers.md`、最终环境锁、真实成片/hash与验收日志。可分享报告脱敏，订阅与Windows密钥不入。

最后继续本仓库VIDEO-PIPELINE.md的视频生产任务，但**本补充覆盖OS和内存旧口径**：H3美国输出商用许可保持pending、云视频预算0、单GPU/batch1、素材/成片人审、没有自动发布/挂车。联网工具不是绕过这些门槛的办法。
