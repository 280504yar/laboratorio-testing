# Parte 4: casos borde y semillas

## ¿Qué es un caso borde?

Es un valor que está justo en el límite de lo que una función acepta, o un caso especial: el primero o el último de un rango, el cero, un texto vacío, una sola letra, etc. En esos puntos es donde más fácil se cometen errores, por ejemplo poner `>` cuando era `>=`.

## Casos borde que se agregaron

### En `tests/test_calculator.cpp`

```cpp
TEST(CalculatorTest, DivideNegativeNumbers) {
    EXPECT_EQ(divide(-10, -2), 5);
}

TEST(CalculatorTest, DividePositiveByNegative) {
    EXPECT_EQ(divide(10, -2), -5);
}

TEST(CalculatorTest, ZeroIsEven) {
    EXPECT_TRUE(is_even(0));
}
```

### En `tests/test_string_utils.cpp`

```cpp
TEST(StringUtilsTest, EmptyStringToUppercase) {
    EXPECT_EQ(to_uppercase(""), "");
}

TEST(StringUtilsTest, EmptyStringIsPalindrome) {
    EXPECT_TRUE(is_palindrome(""));
}

TEST(StringUtilsTest, SingleLetterIsPalindrome) {
    EXPECT_TRUE(is_palindrome("a"));
}
```

### En `tests/test_grade_utils.cpp`

```cpp
TEST(GradeUtilsTest, MinimumValidGrade) {
    EXPECT_EQ(letter_grade(0), 'F');
}

TEST(GradeUtilsTest, MaximumValidGrade) {
    EXPECT_EQ(letter_grade(100), 'A');
}

TEST(GradeUtilsTest, NegativeGradeThrowsException) {
    EXPECT_THROW(letter_grade(-1), std::invalid_argument);
}

TEST(GradeUtilsTest, GradeBoundaryBetweenFAndD) {
    EXPECT_EQ(letter_grade(59), 'F');
    EXPECT_EQ(letter_grade(60), 'D');
}

TEST(GradeUtilsTest, GradeBoundaryBetweenDAndC) {
    EXPECT_EQ(letter_grade(69), 'D');
    EXPECT_EQ(letter_grade(70), 'C');
}

TEST(GradeUtilsTest, GradeBoundaryBetweenCAndB) {
    EXPECT_EQ(letter_grade(79), 'C');
    EXPECT_EQ(letter_grade(80), 'B');
}

TEST(GradeUtilsTest, GradeBoundaryBetweenBAndA) {
    EXPECT_EQ(letter_grade(89), 'B');
    EXPECT_EQ(letter_grade(90), 'A');
}
```

## ¿Por qué son importantes estos casos?

| Caso | Por qué sirve |
|---|---|
| División con negativos | Para ver que los signos salgan bien (`-10 / -2 = 5` y `10 / -2 = -5`). |
| `is_even(0)` | El 0 es par, pero es fácil olvidarlo. |
| Texto vacío | Para ver que la función no se caiga ni devuelva basura si no hay nada que procesar. |
| Una sola letra | Es el palíndromo más pequeño que existe. |
| Notas 0 y 100 | Son los extremos del rango y se tienen que aceptar. |
| Nota -1 | Es el primer valor que ya no es válido por abajo. |
| 59/60, 69/70, 79/80, 89/90 | Son los puntos donde cambia la letra. Si alguien pone `grade > 90` en vez de `>= 90`, el 90 daría B y esta prueba lo detectaría. |

## Resultado de las pruebas

```bash
make
./run_tests
```

```text
[==========] Running 40 tests from 3 test suites.
[----------] 13 tests from CalculatorTest
...
[ RUN      ] CalculatorTest.DivideNegativeNumbers
[       OK ] CalculatorTest.DivideNegativeNumbers (0 ms)
[ RUN      ] CalculatorTest.DividePositiveByNegative
[       OK ] CalculatorTest.DividePositiveByNegative (0 ms)
[ RUN      ] CalculatorTest.ZeroIsEven
[       OK ] CalculatorTest.ZeroIsEven (0 ms)
[----------] 13 tests from CalculatorTest (0 ms total)

[----------] 10 tests from StringUtilsTest
...
[ RUN      ] StringUtilsTest.EmptyStringToUppercase
[       OK ] StringUtilsTest.EmptyStringToUppercase (0 ms)
[ RUN      ] StringUtilsTest.EmptyStringIsPalindrome
[       OK ] StringUtilsTest.EmptyStringIsPalindrome (0 ms)
[ RUN      ] StringUtilsTest.SingleLetterIsPalindrome
[       OK ] StringUtilsTest.SingleLetterIsPalindrome (0 ms)
[----------] 10 tests from StringUtilsTest (0 ms total)

[----------] 17 tests from GradeUtilsTest
...
[ RUN      ] GradeUtilsTest.MinimumValidGrade
[       OK ] GradeUtilsTest.MinimumValidGrade (0 ms)
[ RUN      ] GradeUtilsTest.MaximumValidGrade
[       OK ] GradeUtilsTest.MaximumValidGrade (0 ms)
[ RUN      ] GradeUtilsTest.NegativeGradeThrowsException
[       OK ] GradeUtilsTest.NegativeGradeThrowsException (0 ms)
[ RUN      ] GradeUtilsTest.GradeBoundaryBetweenFAndD
[       OK ] GradeUtilsTest.GradeBoundaryBetweenFAndD (0 ms)
[ RUN      ] GradeUtilsTest.GradeBoundaryBetweenDAndC
[       OK ] GradeUtilsTest.GradeBoundaryBetweenDAndC (0 ms)
[ RUN      ] GradeUtilsTest.GradeBoundaryBetweenCAndB
[       OK ] GradeUtilsTest.GradeBoundaryBetweenCAndB (0 ms)
[ RUN      ] GradeUtilsTest.GradeBoundaryBetweenBAndA
[       OK ] GradeUtilsTest.GradeBoundaryBetweenBAndA (0 ms)
[----------] 17 tests from GradeUtilsTest (0 ms total)

[----------] Global test environment tear-down
[==========] 40 tests from 3 test suites ran. (1 ms total)
[  PASSED  ] 40 tests.
```

Pasaron las 40 pruebas: las 27 que ya había más los 13 casos borde nuevos.

### ¿Falló alguna?

No, las 13 pasaron a la primera, lo que quiere decir que el código ya manejaba bien esos límites. Aun así sirven, porque si alguien cambia el código después y daña algo, estas pruebas lo van a detectar.

## Preguntas de reflexión

**1. ¿Por qué no basta con probar casos normales?**
Porque casi nunca los errores están en medio del rango, sino en los límites o en casos especiales como el vacío o el cero. Un código puede pasar todos los casos normales y aun así tener errores.

**2. ¿Qué es un caso borde?**
Un valor justo en el límite de lo permitido, o un caso especial: el mínimo, el máximo, el valor justo antes o después de un cambio, un texto vacío o un solo elemento.

**3. ¿Qué es un caso inválido?**
Un valor que la función no debería aceptar, como una nota de -1 o de 120, o dividir entre 0. La prueba revisa que la función lo rechace bien, en este caso lanzando `std::invalid_argument`.

**4. ¿Qué diferencia hay entre probar `85` y probar exactamente `80`, `90` o `70`?**
El 85 está en medio del rango de la B, así que aunque la condición estuviera mal escrita igual daría B. El 80 está justo donde se cambia de C a B, entonces ahí sí se nota si la comparación está bien.

**5. ¿Cómo puede un caso borde revelar errores ocultos?**
Por ejemplo, si `letter_grade` tuviera `if (grade > 90)`, las pruebas con 95 u 85 pasarían igual, pero con 90 daría B en vez de A. El caso borde muestra un error que con los casos normales no se ve.

---

## Semillas en pruebas

### Prueba que se agregó

Se puso `#include <random>` al inicio de `tests/test_calculator.cpp` y se agregó esta prueba:

```cpp
TEST(CalculatorTest, RandomAdditionsWithFixedSeed) {
    std::mt19937 generator(12345);
    std::uniform_int_distribution<int> distribution(-100, 100);

    for (int i = 0; i < 10; i++) {
        int a = distribution(generator);
        int b = distribution(generator);

        EXPECT_EQ(add(a, b), a + b);
    }
}
```

La prueba saca 10 pares de números "al azar" entre -100 y 100 y revisa que `add` dé lo mismo que `a + b`.

### Con la semilla 12345

```bash
make
./run_tests --gtest_filter='*Random*'
```

```text
Note: Google Test filter = *Random*
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from CalculatorTest
[ RUN      ] CalculatorTest.RandomAdditionsWithFixedSeed
[       OK ] CalculatorTest.RandomAdditionsWithFixedSeed (0 ms)
[----------] 1 test from CalculatorTest (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
```

### Con la semilla 54321

```bash
sed -i 's/generator(12345)/generator(54321)/' ../tests/test_calculator.cpp
grep -n "generator(" ../tests/test_calculator.cpp
make
./run_tests --gtest_filter='*Random*'
```

```text
64:    std::mt19937 generator(54321);
...
Note: Google Test filter = *Random*
[==========] Running 1 test from 1 test suite.
[ RUN      ] CalculatorTest.RandomAdditionsWithFixedSeed
[       OK ] CalculatorTest.RandomAdditionsWithFixedSeed (0 ms)
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
```

Después se volvió a poner la semilla de antes:

```bash
sed -i 's/generator(54321)/generator(12345)/' ../tests/test_calculator.cpp
grep -n "generator(" ../tests/test_calculator.cpp
make
./run_tests | tail -n 3
```

```text
64:    std::mt19937 generator(12345);
[==========] 41 tests from 3 test suites ran. (1 ms total)
[  PASSED  ] 41 tests.
```

### ¿Qué hace la semilla?

Es el número con el que arranca el generador de números aleatorios (`std::mt19937`). En realidad el generador no es aleatorio de verdad: con la misma semilla siempre saca los mismos números en el mismo orden.

### ¿Por qué se usa una semilla fija?

Para que la prueba siempre use los mismos números, ya sea en la compu local o en GitHub Actions. Así la prueba es repetible.

### ¿Qué pasó al cambiar la semilla?

Con 54321 salieron otros 10 pares de números, pero la prueba igual pasó, porque `add` funciona bien con cualquier par de enteros. Cambiar la semilla cambia los datos, pero no el resultado que se espera.

### ¿Por qué esto ayuda a depurar?

Porque si una prueba con números aleatorios falla, con la misma semilla se puede volver a correr y el error vuelve a salir con los mismos números. Así se puede revisar con calma, arreglar y confirmar que ya funciona. Sin semilla fija, el error podría no volver a aparecer.

## Preguntas de reflexión (semillas)

**1. ¿Por qué las pruebas con datos aleatorios pueden ser peligrosas si no se controlan?**
Porque cada vez usarían números distintos y una prueba podría fallar una vez y pasar la siguiente sin haber cambiado nada. Así no se puede confiar en ella ni repetir el error.

**2. ¿Qué ventaja tiene usar una semilla fija?**
Se prueba con números variados, que tal vez no se le ocurrirían a quien hace la prueba, pero siempre son los mismos, así que el resultado se puede repetir.

**3. ¿Cómo ayuda la semilla a reproducir errores?**
Si algo falla con la semilla 12345, cualquier persona que corra la prueba con esa misma semilla va a tener los mismos números y el mismo error, y lo puede investigar.

**4. ¿Por qué es importante documentar la semilla usada?**
Porque si no se sabe qué semilla se usó, no hay forma de volver a generar los números que causaron el error.
