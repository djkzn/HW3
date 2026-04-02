# Отчёт по лабораторной работе №3
## Сборка проектов с использованием CMake

**Студент:** Зотов  
**Дата:** 02.04.2026  
**Репозиторий:** https://github.com/tp-labs/lab03

---

## Цель работы

Освоить систему сборки CMake: научиться конфигурировать и собирать многомодульные C++ проекты, состоящие из статических библиотек и исполняемых приложений.

---

## Ход выполнения работы

### 1. Подготовка рабочего окружения

Работа выполнялась в среде WSL (Windows Subsystem for Linux) на системе Ubuntu. После входа в WSL был выполнен переход в рабочую директорию:

```bash
PS C:\WINDOWS\system32> wsl
zotov@zephyrus:/mnt/c/WINDOWS/system32$ cd ~
zotov@zephyrus:~$ cd workspace/tasks/
```

Первая попытка запустить CMake в уже существующей папке `HW3` завершилась ошибкой — в ней не было `CMakeLists.txt`. Папка была удалена, и репозиторий клонирован повторно напрямую в `HW3`:

```bash
zotov@zephyrus:~/workspace/tasks$ rm -rf HW3
zotov@zephyrus:~/workspace/tasks$ git clone https://github.com/tp-labs/lab03 HW3
```

<details>
<summary>Вывод git clone</summary>

```
Cloning into 'HW3'...
remote: Enumerating objects: 91, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 91 (delta 23), reused 21 (delta 21), pack-reused 61 (from 1)
Receiving objects: 100% (91/91), 1.02 MiB | 2.46 MiB/s, done.
Resolving deltas: 100% (41/41), done.
```

</details>

После клонирования структура проекта:

```
CMakeLists.txt  README.md         formatter_lib            preview.png         solver_lib
LICENSE         formatter_ex_lib  hello_world_application  solver_application
```

---

### 2. Сборка `formatter_lib`

В директории `formatter_lib` файл конфигурации имел неправильное имя — `CMakeList.txt` (без финальной `s`). Это было исправлено:

```bash
zotov@zephyrus:~/workspace/tasks/HW3/formatter_lib$ mv CMakeList.txt CMakeLists.txt
```

После исправления — конфигурирование и сборка прошли успешно:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ cmake -S formatter_lib -B formatter_lib/_build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake --build formatter_lib/_build
```

<details>
<summary>Вывод cmake configure (formatter_lib)</summary>

```
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features - done
-- Configuring done (7.4s)
-- Generating done (0.0s)
-- Build files have been written to: /home/zotov/workspace/tasks/HW3/formatter_lib/_build
```

</details>

```
[ 50%] Building CXX object CMakeFiles/formatter.dir/formatter.cpp.o
[100%] Linking CXX static library libformatter.a
[100%] Built target formatter
```

---

### 3. Сборка `formatter_ex_lib`

Конфигурирование и сборка `formatter_ex_lib` прошли без дополнительных правок. CMake автоматически подключил `formatter_lib` как зависимость:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ cmake -S formatter_ex_lib -B formatter_ex_lib/_build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake --build formatter_ex_lib/_build
```

<details>
<summary>Вывод cmake configure (formatter_ex_lib)</summary>

```
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info - done
-- Detecting CXX compiler ABI info - done
-- Configuring done (6.7s)
-- Generating done (0.0s)
-- Build files have been written to: /home/zotov/workspace/tasks/HW3/formatter_ex_lib/_build
```

</details>

```
[ 25%] Building CXX object formatter/CMakeFiles/formatter.dir/formatter.cpp.o
[ 50%] Linking CXX static library libformatter.a
[ 50%] Built target formatter
[ 75%] Building CXX object CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[100%] Linking CXX static library libformatter_ex.a
[100%] Built target formatter_ex
```

---

### 4. Сборка `hello_world_application`

Для `hello_world_application` потребовалось создать `CMakeLists.txt` через редактор. Сборка завершилась успешно:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ cmake -S hello_world_application -B hello_world_application/_build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake --build hello_world_application/_build
```

<details>
<summary>Вывод cmake configure (hello_world_application)</summary>

```
-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info - done
-- Detecting CXX compiler ABI info - done
-- Configuring done (7.5s)
-- Generating done (0.0s)
-- Build files have been written to: /home/zotov/workspace/tasks/HW3/hello_world_application/_build
```

</details>

```
[ 16%] Building CXX object formatter_ex/formatter/CMakeFiles/formatter.dir/formatter.cpp.o
[ 33%] Linking CXX static library libformatter.a
[ 33%] Built target formatter
[ 50%] Building CXX object formatter_ex/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 66%] Linking CXX static library libformatter_ex.a
[ 66%] Built target formatter_ex
[ 83%] Building CXX object CMakeFiles/hello_world.dir/hello_world.cpp.o
[100%] Linking CXX executable hello_world
[100%] Built target hello_world
```

Запуск приложения:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ ./hello_world_application/_build/hello_world
-------------------------
hello, world!
-------------------------
```

---

### 5. Сборка `solver_application`

Сборка `solver_application` потребовала исправления нескольких последовательных ошибок.

**Ошибка 1:** В `solver_lib` отсутствовал `CMakeLists.txt`. Файл был создан вручную через `subl solver_lib/CMakeLists.txt`.

**Ошибка 2:** В созданном `CMakeLists.txt` было указано неверное имя исходника — `solver_lib.cpp` вместо реального `solver.cpp`. После исправления возникла ошибка компиляции:

<details>
<summary>Ошибка компиляции: std::sqrtf</summary>

```
/home/zotov/workspace/tasks/HW3/solver_lib/solver.cpp: In function 'void solve(float, float, float, float&, float&)':
solver.cpp:14:21: error: 'sqrtf' is not a member of 'std'; did you mean 'sqrt'?
   14 |     x1 = (-b - std::sqrtf(d)) / (2 * a);
      |                     ^~~~~
      |                     sqrt
solver.cpp:15:21: error: 'sqrtf' is not a member of 'std'; did you mean 'sqrt'?
   15 |     x2 = (-b + std::sqrtf(d)) / (2 * a);
      |                     ^~~~~
      |                     sqrt
gmake[2]: *** [solver_lib/CMakeFiles/solver_lib.dir/build.make:76: ...] Error 1
gmake[1]: *** [CMakeFiles/Makefile2:215: ...] Error 2
gmake: *** [Makefile:91: all] Error 2
```

</details>

**Ошибка 3:** В `solver.cpp` использовалась функция `std::sqrtf`, которой нет в пространстве имён `std` в стандарте C++. Функция была исправлена на `std::sqrt`. После пересборки с очисткой `_build`:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ rm -rf solver_application/_build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake -S solver_application -B solver_application/_build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake --build solver_application/_build
```

```
[ 12%] Building CXX object solver_lib/CMakeFiles/solver_lib.dir/solver.cpp.o
[ 25%] Linking CXX static library libsolver_lib.a
[ 25%] Built target solver_lib
[ 37%] Building CXX object formatter_ex/formatter/CMakeFiles/formatter.dir/formatter.cpp.o
[ 50%] Linking CXX static library libformatter.a
[ 50%] Built target formatter
[ 62%] Building CXX object formatter_ex/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 75%] Linking CXX static library libformatter_ex.a
[ 75%] Built target formatter_ex
[ 87%] Building CXX object CMakeFiles/solver.dir/equation.cpp.o
[100%] Linking CXX executable solver
[100%] Built target solver
```

---

### 6. Сборка всего проекта из корня

При сборке через корневой `CMakeLists.txt` возникли две ошибки:

**Ошибка 1:** Корневой `CMakeLists.txt` ссылался на несуществующий файл `sources/print.cpp`. Путь был удалён из конфига.

**Ошибка 2:** Конфликт имён CMake-целей — `formatter`, `formatter_ex`, `solver_lib` уже объявлялись в дочерних `CMakeLists.txt`, а корневой пытался создать их повторно через `add_subdirectory`.

<details>
<summary>Вывод ошибок конфликта имён</summary>

```
CMake Error at formatter_lib/CMakeLists.txt:7 (add_library):
  add_library cannot create target "formatter" because another target with
  the same name already exists. The existing target is a static library
  created in source directory "/home/zotov/workspace/tasks/HW3/formatter_lib".
  See documentation for policy CMP0002 for more details.

CMake Error at formatter_ex_lib/CMakeLists.txt:9 (add_library):
  add_library cannot create target "formatter_ex" because another target with
  the same name already exists.

CMake Error at formatter_ex_lib/CMakeLists.txt:15 (target_link_libraries):
  Attempt to add link library "formatter" to target "formatter_ex" which is
  not built in this directory. This is allowed only when policy CMP0079 is set to NEW.
```

</details>

После устранения дублирования финальная сборка всего проекта прошла успешно:

```bash
zotov@zephyrus:~/workspace/tasks/HW3$ rm -rf build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake -S . -B build
zotov@zephyrus:~/workspace/tasks/HW3$ cmake --build build
```

```
[ 10%] Building CXX object formatter_lib/CMakeFiles/formatter.dir/formatter.cpp.o
[ 20%] Linking CXX static library libformatter.a
[ 20%] Built target formatter
[ 30%] Building CXX object formatter_ex_lib/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 40%] Linking CXX static library libformatter_ex.a
[ 40%] Built target formatter_ex
[ 50%] Building CXX object solver_lib/CMakeFiles/solver_lib.dir/solver.cpp.o
[ 60%] Linking CXX static library libsolver_lib.a
[ 60%] Built target solver_lib
[ 70%] Building CXX object hello_world_application/CMakeFiles/hello_world.dir/hello_world.cpp.o
[ 80%] Linking CXX executable hello_world
[ 80%] Built target hello_world
[ 90%] Building CXX object solver_application/CMakeFiles/solver.dir/equation.cpp.o
[100%] Linking CXX executable solver
[100%] Built target solver
```

---

## Вывод

В ходе лабораторной работы были освоены базовые принципы работы с системой сборки CMake: конфигурирование проектов командами `cmake -S ... -B ...`, сборка командой `cmake --build`, подключение зависимостей через `add_subdirectory` и `target_link_libraries`. Были исправлены типичные ошибки: неверное имя файла `CMakeLists.txt`, неправильные пути к исходным файлам, использование нестандартной функции `std::sqrtf` вместо `std::sqrt`, а также конфликт имён CMake-целей при сборке всего проекта из корня. Все компоненты — `formatter_lib`, `formatter_ex_lib`, `solver_lib`, `hello_world` и `solver` — были успешно собраны.
