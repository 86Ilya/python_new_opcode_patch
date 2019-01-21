# Патч с opcode LOAD_OTUS к Python 2.7.15

Данный патч оптимизирует два opcode:
```
LOAD_FAST 0
LOAD_CONST arg
```
В один `LOAD_OTUS arg`

Перед установкой данного патча, необходимо скачать исходный код Python 2.7.15, выполнив команду
```sh
git clone -b 2.7 --single-branch https://github.com/python/cpython.git
```

Далее скопировать патч `new_opcode.patch` В папку `cpython`
Перед применением патча проверить всё ли в порядке, командой:
```sh
git apply --check new_opcode.patch
```
Если команда не вернула ошибок, то применить патч:
```sh
git am --signoff < new_opcode.patch
```
После, следуя инструкциям Python собрать исходный код:
```sh
./configure
make
```
При успешной сборке, в каталоге появится исполняемый файл `python`

Для проверки следует запустить собранный интерпретатор питон:
```sh
./python
```
и в нём выполнить команды:
```python
def fib(n): return fib(n - 1) + fib(n - 2) if n > 1 else n
import dis
dis.dis(fib)
```

В выводе дизассемблера вы увидите следующее:
```
>>> dis.dis(fib)
  1           0 LOAD_OTUS                1
              3 COMPARE_OP               4 (>)
              6 POP_JUMP_IF_FALSE       31
              9 LOAD_GLOBAL              0 (fib)
             12 LOAD_OTUS                1
             15 BINARY_SUBTRACT     
             16 CALL_FUNCTION            1
             19 LOAD_GLOBAL              0 (fib)
             22 LOAD_OTUS                2
             25 BINARY_SUBTRACT     
             26 CALL_FUNCTION            1
             29 BINARY_ADD          
             30 RETURN_VALUE        
        >>   31 LOAD_FAST                0 (n)
             34 RETURN_VALUE        
>>> 
```

Где `LOAD_OTUS` и есть наш, добавленный opcode

