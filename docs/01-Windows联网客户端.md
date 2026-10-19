# 01 Windows联网客户端：不用找“小火箭Windows破解版”

研究日期2026-10-19。目标是合法连接ZCode、GitHub、官方模型/软件仓库，不是TikTok账号地区/设备身份伪装。以下是官方文档与GitHub元数据核验，未在目标机器安装或实际测速。

## 1. 选型结论

**首选Clash Verge Rev**：Windows中文界面、订阅/规则管理、系统代理与TUN整合，适合这台专用桌面机器。不是笼统断言它在所有协议/线路上“比Shadowrocket更快”；客户端覆盖平台不同，线路质量、协议和服务才决定实际体验。

| 项目 | 本轮核查状态 | 选用条件 |
|---|---|---|
| [Clash Verge Rev](https://github.com/clash-verge-rev/clash-verge-rev) | GPL-3.0，活跃，稳定v2.5.8 | 默认安装一个；先系统代理，按实测给CLI进程代理或启TUN服务 |
| [v2rayN](https://github.com/2dust/v2rayN) | GPL-3.0，活跃，7.25.6 | 用户服务协议/导入格式更适合Xray等，或偏好便携；普通版核.NET运行时 |
| [Throne](https://github.com/throneproj/Throne) | GPL-3.0，活跃，1.3.2 | 明确需要sing-box/nekoray风格，非默认第三套 |
| [nekoray](https://github.com/MatsuriDayo/nekoray) | 已归档，旧4.0.1 | 不作为新机推荐 |
| [sing-box](https://github.com/SagerNet/sing-box) | 开源内核，稳定1.14.3，自有附加名称条款需保留 | 非完整易用Windows GUI，首轮不用手写内核服务取代客户端 |

不用同时安装多套核心并开启系统代理/TUN。不要给ZCode常驻管理员；TUN安装服务时的UAC与普通日常运行分开。

## 2. 官方下载与研究时点校验

[Clash Verge Rev官方Releases](https://github.com/clash-verge-rev/clash-verge-rev/releases)。本次实际GitHub API回执：

- tag `v2.5.8`，发布2026-10-09；Windows **x64** 普通安装资产：`Clash.Verge_2.5.8_x64-setup.exe`。
- bytes `60230483`；SHA256 `821c0f5957f84c8f7d14532044840e8280d708af24a6a143db360b49bd1e7302`。
- 固定链接：https://github.com/clash-verge-rev/clash-verge-rev/releases/download/v2.5.8/Clash.Verge_2.5.8_x64-setup.exe
- 这些hash只适用于这个资产，**换版本/换fixed_webview2包必须重新读取对应官方digest**。不拿签名缺失或hash不符当正常并绕过SmartScreen。

备选[v2rayN 7.25.6](https://github.com/2dust/v2rayN/releases/tag/7.25.6)：`v2rayN-windows-64.zip`，168730000bytes，SHA256 `9b94718c2a214a246b04dec84b9a27f1f0ced8489c2dd25eb80a0fbb806bd13f`；运行时按实际release说明核，不把普通zip、desktop、self-contained混用。

Clash/WebView2缺失时只从官方说明及微软运行时入口处理；有fixed_webview2包可评估，不能把其签名/hash套用普通安装包。v2rayN对应.NET Desktop Runtime同理。SHA一致证明文件匹配该官方元数据，不等于安全审计或绝对无恶意。

## 3. 新机首次联网引导

1. 先测Windows是否已有正常网络、时间/时区正确，网页与DNS是否可达；不要先改全局DNS或卸载网卡驱动。
2. 如果官方GitHub可达，直接取release；否则由用户用另一台可信设备/手机从**官方repo链接**下载x64资产与依赖，通过U盘/可信局域网传入。不能把未知网盘整合包/搜索广告/所谓Windows小火箭破解包当官方。
3. 在目标机用 `Get-FileHash -Algorithm SHA256` 对照正确资产digest，再核 `Get-AuthenticodeSignature` 的结果、来源与release说明。并非每个开源包都有Authenticode，缺失时报告风险按授权处置；hash不符立即停，不用`--ignore-security-hash`。
4. 安装到新应用目录，不移动用户文件/覆盖已有代理配置。无网络时不要不断重试winget或下载核心，先明确哪项运行时/安装资产缺失。
5. **订阅/节点由用户在本机应用内手动输入或通过可信本地渠道导入**；不让用户把URL、二维码、token、UUID/密码粘到issue、聊天或代码仓。没有合法服务器就标记“已装客户端，待用户配置”，不承诺联网已通。

## 4. 系统代理、显式进程代理与TUN

[官方快速开始](https://www.clashverge.dev/guide/quickstart.html)区分系统代理与TUN；[Windows FAQ](https://www.clashverge.dev/faq/windows.html)解释TUN需要服务/管理员权限。

**默认先系统代理**：普通用户界面可用、变更小。浏览器能联网不代表Git/Python/ZCode子进程都正常；Windows代理存在用户WinINET、WinHTTP、应用自带与进程env等不同路径。

对没遵循系统代理的明确进程，优先短期设置该进程的 `HTTP_PROXY` / `HTTPS_PROXY`，使用客户端**实际mixed HTTP端口**，不猜7890/7897；`HTTPS_PROXY`值采用本地HTTP CONNECT代理URL（例如`http://127.0.0.1:实际端口`），不因目标是https就擅自写成https代理。

设置 `NO_PROXY` 至少包括 `localhost,127.0.0.1,::1`；局域网范围按该工具实际支持的语法配置，**不同程序对CIDR、通配符和大小写处理不一致**，不能复制一串CIDR就认定全生效。测试实际NAS/局域网和ComfyUI。关闭子进程应释放临时env；若改已有变量先保存旧值，禁止`setx`一把写全机，不覆盖用户Git全局设置。

只有仍有明确程序无法联网、或确需完整覆盖时，才按授权装客户端服务并启TUN；GUI尽量普通权限运行。TUN会改变路由/DNS/虚拟网卡，先保存现状、安排能现场恢复的时间，不在唯一远程会话里冒险切换。

- 代理自身到上游的连接不能被自己回收成环；按内核的出站绑定/排除机制确认，不盲目加route命令。
- 只允许本机使用：关LAN共享，监听loopback，控制API绑定127.0.0.1并有必要的本地访问控制；本机服务不是对所有本地进程的安全隔离。
- DNS/fake-IP按所选版本/订阅合并结果实际核验，默认不写死某一公共DNS，也不关闭TLS验证来“解决证书问题”。
- TUN不一定改变默认路由成一个肉眼可见的0.0.0.0/0；不能仅用网卡出现或某条路由判断已生效。
- 不全局解除所有UWP回环限制；仅确诊应用需要且用户授权后做最小豁免。

## 5. 全天运行与首次登录问题

**GUI“开机启动”常指用户登录后自启，不保证未登录时上游核心和订阅已准备好。** 装了一个服务也不自动等于那服务在没有GUI/用户配置时已能处理流量。

分开验收四种状态：用户登录且GUI开；GUI正常关闭；用户注销；重启停在登录页。检查核心进程/服务身份、配置读取权限、监听/健康状态、实际网络请求能否成功。

本地ComfyUI生成已缓存权重时应可离线运行；网络相关任务（下载模型、云API、远程LLM/通知）在代理未准备好时进入等待/退避，不丢作业、不把连接失败当素材生成失败、不偷偷改直连/泄漏凭据。若用户真要网络作业也在无人登录后立即恢复，必须现场验证受限后台核心服务的配置与启动依赖，或明确保留“用户需登录才能恢复联网”的限制；不加明文自动登录兜底。

ZCode登录/OAuth只在用户在场时完成；重启应用以继承新的网络设置前先保存WIP，不强杀正在执行的工作。

## 6. 验收不是看一个IP

分两层验收：先用已有浏览器/curl、客户端监听和退出恢复确认引导网络，随后允许安装缺失工具；Git/Python/ZCode未装时标deferred，ComfyUI启动后才测8188/MCP，不让未安装的软件挡住安装步骤。记录每项成功/失败而非“连上节点=全好”：

1. 客户端版本/资产hash/运行时完整，系统代理及loopback监听与预期一致，无LAN意外暴露。
2. 浏览器访问官方项目/文档正常；`curl.exe --proxy http://127.0.0.1:实际端口 --head https://github.com`仅当端口是HTTP/mixed才用。TLS校验保持开启。
3. `git ls-remote https://github.com/git/git.git HEAD`、Python官方包索引查询、小文件HF官方读取、ZCode实际登录/模型请求分别测试；不能以HTTP200代替这些业务验收。
4. `http://127.0.0.1:8188/system_stats`（ComfyUI启动后）和真实LAN设备仍直连可用；代理不能截断本地生成器/MCP。
5. 按系统代理/TUN模式核DNS解析、直连路径、上游出口和IPv6；不保证客户端自动提供隐私/匿名性。
6. 正常退出/暂停核心后，系统网络行为与预期相符。**恢复原先代理/PAC/绕过/DNS/路由值，而不是一概清空或设ProxyEnable=0**（用户可能原来有企业代理）。新机也先读取再说。
7. 注销/重启恢复按上一节实测；不要自动重启，安排人工窗口。未提供订阅仅能离线验收UI，状态pending。

## 7. 保护订阅与回滚

[官方隐私说明](https://www.clashverge.dev/privacy.html)提示配置和profiles可能含订阅URL/token/节点密码等，日志含域名/IP元数据；远程WebDAV备份不等于已剔除订阅秘密。

- 订阅只在目标本机受限ACL目录保存，不进入公开备份、ZIP、issue、截图或诊断全文。备份需要加密/合适ACL，记录路径和权限但不记录秘密值；token泄漏需在服务商处轮换，代理作者“无遥测”不等于不会有订阅/DNS/更新请求。
- 先保存系统代理相关键、WinHTTP现状、原DNS/路由和客户端设置；秘密备份独立于可分享变更台账。
- 回滚按字段恢复旧值，正常退出而非`taskkill`；若网络断而GUI不在，按预先记录的本地恢复步骤让用户在场恢复，不一键网络重置。
- 不自动删除配置、卸载网卡/软件、清profiles或清全局Git配置；移除/卸载是另一次需要授权的动作。

本次没有订阅、没有代理安装或联网实测，不宣称配置完成。该客户端是合法网络工具，不提供任何规避H3地域许可/美区店铺身份与准入要求的能力。
