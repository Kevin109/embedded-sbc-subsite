# ESB-P017–P024：8 篇文章交付记录

> 完成日期：2026-10-27
>
> 状态：本地内容完成，`draft: false`；构建通过。未执行 Git 提交、推送或线上部署。
>
> 发布日期按用户指定区间 2026-09-18–2026-10-25 分配；实际本次修订时间统一记录为 `lastmod: 2026-10-27`。

## 文章与日期

| 编号 | 状态 | 发布日期 | 文章 | 正文词数约 |
|---|---|---|---|---:|
| ESB-P017 | ✅ | 2026-09-18 | [INT8 Quantization for Edge AI: Accuracy, Speed, and Calibration](../content/posts/int8-quantization-edge-ai.md) | 1239 |
| ESB-P018 | ✅ | 2026-09-23 | [EVT, DVT, and PVT for Embedded Hardware Products](../content/posts/evt-dvt-pvt-embedded-hardware.md) | 1229 |
| ESB-P019 | ✅ | 2026-09-28 | [Embedded Linux Boot Time Optimization](../content/posts/embedded-linux-boot-time-optimization.md) | 1252 |
| ESB-P020 | ✅ | 2026-10-03 | [CAN FD Interface Design for Embedded Linux SBCs](../content/posts/can-fd-interface-design-linux-sbc.md) | 1178 |
| ESB-P021 | ✅ | 2026-10-09 | [Secure Device Identity and Factory Key Provisioning](../content/posts/secure-device-identity-factory-provisioning.md) | 1229 |
| ESB-P022 | ✅ | 2026-10-14 | [Yocto Layer Architecture for a Maintainable Product BSP](../content/posts/yocto-layer-architecture-product-bsp.md) | 1192 |
| ESB-P023 | ✅ | 2026-10-20 | [Multi-Camera Bandwidth and Memory Planning for Edge Vision](../content/posts/multi-camera-bandwidth-memory-edge-vision.md) | 1230 |
| ESB-P024 | ✅ | 2026-10-25 | [Wide-Temperature Validation for Industrial SBC Products](../content/posts/wide-temperature-validation-industrial-sbc.md) | 1203 |

正文词数为去除 Front Matter 后的空白分词近似值，包含表格、代码和图注。

## 内容与 SEO 检查

- 每篇包含独立 SEO 标题、描述、关键词、作者、发布日期和最后修订日期。
- 每篇包含所属 Hub、至少 2 篇本批前已有文章，以及至少 1 篇路线图文章的内链；使用主题关键词锚文本，不使用被链接文章的完整标题。
- 每篇提供实际决策资产：量化验收表、阶段退出矩阵、启动定位表、CAN FD 联调矩阵、灌装状态机、Yocto 分层检查表、多摄像头计算表或温度验证矩阵。
- 技术依据链接到官方文档、标准发布机构及原始工程实践资料；不虚构实验结果、客户案例、认证、个人履历或亲历经验。
- 本批内容为 AI 辅助撰写和编辑，未声称真人独立撰写；EEAT 通过可核验来源、明确假设和工程验证方法支持，不以虚假作者资历包装。
- 示例数字明确标为计算或规划示例，不作为设备实测成绩。

## 图片来源与格式

8 张 Hero 均为本次独立生成，没有复用旧站图片；使用 imagegen 的写实摄影风格生成，但不是实地拍摄。文章封面图注明确说明 AI-generated engineering illustration。每张转为真正的 JPEG，1600 × 900，未只改扩展名。8 张正文 SVG 为本次原创技术示意图，可点击打开大图。

| 编号 | Hero 文件 | 文件大小 | 正文图 |
|---|---|---:|---|
| P017 | [JPG](../static/images/posts/int8-quantization-edge-ai-hero.jpg) | 182 KiB | [SVG](../static/images/posts/int8-quantization-edge-ai-workflow.svg) |
| P018 | [JPG](../static/images/posts/evt-dvt-pvt-embedded-hardware-hero.jpg) | 304 KiB | [SVG](../static/images/posts/evt-dvt-pvt-embedded-hardware-workflow.svg) |
| P019 | [JPG](../static/images/posts/embedded-linux-boot-time-optimization-hero.jpg) | 197 KiB | [SVG](../static/images/posts/embedded-linux-boot-time-optimization-workflow.svg) |
| P020 | [JPG](../static/images/posts/can-fd-interface-design-linux-sbc-hero.jpg) | 211 KiB | [SVG](../static/images/posts/can-fd-interface-design-linux-sbc-workflow.svg) |
| P021 | [JPG](../static/images/posts/secure-device-identity-factory-provisioning-hero.jpg) | 191 KiB | [SVG](../static/images/posts/secure-device-identity-factory-provisioning-workflow.svg) |
| P022 | [JPG](../static/images/posts/yocto-layer-architecture-product-bsp-hero.jpg) | 207 KiB | [SVG](../static/images/posts/yocto-layer-architecture-product-bsp-workflow.svg) |
| P023 | [JPG](../static/images/posts/multi-camera-bandwidth-memory-edge-vision-hero.jpg) | 201 KiB | [SVG](../static/images/posts/multi-camera-bandwidth-memory-edge-vision-workflow.svg) |
| P024 | [JPG](../static/images/posts/wide-temperature-validation-industrial-sbc-hero.jpg) | 249 KiB | [SVG](../static/images/posts/wide-temperature-validation-industrial-sbc-workflow.svg) |

## 验证记录

- `hugo --minify`：通过；106 个页面输出、107 个静态文件。
- `git diff --check`：通过。
- 8 个新文章页面进入 sitemap，生产 canonical 指向 `https://embedded-sbc.com/posts/<slug>/`。
- 8 个页面生成 BlogPosting 结构化数据；datePublished 对应分配日期，dateModified 为 2026-10-27。
- 1440px 桌面与 390px 手机宽度共 16 次页面检查：无整页横向溢出。
- 滚动触发懒加载并等待解码后，32 次图片加载检查全部通过；两种宽度下抽查量化与多摄像头全文截图。
- 8 张 SVG 汇总渲染已检查，文字无重叠、箭头与矩阵正常；移动端可点击查看原图。
- 全站文章为 74 篇，路线图完成 24/100；编号、状态和链接同步维护于 [内容路线图](content-roadmap.md)。

### 单独记录的现有站点问题

390px 手机宽度下，现有全站导航的站名与菜单发生重叠。本文新增文章正文、封面与图表不造成整页横向溢出；此导航问题位于未修改的共享主题，本批只记录，未扩大范围修改全站导航。技术示意图在手机端可点击查看大图。

预览热更新期间曾出现缓存残留，重新启动预览后复核 16 个页面均无短代码残留；正式 `hugo --minify` 输出也已检查，不包含该残留。

## 各篇一手资料

### ESB-P017 · INT8 Quantization for Edge AI: Accuracy, Speed, and Calibration

- [ONNX Runtime quantization documentation](https://onnxruntime.ai/docs/performance/model-optimizations/quantization.html)
- [LiteRT full-integer conversion guide](https://developers.google.com/edge/litert/conversion/tensorflow/quantization/post_training_integer_quant)

### ESB-P018 · EVT, DVT, and PVT for Embedded Hardware Products

- [Instrumental's stage-gate definitions](https://instrumental.com/build-better-handbook/evt-dvt-pvt)
- [binomial distribution documented by NIST](https://itl.nist.gov/div898/handbook/eda/section3/eda366i.htm)

### ESB-P019 · Embedded Linux Boot Time Optimization

- [systemd-analyze manual](https://www.freedesktop.org/software/systemd/man/latest/systemd-analyze.html)
- [boot parameter reference](https://docs.kernel.org/admin-guide/kernel-parameters.html)

### ESB-P020 · CAN FD Interface Design for Embedded Linux SBCs

- [Linux SocketCAN documentation](https://docs.kernel.org/networking/can.html)
- [selectable CAN termination reference design](https://www.ti.com/tool/TIDA-01238)

### ESB-P021 · Secure Device Identity and Factory Key Provisioning

- [ATECC608B cryptographic-operation documentation](https://onlinedocs.microchip.com/oxy/GUID-5C346303-A9CB-4341-B67F-4354AB92FE04-en-US-3/GUID-404D6C76-495B-4F8F-B534-2590E9C3102C.html)
- [OASIS base specification](https://docs.oasis-open.org/pkcs11/pkcs11-base/v3.0/os/pkcs11-base-v3.0-os.html)
- [RFC 7030](https://www.rfc-editor.org/rfc/rfc7030)

### ESB-P022 · Yocto Layer Architecture for a Maintainable Product BSP

- [Yocto BSP guide](https://docs.yoctoproject.org/scarthgap/bsp-guide/bsp.html)
- [Yocto layer manual](https://docs.yoctoproject.org/scarthgap/dev-manual/layers.html)

### ESB-P023 · Multi-Camera Bandwidth and Memory Planning for Edge Vision

- [V4L2 pixel-format documentation](https://docs.kernel.org/userspace-api/media/v4l/pixfmt-v4l2.html)
- [V4L2 DMA-BUF interface](https://docs.kernel.org/userspace-api/media/v4l/dmabuf.html)
- [kernel DMA-BUF synchronization documentation](https://docs.kernel.org/driver-api/dma-buf.html)

### ESB-P024 · Wide-Temperature Validation for Industrial SBC Products

- [IEC 60068-2-1:2025](https://webstore.iec.ch/en/publication/82354)
- [IEC 60068-2-2:2025](https://webstore.iec.ch/en/publication/82355)
- [semiconductor thermal metrics application note](https://www.ti.com/lit/an/spra953d/spra953d.pdf)

## Hero 生成提示词与原始文件

保留原始生成文件；下列为实际使用的完整提示词。JPG 转换仅涉及尺寸和格式，不把生成场景当作测量证据。

### ESB-P017 · INT8 Quantization for Edge AI: Accuracy, Speed, and Calibration

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-34618452-0223-4b15-ad86-a4e52e30761e.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: INT8 Quantization for Edge AI: Accuracy, Speed, and Calibration. Scene: Embedded AI quantization laboratory: one compact fanless neural accelerator board connected to an industrial camera aimed at a small collection of nuts bolts and color cards; laptop in soft background shows two comparable camera views and a simple unreadable histogram. Tight three-quarter composition, board and camera sharp. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P018 · EVT, DVT, and PVT for Embedded Hardware Products

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-9f1c49c7-d30e-4d85-90d5-cbda952d3262.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: EVT, DVT, and PVT for Embedded Hardware Products. Scene: Hardware development stages on an electronics workbench: one hand-reworked prototype PCB with fine bodge wires, one assembled production-intent board in an open metal enclosure, and one finished boxed industrial controller beside a bed-of-nails test fixture. Three genuine-looking stages, not duplicates, wide tabletop composition. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P019 · Embedded Linux Boot Time Optimization

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-9fe305e1-2dbc-4f68-a6bd-f065ce34a431.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: Embedded Linux Boot Time Optimization. Scene: Embedded Linux boot timing measurement bench: an oscilloscope with a few plausible clean digital transition traces in the blurred background, a logic analyzer connected by short wires to an SBC, a programmable bench power supply and small LCD displaying a simple ready indicator. Macro foreground, no giant chips or impossible wiring. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P020 · CAN FD Interface Design for Embedded Linux SBCs

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-ec6cb0d7-9887-4818-8f0c-03f17b9740a6.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: CAN FD Interface Design for Embedded Linux SBCs. Scene: Industrial CAN FD engineering bench: compact embedded SBC with a small isolated CAN transceiver daughterboard, a clearly visible twisted-pair cable forming a simple linear connection to two small controller modules, terminal blocks and differential probe in foreground. Oscilloscope blurred in background. Realistic green PCB and metal connectors. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P021 · Secure Device Identity and Factory Key Provisioning

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-139bf5fa-6561-4a1f-928c-99747024f9e0.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: Secure Device Identity and Factory Key Provisioning. Scene: Secure electronics provisioning station: a single industrial SBC clamped in a realistic spring-loaded pogo-pin factory fixture, USB hardware security token on the workstation and neat trays of unprogrammed boards in the background. Subtle status LEDs, matte ESD surface, clean controlled factory bench, no padlock holograms. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P022 · Yocto Layer Architecture for a Maintainable Product BSP

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-3c373bc3-b831-4e5c-bafb-bbd38eb6e291.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: Yocto Layer Architecture for a Maintainable Product BSP. Scene: Embedded Linux software engineering workbench with three visibly different small SBC product variants, one with small display, one with ethernet terminals, one camera board. One laptop behind them has softly defocused code editor panes and build logs, no legible text. Real cables, disciplined desk, natural side light. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P023 · Multi-Camera Bandwidth and Memory Planning for Edge Vision

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-3d2db494-97aa-4710-852d-c3d1a16d84f4.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: Multi-Camera Bandwidth and Memory Planning for Edge Vision. Scene: Multi-camera embedded vision rig: four small industrial camera modules with real lenses positioned around a tabletop inspection object, ribbon cables routed neatly to a custom embedded carrier board with heat spreader, a monitor in background with four distinct softly blurred views. Show plausible connectors and compact scale, laboratory documentary photograph. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```

### ESB-P024 · Wide-Temperature Validation for Industrial SBC Products

原始文件：`/Users/kevin/.codex/generated_images/019f52cc-badf-7ac3-b257-47f17fde3ce4/exec-e9b34350-13a5-4ad4-8b70-b9bf1ba07747.png`

```text
Use case: photorealistic-natural. Asset: original editorial website hero, wide landscape 16:9. Article: Wide-Temperature Validation for Industrial SBC Products. Scene: Environmental qualification lab: industrial SBC on a wire rack inside an open stainless-steel temperature chamber, thin thermocouple wires attached to processor heatsink, storage and power components, cable feedthrough visible in side wall and data logger outside. Dry clean surfaces, no frost, no liquid, chamber controls out of focus, no people. Style: near-real documentary engineering photography, physical components, honest material textures, restrained colors, soft practical lighting, shot on a 50mm lens with moderate depth of field. No people, no hands, no branding, no readable text, no watermark, no rendered infographic, no collage, no floating objects. Compose essential hardware inside the central 80% for safe crop. This is an illustrative engineering setup, not evidence of any product test.
```
