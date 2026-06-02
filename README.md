# Лабораторная работа №3: Изучение систем автоматизации сборки проекта на примере CMake
## Цель работы
Освоение основ автоматизации сборки проектов с помощью CMake.

## Подготовка

```bash
export GITHUB_USERNAME=blyov1301
cd ${GITHUB_USERNAME}/workspace
source scripts/activate
```

## Tutorial

### 1. Клонирование и настройка репозитория
Берём за основу репозиторий lab02 и перенастраиваем его для lab03.

```bash
git clone https://github.com/blyov1301/lab02.git projects/lab03
cd projects/lab03
git remote remove origin
git remote add origin https://github.com/blyov1301/lab03.git
```

### 2. Ручная компиляция (без CMake)
Компилируем вручную, чтобы понять разницу с автоматизированной сборкой.

```bash
g++ -std=c++11 -I./include -c sources/print.cpp
ar rvs print.a print.o
g++ -std=c++11 -I./include -c examples/example1.cpp
g++ example1.o print.a -o example1
./example1

g++ -std=c++11 -I./include -c examples/example2.cpp
g++ example2.o print.a -o example2
./example2
cat log.txt
```

### 3. Очистка
Удаляем артефакты ручной сборки.

```bash
rm -rf *.o *.a example1 example2 log.txt
```

### 4. Создание CMakeLists.txt
Пишем первый конфигурационный файл для CMake.

```bash
cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

### 5. Сборка с CMake
Запускаем конфигурацию и сборку через CMake.

```bash
cmake -H. -B_build
cmake --build _build
```

### 6. Добавление исполняемых файлов
Расширяем CMakeLists.txt: добавляем два примера и линкуем их с библиотекой.

```bash
cat >> CMakeLists.txt <<EOF
add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF

cmake --build _build
_build/example1
_build/example2
cat log.txt
```

### 7. Установка (install)
Настраиваем установку библиотеки и исполняемых файлов в отдельную директорию.

```bash
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
cmake --build _build --target install
tree _install
```

### 8. Публикация
Сохраняем изменения в репозитории.

```bash
git add CMakeLists.txt
git commit -m "added CMakeLists.txt"
git push origin master
```

## Домашнее задание

**Контекст:** вы стажёр в компании "Formatter Inc.". Вам поручено перевести проекты на CMake.

### Задание 1
**Цель:** создать CMakeLists.txt для статической библиотеки formatter.

```bash
mkdir -p formatter_lib
cat > formatter_lib/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(formatter)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
EOF
```

### Задание 2
**Цель:** создать CMakeLists.txt для библиотеки formatter_ex, которая зависит от formatter.

```bash
mkdir -p formatter_ex_lib
cat > formatter_ex_lib/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)
include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(formatter_ex formatter)
EOF
```

### Задание 3
**Цель:** создать CMakeLists.txt для двух приложений: hello_world (использует formatter_ex) и solver (использует formatter_ex + solver_lib).

#### 3.1. Приложение hello_world
```bash
mkdir -p hello_world_application
cat > hello_world_application/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world \${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)
target_link_libraries(hello_world formatter_ex)
EOF
```

#### 3.2. Библиотека solver_lib
```bash
mkdir -p solver_lib
cat > solver_lib/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(solver_lib)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver_lib STATIC \${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)
include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
EOF
```

#### 3.3. Приложение solver
```bash
mkdir -p solver_application
cat > solver_application/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(solver)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(solver \${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)
target_link_libraries(solver formatter_ex solver_lib)
EOF
```

## Использованные команды

`g++`, `ar`, `nm`, `cmake`, `tree`, `git`
