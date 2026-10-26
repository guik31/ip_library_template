# <LDO_NAME> - заполняемый datasheet LDO

Редакция шаблона 2.0. Основа документа - структура IP datasheet SGC77, дополненная однозначными критериями и ссылками на измерения. Рабочие пределы, номиналы и архитектуру задаёт владелец вашего IP.

## 1. Идентификация / конфигурация

IP/top: `<...>`. IP version / document rev: `<...>`. PDK/metal stack: `<...>`. Schematic/layout revision: `<...>`. Автор/reviewer/date: `<...>`. Статус: `draft`.

Выбранные опции: `<fixed/programmed VOUT; IOUT class; supply domains; external/internal VREF и bias; PG; protection; shutdown state; test bus>`. Не существующие в реальной реализации функции отмечаются N/A.

**Как заполнить:** сверить symbol, schematic, CDL и GDS/LEF. Compile-time опции отделить от runtime code. Развёрнутые общие разделы находятся в datasheet_template.md.

## 2. Назначение и типовое включение

`<Что стабилизирует блок, для какой нагрузки и какие внешние IP/компоненты нужны>`.

Структурная схема: `<figures/...>`. Типовое включение: `<figures/...>`.

**Как проверить:** типовое включение повторяет рабочий testbench; на нём видны VIN/AVDD/DVDD, VREF/bias, feedback/return и все обязательные control/test pins.

## 3. Выводы и режимы

| Вывод | Домен / тип | Назначение | Подключение, default и off-state |
|---|---|---|---|
| `<VIN>` | `<...>` | Силовой вход | `<диапазон, ток, развязка>` |
| `<VOUT>` | `<...>` | Регулируемый выход | `<нагрузка, COUT, разрешён ли внешний drive>` |
| `<VREF / IBIAS / VFB>` | `<реальные pins или N/A>` | Reference/bias/sense | `<уровни, направление тока, возврат>` |
| `<EN / PG / CODE / TEST>` | `<реальные pins или N/A>` | Управление/статус/test | `<domain, polarity, default, нагрузки>` |
| `<grounds>` | `<AGND / DGND / SGND / REF_GND>` | Функции возврата | `<реальная схема соединения>` |

Порядок `.SUBCKT`: `<...>`.

Режимы normal/shutdown/startup/fault/recovery/test: `<условия и поведение; M11-M14, M18-M23>`.

## 4. Условия и представление

Nominal: `<process, TJ, все supplies, VREF/bias, IOUT, COUT/ESR/ESL, code/mode>`.

Min/Max scope: `<полная проверенная сетка и ссылки на runs>`.

Уровень: `<schematic / post_layout / silicon>`. Смысл чисел: `<target / observed / guaranteed>`. Статистические условия: `<MC settings или not_run>`.

Absolute maximum ratings: `<PDK-based limits / reviewer или not_defined; M23>`. Не выводить их из успешной симуляции.

## 5. Электрические характеристики

`NC` - не характеризовано; `N/A` - функция отсутствует; `—` - граница не задана. Строки ниже - перечень для отбора по реальной архитектуре. Для каждого supply, code transition, frequency или PG direction при необходимости добавляется отдельная строка. Цели, observed результаты и гарантированные пределы оформляются отдельными таблицами.

Recommended conditions VIN/AVDD/DVDD/TJ/load/VREF/bias/COUT/ESR/ESL заполняются отдельно в разделе 4 по методам M01-M03/M21. Boundary/area/routing budget - в разделе 7 по M24; stress/sequence review - в разделе 8 по M23. Это условия или предмет review, а не одна сводная электрическая величина.

| ID / параметр | Min | Typ | Max | Ед. | Метод / обязательные условия |
|---|---:|---:|---:|---|---|
| P01 / VOUT | `<...>` | `<...>` | `<...>` | V | M05; nominal/PVT; идеальный или реальный reference; `<run / source>` |
| P02 / Output accuracy | `<...>` | `<...>` | `<...>` | % | M05; signed error от target; `<run / source>` |
| P03 / Line regulation, absolute | `<...>` | `<...>` | `<...>` | mV | M06; VIN1/VIN2 и фиксированная нагрузка; `<run / source>` |
| P04 / Line regulation, normalized | `<...>` | `<...>` | `<...>` | mV/V | M06; явно выбранное нормирование; `<run / source>` |
| P05 / Load regulation, absolute | `<...>` | `<...>` | `<...>` | mV | M07; I1/I2, точка измерения/sense; `<run / source>` |
| P06 / Load regulation, normalized | `<...>` | `<...>` | `<...>` | mV/mA | M07; отдельно от absolute; `<run / source>` |
| P07 / Dropout | `<...>` | `<...>` | `<...>` | mV | M08; load/PVT и критерий падения; `<run / source>` |
| P08 / PSRR at <frequency> | `<...>` | `<...>` | `<...>` | dB | M09; supply domain, headroom, C/ESR/ESL; `<run / source>` |
| P09 / Output noise density at <frequency> | `<...>` | `<...>` | `<...>` | nV/sqrt(Hz) | M10; ASD, включённые шумовые источники; `<run / source>` |
| P10 / Integrated output noise | `<...>` | `<...>` | `<...>` | µVrms | M10; f1/f2 и noise models; `<run / source>` |
| P11 / EN startup settling | `<...>` | `<...>` | `<...>` | µs | M11; trigger, window, hold time; `<run / source>` |
| P12 / Output rise time 10-90% | `<...>` | `<...>` | `<...>` | µs | M11; baseline/final values; `<run / source>` |
| P13 / Inrush current peak | `<...>` | `<...>` | `<...>` | mA | M11; power/EN sequence и COUT; `<run / source>` |
| P14 / Code/setpoint settling | `<...>` | `<...>` | `<...>` | µs | M12; оба направления; N/A для fixed; `<run / source>` |
| P15 / Load-step undershoot | `<...>` | `<...>` | `<...>` | mV | M13; I1/I2, edge, включённые spikes; `<run / source>` |
| P16 / Load-step overshoot | `<...>` | `<...>` | `<...>` | mV | M13; I2/I1, edge, включённые spikes; `<run / source>` |
| P17 / Load-step recovery | `<...>` | `<...>` | `<...>` | µs | M13; final level, window, hold time; `<run / source>` |
| P18 / Line-transient deviation | `<...>` | `<...>` | `<...>` | mV | M14; VIN step/edge, load; `<run / source>` |
| P19 / Phase margin | `<...>` | `<...>` | `<...>` | degrees | M15; probe и crossing; `<run / source>` |
| P20 / Gain margin | `<...>` | `<...>` | `<...>` | dB | M15; применимые crossing; `<run / source>` |
| P21 / Loop unity-gain frequency | `<...>` | `<...>` | `<...>` | kHz | M15; не closed-loop bandwidth без определения; `<run / source>` |
| P22 / IQ of <supply domain> | `<...>` | `<...>` | `<...>` | µA | M16; отдельная строка на каждый supply; `<run / source>` |
| P23 / Shutdown current of <domain> | `<...>` | `<...>` | `<...>` | µA | M17; input/off/output states; `<run / source>` |
| P24 / Maximum regulated output current | `<...>` | `<...>` | `<...>` | mA | M18; выполнение всех normal specs; `<run / source>` |
| P25 / Current-limit threshold | `<...>` | `<...>` | `<...>` | mA | M18; критерий knee/plateau; если реализован; `<run / source>` |
| P26 / Foldback/short-circuit current | `<...>` | `<...>` | `<...>` | mA | M18; VOUT, average/peak и fault mode; `<run / source>` |
| P27 / Power-good rising threshold | `<...>` | `<...>` | `<...>` | V | M19; относительно какого target; N/A без PG; `<run / source>` |
| P28 / Power-good falling threshold | `<...>` | `<...>` | `<...>` | V | M19; направление и ramp; `<run / source>` |
| P29 / Power-good hysteresis | `<...>` | `<...>` | `<...>` | mV | M19; rise minus fall; `<run / source>` |
| P30 / Power-good delay | `<...>` | `<...>` | `<...>` | µs | M19; ramp rate и digital output load; `<run / source>` |
| P31 / VIH / VIL | `<...>` | `<...>` | `<...>` | V | M20; разделить на две строки, domain/criteria; `<run / source>` |
| P32 / VOH / VOL | `<...>` | `<...>` | `<...>` | V | M20; разделить на две строки, source/sink current; `<run / source>` |
| P33 / Discharge resistance | `<...>` | `<...>` | `<...>` | Ω | M22; напряжение и linear/nonlinear mode; `<run / source>` |
| P34 / Discharge time | `<...>` | `<...>` | `<...>` | µs | M22; threshold pair, load/COUT; `<run / source>` |
| P35 / VREF input current | `<...>` | `<...>` | `<...>` | µA | M02; направление, supply и reference voltage; `<run / source>` |
| P36 / Bias-port compliance | `<...>` | `<...>` | `<...>` | V | M03; если внешний bias реализован; `<run / source>` |
| P37 / Code-to-output step | `<...>` | `<...>` | `<...>` | mV | M04; code pair; N/A для fixed; `<run / source>` |

## 6. Пояснения к каждой строке и способ измерения

Полная процедура, формулы и типичные ошибки даны по тем же ID в LDO_measurement_guide.md. Ниже краткая инструкция непосредственно в шаблоне; это не численные требования к вашему LDO.

### M01. Рабочие питания и температура

**Смысл:** VIN питает силовой путь; AVDD и DVDD могут питать аналоговую часть и интерфейс отдельно. Рабочий диапазон означает выполнение всех нужных характеристик, а не просто наличие VOUT.

**Измерение:** В ADE задать источники analogLib параметрами vin_dc, avdd_dc, dvdd_dc, temp. Сначала dc operating point, затем DC/parametric sweep и выбранные tran tests на краях диапазона.

**Записать:** Таблица разрешённых сочетаний supply, process sections, TJ, нагрузка; ссылки на tests. Absolute maximum ratings - отдельный документированный предел.

Заполняемые условия / критерий / Run ID: `<...>`.

### M02. Внешний reference: уровень, чувствительность, импеданс

**Смысл:** Reference задаёт масштаб выходного напряжения. Для LDO с feedback factor beta в первом приближении VOUT = VREF/beta; ошибка и шум reference могут передаваться на выход.

**Измерение:** DC sweep VREF в разрешённом диапазоне; измерить VOUT и ток порта VREF. Затем добавить последовательный R и при необходимости реальную модель reference. AC=1 на VREF при AC=0 на остальных источниках даёт передаточную функцию reference-to-output.

**Записать:** VREF nominal/range, ток порта, допустимый source impedance, режим запуска, ограничения шума и путь возврата REF_GND.

Заполняемые условия / критерий / Run ID: `<...>`.

### M03. Bias current и compliance

**Смысл:** Внешний bias current задаёт токи внутренних каскадов. Compliance - область напряжений bias-порта, в которой требуемый ток действительно обеспечивается источником и корректно принимается блоком.

**Измерение:** Использовать рекомендованную bias cell или её согласованную модель; sweep bias magnitude и supply/PVT. Для compliance менять напряжение порта в отдельном testbench с корректной моделью источника, наблюдая реальный ток и device operating points.

**Записать:** Ток/направление, модель источника, compliance range, опорная земля, PVT и токовое потребление источника отдельно от IP.

Заполняемые условия / критерий / Run ID: `<...>`.

### M04. Диапазон VOUT, коды и trim

**Смысл:** Диапазон семейства, выход одной fixed-версии и runtime programming - разные свойства. Шаг кода - изменение установившегося VOUT между соседними кодами, а не автоматически точность.

**Измерение:** Для каждого допустимого кода запустить DC или установившийся tran. В ADE XL оформить code как design variable/sweep; цифровые уровни сформировать источниками или согласованным decoder, а не считать integer напрямую напряжением pin.

**Записать:** Default/reset code, диапазон, шаги, тип rounding/selection, запрещённые коды, результаты PVT и доступность runtime update.

Заполняемые условия / критерий / Run ID: `<...>`.

### M05. Точность выходного напряжения

**Смысл:** Показывает отклонение установившегося выхода от заданного целевого значения. Общая accuracy может включать process, voltage, temperature, load, reference и trim - это надо перечислить.

**Измерение:** DC operating point по PVT/load/codes. Для режимов без корректной DC точки использовать установившийся tran и среднее на определённом окне.

**Записать:** Проценты и при необходимости mV; область PVT/load, fixed или ideal reference, representation, untrimmed/trimmed.

Заполняемые условия / критерий / Run ID: `<...>`.

### M06. Line regulation

**Смысл:** Насколько меняется DC VOUT при изменении входного питания. Это статическая чувствительность, отличная от transient response и PSRR.

**Измерение:** DC sweep VIN между VIN1 и VIN2. В ViVA/Calculator взять VOUT на концах и максимум/минимум sweep; проверить форму кривой.

**Записать:** Один явно выбранный метод и единицы, VIN1/VIN2, нагрузка, углы и кривая VOUT(VIN).

Заполняемые условия / критерий / Run ID: `<...>`.

### M07. Load regulation

**Смысл:** Изменение DC VOUT при изменении установившегося тока нагрузки. Включает конечное усиление петли и, в зависимости от точки sense, IR drop пути.

**Измерение:** DC sweep тока нагрузки от I1 до I2. Источник тока направить от VOUT к return и сохранить знак; дополнительно посмотреть operating points.

**Записать:** I1/I2, VIN, точки VOUT/VFB, результат в mV и/или mV/mA с точным названием, DC curve.

Заполняемые условия / критерий / Run ID: `<...>`.

### M08. Dropout voltage

**Смысл:** Недостаточный headroom ограничивает способность LDO поддерживать VOUT. Dropout измеряется по явно выбранной величине падения относительно регулируемого baseline.

**Измерение:** Сначала получить VOUT_base при VIN_hi с уверенным regulation. Затем DC sweep VIN вниз. Найти первое при снижении VIN пересечение VOUT = 0.98*VOUT_base; уточнить шаг/интерполяцию. Если DC не описывает состояние, подтвердить медленным tran sweep.

**Записать:** Criterion, VIN_hi, IOUT/PVT, VDO(load), ограничения отдельных AVDD/DVDD. Специальный sweep ниже recommended VIN не расширяет рабочую область.

Заполняемые условия / критерий / Run ID: `<...>`.

### M09. PSRR

**Смысл:** Показывает, насколько малая переменная помеха заданного питающего домена проходит на выход. При принятом положительном знаке больше dB означает лучшее подавление.

**Измерение:** В ADE выбрать AC. В источнике исследуемого питания задать DC=VIN и AC magnitude=1, phase=0; остальные независимые AC stimuli=0. EN держать в valid HIGH. Выполнить logarithmic frequency sweep; вывести transfer VOUT_ac/VIN_ac.

**Записать:** Кривая по frequency и табличные точки, например 1 kHz/100 kHz/1 MHz по требованиям, с PVT/load/headroom и полярностью dB.

Заполняемые условия / критерий / Run ID: `<...>`.

### M10. Спектральная плотность и интегральный шум

**Смысл:** ASD e_n(f) имеет единицы V/sqrt(Hz), PSD S_v(f) - V^2/Hz; integrated output noise имеет единицы Vrms и зависит от полосы. Это три разных представления.

**Измерение:** В ADE выбрать noise, задать output pair VOUT/return и sweep frequency. Если форма требует input source для input-referred результата, использовать корректный выбранный источник; для datasheet брать output-referred результат. Проверить реальные единицы вывода ViVA.

**Записать:** ASD в заданной frequency, например 30 kHz, и/или Vrms в заданной полосе; output node, включённые источники, модель и run.

Заполняемые условия / критерий / Run ID: `<...>`.

### M11. Startup, rise time и soft-start

**Смысл:** Время от события запуска до результата, время нарастания 10-90% и закон ограничения стартового тока описывают разные аспекты включения.

**Измерение:** Tran: сначала EN=0; после установления VIN/VREF включить EN. Отдельный tran запускает supply ramp с заданной скоростью. Сохранить EN, VIN, VREF, VOUT и все supply currents.

**Записать:** Условие trigger, окно/hold time, load/COUT, supply ramp, peak current, over/undershoot. Soft-start profile не сводить к одному t10-90.

Заполняемые условия / критерий / Run ID: `<...>`.

### M12. Установление после изменения code/setpoint

**Смысл:** Это динамическая реакция на новое целевое напряжение. Она отличается от startup и от статического шага DAC.

**Измерение:** Tran: дать установиться начальному режиму; переключить code или reference с заданным фронтом; записать input controls и VOUT. Повторить переход вверх и вниз; при реальной последовательной записи моделировать её.

**Записать:** Code1/code2, момент trigger, величина шага, epsilon/hold_time, over/undershoot и waveform.

Заполняемые условия / критерий / Run ID: `<...>`.

### M13. Load transient: undershoot, overshoot, recovery

**Смысл:** Быстрое увеличение тока временно просаживает выход; уменьшение может вызвать выброс. Амплитуда и время восстановления зависят от петли и R/L/C окружения.

**Измерение:** Tran с pulse/PWL current load. Дать установить low load, перейти к high, дождаться установления, затем обратно. Использовать конечные фронты и save/maxstep, достаточные для формы отклика; проверить с уменьшенным maxstep.

**Записать:** Два направления, исходные/конечные уровни, амплитуда, recovery, окна анализа и waveform VOUT/IOUT. Уточнить, включён ли мгновенный ESR/ESL spike.

Заполняемые условия / критерий / Run ID: `<...>`.

### M14. Line transient

**Смысл:** Реакция на конечный скачок входного питания. Это large-signal time-domain тест, не DC line regulation и не AC PSRR.

**Измерение:** Tran с pulse/PWL supply. Проверить оба направления; реальные связанные домены изменять по выбранному application сценарию, а не автоматически оставлять постоянными.

**Записать:** Supply waveform, изменение VOUT, критерий recovery, PVT/headroom и уровень модели соединений.

Заполняемые условия / критерий / Run ID: `<...>`.

### M15. Устойчивость, loop bandwidth, phase/gain margin

**Смысл:** LDO - замкнутая петля. Phase margin и gain margin характеризуют её small-signal поведение около заданной рабочей точки; выходные C/ESR и нагрузка меняют полюса/нули.

**Измерение:** В измерительной копии schematic поставить analogLib iprobe в согласованной ветви feedback, выбрать STB и instance probe. Выполнить frequency sweep и вывести loopGain, phase margin/gain margin через доступные результаты STB. Сопоставить OP с исходной схемой.

**Записать:** LoopGain magnitude/phase, probe instance/location, PM/GM/crossing, PVT, load и COUT/ESR/ESL, а также small/large-signal transient confirmation.

Заполняемые условия / критерий / Run ID: `<...>`.

### M16. IQ и токи отдельных доменов

**Смысл:** IQ - потребление в заданном режиме, обычно без внешней нагрузки. Многодоменный IP может потреблять IVIN, IAVDD, IDVDD и bias/reference currents отдельно.

**Измерение:** DC operating point либо среднее установившегося tran. В ADE сохранить currents питающих источников и проверить их sign convention на простом режиме. Измерить no-load и нужные loaded точки отдельно.

**Записать:** Режим, load, supplies, reference/bias included/excluded, ток каждого домена и суммарная мощность при разных voltages.

Заполняемые условия / критерий / Run ID: `<...>`.

### M17. Shutdown current / leakage

**Смысл:** Потребление при выключенном IP зависит от уровней остальных входов, температур и наличия внешнего заряда/питания на выходе.

**Измерение:** DC или долгий tran после завершения разряда. Сохранить токи всех питающих и сигнальных портов. Сценарий externally driven output выполнять только если такой режим разрешён.

**Записать:** TJ, input levels, output forcing/charge state, задержка измерения после EN, включённые внешние цепи.

Заполняемые условия / критерий / Run ID: `<...>`.

### M18. Номинальный ток, current limit и foldback

**Смысл:** IOUT_MAX - область нормальной regulation; ILIM - защитное ограничение; foldback - зависимость разрешённого тока от сниженного VOUT. Эти токи не взаимозаменяемы.

**Измерение:** Для IOUT_MAX sweep нагрузку и проверить все спецификации. Для current limit использовать ступени/scan Rload или согласованный electronic load. Для foldback снять IOUT(VOUT) в допустимом testbench с изменением Rload или согласованным voltage clamp, затем подтвердить fault/recovery tran.

**Записать:** IOUT_MAX, ILIM и IFOLDBACK при заданном VOUT, режим защиты, recovery, модель thermal effects либо её отсутствие.

Заполняемые условия / критерий / Run ID: `<...>`.

### M19. Power-good thresholds, hysteresis и delays

**Смысл:** PG сообщает, что выход пересёк заданную область. Порог вверх, порог вниз, hysteresis и задержка - отдельные параметры; output type/domain должны быть указаны.

**Измерение:** DC/медленный tran sweep выхода через доступный sense/test interface: вверх и вниз. При отсутствии такого interface использовать согласованный testbench/изменение нагрузки. Затем отдельные faster ramps для delays. Сохранить VOUT/VFB и PG.

**Записать:** Порог/единицы, polarity, hysteresis, delay, pull-up/load, supply validity и поведение при low VDD.

Заполняемые условия / критерий / Run ID: `<...>`.

### M20. VIH/VIL, VOH/VOL, domains

**Смысл:** VIH/VIL задают допустимые входные логические уровни; observed trip point отдельного экземпляра не равен гарантированным VIH/VIL. VOH/VOL зависят от тока нагрузки выхода.

**Измерение:** Input: DC sweep вниз/вверх и tran конечными фронтами, наблюдая управляемую функцию. Output: DC load sweep в обоих разрешённых состояниях. Timing/drive models нужных digital arcs могут оформляться через Liberate в согласованном flow.

**Записать:** Уровни в V или долях названного DVDD, токи/ёмкость нагрузки, default/floating/off-state, source stage.

Заполняемые условия / критерий / Run ID: `<...>`.

### M21. COUT, ESR, ESL и допустимая нагрузка

**Смысл:** COUT хранит заряд; ESR даёт resistive step/zero; ESL создаёт inductive spike и high-frequency resonance. Номинальная ёмкость детали может отличаться от effective capacitance.

**Измерение:** Параметризовать последовательные C/R/L в output branch. Sweep C/ESR/ESL совместно с load/PVT; использовать M15 для small-signal stability, M11/M13/M14 для динамики.

**Записать:** Effective C range, ESR/ESL model, load, test matrix, предельные точки, link to component assumptions и PDN.

Заполняемые условия / критерий / Run ID: `<...>`.

### M22. Shutdown output: Hi-Z / discharge resistance

**Смысл:** После выключения VOUT может быть Hi-Z, разряжаться через resistor/switch или удерживаться другим способом. Это существенно для sequence и back-power.

**Измерение:** Tran после выключения с заданным COUT/load; сохранить VOUT и discharge current. Для Rdis использовать разрешённое voltage forcing на выключенном выходе и измерить I(VOUT) либо соответствующую модель разряда.

**Записать:** Состояние выхода, Rdis/curve, discharge time thresholds, COUT/load и off-supply assumptions.

Заполняемые условия / критерий / Run ID: `<...>`.

### M23. Power sequencing, terminal stress и review без PERC

**Смысл:** Разные порядки питания способны открывать parasitic junctions или создавать недопустимые VGS/VGD/VDS. Функционирование и надёжность здесь связаны, но это разные критерии.

**Измерение:** Tran sequences/power-off scenarios; сохранить выбранные terminal voltages/currents и operating points. Использовать только реально доступные model/assert/check features simulator. Calibre DRC/LVS сами по себе не дают полного покрытия stress/ESD.

**Записать:** PERC: not_run, reason=deck_not_available. Отдельно - review record, что проверено manually/simulation, что остаётся непокрытым и кто отвечает.

Заполняемые условия / критерий / Run ID: `<...>`.

### M24. Площадь и требования к соединениям

**Смысл:** Macro area - геометрия поставляемой границы; active area/keepout/area с guard ring могут различаться. Routing requirements определяются током, IR budget, coupling и правилами процесса.

**Измерение:** Размеры получить из layout/LEF/GDS в согласованных units; A=W*H для прямоугольной boundary. Calibre PEX даёт выбранные R/C соединений; влияние внешних R/L/C проверить ADE sweep. Допустимые current densities/vias взять из PDK и профильной проверки.

**Записать:** Boundary/layout revision, LEF agreement, route constraints по каждой группе pin и evidence. Проверка EM, если необходима, оформляется отдельно имеющимся утверждённым flow.

Заполняемые условия / критерий / Run ID: `<...>`.

## 7. Графики, test matrix и интеграция

Обязательные для вашего проекта tests: `<nominal -> PVT -> post-layout; статистика по требованиям>`. Графики: VOUT(VIN/IOUT), dropout(load), PSRR(f), noise(f), startup, load step, loopGain; для отсутствующей функции N/A.

| Test / figure | Файл / Run ID | Условия и результат |
|---|---|---|
| `<...>` | `<...>` | `<...>` |

Силовые пути и reference/feedback/ground routes: `<R/C/L/current/EM budget, shield/keepout, реальные точки sense; M24>`. Внешние компоненты: `<effective C, ESR/ESL, tolerances; M21>`. Ориентации/boundary: `<утверждённые>`.

## 8. Test modes, signoff и выпуск

Code/default/test bus: `<таблица реальных modes или N/A; M04/M19/M20>`. Отдельно указать режимы с изменённой feedback/protection topology.

Calibre DRC: `<исходный result/run/deck/waivers>`. LVS: `<MATCH/MISMATCH/run/deck>`. PEX: `<deck, RC corner, options, view/netlist>`.

Electrical checks: `<только реально доступное покрытие deck и review>`.

PERC: `not_run`; причина `deck_not_available`. Reliability/ESD/latch-up review: `<область и решение либо pending>`. Отсутствие deck не означает завершённую проверку.

Поставка: `<GDS/LEF/CDL/LVS netlist/PEX/model/lib/report/data>`. Открытые вопросы и согласование: `<ID, owner, срок, review reference>`.
