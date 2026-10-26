# ldo_1v2_5ma - пример заполнения, редакция 2.0

**Демонстрация документа.** Числа синтетические, реальных schematic/layout, запусков ADE, netlist/GDS и silicon results нет. Пример не является SGC77 и не воспроизводит его характеристики. Для заполнения собственного IP служат datasheet_template.md, datasheet_LDO_template.md и LDO_measurement_guide.md.

## 1. Идентификация и конфигурация

| Поле | Значение |
|---|---|
| IP / top | LDO_1V2_5MA_DEMO / ldo_1v2_5ma |
| IP version / document revision | 0.1.0-demo / 2.0 от 2026-10-26 |
| Status | example_only; согласование отсутствует |
| Process / PDK / stack | DEMO CMOS 180 nm / DEMO_PDK / DEMO_1P6M |
| Source / layout revision | not_available |
| Архитектурное предположение | PMOS LDO, внешний VREF; feedback factor 0.5 |
| Fixed / programming | Fixed 1.2 V при VREF=0.600 V; code/trim N/A |
| Дополнительные функции | PG/test bus N/A в функциональной иллюстрации; внешнего bias pin нет |
| Защита / shutdown | ILIM и thermal protection не определены; активный discharge не предполагается |

## 2. Назначение и включение

Маломощный LDO для аналоговой нагрузки до условных 5 mA. В функциональную модель входят error amplifier, PMOS pass device и divider. Внешний Bandgap и pad protection не входят.

![Типовое включение и точки измерения](figures/ldo_testbench.png)

На рисунке внешние R/L и bias-порт показаны как адаптируемые элементы методики; у конкретного пяти-pin примера внешнего bias pin нет.

## 3. Выводы и домены

| Вывод | Тип / домен | Функция | Правило подключения |
|---|---|---|---|
| VIN | power in / AVDD | Вход питания | Локальная развязка; 1.5-3.6 V в условной рабочей области |
| VOUT | analog out / VREG | 1.2 V номинально | COUT/load к VSS; внешний drive не предусмотрен |
| VREF | analog in / reference | 0.600 V nominal | Stable до EN; не floating; source impedance проверяется отдельно |
| EN | digital in / VIN | Active HIGH enable | LOW<=0.3*VIN, HIGH>=0.7*VIN - условные проектные границы, не измеренные trip points |
| VSS | power in / AGND | Общий возврат | Reference и нагрузка возвращаются к определённому узлу |

Порядок `.SUBCKT`: VIN VOUT VREF EN VSS - пример будущего интерфейса, netlist не приложен.

## 4. Условия

Nominal: TT, TJ=25 °C, VIN=2.5 V, VREF=0.600 V ideal/noiseless, IOUT=1 mA, COUT=2.2 µF, ESR=0.1 Ω, ESL=0 nH, EN=VIN. Номиналы не являются переносом спецификации SGC77.

План S1: process TT/SS/FF/SF/FS только если такие models существуют; TJ=-40/25/125 °C; VIN=1.5/2.5/3.6 V; IOUT=0/0.1/1/5 mA; COUT=1/2.2/10 µF; ESR=0.01/0.1/1 Ω; ESL=0/2 nH. Это план сетки, а не выполненные runs.

Recommended operating envelope: VIN=1.5-3.6 V, IOUT=0-5 mA с достаточным headroom, TJ=-40..125 °C. Внешний VREF=0.600 V без включённого допуска.

Absolute maximum ratings: not_defined; нужны реальный PDK и reliability reviewer. Model-temperature range не является absolute maximum.

## 5. Условные электрические результаты

Числа показывают оформление observed min/typ/max, не фактическую статистику. Все строки имеют simulation status=not_run. Typ - nominal с переопределениями строки; остальные колонки иллюстрируют min/max по S1. `NC` - не характеризовано.

| ID / параметр | Min | Typ | Max | Ед. | Условия / метод |
|---|---:|---:|---:|---|---|
| VOUT | 1.182 | 1.200 | 1.219 | V | Ideal VREF; M05 |
| Accuracy | -1.5 | 0 | 1.583 | % | Target=1.200 V; те же границы VOUT; M05 |
| Dropout | — | 90 | 180 | mV | IOUT=5 mA; 2% от собственного baseline; M08 |
| Line regulation | — | 0.4 | 1.5 | mV/V | VIN=1.5..3.6 V; IOUT=1 mA; endpoint method M06 |
| Load regulation | — | 0.35 | 1.5 | mV/mA | IOUT=0..5 mA, VIN=2.5 V; M07 |
| PSRR(1 kHz) | 55 | 68 | — | dB | Quiet VREF/EN; M09 |
| Integrated output noise | — | 48 | 80 | µVrms | 10 Hz..100 kHz; ideal noiseless VREF; M10 |
| Startup settling | — | 450 | 900 | µs | Supply/reference stable, EN edge=1 µs; ±2%, hold=100 µs; M11 |
| Load-step undershoot | — | 40 | 90 | mV | 0.1->5 mA, edge=1 µs; M13 |
| Load-step overshoot | — | 35 | 80 | mV | 5->0.1 mA, edge=1 µs; M13 |
| Phase margin | 52 | 68 | — | degrees | S1; условный STB, probe ещё не определён; M15 |
| IQ (VIN, no load) | — | 18 | 30 | µA | EN high; IOUT=0; excludes VREF source current; M16 |
| ISHDN (VIN) | — | 0.08 | 0.5 | µA | EN=0, VOUT not forced; M17 |
| ILIM / foldback / IREV | NC | NC | NC | mA/µA | Методы/архитектура ещё не определены |

**Пример расчёта accuracy:** 100*(1.182-1.200)/1.200=-1.5%; верхнее отклонение 100*(1.219-1.200)/1.200=1.5833%. Требование target задаётся отдельно, например ±2%; приведённые экстремумы не являются гарантией.

**Пример различия единиц:** LR=0.4 mV/V на интервале 2.1 V означает endpoint change 0.84 mV. Load regulation 0.35 mV/mA на интервале 5 mA означает 1.75 mV при том же определении. Эти расчёты иллюстративные.

## 6. Критерии и данные

- Dropout: получить VOUT_base при VIN=2.5 V и том же IOUT/PVT; при снижении VIN найти VOUT=0.98*VOUT_base; VDO=VIN_at_cross-VOUT_at_cross. Низковольтные protection modes рассматриваются отдельно.
- Startup: от EN crossing 0.5*VIN до входа в ±2% final VOUT с удержанием 100 µs. 10-90% rise time - отдельный показатель и в этом примере NC.
- Noise: sqrt(integral(PSD,10 Hz..100 kHz)). Значение noise density на одной frequency - отдельная метрика и здесь NC.
- STB: требуется согласованный measurement view/probe и сохранение DC/loading; отсутствие реального probe означает, что показанное число не имеет измерительного evidence.

Testbench/revisions/netlists/CSV/raw/plots: not_available. PVT: not_run; MC: not_run; PEX: not_run; silicon: not_available. ID методов ведут к подробным карточкам LDO_measurement_guide.md.

## 7. Интеграция и режимы

Включение: VSS -> VIN -> stable VREF -> EN HIGH. Выключение: EN LOW -> disable VREF -> remove VIN. Это выбранное поведение функциональной иллюстрации, не подтверждённый sequence test.

COUT должен иметь указанную effective capacitance и ESR/ESL. Внутреннюю feedback-сеть не выводить за boundary. Ground/reference topology и R/C/L routing budget для реального layout требуют подтверждения. Номинальный current class не даёт разрешения на произвольный metal/via current.

EN LOW отключает pass device; скорость разряда определяется нагрузкой/утечкой. Внешнее питание VOUT и back-power в спецификацию не входят. Current limit/thermal protection не гарантируются.

## 8. Проверки и поставка

| Проверка / представление | Статус | Причина / область |
|---|---|---|
| Calibre DRC/LVS | not_run | Реальный layout/netlist отсутствует |
| Calibre PEX | not_run | Нет extraction data |
| Electrical deck checks | not_run | Реальное покрытие не задано |
| PERC | not_run | deck_not_available для вашего процесса |
| Reliability / ESD / latch-up review | pending | Нужны PDK, реализация и определённый scope |
| GDS / LEF / CDL / PEX / models | not_available | Демонстрация документа |
| Liberty | N/A для этого документа | При нуждах digital flow подготовить согласованную модель отдельно |

Открытые вопросы: все реальные implementation, qualification и signoff данные. Владелец блока назначает ответственных и сроки. Данный пример не является решением о выпуске.
