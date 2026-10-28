# Parte 3: pruebas fallidas y corrección de errores

## Cambio incorrecto que se hizo

En `src/calculator.cpp` se metió un error a propósito en la función `is_even`:

```cpp
// Bien
bool is_even(int number) {
    return number % 2 == 0;
}

// Mal (error a propósito)
bool is_even(int number) {
    return number % 2 == 1;
}
```

Con `== 1` la función da `true` para los impares, o sea, hace justo lo contrario de lo que debería.

### Comandos

```bash
sed -i 's/return number % 2 == 0;/return number % 2 == 1;/' ../src/calculator.cpp
grep -n "number % 2" ../src/calculator.cpp
make
./run_tests
```

```text
25:    return number % 2 == 1;
[  6%] Building CXX object CMakeFiles/project_lib.dir/src/calculator.cpp.o
[ 12%] Linking CXX static library libproject_lib.a
...
[ 75%] Built target run_tests
```

Compiló sin ningún problema. El compilador no tiene forma de saber que `== 1` está mal.

## Pruebas que fallaron

```text
[ RUN      ] CalculatorTest.DetectEvenNumber
/home/yara/Documentos/laboratorio-testing/tests/test_calculator.cpp:29: Failure
Value of: is_even(8)
  Actual: false
Expected: true
[  FAILED  ] CalculatorTest.DetectEvenNumber (0 ms)
[ RUN      ] CalculatorTest.DetectOddNumber
/home/yara/Documentos/laboratorio-testing/tests/test_calculator.cpp:33: Failure
Value of: is_even(7)
  Actual: true
Expected: false
[  FAILED  ] CalculatorTest.DetectOddNumber (0 ms)
...
[==========] 27 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 25 tests.
[  FAILED  ] 2 tests, listed below:
[  FAILED  ] CalculatorTest.DetectEvenNumber
[  FAILED  ] CalculatorTest.DetectOddNumber

 2 FAILED TESTS
```

Fallaron 2 de las 27 pruebas: `DetectEvenNumber` y `DetectOddNumber`. Las otras 25 siguieron pasando porque no usan `is_even`.

## Mensaje de Google Test

En cada error sale:

- **Dónde está:** `tests/test_calculator.cpp:29` y `:33`.
- **Qué se revisó:** `Value of: is_even(8)` y `Value of: is_even(7)`.
- **Qué salió:** `Actual: ...`
- **Qué se esperaba:** `Expected: ...`

## Lo que se esperaba y lo que salió

| Prueba | Llamada | Se esperaba | Salió |
|---|---|---|---|
| `DetectEvenNumber` | `is_even(8)` | `true` | `false` |
| `DetectOddNumber` | `is_even(7)` | `false` | `true` |

Los dos resultados salieron al revés, entonces el problema tenía que estar en la condición `number % 2 == ...` de `is_even`.

## Corrección

Se volvió a dejar en `== 0`:

```bash
sed -i 's/return number % 2 == 1;/return number % 2 == 0;/' ../src/calculator.cpp
grep -n "number % 2" ../src/calculator.cpp
make
./run_tests | tail -n 3
```

```cpp
bool is_even(int number) {
    return number % 2 == 0;
}
```

## Resultado final

```text
25:    return number % 2 == 0;
...
[----------] Global test environment tear-down
[==========] 27 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 27 tests.
```

Las 27 pruebas volvieron a pasar.

## Preguntas de reflexión

**1. ¿Cómo ayudó Google Test a identificar el error?**
Apenas se corrieron las pruebas, dijo cuáles dos fallaban, en qué líneas, y que los resultados salían al revés. Como las dos solo usan `is_even`, quedó claro que el error estaba ahí, sin tener que revisar todo el proyecto.

**2. ¿Qué información útil muestra una prueba fallida?**
El nombre de la prueba, el archivo y la línea, qué se estaba revisando, qué se esperaba y qué salió. Al final también da un resumen de cuántas pasaron y cuáles fallaron.

**3. ¿Por qué es importante ejecutar pruebas después de modificar código?**
Porque un cambio pequeñito, como poner un 1 en vez de un 0, puede dañar algo que antes servía, y el compilador no avisa. Las pruebas confirman que todo sigue funcionando.

**4. ¿Qué riesgo existe si se cambia código y no se ejecutan las pruebas?**
Que el error se quede escondido y llegue hasta la rama principal o a los usuarios. Después cuesta más encontrarlo porque ya no se sabe qué cambio lo causó.
