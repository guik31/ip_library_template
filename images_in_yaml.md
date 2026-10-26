# Изображения в YAML, Markdown и PDF

## 1. Может ли YAML содержать графику?

YAML - формат данных, не страничный документ и не графический viewer. Он может содержать путь/URL изображения, подпись, ID, условия и связь с параметром. Поддерживающий соответствующий тип parser может прочитать `!!binary` с Base64-данными изображения; сам факт хранения bytes не создаёт отображение, а поддержка расширенных tags зависит от parser/schema.

Для библиотеки IP удобнее внешние PNG/SVG/PDF плюс относительные пути в YAML: файлы проще смотреть, сравнивать, заменять и хранить вместе с raw results. Локальные относительные пути лучше внешних URLs для переносимого release package.

## 2. Соглашение о пути

В `datasheet_media.yaml` пути считаются от корня комплекта. В реальном release это, например, `doc/figures/...`; сам YAML может лежать в другом подкаталоге. Renderer должен явно знать release root.

В Markdown путь картинки считается **от каталога Markdown-файла**. Поэтому для `doc/datasheet.md` и `doc/figures/startup.png` достаточно `figures/startup.png`. Это другое соглашение, его нельзя молча путать с release-root YAML.

## 3. Рабочий пример YAML

```yaml
heading: "LDO - рисунки и графики"
description: "Связь изображений с документом и методами измерения."
figures:
  - id: "FIG-TB"
    file: "figures/ldo_testbench.png"
    caption: "Типовой testbench: источники, DUT, нагрузка и точки измерения."
    alt: "Функциональная схема измерения LDO"
    kind: "diagram"
    method_ids: ["M01", "M13", "M21"]
    run_id: null
    status: "illustration"
```

Этот пример ссылается на реально приложенный PNG. Он иллюстративный; Run ID отсутствует, потому что изображение не является результатом ADE.

## 4. Вставка в Markdown

```markdown
![Типовой testbench LDO](figures/ldo_testbench.png)

Рисунок 1. Источники питания/reference, DUT и выходная нагрузка.
```

В готовом PDF/HTML renderer открывает файл и вставляет саму графику. Обычная ссылка на файл в YAML без такого шага останется строкой пути.

В комплекте PDF-методика реально использует приложенные PNG-диаграммы. Для traceability их IDs/подписи продублированы в datasheet_media.yaml. Подготовка этого PDF выполнена из Markdown; автоматического общего YAML-to-datasheet движка в комплекте нет.

## 5. Графики из ADE / ViVA

1. Построить waveform/graph с понятными осями, units и legend. Сохранить Run ID, representation, PVT, supplies, load и measurement window в подписи или связанном metadata.
2. Экспортировать график штатным механизмом вашей версии ViVA. Если доступен vector export, сохранить его; иначе PNG достаточного разрешения. Не растягивать маленький скриншот до страницы.
3. Положить plot в figures, а CSV/PSF/raw - в characterization data. Рисунок помогает чтению; исходные данные нужны для воспроизводимости.
4. Добавить запись в YAML и изображение в Markdown. Для SVG проверить поддержку renderer; PNG - самый переносимый вариант, PDF-рисунок может потребовать отдельного embedding/conversion.

Для графика результата запись расширяется: kind=simulation_plot, status=observed, run_id=реальный run, conditions=строка условий, parameter_ids/representation и data_file=путь к CSV. Не указывать вымышленный Run ID.

## 6. Скриншоты к waiver

Текущий `violations[].evidence` - список ссылок на evidence. Объект изображения можно добавить по согласованной схеме:

```yaml
violations:
  - rule_id: "<EXACT_RULE_ID>"
    disposition: "pending"
    evidence:
      - kind: "image"
        file: "results/drc/run_001/figures/marker_001.png"
        caption: "Нарушение в контексте соседних слоёв и ячейки."
        marker_id: "<MARKER_ID>"
```

Это расширение структуры данных; существующий consumer должен его поддерживать. Сам DRC summary остаётся неизменным; PNG не заменяет results database, rule details и обоснование решения.

## 7. Источники формата

- YAML 1.2.2: https://yaml.org/spec/1.2.2/
- Binary tag (описание типа YAML 1.1, расширенный тип): https://yaml.org/type/binary.html

Base64 допускается только в поддерживающем flow. Для вашего комплекта выбрана переносимая связка YAML + внешние изображения + Markdown/PDF.
