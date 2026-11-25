# ESB-P025–P032 文章交付记录

完成日期为 2026-11-24。文章已进入本地 Hugo 发布构建，线上部署未执行。发布日期以北京时间 09:00 为准。

| 编号 | 发布日期 | 文章 | 封面 | 正文图 | 内链 |
|---|---|---|---|---|---:|
| ESB-P025 | 2026-10-26 | [AOSP, CTS, VTS, and GMS: What Embedded Android Teams Need](../content/posts/android-aosp-cts-vts-gms.md) | [JPG](../static/images/posts/android-aosp-cts-vts-gms-hero.jpg) | [技术图](../static/images/posts/android-aosp-cts-vts-gms-workflow.svg) | 6 |
| ESB-P026 | 2026-10-30 | [How Much RAM Does an Embedded SBC Really Need?](../content/posts/embedded-sbc-ram-sizing.md) | [JPG](../static/images/posts/embedded-sbc-ram-sizing-hero.jpg) | [技术图](../static/images/posts/embedded-sbc-ram-sizing-workflow.svg) | 5 |
| ESB-P027 | 2026-11-03 | [STM32MP1 vs STM32MP2 for Industrial Linux Products](../content/posts/stm32mp1-vs-stm32mp2-industrial-linux.md) | [JPG](../static/images/posts/stm32mp1-vs-stm32mp2-industrial-linux-hero.jpg) | [技术图](../static/images/posts/stm32mp1-vs-stm32mp2-industrial-linux-workflow.svg) | 6 |
| ESB-P028 | 2026-11-07 | [Android Boot Flow and Partition Layout on Custom SBCs](../content/posts/android-boot-flow-partition-layout.md) | [JPG](../static/images/posts/android-boot-flow-partition-layout-hero.jpg) | [技术图](../static/images/posts/android-boot-flow-partition-layout-workflow.svg) | 5 |
| ESB-P029 | 2026-11-11 | [Device Tree Overlays for Product Variants](../content/posts/device-tree-overlays-product-variants.md) | [JPG](../static/images/posts/device-tree-overlays-product-variants-hero.jpg) | [技术图](../static/images/posts/device-tree-overlays-product-variants-workflow.svg) | 5 |
| ESB-P030 | 2026-11-15 | [PMIC Selection and Power Sequencing for Embedded SoCs](../content/posts/pmic-selection-power-sequencing-embedded-soc.md) | [JPG](../static/images/posts/pmic-selection-power-sequencing-embedded-soc-hero.jpg) | [技术图](../static/images/posts/pmic-selection-power-sequencing-embedded-soc-workflow.svg) | 5 |
| ESB-P031 | 2026-11-19 | [OPC UA Edge Gateway Architecture](../content/posts/opc-ua-edge-gateway-architecture.md) | [JPG](../static/images/posts/opc-ua-edge-gateway-architecture-hero.jpg) | [技术图](../static/images/posts/opc-ua-edge-gateway-architecture-workflow.svg) | 6 |
| ESB-P032 | 2026-11-24 | [MQTT Store-and-Forward for Unreliable Industrial Networks](../content/posts/mqtt-store-forward-industrial-networks.md) | [JPG](../static/images/posts/mqtt-store-forward-industrial-networks-hero.jpg) | [技术图](../static/images/posts/mqtt-store-forward-industrial-networks-workflow.svg) | 4 |

正文使用英文，包含官方一手来源、计算示例和工程验收方法。稿件由 Codex 撰写，作者字段沿用站点团队名，当前未进行真人审校。各篇的 SEO 标题、描述、规范 URL、分享图和 BlogPosting 数据已检查，42 条正文内链均使用 2–5 个英文单词的主题锚文本。

8 张 Hero 由内置 imagegen 为本批文章独立生成，图注标明 AI 生成。成品尺寸为 1600 × 900，编码为 JPEG。正文技术图以文中的工程流程和算例为准，可点击放大。

验证通过 `hugo --minify` 和 `git diff --check`。8 篇文章均出现在站点地图中。路线图已完成 32/100，站点共有 82 篇进入发布构建的文章。

## Hero 生成提示词

### ESB-P025

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: AOSP, CTS, VTS, and GMS: What Embedded Android Teams Need. Scene: Three small embedded touchscreen validation units on a grey ESD lab bench alongside a compact Android development SBC. One screen shows a restrained grid of generic app icons. A laptop in the background shows a softly defocused test dashboard. Short realistic USB leads, no legible text. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P026

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: How Much RAM Does an Embedded SBC Really Need?. Scene: Macro close-up of one unbranded embedded SBC lying flat on a dark ESD mat. Two matte-black soldered rectangular LPDDR packages sit close to the larger square processor, surrounded by realistic fine passives and PCB traces. A second board is softly out of focus. Natural side lighting makes chip shapes readable. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P027

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: STM32MP1 vs STM32MP2 for Industrial Linux Products. Scene: Two distinct unbranded industrial MPU evaluation boards placed side by side at the same scale on a light electronics lab bench. One older compact green board with Ethernet and headers, one newer dark blue board with more I/O and a low metal heat spreader. Neat, plausible soldering, no specific commercial board replicas. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P028

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: Android Boot Flow and Partition Layout on Custom SBCs. Scene: A small bare embedded development board with a low heatsink connected by short serial leads to a USB UART dongle on an orderly lab desk. Laptop in background with out-of-focus boot log and a small blank-backlit LCD next to the board. Technical documentary close-up with board and UART adapter in focus. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P029

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: Device Tree Overlays for Product Variants. Scene: Modular embedded engineering workbench showing a single baseboard with a small display connected by a flexible ribbon and a separate alternate camera daughterboard resting beside it, one disconnected at its clearly visible connector. Overhead three-quarter angle, realistic 40-pin and ribbon connectors, tidy ESD work surface. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P030

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: PMIC Selection and Power Sequencing for Embedded SoCs. Scene: Close technical photograph of a custom embedded PCB power section with compact square gray inductors beside a tiny PMIC, short thin probe leads to nearby test pads, and a softly defocused oscilloscope behind it showing simple stepped timing traces. Stable PCB on standoffs, neutral lab lighting. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P031

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: OPC UA Edge Gateway Architecture. Scene: An open industrial control cabinet with a small rail-mounted fanless Ethernet gateway beside a modular PLC and managed industrial switch. Blue network leads and neat terminal blocks, plausible ports and cable routing. Slight side angle, real brushed metal, workshop fluorescent light. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```

### ESB-P032

```text
Use case: photorealistic-natural. Asset type: original engineering editorial blog hero, wide landscape 16:9 prepared for JPG. Article subject: MQTT Store-and-Forward for Unreliable Industrial Networks. Scene: A compact rugged fanless data-logging gateway with two external antenna leads, an industrial cellular router and a small DIN-rail power supply on a test rack. An Ethernet link connects the two boxes. One small status LED on the gateway in focus, soft warm workshop light, realistic rack hardware. Style: candid documentary engineering photography with natural exposure, realistic materials and restrained colors, physical hardware at believable scale, moderate depth of field. Keep primary equipment inside the central 80 percent for cropping. No people, hands, readable text, labels, trademarks, watermarks, collage, overlays or graphic annotation.
```
