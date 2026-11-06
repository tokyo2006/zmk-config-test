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

### PSP 摇杆（右半 / central）

| 信号 | 引脚 |
|------|------|
| X    | P0.05 (AIN3) |
| Y    | P0.28 (AIN4，从矩阵列 5 腾出) |
| VCC  | 3.3V |
| GND  | GND |

这块板子没有引出 P0.04 (AIN2) 和 P0.31 (AIN7)，所以只有一个空闲 ADC 脚
P0.05。为了得到第二个模拟输入，把右半矩阵的**第 6 列（col 5）**从
`P0.28` 挪到了 `P0.17`（原 OLED MOSI 焊盘，sofle_cirque 不用屏幕，该脚空闲），
从而把 `P0.28 (AIN4)` 让给摇杆 Y。

硬件需要做两处改动：
1. 断开矩阵第 6 列到 `P0.28` 的走线；
2. 把该列改接到 `P0.17`（OLED 排针的 MOSI 脚），并把摇杆 Y 接到 `P0.28`。

右半启用 `tokyo2006/zmk-driver-psp-joystick-with-custom-studio-rpc`：
- X/Y 接到 `P0.05` / `P0.28`（ADC channel 3/4）
- 默认作为鼠标指针（`zmk,input-listener`）
- 运行时通过 `tokyo2006__psp_joystick` Studio RPC 调参/校准
  （当前模块没有专用网页 UI，可使用通用 RPC 面板）
- 左半不启用，模块保持 inert

摇杆电位器请与 MCU 使用同一路 3.3V 供电；nRF52840 使用
`ADC_GAIN_1_6` + 内部 0.6V 参考，满量程约 3.6V。

## DYA Studio

- ZMK 使用 `cormoran/zmk#main+dya` 分支 + 全套 `zmk-feature-*` 模块。
- Cirque 触控板驱动用 `tokyo2006/cirque-input-module`（`feat/dya-studio-rpc` 分支），
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
