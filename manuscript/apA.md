# Functional-Light JavaScript
# Приложение A: Трансдьюсеры

Трансдьюсеры (transducing) — это более продвинутая техника, чем те, что мы рассматривали в книге. Она расширяет многие концепции из [Главы 9](ch9.md) об операциях со списками.

Я бы не назвал эту тему строго «Functional-Light» — скорее это бонус сверху. Я поместил её в приложение, потому что вы вполне можете пропустить это обсуждение сейчас и вернуться к нему, когда будете достаточно уверены в основных концепциях книги — и главное, отработаете их на практике!

Если честно, даже после многократного преподавания трансдьюсеров и написания этой главы я всё ещё пытаюсь полностью уложить эту технику в голове. Так что не расстраивайтесь, если она запутает вас. Добавьте это приложение в закладки и вернитесь, когда будете готовы.

Трансдьюсирование означает преобразование через свёртку.

Понимаю, это может звучать как нагромождение слов, которое запутывает больше, чем проясняет. Но давайте посмотрим, насколько мощной может быть эта техника. На самом деле я считаю её одной из лучших иллюстраций того, чего можно достичь, освоив принципы Functional-Light Programming.

Как и в остальной части книги, мой подход: сначала объяснить *почему*, затем *как*, и наконец свести это к упрощённому, воспроизводимому *что*. Это часто противоположно тому, как учат многие, но я думаю, что так вы усвоите тему глубже.

## Сначала — почему

Начнём с расширения [сценария из Главы 3](ch3.md/#user-content-shortlongenough), где мы тестируем слова на соответствие условию «достаточно короткое» и/или «достаточно длинное»:

```js
function isLongEnough(str) {
    return str.length >= 5;
}

function isShortEnough(str) {
    return str.length <= 10;
}
```

В [Главе 3 мы использовали эти предикатные функции](ch3.md/#user-content-shortlongenough) для проверки одного слова. Затем в Главе 9 мы узнали, как повторять такие проверки [с использованием операций над списками, например `filter(..)`](ch9.md/#filter):

```js
var words = [ "You", "have", "written", "something", "very",
    "interesting" ];

words
.filter( isLongEnough )
.filter( isShortEnough );
// ["written","something"]
```

Может быть не очевидно, но этот паттерн из отдельных соседних операций над списками имеет некоторые не идеальные характеристики. Когда мы работаем с одним массивом небольшого числа значений — всё нормально. Но если в массиве много значений, каждый `filter(..)`, обрабатывающий список отдельно, может несколько замедлиться.

Аналогичная проблема производительности возникает, когда наши массивы асинхронны/ленивы (aka Observable), обрабатывая значения со временем в ответ на события (см. [Главу 10](ch10.md)). В этом сценарии через поток событий проходит только одно значение за раз, поэтому обработка этого дискретного значения двумя отдельными вызовами `filter(..)` не такая уж большая проблема.

Но что не очевидно: каждый метод `filter(..)` создаёт отдельный observable. Накладные расходы на передачу значения из одного observable в другой могут существенно накапливаться. Особенно это справедливо, поскольку в таких случаях нередко обрабатываются тысячи или миллионы значений — даже небольшие накладные расходы быстро суммируются.

Другим недостатком является читаемость, особенно когда нам нужно повторять одну и ту же серию операций для нескольких списков (или Observable):

```js
zip(
    list1.filter( isLongEnough ).filter( isShortEnough ),
    list2.filter( isLongEnough ).filter( isShortEnough ),
    list3.filter( isLongEnough ).filter( isShortEnough )
)
```

Повторяемость налицо, не правда ли?

Разве не было бы лучше (и для читаемости, и для производительности), если бы мы могли объединить предикат `isLongEnough(..)` с предикатом `isShortEnough(..)`? Это можно сделать вручную:

```js
function isCorrectLength(str) {
    return isLongEnough( str ) && isShortEnough( str );
}
```

Но это не ФП-подход!

В [Главе 9 мы говорили о слиянии (fusion)](ch9.md/#fusion) — компоновке соседних функций отображения. Вспомним:

```js
words
.map(
    pipe( removeInvalidChars, upper, elide )
);
```

К сожалению, объединение соседних предикатных функций работает не так просто, как объединение соседних функций отображения. Чтобы понять почему, подумайте о «форме» предикатной функции — своеобразном академическом способе описания сигнатуры входов и выхода. Она принимает одно значение и возвращает `true` или `false`.

Если вы попробуете `isShortEnough(isLongEnough(str))`, это не сработает правильно. `isLongEnough(..)` вернёт `true`/`false`, а не строку, которую ожидает `isShortEnough(..)`. Облом.

Аналогичная проблема возникает при попытке скомпоновать две соседние функции-редьюсера. «Форма» редьюсера — это функция, принимающая два значения на входе и возвращающая одно объединённое значение. Выход редьюсера как единственное значение не подходит для входа другому редьюсеру, ожидающему два входных значения.

Более того, хелпер `reduce(..)` принимает необязательное значение `initialValue`. Иногда его можно опустить, но иногда его нужно передать. Это ещё больше усложняет компоновку, поскольку одной свёртке может понадобиться одно `initialValue`, а другой — другое. Как это возможно сделать, если мы делаем только один вызов `reduce(..)` с каким-то скомпонованным редьюсером?

Рассмотрим цепочку вроде этой:

```js
words
.map( strUppercase )
.filter( isLongEnough )
.filter( isShortEnough )
.reduce( strConcat, "" );
// "WRITTENSOMETHING"
```

Можете ли вы представить себе композицию, включающую все эти шаги: `map(strUppercase)`, `filter(isLongEnough)`, `filter(isShortEnough)`, `reduce(strConcat)`? Форма каждого оператора разная, поэтому они не будут напрямую компоноваться. Нам нужно немного изменить их форму, чтобы они подошли друг к другу.

Надеюсь, эти наблюдения проиллюстрировали, почему простая компоновка в стиле слияния не справляется с задачей. Нам нужна более мощная техника — и трансдьюсирование является таким инструментом.

## Затем — как

Давайте поговорим о том, как мы могли бы получить композицию маперов, предикатов и/или редьюсеров.

Не перегружайтесь: в своём программировании вам не придётся проходить через все эти мысленные шаги, которые мы вот-вот исследуем. Как только вы поймёте и сможете распознать проблему, которую решает трансдьюсирование, вы сможете сразу перейти к использованию утилиты `transduce(..)` из ФП-библиотеки и продолжить работу над остальным приложением!

Поехали.

### Выражение Map/Filter через Reduce

Первый трюк, который нужно выполнить, — выразить вызовы `filter(..)` и `map(..)` как вызовы `reduce(..)`. Вспомните [как мы это делали в Главе 9](ch9.md/#map-as-reduce):

```js
function strUppercase(str) { return str.toUpperCase(); }
function strConcat(str1,str2) { return str1 + str2; }

function strUppercaseReducer(list,str) {
    list.push( strUppercase( str ) );
    return list;
}

function isLongEnoughReducer(list,str) {
    if (isLongEnough( str )) list.push( str );
    return list;
}

function isShortEnoughReducer(list,str) {
    if (isShortEnough( str )) list.push( str );
    return list;
}

words
.reduce( strUppercaseReducer, [] )
.reduce( isLongEnoughReducer, [] )
.reduce( isShortEnoughReducer, [] )
.reduce( strConcat, "" );
// "WRITTENSOMETHING"
```

Это приличное улучшение. Теперь у нас четыре соседних вызова `reduce(..)` вместо смеси трёх разных методов с разными формами. Тем не менее мы всё ещё не можем просто `compose(..)` эти четыре редьюсера, поскольку они принимают два аргумента, а не один.

<a name="cheating"></a>

В [Главе 9 мы немного мошенничали](ch9.md/#user-content-reducecheating) и использовали `list.push(..)` для мутации как побочного эффекта вместо создания нового массива для конкатенации. Давайте шагнём назад и будем немного более формальными:

```js
function strUppercaseReducer(list,str) {
    return [ ...list, strUppercase( str ) ];
}

function isLongEnoughReducer(list,str) {
    if (isLongEnough( str )) return [ ...list, str ];
    return list;
}

function isShortEnoughReducer(list,str) {
    if (isShortEnough( str )) return [ ...list, str ];
    return list;
}
```

Позже мы вернёмся к вопросу, необходимо ли здесь создание нового массива (например, `[...list,str]`) для конкатенации или нет.

### Параметризация редьюсеров

Оба фильтрующих редьюсера почти идентичны, за исключением того, что они используют разную предикатную функцию. Давайте параметризуем это, чтобы получить одну утилиту, способную создавать любой фильтрующий-редьюсер:

```js
function filterReducer(predicateFn) {
    return function reducer(list,val){
        if (predicateFn( val )) return [ ...list, val ];
        return list;
    };
}

var isLongEnoughReducer = filterReducer( isLongEnough );
var isShortEnoughReducer = filterReducer( isShortEnough );
```

Сделаем то же самое для параметризации `mapperFn(..)` в утилиту, создающую любой отображающий-редьюсер:

```js
function mapReducer(mapperFn) {
    return function reducer(list,val){
        return [ ...list, mapperFn( val ) ];
    };
}

var strToUppercaseReducer = mapReducer( strUppercase );
```

Наша цепочка по-прежнему выглядит так же:

```js
words
.reduce( strUppercaseReducer, [] )
.reduce( isLongEnoughReducer, [] )
.reduce( isShortEnoughReducer, [] )
.reduce( strConcat, "" );
```

### Извлечение общей логики объединения

Посмотрите очень внимательно на предыдущие функции `mapReducer(..)` и `filterReducer(..)`. Видите ли вы общую функциональность, разделяемую ими?

Вот эта часть:

```js
return [ ...list, .. ];

// или
return list;
```

Давайте определим хелпер для этой общей логики. Но как его назвать?

```js
function WHATSITCALLED(list,val) {
    return [ ...list, val ];
}
```

Если исследовать, что делает эта функция `WHATSITCALLED(..)`: она принимает два значения (массив и другое значение) и «объединяет» их, создавая новый массив и конкатенируя значение в его конец. Без лишних изысков можно назвать это `listCombine(..)`:

```js
function listCombine(list,val) {
    return [ ...list, val ];
}
```

Теперь перепишем наши хелперы-редьюсеры с использованием `listCombine(..)`:

```js
function mapReducer(mapperFn) {
    return function reducer(list,val){
        return listCombine( list, mapperFn( val ) );
    };
}

function filterReducer(predicateFn) {
    return function reducer(list,val){
        if (predicateFn( val )) return listCombine( list, val );
        return list;
    };
}
```

Наша цепочка по-прежнему выглядит так же (повторять не будем).

### Параметризация объединения

Наша простая утилита `listCombine(..)` — лишь один из возможных способов объединить два значения. Давайте параметризуем её использование, чтобы сделать наши редьюсеры более обобщёнными:

```js
function mapReducer(mapperFn,combinerFn) {
    return function reducer(list,val){
        return combinerFn( list, mapperFn( val ) );
    };
}

function filterReducer(predicateFn,combinerFn) {
    return function reducer(list,val){
        if (predicateFn( val )) return combinerFn( list, val );
        return list;
    };
}
```

Чтобы использовать эту форму наших хелперов:

```js
var strToUppercaseReducer = mapReducer( strUppercase, listCombine );
var isLongEnoughReducer = filterReducer( isLongEnough, listCombine );
var isShortEnoughReducer = filterReducer( isShortEnough, listCombine );
```

Определение этих утилит с двумя аргументами вместо одного менее удобно для компоновки, поэтому используем подход с `curry(..)`:

```js
var curriedMapReducer =
    curry( function mapReducer(mapperFn,combinerFn){
        return function reducer(list,val){
            return combinerFn( list, mapperFn( val ) );
        };
    } );

var curriedFilterReducer =
    curry( function filterReducer(predicateFn,combinerFn){
        return function reducer(list,val){
            if (predicateFn( val )) return combinerFn( list, val );
            return list;
        };
    } );

var strToUppercaseReducer =
    curriedMapReducer( strUppercase )( listCombine );
var isLongEnoughReducer =
    curriedFilterReducer( isLongEnough )( listCombine );
var isShortEnoughReducer =
    curriedFilterReducer( isShortEnough )( listCombine );
```

Выглядит немного более многословно и, возможно, не особо полезным.

Однако это действительно необходимо для следующего шага нашего вывода. Помните: наша конечная цель — иметь возможность `compose(..)` эти редьюсеры. Мы почти у цели.

### Компоновка каррированных функций

Этот шаг — самый сложный для визуализации. Читайте медленно и внимательно.

Рассмотрим каррированные функции из предыдущего примера, но без переданной в каждую функции `listCombine(..)`:

```js
var x = curriedMapReducer( strUppercase );
var y = curriedFilterReducer( isLongEnough );
var z = curriedFilterReducer( isShortEnough );
```

Подумайте о форме всех трёх промежуточных функций: `x(..)`, `y(..)` и `z(..)`. Каждая ожидает единственную функцию объединения и создаёт с её помощью функцию-редьюсер.

Напомним: если нам нужны независимые редьюсеры из всех этих функций, мы можем:

```js
var upperReducer = x( listCombine );
var longEnoughReducer = y( listCombine );
var shortEnoughReducer = z( listCombine );
```

Но что вы получите, если вызовете `y(z)` вместо `y(listCombine)`? По сути, что происходит при передаче `z` как `combinerFn(..)` в вызов `y(..)`? Возвращаемая функция-редьюсер внутри выглядит примерно так:

```js
function reducer(list,val) {
    if (isLongEnough( val )) return z( list, val );
    return list;
}
```

Видите вызов `z(..)` внутри? Это должно выглядеть неправильно, поскольку функция `z(..)` должна получать только один аргумент (a `combinerFn(..)`), а не два (`list` и `val`). Формы не совпадают. Это не сработает.

Давайте вместо этого рассмотрим композицию `y(z(listCombine))`. Разобьём её на два отдельных шага:

```js
var shortEnoughReducer = z( listCombine );
var longAndShortEnoughReducer = y( shortEnoughReducer );
```

Мы создаём `shortEnoughReducer(..)`, затем передаём *его* как `combinerFn(..)` в `y(..)` вместо вызова `y(listCombine)`; этот новый вызов создаёт `longAndShortEnoughReducer(..)`. Перечитайте несколько раз, пока не щёлкнет.

Теперь задумайтесь: как выглядят `shortEnoughReducer(..)` и `longAndShortEnoughReducer(..)` внутри? Видите ли вы их в уме?

```js
// shortEnoughReducer, из вызова z(..):
function reducer(list,val) {
    if (isShortEnough( val )) return listCombine( list, val );
    return list;
}

// longAndShortEnoughReducer, из вызова y(..):
function reducer(list,val) {
    if (isLongEnough( val )) return shortEnoughReducer( list, val );
    return list;
}
```

Видите, как `shortEnoughReducer(..)` занял место `listCombine(..)` внутри `longAndShortEnoughReducer(..)`? Почему это работает?

Потому что **форма `reducer(..)` и форма `listCombine(..)` одинаковы.** Иными словами, редьюсер может использоваться как функция объединения для другого редьюсера — вот как они компонуются! Функция `listCombine(..)` создаёт первый редьюсер, затем *этот редьюсер* может использоваться как функция объединения для создания следующего редьюсера, и так далее.

Давайте протестируем наш `longAndShortEnoughReducer(..)` с несколькими значениями:

```js
longAndShortEnoughReducer( [], "nope" );
// []

longAndShortEnoughReducer( [], "hello" );
// ["hello"]

longAndShortEnoughReducer( [], "hello world" );
// []
```

Утилита `longAndShortEnoughReducer(..)` фильтрует как значения, недостаточно длинные, так и значения, недостаточно короткие, делая оба фильтра в одном шаге. Это скомпонованный редьюсер!

Дайте этому ещё немного уложиться. До сих пор это взрывает мне мозг.

Теперь введём `x(..)` (производитель редьюсера для перевода в верхний регистр) в композицию:

```js
var longAndShortEnoughReducer = y( z( listCombine) );
var upperLongAndShortEnoughReducer = x( longAndShortEnoughReducer );
```

Как следует из имени `upperLongAndShortEnoughReducer(..)`, он выполняет все три шага сразу — одно отображение и два фильтра! Внутри это выглядит примерно так:

```js
// upperLongAndShortEnoughReducer:
function reducer(list,val) {
    return longAndShortEnoughReducer( list, strUppercase( val ) );
}
```

Строка `val` поступает внутрь, переводится в верхний регистр через `strUppercase(..)` и затем передаётся в `longAndShortEnoughReducer(..)`. *Та* функция условно добавляет эту строку в верхнем регистре в `list` только если она одновременно достаточно длинная и достаточно короткая. Иначе `list` остаётся без изменений.

Моему мозгу понадобились недели, чтобы полностью осознать последствия этого жонглирования. Поэтому не беспокойтесь, если вам нужно остановиться здесь и перечитать несколько (десятков!) раз. Не торопитесь.

Теперь проверим:

```js
upperLongAndShortEnoughReducer( [], "nope" );
// []

upperLongAndShortEnoughReducer( [], "hello" );
// ["HELLO"]

upperLongAndShortEnoughReducer( [], "hello world" );
// []
```

Этот редьюсер является композицией отображения и обоих фильтров! Это потрясающе!

Подведём итог того, где мы находимся:

```js
var x = curriedMapReducer( strUppercase );
var y = curriedFilterReducer( isLongEnough );
var z = curriedFilterReducer( isShortEnough );

var upperLongAndShortEnoughReducer = x( y( z( listCombine ) ) );

words.reduce( upperLongAndShortEnoughReducer, [] );
// ["WRITTEN","SOMETHING"]
```

Довольно круто. Но сделаем ещё лучше.

`x(y(z( .. )))` — это композиция. Пропустим промежуточные имена переменных `x` / `y` / `z` и выразим эту композицию напрямую:

```js
var composition = compose(
    curriedMapReducer( strUppercase ),
    curriedFilterReducer( isLongEnough ),
    curriedFilterReducer( isShortEnough )
);

var upperLongAndShortEnoughReducer = composition( listCombine );

words.reduce( upperLongAndShortEnoughReducer, [] );
// ["WRITTEN","SOMETHING"]
```

Подумайте о потоке «данных» в этой скомпонованной функции:

1. `listCombine(..)` поступает как функция объединения для создания фильтрующего-редьюсера для `isShortEnough(..)`.
2. *Эта* получившаяся функция-редьюсер затем поступает как функция объединения для создания фильтрующего-редьюсера для `isLongEnough(..)`.
3. Наконец, *эта* получившаяся функция-редьюсер поступает как функция объединения для создания отображающего-редьюсера для `strUppercase(..)`.

В предыдущем фрагменте `composition(..)` — это скомпонованная функция, ожидающая функцию объединения для создания редьюсера; `composition(..)` здесь имеет особое название: **трансдьюсер**. Передача функции объединения в трансдьюсер создаёт скомпонованный редьюсер:

```js
var transducer = compose(
    curriedMapReducer( strUppercase ),
    curriedFilterReducer( isLongEnough ),
    curriedFilterReducer( isShortEnough )
);

words
.reduce( transducer( listCombine ), [] );
// ["WRITTEN","SOMETHING"]
```

**Примечание:** Следует заметить кое-что о порядке `compose(..)` в предыдущих двух фрагментах, что может смущать. Напомним, что в нашей исходной цепочке мы применяем `map(strUppercase)`, затем `filter(isLongEnough)` и наконец `filter(isShortEnough)` — операции выполняются именно в таком порядке. Но в [Главе 4](ch4.md/#user-content-generalcompose) мы узнали, что `compose(..)` обычно запускает свои функции в обратном порядке перечисления. Почему же нам *здесь* не нужно переворачивать порядок для получения того же желаемого результата? Абстракция `combinerFn(..)` из каждого редьюсера скрыто обращает эффективный порядок применения операций. Поэтому, как ни контринтуитивно это звучит, при компоновке трансдьюсера вы на самом деле хотите перечислять их в желаемом порядке выполнения!

#### Объединение списков: чистое vs. нечистое

Небольшое отступление: пересмотрим нашу реализацию функции объединения `listCombine(..)`:

```js
function listCombine(list,val) {
    return [ ...list, val ];
}
```

Хотя этот подход чист, он имеет негативные последствия для производительности: на каждом шаге свёртки мы создаём целый новый массив для добавления значения, фактически выбрасывая предыдущий. Это огромное количество создаваемых и выбрасываемых массивов, что плохо как для процессора, так и для сборки мусора.

В противоположность этому посмотрите снова на более производительную, но нечистую версию:

```js
function listCombine(list,val) {
    list.push( val );
    return list;
}
```

Рассматривая `listCombine(..)` в изоляции, нет сомнений, что она нечиста — и обычно этого хочется избегать. Однако есть более широкий контекст, который нужно учитывать.

`listCombine(..)` — функция, с которой мы вообще не взаимодействуем напрямую. Мы не используем её нигде в программе непосредственно; вместо этого мы позволяем процессу трансдьюсирования использовать её.

В [Главе 5](ch5.md) мы утверждали, что наша цель в сокращении побочных эффектов и определении чистых функций — только в том, чтобы предоставлять чистые функции на уровне API функций, которые мы будем использовать во всей программе. Мы отмечали, что внутри чистой функции она может схитрить ради производительности сколько угодно, если только не нарушает внешний договор о чистоте.

`listCombine(..)` является скорее внутренней деталью реализации трансдьюсирования — и на самом деле она нередко будет предоставлена вам библиотекой трансдьюсирования! — а не методом верхнего уровня, с которым вы обычно взаимодействуете в программе.

Итог: я думаю, что использовать производительно-оптимальную нечистую версию `listCombine(..)` вполне приемлемо и даже рекомендуется. Просто убедитесь, что документируете её нечистоту комментарием к коду!

### Альтернативное объединение

Итак, вот что мы получили с трансдьюсированием:

```js
words
.reduce( transducer( listCombine ), [] )
.reduce( strConcat, "" );
// WRITTENSOMETHING
```

Неплохо, но у нас есть ещё один финальный трюк в рукаве. И откровенно говоря, я думаю, именно эта часть делает все мысленные усилия, которые вы потратили до сих пор, действительно стоящими.

Можем ли мы каким-то образом «скомпоновать» эти два вызова `reduce(..)` в один? К сожалению, мы не можем просто добавить `strConcat(..)` в вызов `compose(..)`; поскольку это редьюсер, а не функция, ожидающая объединения, его форма не подходит для композиции.

Но давайте посмотрим на эти две функции рядом:

```js
function strConcat(str1,str2) { return str1 + str2; }

function listCombine(list,val) { list.push( val ); return list; }
```

Если прищурить глаза, можно почти увидеть, что эти две функции взаимозаменяемы. Они работают с разными типами данных, но концептуально делают одно и то же: объединяют два значения в одно.

Иными словами, `strConcat(..)` является функцией объединения!

Это означает, что мы можем использовать *её* вместо `listCombine(..)`, если наша конечная цель — конкатенация строк, а не список:

```js
words.reduce( transducer( strConcat ), "" );
// WRITTENSOMETHING
```

Бум! Вот оно — трансдьюсирование. Я не брошу микрофон, просто аккуратно положу его...

## Наконец — что

Сделайте глубокий вдох. Это было непросто.

Очистив голову на минуту, переключим внимание обратно на использование трансдьюсирования в наших приложениях, не проходя через все эти мысленные прыжки для понимания того, как это работает.

Вспомним хелперы, которые мы определили ранее; переименуем их для ясности:

```js
var transduceMap =
    curry( function mapReducer(mapperFn,combinerFn){
        return function reducer(list,v){
            return combinerFn( list, mapperFn( v ) );
        };
    } );

var transduceFilter =
    curry( function filterReducer(predicateFn,combinerFn){
        return function reducer(list,v){
            if (predicateFn( v )) return combinerFn( list, v );
            return list;
        };
    } );
```

Также вспомним, как мы их используем:

```js
var transducer = compose(
    transduceMap( strUppercase ),
    transduceFilter( isLongEnough ),
    transduceFilter( isShortEnough )
);
```

`transducer(..)` всё ещё нужна функция объединения (как `listCombine(..)` или `strConcat(..)`), переданная в неё для создания функции-редьюсера для трансдьюсирования, которую затем можно использовать (вместе с начальным значением) в `reduce(..)`.

Но чтобы выразить все эти шаги трансдьюсирования более декларативно, создадим утилиту `transduce(..)`, которая выполняет эти шаги за нас:

```js
function transduce(transducer,combinerFn,initialValue,list) {
    var reducer = transducer( combinerFn );
    return list.reduce( reducer, initialValue );
}
```

Вот наш текущий пример, приведённый в порядок:

```js
var transducer = compose(
    transduceMap( strUppercase ),
    transduceFilter( isLongEnough ),
    transduceFilter( isShortEnough )
);

transduce( transducer, listCombine, [], words );
// ["WRITTEN","SOMETHING"]

transduce( transducer, strConcat, "", words );
// WRITTENSOMETHING
```

Неплохо, правда!? Видите, как функции `listCombine(..)` и `strConcat(..)` используются взаимозаменяемо как функции объединения?

### Transducers.js

Наконец, проиллюстрируем наш текущий пример с использованием библиотеки [`transducers-js`](https://github.com/cognitect-labs/transducers-js):

```js
var transformer = transducers.comp(
    transducers.map( strUppercase ),
    transducers.filter( isLongEnough ),
    transducers.filter( isShortEnough )
);

transducers.transduce( transformer, listCombine, [], words );
// ["WRITTEN","SOMETHING"]

transducers.transduce( transformer, strConcat, "", words );
// WRITTENSOMETHING
```

Выглядит почти идентично тому, что выше.

**Примечание:** Предыдущий фрагмент использует `transformers.comp(..)`, поскольку библиотека его предоставляет, но в данном случае наш [`compose(..)` из Главы 4](ch4.md/#user-content-generalcompose) дал бы тот же результат. Иными словами, композиция сама по себе не является чувствительной к трансдьюсированию операцией.

Скомпонованная функция в этом фрагменте называется `transformer` вместо `transducer`. Это потому, что при вызове `transformer( listCombine )` (или `transformer( strConcat )`) мы не получим простую функцию-редьюсер-для-трансдьюсирования как раньше.

`transducers.map(..)` и `transducers.filter(..)` — специальные хелперы, адаптирующие обычные предикатные или маппер-функции в функции, создающие специальный объект-трансформер (с функцией трансдьюсера внутри); библиотека использует эти объекты-трансформеры для трансдьюсирования. Дополнительные возможности этой абстракции объекта-трансформера выходят за рамки того, что мы рассмотрим здесь, поэтому обратитесь к документации библиотеки для получения дополнительной информации.

Поскольку вызов `transformer(..)` создаёт объект-трансформер, а не типичную бинарную функцию-редьюсер-для-трансдьюсирования, библиотека также предоставляет `toFn(..)` для адаптации объекта-трансформера для использования нативным `reduce(..)` массива:

```js
words.reduce(
    transducers.toFn( transformer, strConcat ),
    ""
);
// WRITTENSOMETHING
```

`into(..)` — ещё один предоставляемый хелпер, автоматически выбирающий функцию объединения по умолчанию на основе типа указанного пустого/начального значения:

```js
transducers.into( [], transformer, words );
// ["WRITTEN","SOMETHING"]

transducers.into( "", transformer, words );
// WRITTENSOMETHING
```

При указании пустого массива `[]` вызываемый внутри `transduce(..)` использует реализацию по умолчанию, аналогичную нашему хелперу `listCombine(..)`. Но при указании пустой строки `""` используется нечто похожее на наш `strConcat(..)`. Круто!

Как видите, библиотека `transducers-js` делает трансдьюсирование достаточно простым. Мы можем очень эффективно использовать мощь этой техники, не углубляясь в определение всех тех промежуточных утилит для создания трансдьюсеров самостоятельно.

## Резюме

Трансдьюсировать означает преобразовывать через свёртку. Точнее, трансдьюсер — это компонуемый редьюсер.

Мы используем трансдьюсирование для компоновки соседних операций `map(..)`, `filter(..)` и `reduce(..)`. Достигается это сначала выражением `map(..)` и `filter(..)` через `reduce(..)`, а затем абстрагированием общей операции объединения для создания унарных функций, производящих редьюсеры, которые легко компонуются.

Трансдьюсирование прежде всего улучшает производительность, что особенно заметно при использовании с Observable.

Но в более широком смысле трансдьюсирование — это способ выразить более декларативную композицию функций, которые иначе не могли бы напрямую компоноваться. Результат при правильном использовании, как и все другие техники этой книги, — более ясный, более читаемый код! Единственный вызов `reduce(..)` с трансдьюсером проще осмыслить, чем прослеживать несколько вызовов `reduce(..)`.
