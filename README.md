# 🧬 PNCATEHO — ZMK прошивка для скальперского терминала

![Раскладка PNCATEHO](https://github.com/user-attachments/assets/d164848a-40fe-4b81-b8a0-a2f7820bdef2)

Персональная прошивка на базе [ZMK](https://zmk.dev/) для беспроводной клавиатуры на `nice!nano v2` (nRF52840).
Создана под конкретные задачи «Скальперского терминала» — быстрый ввод торговых команд, переключение окон и работа с функциями.

---

## 🔧 Аппаратная основа

- **Контроллер:** nice!nano v2 (nRF52840)
- **Shield:** `pncateho`
- **Матрица:** 3 ряда × 4 столбца (12 активных клавиш)
- **Раскладка:** физически соответствует половине сплит‑клавиатуры (левая сторона)

---

## ⌨️ Слои и назначения

### 🔹 Default (слой 0)
Основной слой для навигации и модификаторов.

|       | Col 0 | Col 1 | Col 2 | Col 3 |
|-------|-------|-------|-------|-------|
| Row 0 | LSHFT | P     | LALT  | A     |
| Row 1 | Z     | V     | N     | S     |
| Row 2 |       |       | MO(2) | MO(1) |

- `MO(2)` → переход на **Numbers**
- `MO(1)` → переход на **Other**

### 🔹 Other (слой 1)
Слой для ввода текста и навигации по интерфейсу.

|       | Col 0 | Col 1 | Col 2  | Col 3 |
|-------|-------|-------|--------|-------|
| Row 0 | TAB   | C     | LALT   | W     |
| Row 1 | L     | U     | SPACE  | E     |
| Row 2 |       |       | MO(3)  |       |

- `MO(3)` → переход на **Functions**
- Слой содержит часто используемые буквы и пробел для быстрой печати.

### 🔹 Numbers (слой 2)
Функциональные клавиши и цифры.

|       | Col 0 | Col 1 | Col 2 | Col 3 |
|-------|-------|-------|-------|-------|
| Row 0 | 1     | 2     | 3     | 4     |
| Row 1 | F1    | F2    | F3    | 5     |
| Row 2 |       |       |       | X     |

- Используется для ввода числовых параметров и вызова функциональных клавиш.

### 🔹 Functions (слой 3)
Редкие системные команды.

|       | Col 0       | Col 1 | Col 2 | Col 3 |
|-------|-------------|-------|-------|-------|
| Row 0 |             |       |       |       |
| Row 1 | `&bootloader` |       |       |       |
| Row 2 |             |       |       |       |

- Удержание клавиши в Row 1, Col 0 переводит контроллер в режим загрузчика (сброс прошивки).

---

## 💥 Комбо (одновременные нажатия)

На тех же слоях работают комбинации клавиш, возвращающие дополнительные символы/модификаторы.  
Для их работы в конфигурации shield'а должна быть включена опция `CONFIG_ZMK_COMBO=y`.

| Комбинация           | Результат | Активный слой | Зачем?                          |
|----------------------|-----------|---------------|----------------------------------|
| `LSHIFT` + `P` + `LALT` | `M`       | Default (0)   | Активация торговой команды `M` |
| `TAB` + `C` + `LALT`   | `LCTRL`   | Other (1)     | Эмуляция Ctrl для горячих клавиш |
| `F1` + `F2`           | `F4`      | Numbers (2)   | Быстрый вызов F4 без отдельной клавиши |

> **Важно:** комбо привязаны к конкретным слоям, чтобы не конфликтовать с обычным набором.

---

## ⚙️ Файлы прошивки

### `pncateho.keymap`
```dts
#include <behaviors.dtsi>
#include <dt-bindings/zmk/keys.h>
#include <dt-bindings/zmk/bt.h>

/ {
    keymap {
        compatible = "zmk,keymap";
        default_layer {
            label = "Default";
            bindings = <
                &kp LSHFT  &kp P   &kp LALT    &kp A        &none &none &none &none
                &kp Z      &kp V   &kp N       &kp S        &none &none &none &none
                                   &mo 2       &mo 1        &none &none
            >;
        };

        other_layer {
            label = "Other";
            bindings = <
                &kp TAB    &kp C    &kp LALT    &kp W    &none &none &none &none
                &kp L      &kp U    &kp SPACE   &kp E    &none &none &none &none
                                    &mo 3       &none    &none &none
            >;
        };

        num_layer {
            label = "Numbers";
            bindings = <
                &kp N1   &kp N2   &kp N3   &kp N4   &none &none &none &none
                &kp F1   &kp F2   &kp F3   &kp N5   &none &none &none &none
                                  &none    &kp X    &none &none 
            >;
        };

        func_layer {
            label = "Functions";
            bindings = <
                &none        &none   &none   &none        &none &none &none &none
                &bootloader  &none   &none   &none        &none &none &none &none
                                     &none   &none        &none &none
            >;
        };
    };

    combos {
        compatible = "zmk,combos";

        combo-ls-p-la {
            bindings = <&kp M>;
            key-positions = <0 1 2>;
            layers = <0>;
        };

        combo-ctrl {
            bindings = <&kp LCTRL>;
            key-positions = <0 1 2>;
            layers = <1>;
        };

        combo-f1-f2 {
            bindings = <&kp F4>;
            key-positions = <8 9>;
            layers = <2>;
        };
    };
};
