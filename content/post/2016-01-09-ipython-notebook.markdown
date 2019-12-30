---
author: re9ulus
date: "2016-01-09T16:00:00Z"
header-img: img/post-bg-06.jpg
title: IPython Notebook
---

Как установить и что с ним потом делать
<!--more-->
IPython Notebook - это интерактивная среда для программирования на языке python, которая позволяет объединить код, текст (включая [Markdown](https://daringfireball.net/projects/markdown/ "Markdown")), графики, математические формулы ([MathJax](https://www.mathjax.org/ "Mathjax")) и скомбинировать все в одном отчете. Отчет можно конвертировать в html, LaTeX, pdf и другие форматы.

Удобный инструмент для ведения исследований, заметок, конспектов и тому подобного.
Существуют [статьи и учебные материалы](http://nb.bianp.net/sort/views/ "Links to the best IPython and Jupyter Notebooks.") распространяемые в качестве ноутбуков.

# Установка

## 1 способ
Самым простым способом установки является использование python дистрибутива [Anaconda](https://www.continuum.io/downloads "Anaconda"), содержащего более 300 научных python пакетов, включая Ipython.

## 2 способ
Для установки на \*nix системах

{{< highlight bash >}}
pip install ipython[all]
{{< / highlight >}}

Для установки на windows необходимо последовательно установить:

1. [setuptools](https://pypi.python.org/pypi/setuptools "setuptools")
2. [pyreadline](https://pypi.python.org/pypi/pyreadline "pyreadline")
3. [IPython](https://pypi.python.org/pypi/ipython "ipython")

# Запуск

Для запуска ноутбука необходимо в терминале выбрать директорию, где будут храниться заметки и воспользоваться командой

{{< highlight bash >}}
ipython notebook
{{< / highlight >}}

На 8888 порту будет запущен локальный сервер и в браузере откроется страница с ноутбуком.

{{<image src="/img/ipython_notebook/notebook_1_start.png" alt="jupyter notebook" position="center">}}

# Использование

Новый документ можно создать с помощью выпадающего меню.

{{<image src="/img/ipython_notebook/notebook_2_new_document.png" alt="New document" position="center">}}

После чего будет создан новый, пустой документ. Переименовать документ можно нажав на строку *Untitled*, либо через меню *File->Rename*.

{{<image src="/img/ipython_notebook/notebook_3_new_document_view.png" alt="New document view" position="center">}}

Документ сохраняется автоматически и правильно понимает сочетание *Ctrl-S*.

Код в Ipython документах организован в ячейки (`cells`). По нажатии *Ctrl-Enter* код в ячейке выполняется и результат вычислений отображается под ней.

{{<image src="/img/ipython_notebook/notebook_4_cell_example.png" alt="Cell example" position="center">}}

Ячейка может относиться к одному из нескольких типов. Выбрать тип ячейки можно используя пункт меню *Cell->Cell Type*.

## Code cell
Позволяет писать и редактировать код с подсветкой синтаксиса и автоподстановкой (*Tab*). После добавления директивы 

{{< highlight python >}}
%matplotlib inline
{{< / highlight >}}

в начале ячейки, появляется возможность строить графики прямо в документе.

{{< highlight python >}}
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import numpy as np

img=mpimg.imread('city.jpg')
print "Image size: {0}".format(img.shape)

# Select green channel
img_green = img[:,:,1]
plt.imshow(img_green, cmap="Greys_r")
plt.show()

# Plot green channel row intensity
plt.plot(img_green[150, :])
plt.show()
{{< / highlight >}}

{{<image src="/img/ipython_notebook/notebook_5_code_cell.png" alt="Code cell" position="center">}}

## Markdown cell
Ячейки для документирования, позволяют использовать [Markdown](https://daringfireball.net/projects/markdown/ "Markdown") синтаксис и математические формулы [MathJax](https://www.mathjax.org/ "Mathjax").

{{< highlight python >}}
### H3 title for the text ###

TODO List:

 1. Create note about IPython notebook.
 2. Add part about Markdown.
 3. Do not forget about MathJax.
 4. Add table in the end.
 
<h4>An Identity of Ramanujan</h4>

$$ \frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} =
1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}}
{1+\frac{e^{-8\pi}} {1+\ldots} } } } $$

#### Simple table

| Tables | True  | False |
| ------ |:-----:| -----:|
| True   | 78    | 14    |
| False  | 3     | 45    |
{{< / highlight >}}


{{<image src="/img/ipython_notebook/notebook_6_markdown_cell.png" alt="Markdown cell" position="center">}}

## Raw cell
Эти ячейки служат для хранения дополнительной информации, никак не исполняются и не модифицируются. Например в них можно хранить LaTeX для статьи.

# Конвертация notebook-a в другие форматы

[Конвертировать ноутбук](https://ipython.org/ipython-doc/1/interactive/nbconvert.html "Conver notebook") можно командой

{{< highlight bash >}}
ipython nbconvert --to FORMAT notebook.ipynb
{{< / highlight >}}

Допустимые форматы:

- `--to html` - статическая html страница
- `--to latex` - LaTeX
- `--to slides` - [Reveal.js](http://lab.hakim.se/reveal-js/#/ "Reveal.js") html слайдшоу
- `--to markdown` - Markdown, код будет экранирован
- `--to rst` - формат для [Sphinx docs](http://www.sphinx-doc.org/en/stable/)
- `--to python` - python скрипт.

# Комбинации клавиш

- `Esc` - командный режим (создание, удаление, перемещение ячеек)
- `Enter` - режим редактирования (изменение содержимого ячейки)
- `Ctrl-Enter` - выполнить код в ячейке
- `Shift-Enter` - выполнить код в ячейке и перейти к следующей
- `Alt-Enter` - выполнить код в ячейке и вставить ячейку снизу
- `Ctrl-M` - прервать исполнение
- `A` - вставить ячейку выше
- `B` - вставить ячейку ниже
- `K` - выбрать предыдущую ячейку
- `J` - выбрать следующую ячейку
- `D,D` - удалить ячейку
- `Y` - в code cell
- `M` - в markdown cell
- `R` - в raw cell
