## __exec - проблемы & уязвимости (NodeJS).__

### _В этой статье мы рассмотрим проблемные участки использвания_

### _exec/execSync, а также их решения (с примерами кода)._

# 

### Версия node:

| NodeJS | LTS 18.20.6 |
| ------ | ----------- |

# 

### Краткое описание:

+ #### _[Вступление](1)_
+ #### _[Command injection](1)_
+ #### _[Переполнение буфера]()_
+ #### _[Доступ к файловой системе]()_
+ #### _[Блокирование выполнения процесса]()_

# 

### Вступление:

_**NodeJS** — среда выполнения JavaScript вне браузера. Она предоставляет множество встроенных модулей для работы с файловой системой, операционной системой, сетью, криптографией и другими аспектами.
В этой статье мы рассмотрим модуль child_process, а именно его методы exec и execSync, разберём потенциальные уязвимости при их использовании и способы защиты от них._

_Методы **exec и execSync** позволяют выполнять команды оболочки (shell) прямо из Node.js, что даёт возможность запускать системные утилиты и скрипты. Однако при неправильном использовании они могут представлять угрозу безопасности._

_Пример использования exec:_
```js
const { exec, execSync } = require("child_process");

exec("echo exec", (err, stdout, stderr) => {
    if (err) {
        console.error(`Error: ${err}`);
        return;
    }

    console.log(stdout); // exec
})
```

#

#### 1. Command injection

_Передача пользовательских команд в exec/execSync без должной фильтрации и валидации может привести к критическим уязвимостям, таким как выполнение произвольного кода, удаление файловой системы или компрометация системы._
Пример:
```js
const { exec } = require("child_process");

const processCommands = process.argv;

console.log(processCommands[2]); // rm -rf *

exec(processCommands[2], (err) => {
    if (err) {
        console.error(`Error: ${err}`);
        return;
    }
})
```
