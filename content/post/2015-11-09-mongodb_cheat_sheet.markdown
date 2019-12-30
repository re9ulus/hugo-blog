---
author: re9ulus
date: "2015-11-09T22:00:00Z"
header-img: img/post-bg-04.jpg
title: Заметки об MongoDB
---

Мои заметки об *MongoDB* по  мотивам [курса](https://university.mongodb.com/courses/M101P/about).
<!--more-->

*MongoDB* — кросс-платформенная, документо-ориентированная NoSQL система управления базами данных с открытым исходным кодом.

## CRUD в MongoDB

Сопоставление `CRUD` операций и соответствующих им команд в MongoDB и SQL.

|     CRUD        |    Mongo    |        SQL      | 
|-----------------|-------------|-----------------|
|      Create     |     Insert  |      Insert     |
|      Read       |     Find    |      Select     | 
|      Update     |     Update  |      Update     |
|      Delete     |     Remove  |      Delete     |

## Команды консоли MongoDB
{{< highlight javascript >}}

//=//=// Основы

// Показать базы данных
show dbs

// Создать базу данных people
use people

// Текущая база данных
db

// Показать коллекции в текущей базе данных
show collections

// Добавление документа в коллекцию
doc = { name: "Smith", age: 30, profession: "hacker" };
db.people.insert(doc);
// В одну строку
db.people.insert({ name: "Jones", age: 25, profession: "designer" })

// Получить все документы из коллекции people
db.people.find(); 

//  Получить один, случайный документ из коллекции people
db.people.findOne();


//=//=//  Запросы

// Получить документ из коллекции people, с полем "name" равным "Jones"
db.people.findOne({ "name": "Jones" });

// Первый аргумент: что найти
// Второй аргумент: что получить из базы данных.
// Поле "_id" всегда присутствует по умолчанию,
// поэтому чтобы получить только профессию, необходимо явно указать что поле _id не нужно
db.people.findOne({ "name": "Jones"}, { "profession": true, "_id": false });

// Из консоли mongodb можно выполнять практически любой javascript код
for (i = 0; i < 1000; i++) {
    names = ["exam", "essay", "quiz"];
    for (j = 0; j < 3; ++j) {
        db.scores.insert({"student": i, 
            "type": names[j],
            "score": Math.round(Math.random() * 100)});
    }
}

// Получить все документы и вывести в читаемом виде
db.scored.find().pretty();
// Получить оценки всех эссе 19-го студента
db.scores.find({ student: 19, type: "essay" }, { "score": true, "_id": false });

// Использование $gt, $lt, $gte, $lte
// Получить документы с оценкой выше 95
db.scores.find({ score: { $gt: 95 }});
// Получить эссе с оценкой выше 95 и ниже, либо равной 98
db.scores.find({ score : { $gt : 95, $lte: 98 }, type : "essay" });
// Можно использовать со строками, строки сравниваются в лексикографическом порядке
db.scores.find({ name: { $lt : "D", $gt "B" }});

// Использование $exists и $type
// Найти документы, которые содержат поле profession
db.people.find({ profession : { $exists: true }});
// в которых отсутствует поле profession
db.people.find({ profession : { $exists: false }});
// Найти документы, в которых name является строкой
// Типы закодированы числами (https://docs.mongodb.org/v3.0/reference/operator/query/type/)
db.people.find( {name : { $type : 2 }});
// Найти докменты с помощью регулярного выражения
db.people.find( {name: { $regex : 'o' }});


//=//=// Логические операторы

// Использование $or
// Найти документы, заканчивающиеся на "e", или содержащие поле "age"
db.people.find({ $or: [{ name: { regex: 'e$' }}, { age: { $exists: true }}]});

// Использование $and
// Найти документы, name в которых лексиграфически больше "c" и содержит "a"
db.people.find({ $and: [{ name: { $gt: 'c' }}, {name: { $regex: 'a' }}]});
// то же самое
db.people.find({ name: {$gt: 'c', $regex: 'a'}});


//=//=//  Запросы внутри массивов

// Добавляем документы
db.accounts.insert({ name: "Howard", favorites: [ "pretzels", "beer" ]});
db.accounts.insert({ name: "George", favorites: [ "ice cream", "pretzels" ]});

// Если поле запроса является массивом, поиск будет выполнен и внутри массива
db.accounts.find( { favorites: "pretzels" })

// Использование $in и $all
// $all - массив должен содержать все указанные элементы, порядок неважен
db.accounts.find({ favorites: { $all: [ "pretzels", "beer" ]}});
// $in - Документ должен содержать один из указанных элементов
db.accounts.find({ favorites: { $in: [ "ice cream", "beer" ]}});

// Запросы с использованием dot notation
// Добавляем документ со сложной структурой
db.users.insert({ name: "richard", email: { work: "richard@mail.com",
    personal: "kreuter@example.com" }});
// Получаем документ используя dot notation, запрашиваемое поле необходимо заключать в кавычки
db.users.find({ 'email.work': "richard@mail.com" });

// Использование курсоров
var cur = db.people.find();
cur.hasNext();
cur.next();
// получить 3 документа
cur.limit(3);

// Подсчет документов
db.scores.count({ type: 'exam' });


//=//=// Обновление документов

// Первый аргумент - что найти
// Второй аргумент - на что заменить
// Предыдущая версия документа при этому будет потеряна
db.people.update({ name: 'Smith'}, { name: 'Thompson', salary : 25 });

// $set обновляет необходимые поля в документе, не теряя при этом остальне поля
// если такого поля небыло, создает его
db.people.update( { name: "Alice" }, { $set: { age: 30 }});

// $inc инкреметирует поле документа,
// если такого поля не было будет создано новое поле со значением инкремента.
db.peiple.update( {name: "Alice" }, { $inc: { age: 1 }});

// $unset удаляет поле из документа
// Значение указываемое после удаляемого аргумента не имеет значения,
// просто необходимо любое значение. В данном случае "1".
db.people.update({ name: "Jones" }, { $unset:  { profession : 1 }});

// Параметр upsert: true обновит найденный документ,
// если документы удовлетворяющие запросу отсутствуют будет создан новый документ
db.people.update({ name: "George" }, { name: "George", age: 40 }, { upsert : true });

// Множественное обновление
// { } - пустое условие, т.е. выбрать каждый элемент в коллекции
// Параметр multi: true говорит обновить все документы удовлетворяющие запросу,
// без данного параметра будет обновлен только один документ
db.people.update( { }, { $set: { title : "Dr" }}, { multi : true });


//=//=// Манипулирование массивами

// Добавляем документ, содержащий массив
db.arrays.insert({ _id: 0 , a : [1, 2, 3, 4, 5]});

// $set изменяет значение отдельного элемента массива по индексу
db.arrays.update({ _id: 0 }, { $set: { "a.2": 5 }});

// $push добавляет новый элемент к массиву
db.arrays.update({ _id: 0 }, { $push: { a: 6 }});

// $pop удаляет последний элемент массива
db.arrays.update({ _id: 0 }, { $pop: { a: 1 }});

// $pop с отрицательным аргументов удаляет первый элемент массива
db.arrays.update({ _id: 0 }, { $pop : { a : -1 }});

// $pushAll добавление нескольких элементов в массив
db.arrays.update({ _id: 0 }, { $pushAll: { a: [ 7, 8, 9 ] }});

// $pull удаляет любой элемент из массива, в данном случае удаляет число 5
db.arrays.update({ _id: 0 }, { $pull: { a: 5 }});

// $pullAll удаляет все указанные элементы из массива
db.arrays.update({ _id: 0 }, { $pullAll: { a: [5, 6, 7] }});

// $addToSet - добавляет элемент в массив только если в массиве нет данного элемента
db.arrays.update({ _id: 0 }, { $addToSet: { a: 5 }});


//=//=// Удаление данных

// remove служит для удаления данных из коллекции
db.poeple.remove( {name: "Alex" });
db.people.remove( {name: { $gt : "M" }});

// Удаление всех документов из коллекции
db.people.remove( { } );

// Удаление всей коллекции.
db.people.drop();

{{< / highlight >}}
