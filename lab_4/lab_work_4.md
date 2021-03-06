# Лабораторная работа №4

## Дано

1. Набор заданных английских текстов большого размера.
    Набор таких файлов можно скачать и положить в папку `lab_4`:

    * [5_7.txt](https://www.dropbox.com/s/ch486h1avdjaifg/5_7.txt?dl=1)
    * [15_2.txt](https://www.dropbox.com/s/kkz12uriw6gj6qx/15_2.txt?dl=1)
    * [10547_3.txt](https://www.dropbox.com/s/75yzydzwxgud1j1/10547_3.txt?dl=1)
    * [12230_7.txt](https://www.dropbox.com/s/tzm7iylvkptw1kr/12230_7.txt?dl=1)

Таким образом, повторить структуру:

```bash
|-- 2018-2-level-labs
  |-- lab_4
    |-- 5_7.txt
    |-- 15_2.txt
    |-- 10547_3.txt
    |-- 12230_7.txt
```

Каждый текст - это отдельный текстовый файл.

Если интересно, то весь набор текстов можно скачать 
[по ссылке](http://ai.stanford.edu/~amaas/data/sentiment/).

## Что надо сделать

### Шаг 0.1 Подготовка (проделать вместе с преподавателем на практике)

1. В имеющемся форке репозитория, обновить содержимое до последнего доступного
состояния в родительском репозитории.
2. Изменить файл `main.py`
3. Закоммитить изменения и создать pull request

### Шаг 0.2 Прочитать содержимое документов

Необходимо считать все документы в список из строк. 

> **Внимание**: этот код уже написан в `main.py`. Если у вас больше файлов, убедитесь, что
> все файлы указаны в этом списке.

### Шаг 1. Подготовка корпуса к использованию 

Требуется сделать очистку текста от ненужных символов и знаков пунктуации
и токенизацию (разбиение на слова).

Для этого реализуем метод `clean_tokenize_corpus(texts) -> list`:

```python
def clean_tokenize_corpus(texts):
  pass
```

Метод возвращает список из текстов, каждый из которых - список токенов (слов).

Например, есть два текста:

1. `Mar#y wa$nted, to swim! However, she was afraid of sharks.`
2. `The film concerns a genetically engineered snake`.

Из них должен получиться следующий список:

```json
[
  [
    "mary", "wanted", "to", "swim", "however", "she", "was", "afraid", "of", "sharks"
  ],
  [
    "the", "file", "concerns", "a", "genetically", "engineered", "snake"
  ]
]
```

## Шаг 2. Создание класса расчета TF-IDF

### Шаг 2.1 Объявление полей класса

Создаем класс `TfIdfCalculator`. Внутри него задаем поля `corpus`, `tf_values`, `idf_values`, `tf_idf_values`.

### Шаг 2.2 Заполнение класса 

Класс `TfIdfCalculator` имеет конструктор, принимающий на вход корпус, то есть
список из строк и сохраняющий этот корпус во внутреннее поле класса `corpus`.

### Шаг 2.3 Расчет TF

Итак, для каждого слова из каждого документа нужно уметь рассчитать TF-IDF показатель.

А именно, он состоит из двух частей. Первая часть - это TF (term frequency) - обычная частота
слова в рамках заданного документа. Формула расчета такова:
<img src="https://latex.codecogs.com/gif.latex?tf(t,d)&space;=&space;\frac{n_{t}}{\sum_{k}{n_{k}}}" title="tf(t,d) = \frac{n_{t}}{\sum_{k}{n_{k}}}" />

Здесь <img src="https://latex.codecogs.com/gif.latex?n_{t}" title="n_{t}" />
это количество раз слово <img src="https://latex.codecogs.com/gif.latex?t" title="t" />
встречается в документе <img src="https://latex.codecogs.com/gif.latex?d" title="d" />.

**TF** тем больше чем чаще слово встречается в заданном документе и тем меньше чем реже
встречается заданное слово. Таким образом, мы получаем важность слова в данном тексте.

Внутри класса необходимо реализовать метод `calculate_tf(self)`:

```python
class TfIdfCalculator:
  ...
  def calculate_tf(self):
    pass
```

Данный метод для каждого текста из корпуса, который уже хранится в классе,
рассчитывает значение **TF** для каждого слова в этом документе. В результате, для
одного текста получается словарь, который содержит структуру:

```json
{
  "word1": 0.17,
  "word2": 0.45
}
```

А так как таких текстов у нас 
<img src="https://latex.codecogs.com/gif.latex?|D|" title="|D|" />, то и 
в результате работы функции получается список из 
<img src="https://latex.codecogs.com/gif.latex?|D|" title="|D|" /> элементов.

Каждый элемент списка - словарь с **TF** для каждого слова.

Полученный список метод `calculate_tf(self)` сохраняет в собственное поле 
`self.tf_values`.

Обратите внимание, вам требуется найти частоту слова в данном тексте, что посчитать
значение **TF** для этого слова.
Вы это уже делали в первой лабораторной работе, однако, теперь текст - это не
единая строка, а массив из строк, вспомните и сделайте аналогично, слегка
переработав.

### Шаг 2.4 Расчет IDF

Вторая часть - это IDF (inversed document frequency):
<img src="https://latex.codecogs.com/gif.latex?idf(t,D)&space;=&space;log(\frac{|D|}{|\{d_{i}&space;\epsilon&space;D&space;|&space;t&space;\epsilon&space;d_{i}\}|})" title="idf(t,D) = log(\frac{|D|}{|\{d_{i} \epsilon D | t \epsilon d_{i}\}|})" />

**IDF** тем больше чем реже слово встречается в корпусе и тем меньше чем чаще
встречается заданное слово.

Здесь: <img src="https://latex.codecogs.com/gif.latex?|D|" title="|D|" /> - это 
количество документов в корпусе, а 
<img src="https://latex.codecogs.com/gif.latex?|\{d_{i}&space;\epsilon&space;D&space;|&space;t&space;\epsilon&space;d_{i}\}|" title="|\{d_{i} \epsilon D | t \epsilon d_{i}\}|" /> 
- это количество документов, в которых присутствовало слово 
<img src="https://latex.codecogs.com/gif.latex?t" title="t" />.

Внутри класса необходимо реализовать метод `calculate_idf(self)`:

```python
class TfIdfCalculator:
  ...
  def calculate_idf(self):
    pass
```

Данный метод для каждого текста из корпуса, который уже хранится в классе,
рассчитывает значение **IDF** для каждого слова, которое встречается хотя бы в одном
документе. В результате, получается словарь, который содержит структуру:

```json
{
  "word1": 0.33,
  "word2": 0.21
}
```

Ключ - слово, значение - **IDF** для данного слова.

Полученный список метод `calculate_idf(self)` сохраняет в собственное поле 
`self.idf_values`.

### Шаг 2.5 Расчет TF-IDF

Теперь можем рассчитать общую метрику **TF-IDF**:
<img src="https://latex.codecogs.com/gif.latex?\operatorname&space;{tf-idf}(t,d,D)=\operatorname&space;{tf}(t,d)\times&space;\operatorname&space;{idf}(t,D)" title="\operatorname {tf-idf}(t,d,D)=\operatorname {tf}(t,d)\times \operatorname {idf}(t,D)" />

Внутри класса необходимо реализовать метод `calculate(self)`:

```python
class TfIdfCalculator:
  ...
  def calculate(self):
    pass
```

Данный метод для каждого текста из корпуса, который уже хранится в классе,
рассчитывает значение **TF-IDF** для каждого слова, которое встречается хотя бы в одном
документе. В результате, получается словарь, который содержит структуру:

```json
{
  "word1": 0.33,
  "word2": 0.21
}
```

Ключ - слово, значение - **TF-IDF** для данного слова.

Полученный список метод `calculate(self)` сохраняет в собственное поле 
`self.tf_idf_values`.

Если значений `self.tf_values`, `self.idf_values` нет или они пусты, 
метод ничего не делает.

### Шаг 2.6 Получение отчета для данного слова

Теперь, когда были произведены расчеты **TF-IDF** для каждого слова, мы можем начать
анализировать результаты, например, сравнивать значения метрики для нескольких слов.

Чтобы узнать информацию по выбранному слову, требуется реализовать метод 
`report_on_word(self, word)`:

```python
class TfIdfCalculator:
  ...
  def report_on_word(self, word, document_index):
    pass
```

Данный метод возвращает значение кортеж, где первый элемент - это **TF-IDF** для 
заданного слова, если оно встречалось в тексте и `None` иначе, второе - место, 
которое данное слово занимает в рейтинге значимости слов в заданном тексте.

Например, получаем кортеж (1.3, 100).
Это означает, что слово имеет значение **TF-IDF** равное 1.3, является сотым
по значимости в тексте. Важно при определении места учитывать тот факт, что на
одном месте в рейтинге могут быть сразу несколько слов. 

В таком рейтинге, первое место у самого значимого слова, то есть у того, чьё значение
**TF-IDF** максимально.

### Шаг 2.7 Сохранение отчета в Excel-приемлемом формате

> **Важно:** выполнение заданий 1, 2.1-2.6 + 2.7 соответствует оценке 9 баллов.

Требуется сохранить содержимое наших расчетов в `.csv` файл. Особенность такого файла
заключается в том, что все строки - это наборы значений, разделенных запятой.

Например, есть таблица:

| Слово | TF (text_1) | TF (text_1) | IDF | TF-IDF (text_1) | TF-IDF (text_2) |
| ------------- |-------------| -----| -----| -----| -----|
| bag   | 0.142        | 0        | 0.3           | 0.043 | 0 |

Превращается в текстовый файл (**не бинарный**) `.csv`, с содержимым:

```sh
word,tf_text_1,tf_text_2,idf,tf_idf_text_1,tf_idf_text_2
bag,0.142,0,0.3,0.043,0
```

Вам необходимо обеспечить сохранение в такой файл расчетов по заданному корпусу.

Для этого, требуется реализовать метод `dump_report_csv(self)`:

```python
class TfIdfCalculator:
  ...
  def dump_report_csv(self):
    pass
```

Этот метод создает файл `report.csv` в той же директории, что и сам `main.py` для
Лабораторной работы №4. Если расчеты не производились, то создается пустой файл
(без строк).

Проверить вы себя сможете легко, для этого можно открыть Excel и импортировать данные
из `.csv` файла - 
[инструкция](http://office-guru.ru/excel/preobrazovyvaem-csv-v-excel-kak-importirovat-faily-csv-v-elektronnye-tablicy-excel-368.html#csv1-3).

Обратите внимание на то, что первой строкой идет заголовок таблицы, он должен
соответствовать количеству колонок. 

В названии колонок вместо 
`_text_1` нужно вставить название файла, которому соответствует данная колонка.
Для этого, вам нужно расширить конструктор класса списком имён файлов и сохранить
его в собственном поле `file_names`. 

> **Важно:** Порядок в списке имён должен соответствовать
> порядку текстов в корпусе

### Шаг 2.8 Расчет расстояния между представлениями текстов

> **Важно:** выполнение заданий 1, 2.1-2.7 + 2.8 соответствует оценке 10 баллов.

Итак, пусть у нас есть посчитанные значения **TF-IDF** для каждого слова.
Кроме того, нам известны все слова из данного корпуса.

Попробуем найти "расстояние" между двумя текстами.
Идея такая, что тексты на одну и ту же тему должны быть "ближе", чем тексты
на разные темы. Если говорить технически, то если бы мы могли представить тексты
как векторы, то расстояние между векторами для текстов одной темы были бы меньше
чем расстояние между векторами для текстов разных тем.

Такой подход (превращение текстов в векторы) используется при работе нейронных сетей.
Попробуем сделать первый шаг и представить наши тексты как векторы и посчитать расстояние между ними.

Для этого:

1. Есть два текста: 
    `["mary", "was", "angry", "with", "tom"]` и `["tom", "liked", "mary"]`.
    Возьмем список все слов. Именно список, так как нам впоследствии будет важен
    порядок.
    Например: `words=['mary', 'was', 'angry', 'with', 'tom', 'liked']`.
2. Теперь представим тексты как векторы, где на позиции `i` будет стоять либо 
    значение **TF-IDF**, если `words[i]` есть в тексте либо 0.
    Получаем, для первого слова: `[0.1, 0.2, 0.3, 0.4, 0.5, 0]`, а для второго:
    `[0.12, 0, 0, 0, 0.13, 0.17]`. Числа, конечно, вымышленные.
3. Найдем косинусное расстояние между векторами: <img src="https://latex.codecogs.com/gif.latex?cos(A,B)&space;=&space;\frac{A&space;\cdot&space;B}{||A||||B||}&space;=&space;\frac{\sum_{i=1}^{n}A_i&space;\times&space;B_i}{\sqrt{\sum_{i=1}^{n}{(A_i)^{2}}}&space;\times&space;\sqrt{\sum_{i=1}^{n}{(B_i)^2}}}" title="cos(A,B) = \frac{A \cdot B}{||A||||B||} = \frac{\sum_{i=1}^{n}A_i \times B_i}{\sqrt{\sum_{i=1}^{n}{(A_i)^{2}}} \times \sqrt{\sum_{i=1}^{n}{(B_i)^2}}}" />

Для этого, требуется реализовать метод 
`cosine_distance(self, index_text_1, index_text_2)`:

```python
class TfIdfCalculator:
  ...
  def cosine_distance(self, index_text_1, index_text_2):
    pass
```

Этот метод создает принимает на вход индексы двух текстов в корпусе, который мы
использовали для инициализации экземпляра класса. Он должен возвращать значение
расстояния двух векторов. В случае несуществования такого/таких индексов возвращается
очень большое число - `1000`.

## Ссылки

В данной работе предполагается использовать избранные тексты из

```sh
@InProceedings{maas-EtAl:2011:ACL-HLT2011,
  author    = {Maas, Andrew L.  and  Daly, Raymond E.  and  Pham, Peter T.  and  Huang, Dan  and  Ng, Andrew Y.  and  Potts, Christopher},
  title     = {Learning Word Vectors for Sentiment Analysis},
  booktitle = {Proceedings of the 49th Annual Meeting of the Association for Computational Linguistics: Human Language Technologies},
  month     = {June},
  year      = {2011},
  address   = {Portland, Oregon, USA},
  publisher = {Association for Computational Linguistics},
  pages     = {142--150},
  url       = {http://www.aclweb.org/anthology/P11-1015}
}
```
