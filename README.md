# Elkin Skills — Instalación

Guía para instalar las skills personalizadas de **Spec-Driven Development (SDD)** del repositorio [`efruto/elkin-skills`](https://github.com/efruto/elkin-skills) en cualquier proyecto compatible.

## Requisitos previos

- Tener [Node.js y npm](https://nodejs.org/) instalados.
- Tener Git instalado si el método de instalación o el entorno lo requiere.
- Contar con un proyecto local donde se instalarán las skills.
- Utilizar un agente de programación compatible con la herramienta `skills`.

Comprueba las versiones instaladas:

```bash
node --version
npm --version
git --version
```

## Skills disponibles

| Skill | Propósito |
|---|---|
| `spec` | Analiza una solicitud y genera una especificación funcional y técnica verificable antes de implementar. |
| `spec-impl` | Implementa una especificación existente, ejecuta las comprobaciones disponibles y documenta los resultados. |

## Instalar en un proyecto

Abre una terminal en la raíz del proyecto donde deseas instalar las skills:

```bash
cd ruta/a/mi-proyecto
```

### Instalar únicamente `spec`

```bash
npx skills add https://github.com/efruto/elkin-skills --skill spec
```

### Instalar únicamente `spec-impl`

```bash
npx skills add https://github.com/efruto/elkin-skills --skill spec-impl
```

### Instalar ambas skills

```bash
npx skills add https://github.com/efruto/elkin-skills --skill spec --skill spec-impl
```

### Consultar las skills disponibles

```bash
npx skills add https://github.com/efruto/elkin-skills --list
```

Durante la instalación, revisa las opciones que presente la CLI y selecciona el agente de programación y el destino apropiados para tu entorno.

## Instalar específicamente para OpenCode

Para solicitar la instalación dirigida a OpenCode:

```bash
npx skills add https://github.com/efruto/elkin-skills --skill spec --skill spec-impl --agent opencode
```

Las opciones disponibles pueden variar según la versión de la CLI. Si el instalador muestra una confirmación, revisa el destino antes de aceptarla.

## Estructura esperada en el proyecto

La ubicación exacta depende del agente y de las opciones elegidas. Si se instalan en `.agents/skills/`, la estructura será similar a esta:

```text
mi-proyecto/
├── .agents/
│   └── skills/
│       ├── spec/
│       │   ├── SKILL.md
│       │   └── template.md
│       └── spec-impl/
│           └── SKILL.md
├── src/
├── tests/
└── README.md
```

Verifica que existan los archivos `SKILL.md` y que el agente utilizado reconozca esa ubicación. Algunos agentes pueden usar otro directorio de instalación.

## Instalación global (opcional)

Si quieres instalar las skills para utilizarlas en varios proyectos de tu usuario, puedes solicitar una instalación global:

```bash
npx skills add https://github.com/efruto/elkin-skills --skill spec --skill spec-impl --agent opencode --global
```

La disponibilidad global depende de que el agente sea compatible y descubra la ubicación global de skills. Para equipos o proyectos que deban reproducir exactamente su entorno, se recomienda la instalación por proyecto.

## Flujo de trabajo recomendado

### 1. Generar una especificación

Desde el agente de IA, solicita utilizar `spec`. Por ejemplo:

```text
Utiliza la skill spec para analizar el proyecto y generar
una especificación para la funcionalidad de recuperación de contraseña.
No implementes código todavía.
```

Revisa la especificación generada y resuelve las preguntas pendientes antes de implementarla.

### 2. Implementar la especificación

Cuando la especificación esté revisada, solicita utilizar `spec-impl`:

```text
Utiliza la skill spec-impl para implementar
specs/01-recuperacion-contrasena.md.
Ejecuta las pruebas disponibles y documenta los resultados reales.
```

Cambia la ruta por la ubicación y el nombre reales del archivo de especificación.

### 3. Revisar los resultados

Comprueba que el agente informe:

- Archivos creados o modificados.
- Requisitos implementados.
- Pruebas realmente ejecutadas y sus resultados.
- Comprobaciones que no se pudieron ejecutar.
- Requisitos pendientes, riesgos y limitaciones.

## Actualizar las skills

Para actualizar las skills instaladas, ejecuta:

```bash
npx skills update
```

Comprueba la ayuda de la versión instalada si el comando o las opciones difieren:

```bash
npx skills --help
```

Después de actualizar, verifica que los archivos de las skills correspondan a la versión que esperas utilizar.

## Skills y comandos de barra en OpenCode

Una **skill** proporciona instrucciones que el agente puede consultar. Instalar una skill no garantiza que se cree automáticamente un comando de barra como `/spec` o `/spec-impl`.

Si quieres utilizar comandos como:

```text
/spec specs/01-recuperacion-contrasena.md
/spec-impl specs/01-recuperacion-contrasena.md
```

configura por separado los comandos de OpenCode para que invoquen o sigan las instrucciones de las skills instaladas. La configuración exacta depende de la versión y de la estructura del proyecto.

## Solución de problemas

- **No se encuentra una skill:** comprueba el nombre indicado en `--skill`, la estructura del repositorio y la ubicación donde se instaló.
- **El agente no reconoce la skill:** verifica que el agente sea compatible y que esté configurado para descubrir el directorio de destino.
- **No aparece `/spec` o `/spec-impl`:** recuerda que instalar skills no crea necesariamente comandos de barra; configura esos comandos por separado si los necesitas.
- **La actualización no funciona:** consulta `npx skills --help` y confirma que las skills fueron instaladas con la CLI.
- **El repositorio no es accesible:** comprueba que la URL de GitHub sea correcta y que el repositorio sea público, o configura la autenticación necesaria si es privado.

## Repositorio

- GitHub: <https://github.com/efruto/elkin-skills>

> **Recomendación:** instala las skills por proyecto cuando necesites que el equipo comparta y audite las mismas instrucciones. Utiliza la instalación global para uso personal en varios proyectos, siempre que el agente la admita.
