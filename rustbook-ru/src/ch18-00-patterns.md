# Шаблоны и сопоставление

Шаблоны - это специальный синтаксис в Rust для сопоставления со структурой типов, как сложных, так и простых. Использование шаблонов в сочетании с выражениями `match` и другими конструкциями даёт вам больший контроль над потоком управления программы. Шаблон состоит из некоторой комбинации следующего:

- Литералы
- Реструктуризованные массивы, перечисления, структуры или кортежи
- Переменные
- Специальные символы
- Заполнители

Эти компоненты описывают форму данных, с которыми мы работаем и затем сопоставляем их с указанными значениями в памяти, чтобы определить, есть ли в нашей программе правильные данные для продолжения выполнения определённого фрагмента кода.

Чтобы использовать шаблон, мы сравниваем его с некоторым значением. Если шаблон соответствует значению, мы используем части значения в нашем дальнейшем коде. Вспомните выражения `match` главы 6, в которых использовались шаблоны, например, описание машины для сортировки монет. Если значение в памяти соответствует форме шаблона, мы можем использовать именованные части шаблона. Если этого не произойдёт, то не выполнится код, связанный с шаблоном.

Эта глава - справочник по всем моментам, связанным с шаблонами. Мы расскажем о допустимых местах использования шаблонов, разнице между опровержимыми и неопровержимыми шаблонами и про различные виды синтаксиса шаблонов, которые вы можете увидеть. К концу главы вы узнаете, как использовать шаблоны для ясного выражения многих понятий.
