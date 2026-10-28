# Laboratorio: pruebas de software con Google Test, cobertura y GitHub Actions

**Curso:** IE0417 - Diseño de software para ingeniería
**Estudiante:** Yara Izaguirre Pérez - C33910
**Modalidad:** Individual

![C++ testing](https://github.com/280504yar/laboratorio-testing/actions/workflows/testing.yml/badge.svg)

## Resumen

En este lab se hizo un proyecto pequeño en C++ con tres módulos (`calculator`, `string_utils` y `grade_utils`) y se probó con 48 pruebas unitarias usando Google Test. También se midió la cobertura con `gcov` y `lcov`, y se configuró GitHub Actions para que las pruebas se corran solas en cada push.

| Resultado | Valor |
|---|---|
| Pruebas en total | 48 (14 de calculadora, 10 de texto, 24 de notas) |
| Pruebas que pasan | 48 de 48 |
| Cobertura de líneas | 100% (54/54) |
| Cobertura de funciones | 100% (11/11) |
| Cobertura de ramas | 87.5% (42/48) |
| GitHub Actions | #1 bien, #2 falló (a propósito), #3 bien (corregido), #4 bien (mini reto); las siguientes son de la documentación |

## Índice

| Documento | Partes del enunciado | Qué tiene |
|---|---|---|
| [parte1-plan-pruebas.md](docs/parte1-plan-pruebas.md) | 1 y 9 | CMake, Google Test, estructura del proyecto y tabla de casos funcionales |
| [parte2-pruebas-unitarias.md](docs/parte2-pruebas-unitarias.md) | 2, 3 y 4 | Módulos, primeras pruebas, `./run_tests`, `ctest`, EXPECT vs ASSERT |
| [parte3-pruebas-fallidas.md](docs/parte3-pruebas-fallidas.md) | 5 | Error a propósito en `is_even` y su corrección |
| [parte4-casos-borde.md](docs/parte4-casos-borde.md) | 6 y 7 | Casos borde y semillas |
| [parte5-cobertura.md](docs/parte5-cobertura.md) | 8 | Cobertura con lcov (líneas, funciones y ramas) |
| [parte6-github-actions.md](docs/parte6-github-actions.md) | 10 y 11 | Workflow de GitHub Actions y fallo a propósito en CI |
| [reflexion-final.md](docs/reflexion-final.md) | 12 y 13 | Mini reto `is_valid_grade` y reflexión final |

## Estructura

```text
laboratorio-testing/
├── README.md
├── CMakeLists.txt
├── .gitignore
├── include/          # calculator.h, string_utils.h, grade_utils.h
├── src/              # calculator.cpp, string_utils.cpp, grade_utils.cpp
├── tests/            # test_calculator.cpp, test_string_utils.cpp, test_grade_utils.cpp
├── docs/             # documentación de cada parte (y docs/img con capturas)
└── .github/workflows/testing.yml
```

Como este repo es solo de este lab, `laboratorio-testing` es la raíz, y por eso el workflow no usa `cd laboratorio-testing`.

## Ambiente

- Debian 13 (trixie) con KDE Plasma
- g++ 14.2.0, cmake 3.31.6, GNU Make 4.4.1, git 2.47.3, lcov 2.0-1
- Google Test 1.12.1 (lo baja CMake con `FetchContent`)

## Cómo compilar y correr las pruebas

```bash
mkdir build
cd build
cmake ..
make
./run_tests
ctest --output-on-failure
```

## Cómo medir la cobertura

```bash
rm -rf build && mkdir build && cd build
cmake -DENABLE_COVERAGE=ON ..
make
./run_tests
lcov --capture --directory . --output-file coverage.info --rc branch_coverage=1 --ignore-errors inconsistent,inconsistent,mismatch,mismatch
lcov --remove coverage.info '/usr/*' '*/_deps/*' '*/tests/*' --output-file coverage_filtered.info --rc branch_coverage=1 --ignore-errors unused,inconsistent,mismatch
genhtml coverage_filtered.info --output-directory coverage_report --branch-coverage --ignore-errors inconsistent,inconsistent,mismatch
firefox "$PWD/coverage_report/index.html"
```

Los `--ignore-errors` hacen falta con lcov 2 (el que trae Debian 13). La explicación está en [parte5-cobertura.md](docs/parte5-cobertura.md).
