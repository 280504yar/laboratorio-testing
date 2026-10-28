# Parte 1: preparación del proyecto y plan de pruebas

## Ambiente de trabajo

El laboratorio se hizo en Debian 13 (trixie) con KDE Plasma. Primero se instalaron las herramientas:

```bash
sudo apt update
sudo apt install build-essential cmake git lcov
```

Y se revisaron las versiones:

```bash
$ g++ --version | head -n 1
g++ (Debian 14.2.0-19) 14.2.0
$ cmake --version | head -n 1
cmake version 3.31.6
$ make --version | head -n 1
GNU Make 4.4.1
$ git --version
git version 2.47.3
$ lcov --version
lcov: LCOV version 2.0-1
```

## ¿Qué es CMake?

CMake es una herramienta que prepara todo para compilar un proyecto. En vez de escribir a mano un comando de `g++` para cada archivo, en el `CMakeLists.txt` se dice qué archivos hay, qué librerías se usan y qué ejecutables se quieren. Luego `cmake ..` genera un `Makefile` y `make` compila.

En este lab CMake también se encarga de bajar Google Test solo, con `FetchContent`, así que no hubo que instalarlo aparte.

## ¿Para qué sirve Google Test?

Es una librería para hacer pruebas unitarias en C++. Trae macros como `TEST`, `EXPECT_EQ`, `ASSERT_NE` y `EXPECT_THROW` que sirven para comparar lo que da una función con lo que debería dar. Al correrlo dice qué pruebas pasaron (`OK`) y cuáles fallaron (`FAILED`), y en las que fallan muestra el valor esperado y el que salió.

## ¿Qué significa automatizar pruebas?

Que las pruebas se corren con un solo comando (`./run_tests` o `ctest`) y la compu hace la comparación sola. No hace falta correr el programa, meter datos y revisar a ojo. Así se pueden correr las veces que sea, por ejemplo cada vez que se sube algo a GitHub.

## ¿Qué significa que las pruebas sean repetibles?

Que si se vuelven a correr, en otro momento o en otra compu (la local o la de GitHub Actions), dan el mismo resultado. No dependen de la hora, del azar ni de archivos que solo están en una máquina.

## Archivos y carpetas creados

```text
laboratorio-testing/
├── README.md                      # índice del lab
├── CMakeLists.txt                 # configuración para compilar
├── .gitignore                     # para no subir la carpeta build/
├── include/                       # archivos .h
│   ├── calculator.h
│   ├── string_utils.h
│   └── grade_utils.h
├── src/                           # archivos .cpp con el código
│   ├── calculator.cpp
│   ├── string_utils.cpp
│   └── grade_utils.cpp
├── tests/                         # pruebas con Google Test
│   ├── test_calculator.cpp
│   ├── test_string_utils.cpp
│   └── test_grade_utils.cpp
├── docs/                          # documentación de cada parte
└── .github/workflows/testing.yml  # GitHub Actions
```

Como este repo es solo para este lab, la carpeta `laboratorio-testing` es la raíz del repo. Por eso en `testing.yml` se quitaron los `cd laboratorio-testing`, como dice la nota del enunciado.

## Preguntas de reflexión (Parte 1)

**1. ¿Por qué conviene separar el código fuente de las pruebas?**
Para tener todo más ordenado y que el programa final no lleve el código de las pruebas. Además, el código de `src/` se compila como una librería aparte (`project_lib`), así que se puede usar en otro programa sin arrastrar las pruebas.

**2. ¿Qué ventaja tiene usar CMake en un proyecto de C++?**
Que no hay que escribir los comandos de compilación a mano. CMake se encarga de las dependencias (como Google Test), de las carpetas de `include` y de opciones como la cobertura. Además funciona igual en la compu local y en GitHub Actions.

**3. ¿Por qué es útil que las pruebas se puedan ejecutar con un solo comando?**
Porque así da menos pereza correrlas y se corren más seguido. También permite que GitHub Actions las corra solo.

**4. ¿Qué diferencia hay entre probar manualmente y probar automáticamente?**
Probar a mano es lento, se olvidan casos y depende de que la persona revise bien. Probar automáticamente es rápido, siempre revisa los mismos casos y dice exactamente qué falló.

---

## Casos funcionales (Parte 9)

### Requisito

> El sistema debe convertir una nota numérica entre 0 y 100 a una letra de calificación. Las notas entre 90 y 100 son A, entre 80 y 89 son B, entre 70 y 79 son C, entre 60 y 69 son D, y menores a 60 son F. Si la nota está fuera del rango permitido, el sistema debe rechazarla.

### Tabla de casos funcionales

| ID | Requisito | Entrada | Resultado esperado | Tipo de caso | Prueba que lo cubre | Estado |
|---|---|---:|---|---|---|---|
| TC-001 | Convertir nota excelente | 95 | A | Normal | `LetterGradeA` | Ya estaba |
| TC-002 | Límite inferior de A | 90 | A | Borde | `GradeBoundaryBetweenBAndA` | Ya estaba |
| TC-003 | Límite superior de B | 89 | B | Borde | `GradeBoundaryBetweenBAndA` | Ya estaba |
| TC-004 | Nota inválida baja | -1 | Excepción | Inválido | `NegativeGradeThrowsException` | Ya estaba |
| TC-005 | Nota inválida alta | 101 | Excepción | Inválido | `TC005InvalidHighGrade` | Se agregó |

### Casos que ya estaban cubiertos

- **TC-001:** `LetterGradeA` revisa que `letter_grade(95)` dé `'A'`.
- **TC-002 y TC-003:** `GradeBoundaryBetweenBAndA` revisa que 89 dé `'B'` y que 90 dé `'A'`.
- **TC-004:** `NegativeGradeThrowsException` revisa que `letter_grade(-1)` lance `std::invalid_argument`.

### Caso que se tuvo que agregar

Faltaba el **TC-005**. Ya había una prueba con nota inválida alta (`InvalidGradeThrowsException`), pero usa 120, no 101. El 101 es justo el primer valor que se sale del rango, así que es el que más interesa probar. Se agregó esta prueba en `tests/test_grade_utils.cpp`:

```cpp
TEST(GradeUtilsTest, TC005InvalidHighGrade) {
    EXPECT_THROW(letter_grade(101), std::invalid_argument);
}
```

Después de agregarla se corrieron los casos de la tabla con un filtro, y luego todas las pruebas:

```bash
make
./run_tests --gtest_filter='*TC005*:*BoundaryBetweenBAndA*:*LetterGradeA*:*NegativeGrade*'
./run_tests | tail -n 3
```

```text
Note: Google Test filter = *TC005*:*BoundaryBetweenBAndA*:*LetterGradeA*:*NegativeGrade*
[==========] Running 4 tests from 1 test suite.
[----------] Global test environment set-up.
[----------] 4 tests from GradeUtilsTest
[ RUN      ] GradeUtilsTest.LetterGradeA
[       OK ] GradeUtilsTest.LetterGradeA (0 ms)
[ RUN      ] GradeUtilsTest.NegativeGradeThrowsException
[       OK ] GradeUtilsTest.NegativeGradeThrowsException (0 ms)
[ RUN      ] GradeUtilsTest.GradeBoundaryBetweenBAndA
[       OK ] GradeUtilsTest.GradeBoundaryBetweenBAndA (0 ms)
[ RUN      ] GradeUtilsTest.TC005InvalidHighGrade
[       OK ] GradeUtilsTest.TC005InvalidHighGrade (0 ms)
[----------] 4 tests from GradeUtilsTest (0 ms total)

[----------] Global test environment tear-down
[==========] 4 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 4 tests.
[----------] Global test environment tear-down
[==========] 42 tests from 3 test suites ran. (1 ms total)
[  PASSED  ] 42 tests.
```

Las cuatro pruebas que cubren la tabla pasaron, y en total ya eran 42. Al compilar salió un aviso `libgcov profiling error: ... overwriting an existing profile data`. No es un error de las pruebas: sale porque la carpeta `build` tenía activada la cobertura de la Parte 5 y, al recompilar, se reemplazaron los datos viejos.

### ¿Por qué estas pruebas son funcionales?

Porque salen directamente del requisito, o sea, de lo que el sistema tiene que hacer, y no de cómo está programado por dentro. Cualquier persona puede leer la tabla y entenderla sin saber C++. Si la función se reescribe de otra forma, los casos siguen sirviendo igual.

## Preguntas de reflexión (Parte 9)

**1. ¿Qué diferencia hay entre una prueba unitaria y una prueba funcional?**
La unitaria revisa un pedacito del código, como una función. La funcional revisa que se cumpla un requisito, pensando en lo que espera el usuario. En este lab se usó `TEST` para las dos, pero los casos funcionales se sacaron del requisito.

**2. ¿Por qué una prueba funcional se relaciona con requisitos?**
Porque cada caso revisa una parte del requisito. Por ejemplo, "entre 80 y 89 son B" se revisa probando el 89. Si la prueba pasa, esa parte del requisito se cumple.

**3. ¿Qué significa pensar desde la perspectiva del usuario o del sistema?**
Preguntarse qué espera la persona que usa el programa, en vez de fijarse en cómo está hecho cada `if`. Por ejemplo, el usuario espera que una nota de 101 se rechace, sin importar cómo se programó eso.

**4. ¿Por qué la documentación de casos de prueba ayuda al equipo?**
Porque queda claro qué se probó y qué no. Con los IDs es fácil decir "falla el TC-005", y alguien nuevo en el equipo entiende rápido cómo debe funcionar el sistema.
