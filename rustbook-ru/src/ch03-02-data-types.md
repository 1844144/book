## Типы данных

Любое значение в Rust является определенным *типом данных (data type)*, которое говорит какие данные указаны, так что Rust знает как с ними работать. Рассмотрим два подмножества тип данных: скалярные (простые) и составные (сложные).

Keep in mind that Rust is a *statically typed* language, which means that it
must know the types of all variables at compile time. The compiler can usually
infer what type we want to use based on the value and how we use it. In cases
when many types are possible, such as when we converted a `String` to a numeric
type using `parse` in the [“Comparing the Guess to the Secret
Number”](ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number)<comment> section in
Chapter 2, we must add a type annotation, like this:</comment>

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

If we don’t add the type annotation here, Rust will display the following
error, which means the compiler needs more information from us to know which
type we want to use:

```text
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^
  |         |
  |         cannot infer type for `_`
  |         consider giving `guess` a type
```

Вы увидите различные аннотации типов для разных типов данных.

### Скалярные типы данных

A *scalar* type represents a single value. Rust has four primary scalar types:
integers, floating-point numbers, Booleans, and characters. You may recognize
these from other programming languages. Let’s jump into how they work in Rust.

#### Целые числа

An *integer* is a number without a fractional component. We used one integer
type in Chapter 2, the `u32` type. This type declaration indicates that the
value it’s associated with should be an unsigned integer (signed integer types
start with `i`, instead of `u`) that takes up 32 bits of space. Table 3-1 shows
the built-in integer types in Rust. Each variant in the Signed and Unsigned
columns (for example, `i16`) can be used to declare the type of an integer
value.

<span class="caption">Таблица 3-1: Целые типы Rust</span>

Размер | Знаковый | Беззнаковый
--- | --- | ---
8-bit | `i8` | `u8`
16-bit | `i16` | `u16`
32-bit | `i32` | `u32`
64-bit | `i64` | `u64`
128-bit | `i128` | `u128`
arch | `isize` | `usize`

Each variant can be either signed or unsigned and has an explicit size.
*Signed* and *unsigned* refer to whether it’s possible for the number to be
negative or positive—in other words, whether the number needs to have a sign
with it (signed) or whether it will only ever be positive and can therefore be
represented without a sign (unsigned). It’s like writing numbers on paper: when
the sign matters, a number is shown with a plus sign or a minus sign; however,
when it’s safe to assume the number is positive, it’s shown with no sign.
Signed numbers are stored using [two’s complement](https://en.wikipedia.org/wiki/Two%27s_complement) representation.

Each signed variant can store numbers from -(2<sup>n - 1</sup>) to 2<sup>n -
1</sup> - 1 inclusive, where *n* is the number of bits that variant uses. So an
`i8` can store numbers from -(2<sup>7</sup>) to 2<sup>7</sup> - 1, which equals
-128 to 127. Unsigned variants can store numbers from 0 to 2<sup>n</sup> - 1,
so a `u8` can store numbers from 0 to 2<sup>8</sup> - 1, which equals 0 to 255.

Дополнительные типы, такие как `isize` и `usize` зависят от типа компьютерной системы где работает ваша программа. Они имеют 64 бит, если вы на 64-битной архитектуре и 32 бита, если вы на 32-битной.

Можно писать целые литералы в любой форме из таблицы 3-2. Заметим, что все числовые литералы кроме байтового, позволяют использовать суффиксы, такие как `57u8` и  `_` в качестве визуального разделителя, например `1_000`.

<span class="caption">Таблица 3-2: Целые литералы в Rust</span>

Числовые литералы | Пример
--- | ---
Decimal | `98_222`
Hex | `0xff`
Octal | `0o77`
Binary | `0b1111_0000`
Byte (`u8` only) | `b'A'`

Как узнать, какой литерал необходимо использовать? Если вы не уверены, то варианты по умолчанию в Rust являются хорошим выбором. Типом по умолчанию для целых является `i32`. Данный типа в общем случае самый быстрый даже на 64-битных системах. Основной ситуацией для использования `isize` или `usize` является использование для индексирования некоторой коллекции.

> ##### Переполнение целого (Overflow)
> Например у нас есть переменная типа `u8`. Она может сохранять значения между 0 и 255. Если попытаться изменить значение вне данного диапазона, например в 256, то произойдет *ошибка переполнения (integer overflow)*. В Rust есть несколько интересных правил включая следующее поведение. При компиляции кода в режиме отладки (debug mode), компилятор Rust включает проверки на переполнение целого, которые приводят к *панике (panic)* во время выполнения, если такое случится. В Rust термин паниковать означает, что программа сразу завершается с ошибкой. Мы обсудим "паники" более детально разделе [“Не восстанавливаемые ошибки с помощью `panic!`”](ch09-01-unrecoverable-errors-with-panic.html)<comment> главы 9.</comment>
> При компиляции кода в финальную версию (release mode) при помощи флага `--release`, Rust *НЕ* включает проверки переполнения целого, приводящего к панике. Вместо этого в случае переполнения, Rust выполняет *"обертывание" к дополнению до двух (two’s complement wrapping)*. Если кратко, то значения больше чем максимальное значение, которое может хранить тип, "оборачивается" до минимальных значений данного типа. Для типа  `u8`, число 256 превращается в 0, 257 становится 1 и так далее. Программа не будет "паниковать", но переменная получит значение, которого  вы возможно не ожидали. Полагаться на поведение "обертывания целочисленного переполнения" считается ошибкой. Если нужно обернуть явно, то можно использовать тип из стандартной библиотеки [`Wrapping`](../std/num/struct.Wrapping.html).

#### Числа с плавающей запятой

В Rust есть два примитивных типа *чисел с плавающей точкой  (floating-point numbers)*, которые являются числами с десятичными точками. Числа с плавающей точкой в Rust это `f32` и `f64`, имеющие размер 32 бита и 64 бита соответственно. Тип принятый по умолчанию это `f64`, потому что все современные ЦПУ имеют для него приблизительно одинаковую скорость как для `f32`, но с большей точностью.

Пример для чисел с плавающей точкой в действии:

<span class="filename">Filename: src/main.rs</span>

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

Числа с плавающей точкой представлены согласно стандарту IEEE-754. Тип `f32` является числом с плавающей точкой одинарной точности, а `f64` имеет двойную точность.

#### Чиcловые операции

Rust поддерживает базовые математические операции ожидаемые для всех числовых типов: сложение, вычитание, умножение, деление и деление с остатком. Следующий код показывает использование каждой операции с помощью выражения `let`:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    // addition (сложение)
    let sum = 5 + 10;

    // subtraction (вычитание)
    let difference = 95.5 - 4.3;

    // multiplication (умножение)
    let product = 4 * 30;

    // division (деление)
    let quotient = 56.7 / 32.2;

    // remainder (деление с остатком)
    let remainder = 43 % 5;
}
```

Каждое из этих выражений использует математические операции и вычисляет значение, которое присваивается переменной. Приложение 2 содержит список всех математических операций языка Rust.

#### Логический тип данных

В большинстве языков логический тип как и в Rust может иметь два возможных значения: `true` и `false`. Логический тип занимает размер один байт. Логический тип в Rust указывается используя слово `bool`. Например:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let t = true;

    let f: bool = false; // с помощью явной аннотации типа
}
```

Основной способ использования значений логического типа это условные конструкции, такие как выражение `if`. Мы расскажем про работу выражения `if` в разделе  [“Условные конструкции”](ch03-05-control-flow.html#control-flow)<comment>.</comment>

#### Символьный тип данных

До этого момента мы работали только с числами, но Rust поддерживает также и буквы. Тип `char` является самым примитивным буквенным типом и следующий код показывает как им пользоваться. (Заметим, что `char` литералы определяются с помощью одинарных кавычек, в отличии от строк где используются двойные кавычки.)

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

Тип `char` имеет размер в четыре байта и представляет собой скалярное юникод значение (Unicode Scalar Value), что означает представление гораздо больше чем символы набора ASCII. Акцентированные (Accented) буквы, Китайские, Японские и Корейские символы; эмоджи (emoji) и пробелы нулевой длины все являются корректными значениями `char` в Rust. Скалярное юникод значение имеет диапазон от `U+0000` до `U+D7FF` и от `U+E000` до  `U+10FFFF` включительно. Тем не менее, “символ” на самом деле не является концептом в Юникод. Так что человеческая интуиция о том, что такое “символ” может не совпадать с тем, чем является тип `char` в Rust. Более детально мы обсудим тему в разделе [“Сохранение кодированного в UTF-8 текста как строки” ](ch08-02-strings.html#storing-utf-8-encoded-text-with-strings)<comment> главы 8.</comment>

### Сложные типы данных

*Сложные типы* могут группировать несколько значений в один тип. В Rust есть два примитивных комбинированных типа: кортежи и массивы.

#### Тип кортеж

Кортеж является общим способом совместной группировки нескольких значений различного типа в единый комбинированный тип. Кортежи имеют фиксированную длину после объявления, они не могут расти или уменьшаться в размере.

Кортеж создается записью списка значений, перечисленных через запятую внутри фигурных скобок. Каждая позиция в кортеже имеет тип. Типы различных значений в кортеже не должны быть одинаковыми. В примере мы добавили не обязательные аннотации типов:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

The variable `tup` binds to the entire tuple, because a tuple is considered a
single compound element. To get the individual values out of a tuple, we can
use pattern matching to destructure a tuple value, like this:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

Программа создает кортеж, привязывает его к переменной `tup`. Затем используется шаблон из `let` для присваивания `tup` и превращения его в три отдельных переменные `x`, `y` и  `z`. Это называется *деструктурирование*, потому что оно разрушает один кортеж на три части. В конце программа печатает значение `y`, которое равно `6.4`.

In addition to destructuring through pattern matching, we can access a tuple
element directly by using a period (`.`) followed by the index of the value we
want to access. For example:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

Программа создает кортеж с именем `x`, а затем создает новые переменные для каждого элемента, используя соответствующие индексы. Как в большинстве языков первый индекс в кортеже это ноль.

#### Тип массив

Другим способом получения коллекции из множества значений является *массив (array)*. В отличии от кортежа, каждый элемент массива имеет одинаковый тип. Массивы в Rust отличаются от массивов в некоторых других языках тем, что в Rust они имеют фиксированную длину подобно кортежам.

В Rust, значения попадающие в массив, записываются как список разделенных запятой значений внутри квадратных скобок:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Arrays are useful when you want your data allocated on the stack rather than
the heap (we will discuss the stack and the heap more in Chapter 4) or when
you want to ensure you always have a fixed number of elements. An array isn’t
as flexible as the vector type, though. A vector is a similar collection type
provided by the standard library that *is* allowed to grow or shrink in size.
If you’re unsure whether to use an array or a vector, you should probably use a
vector. Chapter 8 discusses vectors in more detail.

Примером, когда возможно лучше воспользоваться массивом вместо вектора, является программа в которой нужно знать названия месяцев в году. Вряд ли в такой программе необходимо добавлять или удалять месяцы, поэтому можно воспользоваться массивом так как вы знаете, что он всегда включает в себя 12 элементов:

```rust
let months = ["January", "February", "March", "April", "May", "June", 
              "July", "August", "September", "October", "November", 
              "December"];
```

Вы скорее всего запишите тип массива используя квадратные скобки,  внутри которых укажите тип элементов через точку с запятой, а за ней количество элементов в массиве, как в примере:

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

Здесь, `i32` является типом каждого элемента. После точки с запятой указано число `5` указывающее, что массив содержит 5 элементов.

Writing an array’s type this way looks similar to an alternative syntax for
initializing an array: if you want to create an array that contains the same
value for each element, you can specify the initial value, followed by a
semicolon, and then the length of the array in square brackets, as shown here:

```rust
let a = [3; 5];
```

Массив в переменной `a` будет включать `5` элементов, которые будут установлены в начальное значение равное `3`. Данная запись аналогична коду `let a = [3, 3, 3, 3, 3];`, но более кратким способом.

##### Accessing Array Elements

An array is a single chunk of memory allocated on the stack. You can access
elements of an array using indexing, like this:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

В данном примере, переменная `first` получит значение равное `1`, потому что это значение доступно по индексу `[0]` из массива. Переменная с именем `second` получит значение равное `2` из массива по индексу`[1]`.

##### Некорректный доступ к элементу массива

What happens if you try to access an element of an array that is past the end
of the array? Say you change the example to the following code, which will
compile but exit with an error when it runs:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,panics
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("The value of element is: {}", element);
}
```

Запуск кода командой `cargo run` выдает следующий результат:

```text
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/arrays`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is
 10', src/main.rs:5:19
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

Компиляция не вызвала ошибок, но программа завершилась ошибкой *времени выполнения (runtime)* и не выполнилась успешно. Когда вы пробуете получить доступ к элементу используя индекс, Rust проверит, что указанный индекс меньше чем длина массива. Если индекс больше или равен длине массива, Rust вызовет панику (panic).

This is the first example of Rust’s safety principles in action. In many
low-level languages, this kind of check is not done, and when you provide an
incorrect index, invalid memory can be accessed. Rust protects you against this
kind of error by immediately exiting instead of allowing the memory access and
continuing. Chapter 9 discusses more of Rust’s error handling.
