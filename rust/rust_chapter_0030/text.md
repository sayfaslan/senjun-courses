# Глава 3. Условия и циклы

Условия и циклы являются базовыми конструкциями большинства языков программирования. В Rust есть условное выражение `if` и три вида циклов: `loop`, `while` и `for`. В Rust блок кода является выражением и может быть присвоен переменной. Условия и циклы также **являются выражениями.** 

Пример ниже демонстрирует эту особенность языка. В нем вычисляется первое число Фибоначчи, превышающее 100:

```rust {.example_for_playground .example_for_playground_001}
let mut cur = 1;
let mut next = 2;
let over100 = loop {
    if next > 100 {
        break next;
    }
    let new = cur + next;
    cur = next;
    next = new;
};
println!("First Fibonacci number over 100: {over100}");
```

Рассчитанное число Фибоначчи возвращается из выражения `loop` с помощью оператора `break` и сохраняется в переменной `over100`. В результате будет напечатано:

```
First Fibonacci number over 100: 144
```


## Выражение if

Синтаксис выражения `if` cхож c синтаксисом условий в других языках программирования:

```rust
if <condition 1> {
    <branch expression 1>
} [else if <condition 2> {
    <branch expression 2>
}]
...
[else if <condition N> {
    <branch expression N>
}] [else {
    <branch expression N + 1>
}]
```

Если значение выражения `condition 1` истинно, то выполнится первая ветка `branch expression 1`. В противном случае программа перейдет к проверке выражения `condition 2`. Если второе условное выражение ложно, то программа перейдет к проверке следующего. И так до конца, пока не закончатся все ветки выражения `if`.

Последней веткой может быть блок `else`, который выполнится, если условные выражения в предыдущих ветках окажутся ложными. Все блоки выражения `if`, кроме первого, необязательны и могут быть опущены.


Поскольку `if` является выражением, то все его ветки должны возвращать значения одного и того же типа. Блок каждой ветки может состоять из нескольких выражений, последнее выражение в блоке является результирующим. Однако если последнее выражение в блоке оканчивается точкой с запятой `;`, то результирующим значением будет пустой кортеж `()`.

Функция `min_divider()` для переданного параметра вычисляет минимальный делитель меньше 10:

```rust {.example_for_playground .example_for_playground_002}
fn min_divider(value: u32) -> u32 {
    if value % 2 == 0 {
        2
    } else if value % 3 == 0 {
        3
    } else if value % 5 == 0 {
        5
    } else if value % 7 == 0 {
        7
    } else {
        1
    }
}
```

Для входных значений `6, 49, 9, 35, 11` функция вернет: `2, 7, 3, 5, 1`. В функции `min_divider()` отсутствует явное указание возвращаемого значения. По правилам языка ключевое слово `return` можно опустить, а значение последнего выражения будет результатом выполнения функции. В `min_divider()` это условное выражение, определяющее делитель.

Нужно реализовать функцию `calc_season_number()`, вычисляющую порядковый номер времени года (подробнее о функциях будет рассказано в следующей главе). {.task_text}
  - _"Spring"_ ==> 1
  - _"Summer"_ ==> 2
  - _"Autumn"_ ==> 3
  - _"Winter"_ ==> 4
  - 0 (ноль), если время года не удалось распознать
{.task_text}
  
На вход функция получает константную ссылку на символьный литерал `season_name`, а возвращает 32-битное беззнаковое целое: {.task_text}

```rust {.task_source #rust_chapter_0030_task_0010}
fn calc_season_number(season_name: &str) -> u32 {
    // добавьте реализацию функции
}
```
Необходимо использовать выражение `if / else if / else`. В каждой ветке условия нужно сравнить `season_name` с одним названием времени года. При выполнении условия ветки нужно возвращать соответствующий номер времени года. Условное выражение следует завершить веткой `else`, которая будет возвращать 0. {.task_hint}
```rust {.task_answer}
fn calc_season_number(season_name: &str) -> u32 {
    if season_name == "Spring" {
        1
    } else if season_name == "Summer" {
        2
    } else if season_name == "Autumn" {
        3
    } else if season_name == "Winter" {
        4
    } else {
        0
    }
}
```


Важный момент: в условных выражениях необходимо использовать логический тип. Rust — строго типизированный язык и не производит неявных преобразований в логический тип из какого-то другого.

```rust {.example_for_playground .example_for_playground_003}
let x = 11;
if x % 2 {
    println!("The number {} is odd.", x);
}
```

Данный пример не скомпилируется, так как в условии ожидается тип `bool`, а не целочисленный:

```
error[E0308]: mismatched types
 --> src\main.rs:3:8
  |
3 |     if x % 2 {
  |        ^^^^^ expected `bool`, found integer
```

Для исправления ошибки достаточно добавить проверку на неравенство нулю `x % 2 != 0`, и код станет работать как ожидается.


В условных выражениях можно использовать операции сравнения и логические операции. К операциям сравнения относятся:
  - `==` — равно, `135 == 135`
  - `!=` — не равно, `135 != 531`
  - `>` — больше, `24.5 > 24.0`
  - `<` — меньше, `24.5 < 24.6`
  - `>=` — больше или равно, `100 >= 99` и `100 >= 100`
  - `<=` — меньше или равно, `-100 <= -99` и `-100 <= -100`

Логические операции это: отрицание `!`, логическое _«И»_ (конъюнкция) `&&`, логическое _«ИЛИ»_ (дизъюнкция) `||`. Операции `&&` и `||` называют ленивыми, так как правый операнд вычисляется только тогда, когда значения левого операнда недостаточно для определения результата выражения:
  - `&&` — вычисляет свой правый операнд только тогда, когда левый операнд принимает значение `true`,
  - `||` — вычисляет свой правый операнд только тогда, когда левый операнд принимает значение `false`. {#block-logical-and-comparison-operations}


Так как `if` является выражением, то можно его использовать для инициализации переменных:

```rust
let <variable name> = if <condition> { <expression on true> } else { <expression on false> };
```

Такая запись напоминает тернарный оператор из других языков программирования, но обладает более широкими возможностями. Допускается использование блоков `else if` и составных выражений, например:

```rust {.example_for_playground .example_for_playground_004}
let k = 12;
let kaboom = if k % 2 == 0 {
    if k % 3 == 0 {
        "kaboom"
    } else {
        "ka"
    }
} else if k % 3 == 0 {
    "boom"
} else {
    "nope"
};
println!("{k} - {kaboom}");
```

Для четных чисел данное выражение `if` будет возвращать _"ka"_, для чисел кратных 3 — _"boom"_, для четных и кратных 3 — _"kaboom"_, для всех остальных чисел — _"nope"_. Поскольку 12 кратно 2 и 3, то результатом выражения будет _"12 — kaboom"_.

Обратите внимание, что после значения, которое может быть результатом выражения `if`, нет точки с запятой `;`. Именно ее отсутствие указывает, что значение является результатом выражения в данной ветке условия.


Необходимо написать функцию `max()`, возвращающую максимальное значение из двух заданных `first` и `second`. {.task_text}

```rust {.task_source #rust_chapter_0030_task_0020}
fn max(first: i64, second: i64) -> i64 {
    // добавьте реализацию функции
}
```
Необходимо сравнить `first` с `second` и вернуть большее в соответствующей ветке условия. {.task_hint}
```rust {.task_answer}
fn max(first: i64, second: i64) -> i64 {
    if first > second {
        first
    } else {
        second
    }
}
```


В стандартной библиотеке уже имеется обобщенная версия функции `max()`, пригодная для разных типов данных: `std::cmp::max<T>(v1: T, v2: T) -> T`. Поэтому в продуктовой разработке реализовывать такую функцию самостоятельно не требуется.


## Циклы

В языке Rust есть три вида циклов: `for`, `while` и `loop`. Любой цикл можно прервать, использовав ключевое слово `break`, либо продолжить итерирование, пропустив какую-то часть тела цикла с помощью ключевого слова `continue`. Ключевое слово `return` также прервет выполнение цикла и функции, в которой этот цикл находится.


### Выражение for

Выражение `for` используется для последовательного перебора элементов коллекции. Синтаксис `for`:

```rust
for <variable name> in <sequence of values> {
    [body]
}
```

В этом примере последовательно печатаются все элементы массива `grade_array`:

```rust {.example_for_playground .example_for_playground_005}
let grade_array = ["trainee", "junior", "middle", "senior"];

for grade in grade_array {
    println!("{grade}");
}
```

Ожидаемый вывод:

```
trainee
junior
middle
senior
```


Цикл `for` также позволяет итерироваться по заданному диапазону чисел, например, с 0 до 9 включительно:

```rust {.example_for_playground .example_for_playground_006}
for num in 0..10 {
    print!("{num} ");
}
```

Будет напечатано:

```
0 1 2 3 4 5 6 7 8 9 
```

В данном примере функция `sum_positive()` вычисляет сумму всех положительных элементов заданного массива:

```rust {.example_for_playground .example_for_playground_007}
fn sum_positive(numbers: &[i32]) -> usize {
    let mut sum: usize = 0;
    for num in numbers {
        sum += if *num > 0 { *num as usize } else { 0 };
    }
    return sum;
}
```

Стоит обратить внимание, что переменная `num`, используемая в итерации по массиву, является ссылкой `&i32`, а не значением типа `i32`. Поэтому для получения значения текущего элемента применяется оператор разыменования ссылки `*`.

При вычислении суммы происходит преобразование типа `i32` в `usize` с помощью выражения `*num as usize`. Это необходимо, так как Rust строго типизированный язык и не позволяет в арифметических операциях использовать разные целые типы.


Требуется реализовать функцию `count()`, возвращающую количество искомых `desired` элементов в массиве `numbers`. Если в массиве не найдено ни одного указанного числа, то возвращается 0. {.task_text}
```rust {.task_source #rust_chapter_0030_task_0030}

fn count(numbers: &[i32], desired: i32) -> usize {
    // добавьте реализацию функции
}
```
Необходимо объявить счетчик, проинициализировав его 0. В цикле `for` обойти все элементы массива `numbers`. Увеличить значение счетчика на 1 для каждого элемента, равного `desired`. Для возврата значения из функции достаточно на последней строке написать название переменной счетчика без точки с запятой. {.task_hint}
```rust {.task_answer}
fn count(numbers: &[i32], desired: i32) -> usize {
    let mut found: usize = 0;
    for num in numbers {
        found += if *num == desired { 1 } else { 0 }
    }
    found
}
```


### Выражение while

Выражение `while` выполняется до тех пор, пока его условие истинно. Синтаксис `while`:

```rust
while <condition> {
    [body]
}
```

Цикл `while` полезен в тех случаях, когда заранее неизвестно число итераций. В этом примере в заданном наборе чисел находятся все монотонно возрастающие последовательности. Для найденных последовательностей печатается диапазон и содержимое:

```rust {.example_for_playground .example_for_playground_008}
fn find_end(sequence: &[u64], start: usize) -> usize {
    let mut pos = start;
    let len = sequence.len();
    while pos + 1 < len && sequence[pos] < sequence[pos + 1] {
        pos += 1;
    }
    pos + 1
}

fn main() {
    let seq = [6, 1, 2, 4, 3, 5, 7, 9, 11, 8, 16];
    println!("initial: {:?}", seq);
    let mut pos = 0;
    let mut end = find_end(&seq, pos);

    while pos < seq.len() {
        let slice = &seq[pos..end];
        println!("{}..{}: {:?}", pos, end, slice);
        pos = end;
        end = find_end(&seq, pos);
    }
}
```

Функция `find_end()` вычисляет конец монотонно возрастающей последовательности, начиная с позиции `start`. В строке `let slice = &seq[pos..end]` формируется срез (слайс) с `pos` до `end-1` включительно. Для получения длины среза используется метод `len()`. Подробнее о срезах будет рассказано в следующих главах.

Тип переменных `pos`, `end` и `len` не объявлен явно, он вычисляется во время компиляции и определяется как `usize`.

Ожидаемый вывод:

```
initial: [6, 1, 2, 4, 3, 5, 7, 9, 11, 8, 16]
0..1: [6]
1..4: [1, 2, 4]
4..9: [3, 5, 7, 9, 11]
9..11: [8, 16]
```


Требуется реализовать функцию `validate()`, проверяющую корректность входящего массива байт. Массив байт содержит один или нескольких пакетов, последовательно расположенных друг за другом. Каждый пакет состоит из трех частей: типа, размера и блока данных. Тип пакета имеет длину 1 байт и может принимать значения от 1 до 15. Размер данных имеет длину 1 байт и может принимать любое допустимое значение для `u8` (0..255). Количество байт в блоке данных должно быть равным указанному размеру. {.task_text}

Функция `validate()` проверяет, что каждый пакет, содержащийся во входном массиве, корректен. Если найден некорректный пакет или входной массив пустой, то функция возвращает `false`. Если все пакеты массива корректны, то `validate()` возвращает `true`. {.task_text}

Пример 1. Входной массив `[7, 4, 10, 20, 30, 40]` содержит пакет типа `7`, размер блока данных — `4`, блок данных — `[10, 20, 30, 40]`. Для этого массива функция `validate()` должна вернуть `true`. {.task_text}

Пример 2. Входной массив `[2, 1, 100, 5, 8, 11, 10, 13]` содержит два пакета. Первый пакет имеет тип `2`, размер блока данных — `1`, блок данных — `[100]`. Второй пакет имеет тип `5`, размер блока данных — `8` и блок данных — `[11, 10, 13]`. Данные второго пакета неполные, 3 байта вместо ожидаемых 8, поэтому `validate()` должна вернуть `false`. {.task_text}

```rust {.task_source #rust_chapter_0030_task_0040}
fn validate(data: &[u8]) -> bool {
    // добавьте реализацию функции
}
```
Для обхода массива `data` удобнее использовать цикл `while`. На каждой итерации цикла требуется проверить корректность текущего пакета. Сначала нужно убедится, что пакет имеет длину не меньше 2 байт. Потом проверить, что байт с типом пакета имеет корректное значение. В конце итерации следует проверить, что количество оставшихся элементов в массиве не меньше размера текущего пакета. Вычислить начало следующего пакета можно с помощью выражения `data[pos + 1] as usize + 2`. Где `pos` — начало текущего пакета, `data[pos + 1]` — размер данных пакета, а `data[pos + 1] as usize` — выражение преобразования из `u8` в `usize`. {.task_hint}
```rust {.task_answer}
fn validate(data: &[u8]) -> bool {
    let mut pos = 0;
    let end = data.len();

    while pos < end {
        if pos + 2 > end {
            return false;
        }

        let data_type: u8 = data[pos];
        if data_type < 1 || data_type > 15 {
            return false;
        }
        pos += 1;

        let data_size = data[pos] as usize;
        pos += data_size + 1;
        if pos > end {
            return false;
        }
    }

    return end != 0;
}
```


### Выражение loop

Выражение `loop` выполняется до тех пор, пока не встретится оператор прерывания цикла: `break` или `return`. Диспетчер сообщений (event loop), воркер потока — примеры, в которых `loop` будет полезен. Синтаксис:

```rust
loop {
    [body]
}
```

Эта небольшая программа читает из консоли ввод пользователя и выводит на консоль прочитанное. Цикл повторяется до тех пор, пока вo введенной строке не встретится слово _"exit"_ или _"quit"_:

```rust
use std::io;

fn main() -> io::Result<()> {

    loop {
        let mut input = String::new();
        let stdin = io::stdin();
        stdin.read_line(&mut input)?;

        if input.contains("exit") || input.contains("quit") {
            break;
        }
        print!("You typed: {}", input);
    }

    Ok(())
}
```

В этом примере для работы с консолью используется модуль `std::io`. Функция `main()` возвращает пустой кортеж либо ошибку. В строке `stdin.read_line(&mut input)?` оператор `?` распаковывает (unwrap) возвращаемое значение, в случае ошибки завершает работу функции, передавая ошибку вызывающей функции.

Выражение `Ok(())` создает вариант перечисления `io::Result<()>` с пустым кортежем в качестве значения. Так как это последнее выражение в функции, то оно является возвращаемым значением.

Модуль — это логически связанный набор: функций, структур, типажей и других модулей. Модули составляют крейты. Так модуль [std::io](https://doc.rust-lang.org/std/io/index.html) является частью крейта [std](https://doc.rust-lang.org/std/index.html) и содержит внутри себя модуль [std::io::prelude](https://doc.rust-lang.org/std/io/prelude/index.html).

О крейтах, модулях, алгебраических типах и кортежах будет рассказано в других главах.


## Заключение

- В Rust есть условное выражение `if` и три вида циклов: `loop`, `while` и `for`.
- Условия и циклы являются выражениями и могут быть присвоены переменной.
- Условное выражение `if` может иметь более одной ветки `else if` и в конце одну ветку `else`.
- В условных выражениях можно использовать только логический тип.
- Rust имеет общепринятый набор операций сравнения: `==`, `!=`, `>`, `<`, `>=`, `<=`. И логических операций: `!`, `||`, `&&`.
- Логические операции `&&` и `||` являются ленивыми и вычисляются только тогда, когда значения левого операнда недостаточно.
- Цикл `for` используется для последовательного перебора элементов коллекции.
- Цикл `while` требуется в тех случаях, когда заранее неизвестно число итераций.
- Цикл `loop` выполняется, пока не встретится `break` или `return`. Он полезен для организации бесконечных циклов.
