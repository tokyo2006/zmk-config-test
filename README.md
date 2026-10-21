# zmk-config-test

Sofle 分体键盘 + Cirque Pinnacle 触控板的 ZMK 配置（支持 DYA Studio）。

## 硬件

- 板子：`eyelash_nano`（nRF52840，来自 `zmk-board-eyelash`）
- 键盘矩阵：参考 `a741725193/sofle-trackball`（Sofle 58 键分体，5 行 × 7 列/半边，col2row）
- Cirque Pinnacle 1CA027 触控板（I2C），装在**右半（central）**

### Cirque 触控板引脚

| 信号 | 引脚 |
|------|------|
| SDA  | P1.08 |
| SCL  | P0.24 |
| DR   | P1.07 |

复位由驱动通过 I2C 软复位完成，无需专用复位引脚。

## DYA Studio

- ZMK 使用 `cormoran/zmk#main+dya` 分支 + 全套 `zmk-feature-*` 模块。
- Cirque 触控板驱动用 `tokyo2006/cirque-input-module`（`main` 分支），
  提供 `tokyo2006__cirque` Studio RPC 子系统，可在线改触控板状态。
- 连接 <https://studio.dya.cormoran.works/>，按 `&studio_unlock` 后出现 **Cirque** 标签页。

## 构建

推送后 GitHub Actions 自动构建（`.github/workflows/build.yml`），产物：
- `sofle_cirque_left`（左半）
- `sofle_cirque_right`（右半，central + Studio RPC）
- `settings_reset`

本地构建：

```sh
west build -b eyelash_nano --shield sofle_cirque_right -p
```
