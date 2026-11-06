# 本站内链 SEO 审计

审计日期：2026-11-05。范围：当前仓库默认生产构建，74 篇文章、7 个 Hub。以下原始审计为只读检查；后续补链实施结果见下方验收记录。

## 结论

基础抓取结构良好，但主题内链尚未达到优秀水平。首页 → Hub → 文章的框架已经存在，主要缺口是 Hub 未持续收录新文章、旧文章未补充指向新文章的上下文链接，以及文章到 Hub 的回链覆盖不足。

这些结论是结构与内容判断，不是 Google 排名分数；没有 Search Console 数据，不能据此推断索引率、流量或排名损失。


## 补链实施与验收（2026-11-05）

已落实本次指定的三项改进：

- 为原先没有正文入链的 5 篇文章，各新增 1 处相关旧文章正文推荐和 1 个对应 Hub 目录入口。
- 为遗漏的 25 篇文章补充 Hub 目录入口，按 Android 平台、生命周期、设计量产、性能验证、接口设计、构建启动、更新管理及安全维护等读者任务分组。
- 为原先缺少 Hub 正文链接的 39 篇文章添加与内容相关的导读句。

| 指标 | 修改前 | 修改后 |
|---|---:|---:|
| 至少被一个 Hub 正文收录的文章 | 49/74 | 74/74 |
| 正文链接到至少一个 Hub 的文章 | 35/74 | 74/74 |
| 没有其他正文入链的文章 | 5 | 0 |
| 首页最短距离超过 2 跳的文章 | 3 | 0 |
| 本地缺失链接目标 | 0 | 0 |

新增 69 处内链，新增锚文本全部为英文且不超过 4 词。既有文章 Front Matter、发布状态和路线图 ID 保持不变；未创建文章或占用新的路线图条目。`hugo --minify` 成功，并对实际构建 HTML 重新解析验证。

此轮仅处理上述三项。原审计中旧锚文本过长、空卡片链接、面包屑，以及其余 4 篇缺少其他文章正文入链的问题仍保留为后续事项；这 4 篇均已有 Hub 正文入口。下面的原始指标和待处理清单保留为修改前基线，不代表当前状态。

## 方法和统计口径

运行 `hugo --minify --destination /tmp/embedded-sbc-link-audit`，成功。解析实际生成 HTML 的 `<a href>`，核对本地目标与跨页片段，计算从首页出发的最短链接路径。7 个 alias 页面纳入目标检查，不作为文章计数。正文内链指 `.post-content`，包含 Hub Articles 等列表，排除菜单、标签、前后篇导航、当前页锚点。分页复制的首页正文已从正文链接总量去重；并非每条正文链接都属于段落中的上下文推荐。

线上首页已抽样核对：https://embedded-sbc.com/ 。未逐个请求线上 URL，失效链接结论仅适用于本地生成文件，不能替代线上 HTTP 状态检查。

## 核心指标

| 指标 | 结果 | 判断 |
|---|---:|---|
| 已生成文章 | 74 | 与路线图顶部统计相符 |
| 本地内链缺失目标 | 0 | 良好 |
| 跨页片段错误 | 0 | 良好；不含当前页 TOC 自锚点检查 |
| 指向 alias 页的内链 | 0 | 良好 |
| 首页不可达文章 | 0 | 无真正的抓取孤岛 |
| 首页最短距离 | 13 篇 1 跳、58 篇 2 跳、3 篇 3 跳 | 路径较浅；不代表 Google 的实际抓取频率 |
| 正文跨页内链 | 372 | 其中文章发出 279 条、Hub 发出 82 条，其余为首页等页面 |
| 无任何 Hub 正文回链的文章 | 39/74（52.7%） | 主要缺口 |
| 未被任何 Hub 正文收录的文章 | 25/74（33.8%） | Hub 更新未跟上文章发布 |
| 无其他正文入链的文章 | 5/74 | 只能依赖列表、导航等入口 |
| 无其他文章正文入链的文章 | 9/74 | 横向推荐不足 |
| 正文来源页数不超过 1 的文章 | 21/74 | 抗分页变化的主题入口偏少 |
| 指向其他文章少于 2 篇 | 4/74 | 对旧文章优先补齐 |
| 超过 4 个空格分隔词的正文锚文本 | 154/372 | 文章 97、Hub 50、其他 7；属于仓库规则问题 |
| 精确匹配泛化锚文本 | 0 | 未出现 click here/read more/this article/here/learn more |

“最多 4 个英文单词”是本站编辑规范，不是 Google 的硬性限制。统计按空格分词，带数字或符号的首页卡片可人工复核。Google 强调锚文本简洁、准确、自然和上下文相关，也明确没有理想链接数量。因此不能把超过 4 词全部等同于 SEO 惩罚，也不应为了数量加入无关链接。

## 优先处理项

### P1：为 5 篇无正文入链的文章建立主题入口

| 目标文章 | 建议收录 Hub | 建议添加链接的现有文章 | 建议锚文本 |
|---|---|---|---|
| android-kiosk-device-owner | Embedded SBC / Firmware & BSP | android-sbc-overview、custom-android-bsp-development | Android kiosk management |
| can-fd-interface-design-linux-sbc | Embedded Interfaces | rs485-can-ethernet-interface-planning | CAN FD design |
| choosing-socs-for-android-sbcs | Embedded SoC | android-sbc-overview、rockchip-socs | Android SoC selection |
| embedded-linux-boot-time-optimization | Firmware & BSP | embedded-bsp-bring-up-checklist、device-tree-review-checklist | Linux boot optimization |
| mipi-dsi-lvds-edp-hdmi-display-comparison | Embedded Interfaces | display-touch-interface-integration、industrial-hmi-hardware-design | display interface comparison |

以上是补链候选，需要在相关段落中加入适配上下文；不建议机械地追加统一链接块。CAN FD 和启动优化文章虽然已经链接旧文章及 Hub，但尚未获得反向正文推荐，说明新文章发布时缺少对既有内容的同步维护。

### P1：补齐 Hub 双向主题关系

| Hub | Hub 链接的不同文章数 | 正文链接此 Hub 的不同文章数 |
| /embedded-interfaces/ | 9 | 6 |
| /embedded-sbc/ | 10 | 7 |
| /industrial-embedded-computing/ | 10 | 3 |
| /edge-ai-computing/ | 9 | 5 |
| /embedded-firmware-bsp/ | 10 | 10 |
| /embedded-soc/ | 11 | 3 |
| /custom-embedded-systems/ | 9 | 3 |

主菜单在所有页面提供了 Hub 链接，所以“没有正文回链”不等于 Hub 不可抓取；其问题在于缺少文章与所属主题的明确上下文关系。按选型、设计、调试、验证、量产维护等读者任务分组更新 Hub，优先补入已发布的新文章。

### P2：改进锚文本与首页卡片

- `content/embedded-sbc.md` 的 “Embedded SBC Power Input Design for Product Reliability” 可缩为 “SBC power input design”。
- `content/embedded-firmware-bsp.md` 的 “Secure Boot Key Management for Embedded Products” 可缩为 “secure boot key management”。
- 首页 Hub 卡片的 `<a>` 包含序号、名称和说明，整个文本都成为锚文本；可仅链接主题名称，说明置于链接外。
- 首页 Featured Posts 的 “Custom Solutions” 不如 “custom embedded systems” 明确。
- `themes/PaperMod/layouts/_default/list.html:92` 使用空 `<a class="entry-link" aria-label="...">` 覆盖文章卡片。href 可抓取，但链接没有可见文本；建议让文章标题成为真实文本链接。aria-label 有助于无障碍，不能据此确认 Google 如何为该空链接提取锚文本。

不要为追求关键词覆盖反复塞入完全相同的精确匹配词；让链接符合所在句子的含义即可。

### P2：补齐旧文章的后续阅读路径

- `content/posts/Choosing-SoCs-for-Android-SBCs.md`：指向其他文章 1 篇。
- `content/posts/embedded-soc-selection-matrix.md`：指向其他文章 1 篇。
- `content/posts/rockchip-socs.md`：指向其他文章 1 篇。
- `content/posts/understanding-serial-ports-in-single-board-computers.md`：指向其他文章 0 篇。

其中串口文章没有指向其他文章的正文链接，可根据内容补充串口/RS485/CAN 接口规划、GPIO 与隔离设计等后续阅读。

### P3：改善主题导航

`layouts/partials/breadcrumbs.html` 根据物理父路径生成面包屑，并跳过名为 Blog 的节点；目前没有按文章所属 Hub 生成主题层级。可在确定主 Hub 映射后显示 Home → Hub → Article。前后篇导航可保留用于连续阅读，但不能替代主题相关链接。

## 完整待处理清单

### 无其他正文入链（5）
- `content/posts/android-kiosk-device-owner.md`
- `content/posts/can-fd-interface-design-linux-sbc.md`
- `content/posts/Choosing-SoCs-for-Android-SBCs.md`
- `content/posts/embedded-linux-boot-time-optimization.md`
- `content/posts/mipi-dsi-lvds-edp-hdmi-display-comparison.md`

### 无其他文章正文入链（9，包含前述 5 篇）
- `content/posts/android-kiosk-device-owner.md`
- `content/posts/can-fd-interface-design-linux-sbc.md`
- `content/posts/Choosing-SoCs-for-Android-SBCs.md`
- `content/posts/custom-embedded-system-cost-reduction.md`
- `content/posts/edge-ai-data-logging-field-feedback.md`
- `content/posts/embedded-linux-boot-time-optimization.md`
- `content/posts/low-power-embedded-soc-selection.md`
- `content/posts/mipi-dsi-lvds-edp-hdmi-display-comparison.md`
- `content/posts/qualcomm-mediatek-connected-edge-devices.md`

### 正文未链接任何 Hub（39）
- `content/posts/android-sbc-overview.md`
- `content/posts/camera-pipeline-edge-ai-vision.md`
- `content/posts/Choosing-SoCs-for-Android-SBCs.md`
- `content/posts/compute-module-carrier-board-design.md`
- `content/posts/custom-android-bsp-development.md`
- `content/posts/custom-embedded-soc-selection-nxp-st-qualcomm-mtk.md`
- `content/posts/custom-embedded-system-cost-reduction.md`
- `content/posts/custom-embedded-systems.md`
- `content/posts/device-tree-review-checklist.md`
- `content/posts/display-touch-interface-integration.md`
- `content/posts/edge-ai-data-logging-field-feedback.md`
- `content/posts/edge-ai-gateway-design-industrial-systems.md`
- `content/posts/edge-ai-hardware-selection.md`
- `content/posts/embedded-bsp-bring-up-checklist.md`
- `content/posts/embedded-sbc-intro.md`
- `content/posts/embedded-sbc-product-validation-checklist.md`
- `content/posts/embedded-soc-selection-matrix.md`
- `content/posts/factory-test-fixture-design-embedded-products.md`
- `content/posts/fanless-industrial-embedded-computer-design.md`
- `content/posts/field-diagnostics-embedded-industrial-devices.md`
- `content/posts/future-of-embedded-software.md`
- `content/posts/industrial-hmi-hardware-design.md`
- `content/posts/industrial-iot-gateway-design.md`
- `content/posts/industrial-linux.md`
- `content/posts/linux-cross-compilation.md`
- `content/posts/low-power-embedded-soc-selection.md`
- `content/posts/mipi-csi-vs-usb-camera-embedded-vision.md`
- `content/posts/nxp-imx-embedded-sbc-selection.md`
- `content/posts/nxp-vs-st-vs-ti-embedded-soc.md`
- `content/posts/qualcomm-mediatek-connected-edge-devices.md`
- `content/posts/rockchip-socs.md`
- `content/posts/rs485-can-ethernet-interface-planning.md`
- `content/posts/sbc-overview.md`
- `content/posts/sbc-selection-guide.md`
- `content/posts/sbc-vs-som-vs-custom-board.md`
- `content/posts/secure-firmware-update-rollback.md`
- `content/posts/the-right-linux-distro.md`
- `content/posts/ti-embedded-processors-industrial-products.md`
- `content/posts/understanding-serial-ports-in-single-board-computers.md`

### 未被任何 Hub 正文收录（25）
- `content/posts/ab-partition-layout-embedded-linux-ota.md`
- `content/posts/android-ab-ota-updates-embedded-sbc.md`
- `content/posts/android-kiosk-device-owner.md`
- `content/posts/android-sbc-overview.md`
- `content/posts/arm-vs-x86-industrial-embedded-systems.md`
- `content/posts/can-fd-interface-design-linux-sbc.md`
- `content/posts/Choosing-SoCs-for-Android-SBCs.md`
- `content/posts/custom-android-bsp-development.md`
- `content/posts/custom-sbc-schematic-review-checklist.md`
- `content/posts/design-for-manufacturing-embedded-boards.md`
- `content/posts/edge-ai-hardware-benchmark-real-workload.md`
- `content/posts/embedded-linux-boot-time-optimization.md`
- `content/posts/ethernet-phy-magnetics-connector-design.md`
- `content/posts/evt-dvt-pvt-embedded-hardware.md`
- `content/posts/int8-quantization-edge-ai.md`
- `content/posts/mipi-dsi-lvds-edp-hdmi-display-comparison.md`
- `content/posts/modbus-rtu-tcp-industrial-gateway.md`
- `content/posts/multi-camera-bandwidth-memory-edge-vision.md`
- `content/posts/nxp-imx93-vs-imx95-embedded-design.md`
- `content/posts/rockchip-socs.md`
- `content/posts/sbc-lifecycle-obsolescence-planning.md`
- `content/posts/sbom-cve-response-embedded-linux.md`
- `content/posts/secure-device-identity-factory-provisioning.md`
- `content/posts/wide-temperature-validation-industrial-sbc.md`
- `content/posts/yocto-layer-architecture-product-bsp.md`

## 建议验收标准

1. 保持默认生产构建成功、缺失链接目标为 0。
2. 每篇文章至少得到一个相关 Hub 或其他文章的正文链接，优先让上述 5 篇归零；这是维护目标，不是 Google 数量门槛。
3. 每篇新文章明确链接主 Hub，并按仓库规范链接相关既有文章和已发布路线图文章；发布时同步更新 Hub 和相关旧文章。
4. 每次补链复查英文锚文本、最多 4 词及目标主题一致性。
5. 用上线后的 HTTP 抓取和 Search Console 验证线上响应、抓取及索引变化。不要仅凭本地链接图判断搜索表现。

## 参考标准

- [Google Link best practices](https://developers.google.com/search/docs/crawling-indexing/links-crawlable)：可抓取链接、锚文本、上下文和站内交叉引用。
- 仓库 `AGENTS.md`：本站 Hub、相关文章和英文锚文本规范。
