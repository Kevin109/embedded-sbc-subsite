# Embedded-SBC.com 内容盘点与 100 篇文章路线图

> 最后更新：2026-07-31
>
> 维护范围：`content/posts/` 中的文章；7 个顶层 Hub 页面单独统计。
>
> 当前状态：53 篇已发布文章、7 个 Hub 页面、100 篇计划文章（3/100 已完成）。

## 状态标记规则

| 标记 | 状态 | 使用时机 |
|---|---|---|
| ⬜ | Planned | 已列入计划，尚未开始 |
| 🔎 | Research | 正在收集资料、关键词和案例 |
| ✍️ | Writing | 已开始写作 |
| 🧪 | Review | 正在进行技术、语言、SEO 或上线检查 |
| ✅ | Published | 已发布；标题必须链接到文章文件 |
| ⏸️ | On hold | 暂缓；在备注中写明原因 |

每篇计划文章都有唯一的 `ESB-Pxxx` 编号。开始写作后，文章 Front Matter 必须包含：

```yaml
roadmap_id: "ESB-P001"
roadmap_status: "writing"
```

发布时必须同时完成三处更新：

1. 将文章的 `draft` 设为 `false`，并将 `roadmap_status` 改为 `published`。
2. 将本路线图对应行改为 `✅`，并把标题链接到 `content/posts/<slug>.md`。
3. 更新本文顶部的完成数和“最后更新”日期。

## 全站现有内容结论

- 现有文章全部围绕嵌入式 SBC 展开，主轴清晰：SBC/SoC 选型、BSP、工业系统、接口、边缘 AI 和量产。
- 已有 50 篇文章，按发布日期连续分布在 2026-01-07 至 2026-06-30；Hugo 当前会将未显式设置 `draft` 的文章视为可发布内容。
- 已有 7 个 Hub 页面：Embedded SBC、Custom Embedded Systems、Embedded SoC、Edge AI Computing、Industrial Embedded Computing、Embedded Interfaces、Firmware & BSP。
- 现有文章约 600–3,300 词。部分早期文章偏短，后续可另立“内容刷新计划”，但不占用本次新增 100 篇名额。
- 现有主题覆盖“是什么”和“如何选”较多；下一阶段重点补齐可执行的设计、调试、验证、量产、安全合规和运维流程。
- Hub 架构已经建立，但文章到 Hub 的回链覆盖仍低；每篇新文章至少链接所属 Hub、2 篇现有文章和 1 篇同批次文章。

## 现有 50 篇文章清单

以下均标记为已发布基线内容，不占用 `ESB-P001`–`ESB-P100`。

| 日期 | 状态 | 文章 | 主要内容簇 |
|---|---|---|---|
| 2026-01-07 | ✅ | [Introduction to Embedded SBCs](../content/posts/embedded-sbc-intro.md) | Embedded SBC |
| 2026-01-10 | ✅ | [Overview of SBCs](../content/posts/sbc-overview.md) | Embedded SBC |
| 2026-01-12 | ✅ | [SBC vs SOM vs Custom Board for Embedded Products](../content/posts/sbc-vs-som-vs-custom-board.md) | Embedded SBC |
| 2026-01-15 | ✅ | [How to Select the Right SBC](../content/posts/sbc-selection-guide.md) | Embedded SBC |
| 2026-01-18 | ✅ | [NXP i.MX SBC Selection for Embedded Products](../content/posts/nxp-imx-embedded-sbc-selection.md) | Embedded SoC |
| 2026-01-20 | ✅ | [Industrial Linux](../content/posts/industrial-linux.md) | Industrial Computing |
| 2026-01-24 | ✅ | [Embedded SoC Selection Matrix for Product Teams](../content/posts/embedded-soc-selection-matrix.md) | Embedded SoC |
| 2026-01-27 | ✅ | [Custom Embedded Systems](../content/posts/custom-embedded-systems.md) | Custom Systems |
| 2026-01-31 | ✅ | [Compute Module Carrier Board Design for Embedded Products](../content/posts/compute-module-carrier-board-design.md) | Custom Systems |
| 2026-02-03 | ✅ | [Customizing Android BSP](../content/posts/custom-android-bsp-development.md) | Firmware & BSP |
| 2026-02-06 | ✅ | [Choosing SoCs for Custom Embedded Systems](../content/posts/custom-embedded-soc-selection-nxp-st-qualcomm-mtk.md) | Embedded SoC |
| 2026-02-09 | ✅ | [Future of Embedded Software](../content/posts/future-of-embedded-software.md) | Firmware & BSP |
| 2026-02-12 | ✅ | [Fanless Industrial Embedded Computer Design](../content/posts/fanless-industrial-embedded-computer-design.md) | Industrial Computing |
| 2026-02-15 | ✅ | [The Right Linux Distro](../content/posts/the-right-linux-distro.md) | Firmware & BSP |
| 2026-02-18 | ✅ | [RS485, CAN, and Ethernet Interface Planning](../content/posts/rs485-can-ethernet-interface-planning.md) | Embedded Interfaces |
| 2026-02-21 | ✅ | [Linux Cross-Compilation](../content/posts/linux-cross-compilation.md) | Firmware & BSP |
| 2026-02-24 | ✅ | [Embedded BSP Bring-Up Checklist](../content/posts/embedded-bsp-bring-up-checklist.md) | Firmware & BSP |
| 2026-02-27 | ✅ | [Android SBC Overview](../content/posts/android-sbc-overview.md) | Embedded SBC |
| 2026-03-02 | ✅ | [Edge AI Hardware Selection for Embedded Products](../content/posts/edge-ai-hardware-selection.md) | Edge AI |
| 2026-03-07 | ✅ | [Understanding Serial Ports](../content/posts/understanding-serial-ports-in-single-board-computers.md) | Embedded Interfaces |
| 2026-03-08 | ✅ | [Industrial IoT Gateway Design for Embedded Systems](../content/posts/industrial-iot-gateway-design.md) | Industrial Computing |
| 2026-03-14 | ✅ | [Display and Touch Interface Integration for Embedded SBCs](../content/posts/display-touch-interface-integration.md) | Embedded Interfaces |
| 2026-03-20 | ✅ | [Secure Firmware Update and Rollback for Embedded Products](../content/posts/secure-firmware-update-rollback.md) | Firmware & BSP |
| 2026-03-26 | ✅ | [TI Embedded Processors for Industrial Products](../content/posts/ti-embedded-processors-industrial-products.md) | Embedded SoC |
| 2026-04-01 | ✅ | [Industrial HMI Hardware Design for Embedded Products](../content/posts/industrial-hmi-hardware-design.md) | Industrial Computing |
| 2026-04-07 | ✅ | [Device Tree Review Checklist for Embedded Linux Boards](../content/posts/device-tree-review-checklist.md) | Firmware & BSP |
| 2026-04-13 | ✅ | [Edge AI Gateway Design for Industrial Systems](../content/posts/edge-ai-gateway-design-industrial-systems.md) | Edge AI |
| 2026-04-19 | ✅ | [Camera Pipeline Design for Edge AI Vision Products](../content/posts/camera-pipeline-edge-ai-vision.md) | Edge AI |
| 2026-04-25 | ✅ | [Embedded SBC Power Input Design for Product Reliability](../content/posts/embedded-sbc-power-input-design.md) | Custom Systems |
| 2026-05-01 | ✅ | [eMMC, microSD, and NVMe Storage Reliability for Embedded SBCs](../content/posts/embedded-sbc-storage-reliability.md) | Embedded SBC |
| 2026-05-02 | ✅ | [SoCs Used in Android SBCs](../content/posts/Choosing-SoCs-for-Android-SBCs.md) | Embedded SoC |
| 2026-05-05 | ✅ | [Rockchip SoCs](../content/posts/rockchip-socs.md) | Embedded SoC |
| 2026-05-07 | ✅ | [Embedded SBC Product Validation Checklist](../content/posts/embedded-sbc-product-validation-checklist.md) | Industrial Computing |
| 2026-05-13 | ✅ | [Embedded Product Requirements Specification](../content/posts/embedded-product-requirements-specification.md) | Custom Systems |
| 2026-05-19 | ✅ | [Factory Test Fixture Design for Embedded Products](../content/posts/factory-test-fixture-design-embedded-products.md) | Production |
| 2026-05-25 | ✅ | [Custom Embedded System Cost Reduction Without Reliability Loss](../content/posts/custom-embedded-system-cost-reduction.md) | Custom Systems |
| 2026-05-31 | ✅ | [NXP vs ST vs TI Embedded SoC Selection](../content/posts/nxp-vs-st-vs-ti-embedded-soc.md) | Embedded SoC |
| 2026-06-04 | ✅ | [Qualcomm and MediaTek Platforms for Connected Edge Devices](../content/posts/qualcomm-mediatek-connected-edge-devices.md) | Embedded SoC |
| 2026-06-08 | ✅ | [Low-Power Embedded SoC Selection](../content/posts/low-power-embedded-soc-selection.md) | Embedded SoC |
| 2026-06-11 | ✅ | [Edge AI Model Deployment Workflow](../content/posts/edge-ai-model-deployment-workflow.md) | Edge AI |
| 2026-06-14 | ✅ | [Edge AI Thermal Budget Planning](../content/posts/edge-ai-thermal-budget-planning.md) | Edge AI |
| 2026-06-17 | ✅ | [Edge AI Data Logging and Field Feedback](../content/posts/edge-ai-data-logging-field-feedback.md) | Edge AI |
| 2026-06-19 | ✅ | [Industrial Embedded Enclosure Design](../content/posts/industrial-embedded-enclosure-design.md) | Industrial Computing |
| 2026-06-21 | ✅ | [EMC and ESD Design Checklist for Embedded Systems](../content/posts/emc-esd-design-checklist-embedded-systems.md) | Industrial Computing |
| 2026-06-23 | ✅ | [Field Diagnostics for Embedded Industrial Devices](../content/posts/field-diagnostics-embedded-industrial-devices.md) | Industrial Computing |
| 2026-06-25 | ✅ | [USB and PCIe Expansion Planning for Embedded SBCs](../content/posts/usb-pcie-expansion-planning-embedded-sbc.md) | Embedded Interfaces |
| 2026-06-27 | ✅ | [MIPI CSI vs USB Camera for Embedded Vision](../content/posts/mipi-csi-vs-usb-camera-embedded-vision.md) | Embedded Interfaces |
| 2026-06-28 | ✅ | [GPIO, Relay, and Isolated Input Design](../content/posts/gpio-relay-isolated-input-design.md) | Embedded Interfaces |
| 2026-06-29 | ✅ | [Factory Flashing Workflow for Embedded Linux Products](../content/posts/factory-flashing-workflow-embedded-linux.md) | Firmware & BSP |
| 2026-06-30 | ✅ | [Secure Boot Key Management for Embedded Products](../content/posts/secure-boot-key-management-embedded-products.md) | Firmware & BSP |

## 未来 100 篇：推荐写作顺序

顺序按“先补流量入口和决策内容，再补实施、量产和高级专题”排列。英文标题是建议发布标题；写作时可以根据关键词研究微调，但不得复用编号。

### Wave 1：核心缺口（ESB-P001–ESB-P025）

| ID | 状态 | Hub | 建议文章标题 | 核心任务 |
|---|---|---|---|---|
| ESB-P001 | ✅ | Firmware & BSP | [Yocto vs Buildroot for Production Embedded Linux](../content/posts/yocto-vs-buildroot-production-embedded-linux.md) | 用团队规模、更新周期、构建时间和维护成本做选择 |
| ESB-P002 | ✅ | Embedded SoC | [RK3568 vs RK3576 vs RK3588 for Embedded Products](../content/posts/rk3568-vs-rk3576-vs-rk3588-embedded-products.md) | 比较 CPU/NPU、显示、摄像头、功耗与产品定位 |
| ESB-P003 | ✅ | Embedded SBC | [How to Turn Product Requirements into an SBC Specification](../content/posts/product-requirements-to-sbc-specification.md) | 把 PRD 转成可采购、可验证的板卡规格 |
| ESB-P004 | ⬜ | Firmware & BSP | Android A/B OTA Updates on Embedded SBCs | 讲清分区、签名、失败回滚和量产验证 |
| ESB-P005 | ⬜ | Industrial Computing | Designing a Modbus RTU-to-TCP Industrial Gateway | 覆盖隔离、轮询、缓存、异常恢复与测试 |
| ESB-P006 | ⬜ | Edge AI | How to Benchmark Edge AI Hardware with a Real Workload | 建立延迟、吞吐、功耗、温度和准确率方法 |
| ESB-P007 | ⬜ | Custom Systems | Custom SBC Schematic Review Checklist | 面向电源、时钟、启动、DDR、接口和调试的审查表 |
| ESB-P008 | ⬜ | Embedded SBC | ARM vs x86 for Industrial Embedded Systems | 比较性能、功耗、BSP、生命周期与维护 |
| ESB-P009 | ⬜ | Firmware & BSP | A/B Partition Layout for Reliable Embedded Linux OTA | 从存储布局到掉电恢复给出落地方案 |
| ESB-P010 | ⬜ | Embedded SoC | NXP i.MX 93 vs i.MX 95 for New Embedded Designs | 聚焦工业 HMI、网关、AI 与实时控制取舍 |
| ESB-P011 | ⬜ | Embedded Interfaces | MIPI DSI vs LVDS vs eDP vs HDMI for Embedded Displays | 按分辨率、线缆、EMI、成本和驱动支持选择 |
| ESB-P012 | ⬜ | Embedded SBC | SBC Lifecycle and Obsolescence Planning | 建立 EOL、替代料、BSP 和库存风险机制 |
| ESB-P013 | ⬜ | Firmware & BSP | Android Kiosk Mode and Device Owner for Dedicated Devices | 面向 HMI、终端和自助设备的锁定与运维 |
| ESB-P014 | ⬜ | Custom Systems | Design for Manufacturing Checklist for Custom Embedded Boards | 从 PCB 到装配、工艺边和可制造性审查 |
| ESB-P015 | ⬜ | Firmware & BSP | SBOM and CVE Response for Embedded Linux Products | 建立组件清单、漏洞判断、修复和客户通知流程 |
| ESB-P016 | ⬜ | Embedded Interfaces | Ethernet PHY, Magnetics, and Connector Design for SBCs | 覆盖选型、走线、隔离、PoE 冲突和验证 |
| ESB-P017 | ⬜ | Edge AI | INT8 Quantization for Edge AI: Accuracy, Speed, and Calibration | 解释量化数据、算子限制和验收标准 |
| ESB-P018 | ⬜ | Custom Systems | EVT, DVT, and PVT for Embedded Hardware Products | 给出各阶段输入、样机数、测试和退出条件 |
| ESB-P019 | ⬜ | Embedded SBC | Embedded Linux Boot Time Optimization | 用可测量的启动链分析缩短上电到可用时间 |
| ESB-P020 | ⬜ | Embedded Interfaces | CAN FD Interface Design for Embedded Linux SBCs | 覆盖控制器、收发器、终端、隔离和 SocketCAN |
| ESB-P021 | ⬜ | Firmware & BSP | Secure Device Identity and Factory Key Provisioning | 连接安全存储、证书、工厂工位和审计 |
| ESB-P022 | ⬜ | Firmware & BSP | Yocto Layer Architecture for a Maintainable Product BSP | 分离上游、SoC、板级、产品和客户层 |
| ESB-P023 | ⬜ | Edge AI | Multi-Camera Bandwidth and Memory Planning for Edge Vision | 计算 CSI、ISP、DDR、编码与推理数据流 |
| ESB-P024 | ⬜ | Industrial Computing | Wide-Temperature Validation for Industrial SBC Products | 设计冷热启动、满载、存储和接口测试 |
| ESB-P025 | ⬜ | Firmware & BSP | AOSP, CTS, VTS, and GMS: What Embedded Android Teams Need | 澄清认证边界、成本、测试和发布影响 |

### Wave 2：工程实施（ESB-P026–ESB-P050）

| ID | 状态 | Hub | 建议文章标题 | 核心任务 |
|---|---|---|---|---|
| ESB-P026 | ⬜ | Embedded SBC | How Much RAM Does an Embedded SBC Really Need? | 按 UI、容器、AI、缓存和峰值留量估算 |
| ESB-P027 | ⬜ | Embedded SoC | STM32MP1 vs STM32MP2 for Industrial Linux Products | 比较算力、实时域、图形、生态与迁移代价 |
| ESB-P028 | ⬜ | Firmware & BSP | Android Boot Flow and Partition Layout on Custom SBCs | 串起 Boot ROM、bootloader、AVB、动态分区与启动 |
| ESB-P029 | ⬜ | Firmware & BSP | Device Tree Overlays for Product Variants | 管理显示、扩展板和客户配置而不复制整套 DTS |
| ESB-P030 | ⬜ | Custom Systems | PMIC Selection and Power Sequencing for Embedded SoCs | 处理电源轨、时序、复位、睡眠和故障 |
| ESB-P031 | ⬜ | Industrial Computing | OPC UA Edge Gateway Architecture | 覆盖信息模型、安全、缓存与云端连接边界 |
| ESB-P032 | ⬜ | Industrial Computing | MQTT Store-and-Forward for Unreliable Industrial Networks | 设计本地队列、QoS、去重、时钟和恢复 |
| ESB-P033 | ⬜ | Edge AI | NPU Operator Compatibility: Why Models Fail to Compile | 从算子、形状、量化到 CPU 回退定位问题 |
| ESB-P034 | ⬜ | Embedded Interfaces | PCIe Signal Integrity and Bring-Up for Custom Carrier Boards | 覆盖拓扑、参考时钟、复位、走线和训练失败 |
| ESB-P035 | ⬜ | Embedded Interfaces | USB 3.0 Signal Integrity for Embedded Boards | 聚焦阻抗、连接器、ESD、损耗与眼图验证 |
| ESB-P036 | ⬜ | Embedded SBC | Hardware and Software Watchdogs for Unattended Devices | 设计分层看门狗、健康信号和安全恢复 |
| ESB-P037 | ⬜ | Embedded SBC | RTC, NTP, and Timekeeping After Power Loss | 处理时间可信度、日志顺序、电池与网络恢复 |
| ESB-P038 | ⬜ | Firmware & BSP | Vendor BSP Fork or Upstream Linux: How to Decide | 评估上市速度、驱动、维护、CVE 与升级成本 |
| ESB-P039 | ⬜ | Industrial Computing | Crash Dumps and Persistent Logs on Embedded Linux | 在有限存储和频繁掉电条件下保留诊断证据 |
| ESB-P040 | ⬜ | Custom Systems | PCB and Enclosure Co-Design for Embedded Products | 协调连接器、安装、散热、天线和装配空间 |
| ESB-P041 | ⬜ | Custom Systems | Grounding and Shielding for Noisy Embedded Installations | 讲清机壳地、信号地、屏蔽层和接地点 |
| ESB-P042 | ⬜ | Custom Systems | Design for Test: Test Points, Boundary Scan, and Fixtures | 从原理图阶段规划产测覆盖和故障定位 |
| ESB-P043 | ⬜ | Embedded SoC | Memory Bandwidth Budgeting for Display, Camera, and AI | 用并发数据流判断 DDR 带宽是否足够 |
| ESB-P044 | ⬜ | Embedded Interfaces | Capacitive Touch Controller Integration and Debugging | 覆盖 I2C、复位、中断、固件、噪声和校准 |
| ESB-P045 | ⬜ | Edge AI | Lens, Lighting, and Exposure Design for Machine Vision | 把光学与照明纳入 AI 准确率工程 |
| ESB-P046 | ⬜ | Firmware & BSP | SELinux Policy Development for Embedded Android | 从 permissive 到 enforcing 的调试和收敛流程 |
| ESB-P047 | ⬜ | Firmware & BSP | Hardware-in-the-Loop CI for Embedded Linux BSPs | 自动验证启动、接口、更新、恢复与板卡版本 |
| ESB-P048 | ⬜ | Industrial Computing | Wi-Fi and Bluetooth Coexistence in Embedded Devices | 处理天线、射频、共存机制和吞吐测试 |
| ESB-P049 | ⬜ | Industrial Computing | Cellular Gateway Design: LTE, 5G, SIM, and Recovery | 覆盖模块接口、运营商、断线恢复和远程诊断 |
| ESB-P050 | ⬜ | Firmware & BSP | Threat Modeling for Connected Embedded Products | 从资产、攻击面、信任边界到安全需求 |

### Wave 3：量产与维护（ESB-P051–ESB-P075）

| ID | 状态 | Hub | 建议文章标题 | 核心任务 |
|---|---|---|---|---|
| ESB-P051 | ⬜ | Firmware & BSP | Reproducible Embedded Linux Builds in CI | 固定源、工具链、容器、缓存和产物证明 |
| ESB-P052 | ⬜ | Firmware & BSP | U-Boot Boot Flow for Custom Embedded Boards | 解释环境、启动目标、FIT、设备树和恢复入口 |
| ESB-P053 | ⬜ | Firmware & BSP | Verified Boot vs Measured Boot for Embedded Products | 比较阻止篡改与记录度量的架构和适用场景 |
| ESB-P054 | ⬜ | Firmware & BSP | Linux LTS Kernel and Vendor BSP Upgrade Strategy | 制定补丁、回归、硬件兼容与长期维护节奏 |
| ESB-P055 | ⬜ | Firmware & BSP | Android Security Patch Management for Long-Life Devices | 建立公告评估、补丁移植、验证与版本发布 |
| ESB-P056 | ⬜ | Firmware & BSP | Android Display and Touch Bring-Up Workflow | 面向 DRM/显示时序、输入、旋转和休眠调试 |
| ESB-P057 | ⬜ | Firmware & BSP | Porting a Linux Driver to a Custom SBC | 从绑定、probe、时钟、电源到上游质量 |
| ESB-P058 | ⬜ | Firmware & BSP | Recovery Modes for Field-Deployed Embedded Devices | 设计本地、远程、USB 和最小救援系统 |
| ESB-P059 | ⬜ | Custom Systems | BOM Lifecycle Management and Qualified Alternates | 建立 AVL、PCN、替代验证和版本追溯 |
| ESB-P060 | ⬜ | Embedded SBC | SBC Mechanical and Connector Review Checklist | 检查安装孔、连接器方向、线缆、应力与维修 |
| ESB-P061 | ⬜ | Embedded SBC | eMMC Capacity Planning for Logs, OTA, and Product Life | 把分区、写放大、双系统和增长余量量化 |
| ESB-P062 | ⬜ | Embedded Interfaces | USB-C Power Delivery for Embedded Products | 明确供电角色、协商、保护、调试与认证风险 |
| ESB-P063 | ⬜ | Embedded Interfaces | Power over Ethernet for SBC-Based Devices | 比较 PoE 架构、功率等级、隔离、热与布线 |
| ESB-P064 | ⬜ | Custom Systems | Cable Harness and Connector Design for Reliable Products | 覆盖防呆、锁扣、线规、屏蔽、标签和测试 |
| ESB-P065 | ⬜ | Industrial Computing | Shock and Vibration Testing for Embedded Devices | 把安装、连接器、焊点和记录方法纳入验证 |
| ESB-P066 | ⬜ | Industrial Computing | EMC Pre-Compliance Testing Before the Certification Lab | 用近场探头、LISN 和整改闭环降低实验室风险 |
| ESB-P067 | ⬜ | Industrial Computing | DIN-Rail Embedded Computer Design | 处理尺寸、端子、散热、接地与现场安装 |
| ESB-P068 | ⬜ | Industrial Computing | IP-Rated Enclosure Design Without Thermal Surprises | 平衡密封、透气、防水和热路径 |
| ESB-P069 | ⬜ | Industrial Computing | Dual-Ethernet Architecture for Industrial Gateways | 比较交换、路由、隔离、冗余和防火墙方案 |
| ESB-P070 | ⬜ | Industrial Computing | NTP, PTP, and GNSS Time Synchronization at the Edge | 按精度、成本、网络条件和失锁行为选型 |
| ESB-P071 | ⬜ | Edge AI | TensorFlow Lite vs ONNX Runtime vs Vendor NPU SDKs | 比较可移植性、性能、算子与维护成本 |
| ESB-P072 | ⬜ | Edge AI | Edge AI Model Versioning, OTA, and Rollback | 将模型作为可审计、可恢复的软件资产管理 |
| ESB-P073 | ⬜ | Edge AI | Dataset Governance for Deployed Vision Products | 管理来源、标签、版本、偏差和隐私 |
| ESB-P074 | ⬜ | Edge AI | Detecting Model Drift in Edge AI Deployments | 用现场指标、抽样、反馈和再训练闭环发现退化 |
| ESB-P075 | ⬜ | Edge AI | Privacy-by-Design for Edge Vision Systems | 讨论本地处理、数据最小化、脱敏和留存 |

### Wave 4：高级专题与纵深（ESB-P076–ESB-P100）

| ID | 状态 | Hub | 建议文章标题 | 核心任务 |
|---|---|---|---|---|
| ESB-P076 | ⬜ | Embedded SBC | A Practical SBC Performance Benchmarking Method | 避免只看跑分，覆盖持续负载、I/O、温度和功耗 |
| ESB-P077 | ⬜ | Embedded SBC | Total Cost of Ownership for Embedded SBC Platforms | 计算板卡之外的软件、认证、停产和现场维护成本 |
| ESB-P078 | ⬜ | Embedded SBC | From Development Board to Production Hardware | 识别电源、连接器、BSP、供应和认证差距 |
| ESB-P079 | ⬜ | Industrial Computing | Redundancy and High Availability for Edge Gateways | 设计双机、心跳、状态同步和故障切换 |
| ESB-P080 | ⬜ | Custom Systems | Supercapacitor and Backup Power for Graceful Shutdown | 计算保持时间、掉电检测、存储落盘和寿命 |
| ESB-P081 | ⬜ | Custom Systems | PCB Stackup Planning for High-Speed Embedded Boards | 平衡阻抗、回流路径、层数、EMI 与成本 |
| ESB-P082 | ⬜ | Custom Systems | DDR on a Custom Board vs Using a System-on-Module | 用风险、数量、尺寸、成本与进度做边界判断 |
| ESB-P083 | ⬜ | Custom Systems | Requirements Traceability for Embedded Product Development | 串联需求、设计、测试、缺陷和版本发布 |
| ESB-P084 | ⬜ | Embedded SoC | How to Verify an SoC Vendor's Lifecycle Claims | 检查路线图、PCN、软件分支、替代方案和合同证据 |
| ESB-P085 | ⬜ | Embedded SoC | Why NPU TOPS Does Not Predict Edge AI Performance | 解释数据类型、利用率、带宽、算子和散热限制 |
| ESB-P086 | ⬜ | Embedded SoC | Multimedia Pipeline Planning for HMI and Signage SoCs | 预算解码、合成、显示、摄像头和内存带宽 |
| ESB-P087 | ⬜ | Embedded SoC | TPM, Secure Elements, and SoC TEEs Compared | 为身份、密钥、证明和安全启动选择信任根 |
| ESB-P088 | ⬜ | Embedded SoC | Linux MPU Plus Real-Time MCU Architecture | 划分实时任务、通信、故障域、更新和调试 |
| ESB-P089 | ⬜ | Embedded SoC | RISC-V for Embedded Linux Products: Readiness Checklist | 从生态、BSP、工具链、供货和风险判断可用性 |
| ESB-P090 | ⬜ | Embedded SoC | TI AM62x vs AM64x for Industrial Product Design | 对比 HMI、实时网络、控制、功耗与软件栈 |
| ESB-P091 | ⬜ | Industrial Computing | When Does an Industrial Gateway Need TSN? | 从确定性、网络拓扑、软件栈和测试判断需求 |
| ESB-P092 | ⬜ | Industrial Computing | Choosing Industrial Protocols for a New Edge Device | 比较 Modbus、CANopen、OPC UA、EtherCAT 等边界 |
| ESB-P093 | ⬜ | Industrial Computing | Fleet Management Architecture for Embedded Linux Devices | 覆盖注册、配置、更新、分组、审计和离线设备 |
| ESB-P094 | ⬜ | Embedded Interfaces | I2C Reliability: Pull-Ups, Bus Capacitance, and Recovery | 计算上升时间并处理卡总线、长线和多电压域 |
| ESB-P095 | ⬜ | Embedded Interfaces | SPI Timing, Chip Select, and Signal Integrity | 处理模式、时序裕量、多从机和高速走线 |
| ESB-P096 | ⬜ | Embedded Interfaces | Safe GPIO States During Boot, Reset, and Firmware Update | 避免继电器、马达和电源控制在启动时误动作 |
| ESB-P097 | ⬜ | Embedded Interfaces | ADC and DAC Integration on Linux SBCs | 覆盖分辨率、参考源、噪声、采样和 IIO 驱动 |
| ESB-P098 | ⬜ | Embedded Interfaces | Embedded Audio Design: I2S, Codecs, Amplifiers, and Noise | 贯通数字音频、模拟布局、时钟和产品调音 |
| ESB-P099 | ⬜ | Edge AI | OCR at the Edge: Camera, Model, and Product Workflow | 从成像、检测、识别到语言和现场验收 |
| ESB-P100 | ⬜ | Edge AI | Acceptance Testing for Edge AI Products | 定义准确率、延迟、热、坏场景和回归门槛 |

## 选题分布

| Hub | 计划篇数 |
|---|---:|
| Firmware & BSP | 22 |
| Embedded SBC | 12 |
| Industrial Embedded Computing | 17 |
| Custom Embedded Systems | 13 |
| Embedded SoC | 11 |
| Embedded Interfaces | 13 |
| Edge AI Computing | 12 |
| **合计** | **100** |

## 每篇文章的最低交付标准

- 目标读者和要解决的问题明确，不写成仅堆砌概念的百科摘要。
- 建议正文 1,200–2,000 英文词；复杂指南可更长，窄主题可适当缩短。
- Front Matter 至少包含 `title`、`seo_title`、`description`、`date`、`draft`、`roadmap_id`、`roadmap_status`、`keywords`、`cover` 和 `images`。
- 至少包含一个可执行资产：检查表、决策矩阵、计算示例、测试步骤、故障树或验收标准。
- 至少 4 条站内链接：所属 Hub 1 条、现有文章 2 条、同路线图文章 1 条；链接必须与上下文相关。
- 技术版本、法规、生命周期、支持期和产品规格在发布前必须用官方一手资料复核。
- 发布前运行 Hugo 构建，检查图片、链接、Front Matter、标题层级和移动端阅读效果。

## 规划依据（2026-07-12 快照）

这些一手资料用于确认未来内容应覆盖长期维护、可复现构建、设备管理、供应链安全和法规准备：

- [Buildroot manual](https://buildroot.org/downloads/manual/manual.html)：包含可复现构建、SBOM、包与板级支持等生产主题。
- [Yocto Project documentation](https://docs.yoctoproject.org/)：适合持续跟踪 LTS、分层 BSP 和产品发行工程。
- [Zephyr device management documentation](https://docs.zephyrproject.org/latest/services/device_mgmt/index.html)：覆盖设备管理、DFU 和 OTA。
- [U-Boot verified boot documentation](https://docs.u-boot.org/en/latest/usage/fit/verified-boot.html) 与 [measured boot documentation](https://docs.u-boot.org/en/latest/usage/measured_boot.html)：支持安全启动专题拆分。
- [NISTIR 8259 Series](https://www.nist.gov/itl/applied-cybersecurity/nist-cybersecurity-iot-program/nistir-8259-series)：覆盖 IoT 产品制造商的设备能力与支持流程。
- [EU Cyber Resilience Act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act)：报告义务自 2026-09-11 起适用，主要义务自 2027-12-11 起适用，安全维护与漏洞响应应提前布局。
