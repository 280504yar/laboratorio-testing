# Parte 2: código base, primeras pruebas unitarias y EXPECT vs ASSERT

## Código inicial (Parte 2)

### Módulos creados

| Módulo | Archivo .h | Archivo .cpp | Funciones |
|---|---|---|---|
| Calculadora | `include/calculator.h` | `src/calculator.cpp` | `add`, `subtract`, `multiply`, `divide`, `is_even` |
| Texto | `include/string_utils.h` | `src/string_utils.cpp` | `to_uppercase`, `is_palindrome`, `count_vowels` |
| Notas | `include/grade_utils.h` | `src/grade_utils.cpp` | `average`, `is_passing`, `letter_grade` (y después `is_valid_grade`) |

### ¿Para qué sirven los archivos `.h`?

Solo dicen qué funciones hay, qué reciben y qué devuelven, pero no cómo funcionan por dentro. Las pruebas los incluyen (`#include "calculator.h"`) para poder llamar esas funciones. El `#ifndef ... #define ... #endif` es para que no se incluyan dos veces.

### ¿Para qué sirven los archivos `.cpp`?

Ahí está el código de verdad de cada función. CMake los compila juntos en una librería llamada `project_lib`, que después se une con el ejecutable de pruebas `run_tests`.

### Casos normales, borde e inválidos de cada función

| Función | Normales | Borde | Inválidos |
|---|---|---|---|
| `add`, `subtract`, `multiply` | Positivos y negativos | Cero, números aleatorios | No tiene (solo podría desbordarse con números gigantes) |
| `divide` | `10 / 2` | Negativos | Dividir entre 0 (lanza excepción) |
| `is_even` | 8 y 7 | 0, negativos | — |
| `to_uppercase` | `"hola"` | Texto vacío, números o símbolos | — |
| `is_palindrome` | `"radar"`, `"software"` | Vacío, una sola letra, espacios, mayúsculas | — |
| `count_vowels` | `"murcielago"` | Sin vocales, vacío | (No cuenta vocales con tilde) |
| `average` | `{80, 90, 100}` | Una sola nota | Lista vacía (lanza excepción) |
| `is_passing` | 85, 50 | 69 y 70 | — |
| `letter_grade` | 95, 85, 75, 65, 50 | 0, 59/60, 69/70, 79/80, 89/90, 100 | -1, 101, 120 (lanzan excepción) |

---

## Primeras pruebas unitarias (Parte 3)

Se hicieron 25 pruebas en total: 8 de la calculadora, 7 de texto y 10 de notas.

### Comandos para compilar

Desde la carpeta `laboratorio-testing`:

```bash
mkdir build
cd build
cmake ..
make
```

Al correr `cmake ..` se descargó Google Test y salieron unos warnings amarillos (`CMP0135` y `Compatibility with CMake < 3.10`). No son errores; salen porque la versión de Google Test que pide el lab (1.12.1) es un poco vieja para CMake 3.31.

```text
-- The CXX compiler identification is GNU 14.2.0
...
-- Configuring done (5.2s)
-- Generating done (0.1s)
-- Build files have been written to: /home/yara/Documentos/laboratorio-testing/build
```

```text
[ 25%] Built target project_lib
[ 37%] Built target gtest
[ 50%] Built target gtest_main
[ 75%] Built target run_tests
[100%] Built target gmock_main
```

### Comandos para correr las pruebas

```bash
./run_tests
ctest --output-on-failure
```

### Resultado de `./run_tests`

```text
Running main() from /home/yara/Documentos/laboratorio-testing/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 25 tests from 3 test suites.
[----------] Global test environment set-up.
[----------] 8 tests from CalculatorTest
[ RUN      ] CalculatorTest.AddPositiveNumbers
[       OK ] CalculatorTest.AddPositiveNumbers (0 ms)
[ RUN      ] CalculatorTest.AddNegativeNumbers
[       OK ] CalculatorTest.AddNegativeNumbers (0 ms)
[ RUN      ] CalculatorTest.SubtractNumbers
[       OK ] CalculatorTest.SubtractNumbers (0 ms)
[ RUN      ] CalculatorTest.MultiplyNumbers
[       OK ] CalculatorTest.MultiplyNumbers (0 ms)
[ RUN      ] CalculatorTest.DivideNumbers
[       OK ] CalculatorTest.DivideNumbers (0 ms)
[ RUN      ] CalculatorTest.DivideByZeroThrowsException
[       OK ] CalculatorTest.DivideByZeroThrowsException (0 ms)
[ RUN      ] CalculatorTest.DetectEvenNumber
[       OK ] CalculatorTest.DetectEvenNumber (0 ms)
[ RUN      ] CalculatorTest.DetectOddNumber
[       OK ] CalculatorTest.DetectOddNumber (0 ms)
[----------] 8 tests from CalculatorTest (0 ms total)

[----------] 7 tests from StringUtilsTest
[ RUN      ] StringUtilsTest.ConvertTextToUppercase
[       OK ] StringUtilsTest.ConvertTextToUppercase (0 ms)
[ RUN      ] StringUtilsTest.ConvertMixedTextToUppercase
[       OK ] StringUtilsTest.ConvertMixedTextToUppercase (0 ms)
[ RUN      ] StringUtilsTest.DetectSimplePalindrome
[       OK ] StringUtilsTest.DetectSimplePalindrome (0 ms)
[ RUN      ] StringUtilsTest.DetectPalindromeWithSpaces
[       OK ] StringUtilsTest.DetectPalindromeWithSpaces (0 ms)
[ RUN      ] StringUtilsTest.DetectNonPalindrome
[       OK ] StringUtilsTest.DetectNonPalindrome (0 ms)
[ RUN      ] StringUtilsTest.CountVowels
[       OK ] StringUtilsTest.CountVowels (0 ms)
[ RUN      ] StringUtilsTest.CountVowelsInTextWithoutVowels
[       OK ] StringUtilsTest.CountVowelsInTextWithoutVowels (0 ms)
[----------] 7 tests from StringUtilsTest (0 ms total)

[----------] 10 tests from GradeUtilsTest
[ RUN      ] GradeUtilsTest.CalculateAverage
[       OK ] GradeUtilsTest.CalculateAverage (0 ms)
[ RUN      ] GradeUtilsTest.AverageEmptyVectorThrowsException
[       OK ] GradeUtilsTest.AverageEmptyVectorThrowsException (0 ms)
[ RUN      ] GradeUtilsTest.PassingGrade
[       OK ] GradeUtilsTest.PassingGrade (0 ms)
[ RUN      ] GradeUtilsTest.FailingGrade
[       OK ] GradeUtilsTest.FailingGrade (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeA
[       OK ] GradeUtilsTest.LetterGradeA (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeB
[       OK ] GradeUtilsTest.LetterGradeB (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeC
[       OK ] GradeUtilsTest.LetterGradeC (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeD
[       OK ] GradeUtilsTest.LetterGradeD (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeF
[       OK ] GradeUtilsTest.LetterGradeF (0 ms)
[ RUN      ] GradeUtilsTest.InvalidGradeThrowsException
[       OK ] GradeUtilsTest.InvalidGradeThrowsException (0 ms)
[----------] 10 tests from GradeUtilsTest (0 ms total)

[----------] Global test environment tear-down
[==========] 25 tests from 3 test suites ran. (1 ms total)
[  PASSED  ] 25 tests.
```

### Resultado de `ctest --output-on-failure`

```text
Test project /home/yara/Documentos/laboratorio-testing/build
      Start  1: CalculatorTest.AddPositiveNumbers
 1/25 Test  #1: CalculatorTest.AddPositiveNumbers ..................   Passed    0.01 sec
      Start  2: CalculatorTest.AddNegativeNumbers
 2/25 Test  #2: CalculatorTest.AddNegativeNumbers ..................   Passed    0.01 sec
...
      Start 24: GradeUtilsTest.LetterGradeF
24/25 Test #24: GradeUtilsTest.LetterGradeF ........................   Passed    0.00 sec
      Start 25: GradeUtilsTest.InvalidGradeThrowsException
25/25 Test #25: GradeUtilsTest.InvalidGradeThrowsException .........   Passed    0.00 sec

100% tests passed, 0 tests failed out of 25

Total Test time (real) =   0.18 sec
```

Las dos formas sirven, pero son un poco distintas. `./run_tests` corre todo de una vez y muestra el detalle de Google Test. `ctest` corre cada prueba por aparte y al final da un resumen con porcentaje. El `--output-on-failure` hace que solo muestre detalles de las que fallan.

### Cantidad de pruebas

| | Cantidad |
|---|---|
| Pruebas ejecutadas | 25 |
| Pruebas que pasaron | 25 |
| Pruebas que fallaron | 0 |

### Explicación de algunas pruebas

1. **`AddPositiveNumbers`:** `EXPECT_EQ(add(2, 3), 5);` es el caso más básico, revisa que la suma de dos positivos esté bien.
2. **`DivideByZeroThrowsException`:** `EXPECT_THROW(divide(10, 0), std::invalid_argument);` revisa que al dividir entre 0 la función lance una excepción, en vez de que el programa se caiga.
3. **`DetectOddNumber`:** `EXPECT_FALSE(is_even(7));` no basta con probar que un par dé `true`, también hay que ver que un impar dé `false`.
4. **`DetectPalindromeWithSpaces`:** `EXPECT_TRUE(is_palindrome("anita lava la tina"));` revisa que la función no tome en cuenta los espacios. Sin espacios queda `anitalavalatina`, que se lee igual al revés.
5. **`CountVowels`:** `EXPECT_EQ(count_vowels("murcielago"), 5);` cuenta u, i, e, a, o. Es un buen ejemplo porque tiene las cinco vocales.
6. **`CalculateAverage`:** usa `EXPECT_DOUBLE_EQ` en vez de `EXPECT_EQ` porque el resultado es un `double`, y con decimales puede haber errores pequeñitos de redondeo.
7. **`AverageEmptyVectorThrowsException`:** revisa que no se pueda sacar el promedio de una lista vacía, porque sería dividir entre 0.
8. **`PassingGrade` y `FailingGrade`:** prueban 70 (gana) y 69 (pierde), que son justo los dos lados del límite.

## Preguntas de reflexión (Parte 3)

**1. ¿Qué significa que una prueba pase?**
Que todas sus comparaciones salieron bien, o sea, la función dio lo que se esperaba. Google Test lo marca con `[       OK ]`.

**2. ¿Qué significa que una prueba falle?**
Que por lo menos una comparación no salió como se esperaba. Se marca con `[  FAILED  ]` y se muestra el archivo, la línea, el valor esperado y el que salió. El error puede estar en el código o en la misma prueba.

**3. ¿Qué diferencia hay entre probar una función manualmente y probarla con Google Test?**
A mano habría que hacer un `main`, imprimir el resultado y revisarlo cada vez. Con Google Test la comparación ya está escrita, se corren todas juntas y el resultado es claro: pasa o falla.

**4. ¿Por qué las pruebas unitarias deben ser rápidas?**
Porque se corren muchas veces, cada vez que se cambia algo. Estas 25 tardaron 1 ms. Si tardaran mucho, nadie las correría seguido.

**5. ¿Por qué las pruebas unitarias deben ser deterministas?**
Porque si una prueba a veces pasa y a veces no, sin cambiar nada, ya no se puede confiar en ella. Si falla, tiene que ser porque hay un error, no por suerte.

---

## EXPECT vs ASSERT (Parte 4)

### Pruebas agregadas en `tests/test_calculator.cpp`

```cpp
TEST(CalculatorTest, MultipleExpectChecks) {
    EXPECT_EQ(add(1, 1), 2);
    EXPECT_EQ(add(2, 2), 4);
    EXPECT_EQ(add(3, 3), 6);
}

TEST(CalculatorTest, AssertBeforeDivision) {
    int divisor = 2;

    ASSERT_NE(divisor, 0);

    EXPECT_EQ(divide(10, divisor), 5);
}
```

### Con `divisor = 2`

```bash
make
./run_tests --gtest_filter='CalculatorTest.*'
```

```text
Note: Google Test filter = CalculatorTest.*
[==========] Running 10 tests from 1 test suite.
...
[ RUN      ] CalculatorTest.MultipleExpectChecks
[       OK ] CalculatorTest.MultipleExpectChecks (0 ms)
[ RUN      ] CalculatorTest.AssertBeforeDivision
[       OK ] CalculatorTest.AssertBeforeDivision (0 ms)
[----------] 10 tests from CalculatorTest (0 ms total)

[----------] Global test environment tear-down
[==========] 10 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 10 tests.
```

Como 2 no es 0, el `ASSERT_NE` pasa, la prueba sigue y `divide(10, 2)` da 5, así que todo pasa.

### Con `divisor = 0`

Se cambió el valor por un momento:

```bash
sed -i 's/int divisor = 2;/int divisor = 0;/' ../tests/test_calculator.cpp
make
./run_tests --gtest_filter='CalculatorTest.*'
```

```text
[ RUN      ] CalculatorTest.AssertBeforeDivision
/home/yara/Documentos/laboratorio-testing/tests/test_calculator.cpp:45: Failure
Expected: (divisor) != (0), actual: 0 vs 0
[  FAILED  ] CalculatorTest.AssertBeforeDivision (0 ms)
[----------] 10 tests from CalculatorTest (0 ms total)

[----------] Global test environment tear-down
[==========] 10 tests from 1 test suite ran. (0 ms total)
[  PASSED  ] 9 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] CalculatorTest.AssertBeforeDivision

 1 FAILED TEST
```

Solo sale un error, el del `ASSERT_NE` en la línea 45. No sale nada de `divide`, porque esa línea ni siquiera se llegó a ejecutar.

### ¿Por qué `ASSERT_NE` detuvo la prueba?

Porque cuando un `ASSERT_` falla, la prueba se termina ahí mismo. Por eso nunca se llamó a `divide(10, 0)`. Si se hubiera llamado, habría lanzado una excepción y el error habría sido más confuso.

Después se volvió a poner el 2:

```bash
sed -i 's/int divisor = 0;/int divisor = 2;/' ../tests/test_calculator.cpp
make
./run_tests | tail -n 3
```

```text
[----------] Global test environment tear-down
[==========] 27 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 27 tests.
```

### Diferencia entre `EXPECT_` y `ASSERT_`

| | `EXPECT_...` | `ASSERT_...` |
|---|---|---|
| Si falla | Marca el error y sigue | Marca el error y para la prueba |
| Lo bueno | Muestra todos los errores de una vez | No ejecuta cosas que ya no tienen sentido |
| Ejemplo | `MultipleExpectChecks`: si falla una suma, igual revisa las otras | `AssertBeforeDivision`: si el divisor es 0, no intenta dividir |

## Preguntas de reflexión (Parte 4)

**1. ¿Cuándo conviene usar `EXPECT_`?**
Casi siempre, cuando las comparaciones no dependen unas de otras. Así, si fallan varias, se ven todas de una vez.

**2. ¿Cuándo conviene usar `ASSERT_`?**
Cuando lo que sigue en la prueba depende de esa condición. Por ejemplo, revisar que un puntero no sea nulo antes de usarlo, o como aquí, que el divisor no sea 0 antes de dividir.

**3. ¿Qué podría pasar si se usa `EXPECT_` cuando en realidad se necesitaba detener la prueba?**
La prueba seguiría con datos malos y podría lanzar excepciones raras o hasta caerse todo el programa de pruebas, con mensajes que confunden más de lo que ayudan.

**4. ¿Qué podría pasar si se usa `ASSERT_` en exceso?**
Al primer error la prueba se para y no se ven los demás. Habría que arreglar uno, volver a correr, ver el siguiente, y así. Se tarda más en encontrar todo.
