# 本地视频生成集成规格

这是一份无需私有项目资料即可实施的规格，**不是已有worker代码或已验证工作流**。与START-HERE中的Windows初始化衔接；云视频预算0，终点为人工待审，不自动登录/上传/挂车。

## 1. 技术路线

1. 真实商品/自有拍摄或有权素材 → FFmpeg确定性剪辑/字幕/配音 → 第一条可审草稿。
2. 原生ComfyUI管理本地视频推理。单GPU、batch1，不同时常驻大LLM抢显存。先官方节点/模板，无需缺节点就任意自动安装插件。
3. Wan2.2 TI2V-5B作为低复杂度试验基线；H3量化按用户偏好做独立试验；LTX2.5为后续候选，不第一轮同时下载所有权重。
4. ComfyTV可作画布，但其MCP写依赖页面，**不能代替无人值守持久队列**。实际LICENSE与README/嵌入组件声明有冲突，安装日再核组件许可。
5. 外部Python worker+SQLite WAL、FFmpeg合成、结果校验、人工审核、监控属于接手代理要实现的薄集成，不是ComfyUI默认完整交付。

## 2. 环境和官方入口

- ComfyUI：https://github.com/Comfy-Org/ComfyUI
- API路由：https://docs.comfy.org/development/comfyui-server/comms_routes
- API示例：https://github.com/Comfy-Org/ComfyUI/blob/master/script_examples/basic_api_example.py
- ComfyTV：https://github.com/jtydhr88/ComfyTV
- Wan2.2：https://github.com/Wan-Video/Wan2.2
- H3：https://github.com/MiniMax-AI/MiniMax-H3
- H3本地教程：https://docs.comfy.org/tutorials/video/minimax/minimax-h3
- H3许可证：https://huggingface.co/MiniMaxAI/MiniMax-H3/raw/main/LICENSE
- LTX：https://github.com/Lightricks/LTX-2

Windows原生managed venv，Python3.12仅兼容起点，按实际节点/受维护版本选择。ComfyUI v0.41.0是2026-10-18研究锚点，不是今天无需验证的万能组合。记录OS/driver/Python/torch/CUDA runtime/ComfyUI与节点commit、模型revision/hash、UI模板与API图hash。新版本在独立环境测试，生产保留离线回滚副本，不直接pull最新。

**32GB系统RAM可能先成为大模型offload限制。** H3还包含Qwen3-VL32B encoder与音视频VAE，不能凭单个16/21GB扩散文件认定整套塞进24GB显存。先5秒/480p等级/batch1，测每阶段RAM/VRAM，页文件仅防崩兜底，不是额外显存。持续换页或OOM时停该lane，其他模板/模型路径继续；硬件升级另行决定。

## 3. 模型与素材许可门

每个权重/encoder/LoRA/声线/音乐/字体都要核具体版本；代码MIT/Apache不替其他资产授权。下载需登录、接受协议或申请许可时由用户在目标机处理。

**H3地域限制涉及Outputs/results，不只权重下载。** 2026-08-02版协议I.3/I.5列出排除美国等地区；V.4涉及适用地域外使用/分发/展示输出。若目标是美国商业展示，未取得覆盖用途的授权或明确书面说明前，H3实验结果隔离，不进获批成片。机器位置或网络代理不能解除条款，API服务也须另核实际合同。

Wan2.2 Apache声明也需核全部组件和输入权利；LTX2.x自有社区许可有营收/用途条件。生成器不能伪造买家体验、名人背书、产品功能或印花工艺。固定图层保留文字不被模型重画，但不自动保证布料形变/产品真实性，必要镜头用实拍。

## 4. 队列与恢复契约

- SQLite记录job、intent、prompt_id、lease、artifacts、append-only events与费用；jobs投影可事务更新，事件不可篡改。
- 使用ComfyUI **API-format JSON**（class_type/inputs），不是UI画布JSON直接发`/prompt`。现场用`/object_info`验证节点，导出/参数绑定与hash固定。
- intent包含所有素材hash、SKU/变体/卖点、语言、权利引用、seed、模型/工作流/合成模板版本，注入API extra_data和输出名前缀，并验证能在queue/history查到。
- `/prompt`发出但prompt_id尚未落库即掉线：UNKNOWN，不盲重试。ComfyUI服务端不自动幂等；历史可能截断/重启丢失，**查不到也不证明没执行**，保留intent人工对账。
- 已有prompt_id时按`/queue`和`/history/{prompt_id}`对账恢复，不因为WebSocket断了就重跑。`/interrupt`只针对当前任务，先核归属防误杀。
- 单worker文件锁；失败分类型，坏输入隔离，连续失败熔断；磁盘低水位暂停大量写盘，不能以“轻合成”继续塞满。
- 代理未就绪时网络任务等待，已缓存且无需远程服务的本地任务可离线跑。后台AtStartup不依赖画布；凭据/BitLocker/UPS/开机恢复限制按目标机实测。
- 输出先staging，技术校验成功进review；人工批准绑定最终SHA。字幕/音轨/画面变更形成新版本重新审，不把批准A用于B。

## 5. 最小作业字段

job_id/intent_id、输入路径/hash/来源与权利、商品SKU/变体与实际样品证据、确认卖点/语言、每镜头来源/秒数、模型与工作流hash/参数/seed、合成模板版本、最终路径/hash/ffprobe报告、审片人/版本/结论/原因、原始日志。秘密只引用受限本地凭据位置，不把密钥值放入作业。

## 6. 验收

先一条真实素材草稿，再8–12条固定金标（不同衣色/文字、错SKU/缺权利负样本），冷/热各至少3次，记录p50/p95、峰值RAM/VRAM、墙上电表、人力和废片。按2h→8h→24h→72h逐级；故障/重启/关画布/注销/提交窗口歧义/磁盘不足/批准后换文件都做负测。7日观察另记，没跑不能提前宣称通过。

生成模型锁seed不保证跨driver/library bit-exact；回滚用语义/技术阈值复验，产物登记新hash。1080×1920、H264/AAC、15–25秒是候选成片建议，不是平台硬性时长或原生生成分辨率保证。

**当前所有Windows/GPU/联网/长稳验收未执行。** 先按START-HERE完成网络与底座，再由目标电脑实施代码与测试。本仓库不承诺吞吐、销售效果或零风险。
