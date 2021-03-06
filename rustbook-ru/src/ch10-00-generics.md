# Обобщённые типы, типажи и время жизни

Каждый язык программирования имеет в своём арсенале эффективные средства борьбы с дублированием концепций. В Rust одним из таким инструментом являются обобщённые типы данных - *generics*. Это абстрактные типы подстановки для конкретных типов или других свойств. Когда мы пишем код, мы можем выразить поведение обобщённых типов или их связь с другими обобщёнными типами, не зная что будет на их месте при компиляции и запуске кода.

Это подобно тому, как функция принимает параметры с неизвестными значениями, чтобы запускать одинаковый код на множестве конкретных значений. Функции могут принимать параметры некоторого "обобщённого" типа вместо конкретного типа, вроде `i32` или `String`. Мы уже использовали такие типы данных в Главе 6 `Option<T>`, в Главе 8 `Vec<T>` и `HashMap<K, V>` и в Главе 9 `Result<T, E>`. В этой главе мы рассмотрим, как определить наши собственные типы данных, функции, методы, используя возможности обобщённых типов.

Прежде всего, мы рассмотрим как для уменьшения дублирования кода извлечь функцию из кода. Далее, мы будем использовать тот же механизм для создания обобщённой функции из двух функций, которые отличаются только типом их параметров. Мы также объясним, как использовать обобщённые типы данных при определении структур и перечислений.

Затем вы изучите как использовать *типажи (traits)* для определения поведения в обобщённом виде. Можно комбинировать типажи с обобщёнными типами для ограничения обобщённого типа только теми типами, которые имеют определённое поведение, в отличии от любых типов.

В конце мы обсудим *времена жизни (lifetimes)*, вариации обобщённых типов, которые дают компилятору информацию о том, как ссылки относятся друг к другу. Времена жизни позволяют одалживать (borrow) значения во многих ситуациях, предоставляя возможность компилятору удостовериться, что ссылки являются корректными.

## Удаление дублирования кода с помощью выделения функции

Прежде чем перейти к рассмотрению синтаксиса обобщённых типов, предлагаю рассмотреть технику устранения дублирования кода, которая не использует обобщённые типы. Затем, мы применим его для извлечения функции с шаблонными типами данных. Способ, которым вы определяете как устранить дублирование кода для получения обычной функции, вы начнёте использовать для устранения дублирования с помощью обобщённых типов.

Рассмотрим небольшую программу, которая ищет наибольшее число в списке, как показано в листинге 10-1.

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("Наибольшее число: {}", largest);
#    assert_eq!(largest, 100);
}
```

<span class="caption">Листинг 10-1: Код поиска наибольшего числа в списке</span>

Программа сохраняет вектор целых чисел в переменной `number_list` и помещает первое значение из списка в переменную `largest`. Далее, итератор проходит по всем элементам списка. Если текущий элемент больше числа сохранённого в переменной `largest`, то его значение заменяет значение в этой переменной. Если текущий элемент меньше или равен "наибольшему" найденному ранее, то значение переменной `largest` не изменяется. После полного перебора всех элементов, переменная `largest` должна содержать наибольшее значение, которое в нашем случае будет равно 100.

Чтобы найти наибольшее число в двух различных списках, мы можем дублировать код листинга 10-1 и использовать такую же логику в двух различных местах программы, как показано в листинге 10-2:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("Наибольшее число: {}", largest);

    let number_list = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("Наибольшее число: {}", largest);
}
```

<span class="caption">Листинг 10-2: Программа поиска наибольшего числа в <em>
двух</em> списках</span>

Несмотря на то, что код программы работает, дублирование кода утомительно и подвержено ошибкам. Мы также должны обновить код в нескольких местах, если мы хотим изменить его.

Для устранения дублирования нам надо создать абстракции с помощью определения функции, которая работает с любым списком целых чисел переданным ей как входной параметр. Данное решение делает код более ясным и позволяет абстрактным образом выразить концепцию поиска наибольшего числа в списке.

В листинге 10-3, мы извлекли код, который находит наибольшее число, в отдельную функцию с именем `largest`. В отличии от кода в листинге 10-1, который находит наибольшее число только в одном конкретном списке, новая программа может искать наибольшее число в двух разных списках.

<span class="filename">Файл: src/main.rs</span>

```rust
fn largest(list: &[i32]) -> i32 {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);
#    assert_eq!(result, 100);

    let number_list = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let result = largest(&number_list);
    println!("The largest number is {}", result);
#    assert_eq!(result, 6000);
}
```

<span class="caption">Листинг 10-3: Функция поиска наибольшего элемента в двух списках</span>

Функция `largest` имеет параметр с именем `list`, который представляет срез любых значений типа `i32`, которые мы можем передать в неё. В результате вызова функции, код выполнится с конкретными, переданными в неё значениями.

Итак, вот шаги выполненные для изменения кода из листинга 10-2 в
листинг 10-3:

1. Определить дублирующийся код.
2. Извлечь дублирующийся код и поместить его в тело функции, определяя входные и выходные значения сигнатуры функции.
3. Обновить и заменить два участка дублирующегося кода вызовом одной функции.

Далее, мы воспользуемся этими же шагами для обобщённых типов, чтобы различными способами уменьшить дублирование кода. Обобщённые типы позволяют работать над абстрактными типами тем же образом, каким тело функции может работать над абстрактным списком `list` вместо конкретных значений.

Например, у нас есть две функции: одна ищет наибольший элемент внутри среза значений типа `i32`, а другая внутри среза значений типа `char`. Как уменьшить такое дублирование? Давайте выяснять!
