## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```sh
$ open https://cmake.org/
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [ ] 2. Ознакомиться со ссылками учебного материала
- [ ] 3. Выполнить инструкцию учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03
$ cd projects/lab03
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git
```

```sh
$ g++ -std=c++11 -I./include -c sources/print.cpp
$ ls print.o
$ nm print.o | grep print
$ ar rvs print.a print.o
$ file print.a
$ g++ -std=c++11 -I./include -c examples/example1.cpp
$ ls example1.o
$ g++ example1.o print.a -o example1
$ ./example1 && echo
```

```sh
$ g++ -std=c++11 -I./include -c examples/example2.cpp
$ nm example2.o
$ g++ example2.o print.a -o example2
$ ./example2
$ cat log.txt && echo
```

```sh
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

```sh
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

```sh
$ cmake -H. -B_build
$ cmake --build _build
```

```sh
$ cat >> CMakeLists.txt <<EOF
add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

```sh
$ cmake --build _build
$ cmake --build _build --target print
$ cmake --build _build --target example1
$ cmake --build _build --target example2
```

```sh
$ ls -la _build/libprint.a
$ _build/example1 && echo
hello
$ _build/example2
$ cat log.txt && echo
hello
$ rm -rf log.txt
```

```sh
$ git clone https://github.com/tp-labs/lab03 tmp
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```sh
$ cat CMakeLists.txt
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ tree _install
```

```sh
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
$ git push origin master
```

## Report

```sh
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

Заходим в formatter_lib и создаем CMakeLists.txt
Туда вписываем
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD 17)
project(formatter_lib)
add_library(${PROJECT_NAME} STATIC
${CMAKE_CURRENT_SOURCE_DIR}/formatter.h
${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp
)
открываем терминал и пишем cmake . -B _build
cd _build
make
### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.
Заходим в formatter_ex_lib и создаем CMakeLists
туда пишем
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD 17)
project(formatter_ex_lib)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib)
add_library(${PROJECT_NAME} STATIC
${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.h
${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp
)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib {CMAKE_CURRENT_SOURCE_DIR}/lib_build)

открываем терминал и пишем cmake . -B _build
cd _build
make

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;

заходим в hello_world_application
создаем CMakeLists.txt и пишем
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD 17)
project(hello_world)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib)
add_executable(hw
hello_world.cpp
)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
{CMAKE_CURRENT_SOURCE_DIR}/lib_build)
target_link_libraries(hw PUBLIC formatter_ex_lib formatter_lib)

открываем терминал и пишем cmake . -B _build
cd _build
make
./hw

* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

заходим в solver_lib и создаем CMakeLists.txt
туда пишем
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD 17)
project(solver)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib)
add_executable(solver
    solver.cpp
    )
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
    {CMAKE_CURRENT_SOURCE_DIR}/lib_build)
target_link_libraries(solver PUBLIC formatter_ex_lib formatter_lib)


затем заходим в solver_application и создаем CMakeLists.txt
туда пишем
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD 17)
project(solver)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib)
add_executable(solver
equation.cpp
)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib
{CMAKE_CURRENT_SOURCE_DIR}/lib_build)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
{CMAKE_CURRENT_SOURCE_DIR}/lib_formatter_build)
target_link_libraries(solver PUBLIC formatter_ex_lib formatter_lib solver_lib)

открываем терминал и пишем cmake . -B _build
cd _build
make
./solver

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)
