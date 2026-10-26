# Дополнение: что брать из SGC67 для DC/DC, а что не переносить в LDO

SGC67 полезен как пример многодоменного IP, интерфейсов, зависимостей, assembly/test документации. Его switching-specific метрики требуют отдельного раздела; методики DC/AC обычного LDO не переносятся на них автоматически.

## 1. Эффективность и потребление

**Смысл:** eta=100*Pout/Pin. В switching block важны режим PWM/VFM/PFM, frequency, load и потери L/C/power devices.

**Как измерить:** длительный tran до установившегося режима; считать average(v(t)*i(t)) на одинаковом интервале для входов/выхода с положительными направлениями потока энергии. Включить все питающие домены, исключая double count. Окно покрывает достаточное число switching/burst cycles и не включает startup.

**Записать:** topology, VIN/VOUT/IOUT, fSW/mode, L/C/DCR/ESR/ESL, модели package/PDN, averaging window. Произведение average(V)*average(I) не всегда равно average(V*I).

## 2. Output ripple и полоса измерения

**Смысл:** period/burst ripple и broadband noise разные метрики; p-p зависит от полосы и окна наблюдения.

**Как измерить:** tran в установившемся режиме с maxstep/output sampling, достаточными для switching spikes. Выбрать честное окно, применить заранее определённый фильтр/measurement bandwidth, затем max(VOUT)-min(VOUT). Если в spec 20 MHz BW, определить filter response и transient settling фильтра; редкая выборка не является 20 MHz фильтром.

**Записать:** p-p/RMS, BW/filter, input/output sampling, mode, fSW, load, L/C/parasitics и что делалось со spikes. Не брать Vrms из p-p без формы сигнала.

## 3. PSRR и устойчивость

**Смысл:** рабочее состояние switching converter периодически меняется; обычный AC/STB вокруг одной DC operating point не описывает полный switching режим.

**Как измерить:** согласованный averaged model только с описанными допущениями, либо periodic steady-state/small-signal flow (например PSS/PAC/PSTB, если доступны solver/license и режим подходит), либо transient sine injection на входе с выделением малой компоненты нужной частоты после установления. При transient повторить меньшую амплитуду для проверки linearity, достаточные время/число циклов и разрешение.

**Записать:** метод, режим PWM/burst, reference signal, injection amplitude/frequency, обработка и scope. Для variable frequency/burst один период PSS может не существовать в выбранной постановке.

## 4. Load transient, current limit и test modes

Из M13/M18 методики LDO взять идею стимулов/метрик, но добавить inductor current, saturation/DCR, switching state, controller mode, recovery и временное averaging. ILIM может означать peak switch current, valley current или output average - определить конкретно.

При test mode без индуктора, bypass loop или принудительных состояниях devices результат относится к этому режиму. Он не заменяет normal-operation test с реальным output filter.

## 5. Интеграция

Особенно важны power loop, LX node, PGND/AGND/reference return, DCR индуктора, hot-loop R/L и voltage domain gate driver/clock. Числа assembly из SGC67 не являются универсальными layout rules для другого IP.

Это дополнение задаёт структуру измерений; готового switching testbench и дополнительных лицензий в комплекте нет.
