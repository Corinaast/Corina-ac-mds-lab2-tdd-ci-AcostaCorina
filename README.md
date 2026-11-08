# String Calculator — kata resuelta con TDD

[![CI](https://github.com/Corinaast/string-calculator-tdd/actions/workflows/ci.yml/badge.svg)](https://github.com/Corinaast/string-calculator-tdd/actions/workflows/ci.yml)

Implementación de la kata **String Calculator** desarrollada con el ciclo *rojo → verde → refactor*: primero la prueba que falla, después el código mínimo para pasarla y al final la limpieza.

El repositorio no solo contiene la solución: contiene el **proceso**, con una iteración por commit, una rama de trabajo y CI en GitHub Actions.

![Java](https://img.shields.io/badge/Java-17-007396?logo=openjdk&logoColor=white)
![JUnit 5](https://img.shields.io/badge/JUnit-5-25A162?logo=junit5&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-3.9-C71A36?logo=apachemaven&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)

---

## La kata

Una calculadora que recibe una cadena y devuelve la suma de los números que contiene. Cada iteración añade una regla.

| # | Regla | Entrada | Salida |
|---|---|---|---|
| 1 | Cadena vacía | `""` | `0` |
| 2 | Un número | `"5"` | `5` |
| 3 | Dos números separados por coma | `"1,2"` | `3` |
| 4 | N números | `"1,2,3,4"` | `10` |
| 5 | Saltos de línea como separador | `"1\n2,3"` | `6` |
| 6 | Delimitador personalizado | `"//;\n1;2"` | `3` |
| 7 | Delimitadores múltiples entre corchetes | `"//[***]\n1***2***3"` | `6` |
| 8 | Números negativos → excepción con la lista completa | `"2,-4,3,-5"` | `IllegalArgumentException` |
| 9 | Ignorar números mayores que 1000 | `"2,1001"` | `2` |

También se cubre la entrada `null`, que devuelve `0`.

## Decisiones de diseño

`StringCalculator.add` es un orquestador corto que delega en tres pasos:

1. **Configuración** — `getDelimiterConfig` detecta el delimitador (coma, salto de línea o el declarado con `//`) y separa la parte numérica.
2. **Procesamiento** — recorre los números, acumula la suma y registra los negativos encontrados.
3. **Validación** — si hubo negativos, lanza una excepción con **todos** los negativos, no solo el primero.

Esa separación evita el método gigante con condicionales anidados y hace que cada regla se pueda probar por separado.

## Pruebas

```bash
mvn -B test
```

- **9 pruebas** con JUnit 5, una o más por iteración.
- Surefire configurado con `useModulePath=false` para ejecutar las pruebas en el classpath.
- Cada prueba se nombró por comportamiento: `testCustomDelimiter`, `testNegativeNumbersThrowException`, `testIgnoreNumbersGreaterThan1000`.

## Integración continua

El flujo de trabajo [`.github/workflows/ci.yml`](.github/workflows/ci.yml) se ejecuta en cada push a `main` y a ramas `feature/*`, y en cada pull request:

```yaml
- name: Set up JDK
  uses: actions/setup-java@v4
  with:
    distribution: 'temurin'
    java-version: '17'

- name: Build and test
  run: mvn -B test
```

El trabajo se desarrolló en la rama `feature/kata-tdd` y se integró en `main`; cada push a `main`, a una rama `feature/*` o a un pull request pasa por la compilación y las pruebas.

## Cómo ejecutar

Requisitos: JDK 17+ y Maven 3.9+.

```bash
git clone https://github.com/Corinaast/string-calculator-tdd.git
cd string-calculator-tdd
mvn -B test
```

Para ver el detalle de cada prueba:

```bash
mvn -B test -Dsurefire.printSummary=true
# reportes HTML: target/site/surefire-report.html (mvn site)
```

## Estructura

```
.
├── pom.xml
├── .github/workflows/ci.yml
└── src/
    ├── main/java/Ejecutable/StringCalculator.java
    └── test/java/Ejecutable/StringCalculatorTest.java
```

## Autora

**Corina Acosta** — [@Corinaast](https://github.com/Corinaast)

Laboratorio de **Métodos de Desarrollo de Software** (ESPE): TDD, control de versiones con ramas y integración continua.
