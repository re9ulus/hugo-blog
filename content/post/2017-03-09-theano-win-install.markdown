---
author: re9ulus
date: "2017-03-09T00:00:00Z"
header-img: img/post-bg-11.jpg
title: Ставим Theano на Windows
---

*Theano* — популярная [библиотека](http://deeplearning.net/software/theano/) для работы с нейронными сетями. И как многие полезные Machine Learning инструменты, на Windows она просто так не ставится.<!--more-->

В заметке описан самый простой из известных мне на данный момент способов установки *Theano* на Windows, благодаря которому у меня получилось поставить на `Python 3.5 x64` пакет `Theano 0.9.0.rc3` с работающей поддержкой GPU, система `win8.1 x64`.

Еще можно воспользоваться [официальной документацией](http://deeplearning.net/software/theano/install.html) или [альтернативной инструкцией](https://github.com/Theano/Theano/issues/5348).

## Установка Theano

1\. Нам понадобится [Conda](https://conda.io/docs/intro.html), которая идет в пакете [Anaconda](https://www.continuum.io/downloads) для ML в Python. Я использую `Anaconda  3` и `Python 3.5`.

2\. В консоли ставим нужные пакеты.

```
conda install mingw libpython
```

3\. Клонируем репозиторий *Theano*. (Естественно понадобится [Git](https://git-scm.com))

```
git clone https://github.com/Theano/Theano.git
```

4\. Устанавливаем Python пакет.

```
cd Theano
python setup.py install
```

Готово.

*Theano* должен быть установлен. Для проверки корректности можно попробовать выполнить простой скрипт в интерпретаторе.

```python
>>> import theano.tensor as T
>>> from theano import function
>>> x = T.dscalar('x')
>>> y = T.dscalar('y')
>>> z = x + y
>>> f = function([x, y], z)
>>> f(2, 3)
# array(5.0)
```

Если выполнилось — *Theano* нормально установлен. Но работает он только на процессоре (CPU), значит сети будут считаться очень медленно. Настроим работу с видеокартой (GPU).

## Настраиваем работу Theano с GPU

1\. Нам понадобится Visual Studio. Я использовал `Enterprise edition 2013`.
Можно попробовать использовать Community version студии и [MS SDK for Win7](https://www.microsoft.com/en-us/download/details.aspx?id=8279).
VS 2015 использовать не стоит, она не поддерживает CUDA 7.5.

2\. Устанавливаем [CUDA 7.5 Toolkit](https://developer.nvidia.com/cuda-75-downloads-archive). Придется зарегистрироваться на сайте NVidia.

3\. Добавляем в домашнюю директорию пользователя (например `C:\Users\re9ulus`) файл `.theanorc.txt` с содержимым:

```
#!sh
[global]
device = gpu
floatX = float32
[nvcc]
compiler_bindir=D:\soft\dev\vs2013\VC\bin
```

где `compiler_bindir` — путь к папке `bin` в установленной *Visual Studio*.

Готово. Теперь *Theano* должна использовать GPU. Проверить можно с помощью примера из [официальной документации](http://deeplearning.net/software/theano/tutorial/using_gpu.html):

```python
from theano import function, config, shared, sandbox
import theano.sandbox.cuda.basic_ops
import theano.tensor as T
import numpy
import time

vlen = 10 * 30 * 768  # 10 x #cores x # threads per core
iters = 1000

rng = numpy.random.RandomState(22)
x = shared(numpy.asarray(rng.rand(vlen), 'float32'))
f = function([], sandbox.cuda.basic_ops.gpu_from_host(T.exp(x)))
print(f.maker.fgraph.toposort())
t0 = time.time()
for i in range(iters):
    r = f()
t1 = time.time()
print("Looping %d times took %f seconds" % (iters, t1 - t0))
print("Result is %s" % (r,))
print("Numpy result is %s" % (numpy.asarray(r),))
if numpy.any([isinstance(x.op, T.Elemwise) for x in f.maker.fgraph.toposort()]):
    print('Used the cpu')
else:
    print('Used the gpu')
```
