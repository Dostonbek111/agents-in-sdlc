---
title: "Lección 1: Instala GitHub Copilot CLI"
description: "Instala y autentica Copilot CLI en tu Codespace, familiarízate con la herramienta y localiza la incidencia de filtrado del backlog."
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

[GitHub Copilot CLI][about-copilot-cli] es un potente asistente de programación con agentes que se ejecuta en el terminal. Permite explorar bases de código, generar código, ejecutar comandos e interactuar con herramientas externas mediante Model Context Protocol (MCP), que conecta el agente con herramientas externas. Puedes delegar tareas, solicitar cambios y mantener la concentración. El primer paso es instalar la herramienta; para ello usarás herramientas que ya conoces.

En esta lección:

- instalarás GitHub Copilot CLI mediante npm.
- te autenticarás con tu cuenta de GitHub.
- confiarás en el repositorio del taller y probarás una conversación.
- localizarás la incidencia de filtrado con el servidor MCP integrado de GitHub.

## Escenario

Tu equipo está empezando a usar agentes de IA para abordar un backlog cada vez mayor. Copilot CLI incorpora esta capacidad al terminal, donde ya trabajan habitualmente muchos desarrolladores. En esta lección instalarás y autenticarás la herramienta para utilizarla durante el resto del taller.

## Instala Copilot CLI

Puedes instalar Copilot CLI con [npm][install-cli], WinGet o Homebrew. Como GitHub Codespaces ya incluye Node.js, usarás npm:

1. Vuelve al Codespace y abre un terminal.
2. Comprueba que Node.js está instalado y cumple el requisito de versión:

   ```bash
   node --version
   ```

   La versión debe ser 24 o posterior.

3. Instala Copilot CLI de forma global:

   ```bash
   npm install -g @github/copilot
   ```

4. Comprueba la instalación:

   ```bash
   copilot --version
   ```

   Se mostrará el número de versión.

## Autentícate con GitHub

La primera vez que inicies Copilot CLI, se te pedirá que te autentiques con tu cuenta de GitHub:

1. Inicia Copilot CLI:

   ```bash
   copilot
   ```

2. Si se te solicita, sigue las instrucciones del código de dispositivo para autenticarte y autorizar Copilot CLI.
3. Copilot CLI mostrará este mensaje:

   ```plaintext
   Copilot can read files in this folder and, with your permission, edit them or run code and shell commands. It will remember your permissions for the rest of this session.

   Do you trust the files in this folder?
   ```

4. Comprueba que la ruta corresponde al repositorio de Tailspin Toys y selecciona **Yes, and remember this folder for future sessions**.

> [!NOTE]
> En un Codespace, es posible que ya estés autenticado mediante tu sesión de GitHub. Si Copilot CLI se inicia sin pedir autenticación, ya puedes continuar.

## Familiarízate con la herramienta

Los comandos que escribes en el prompt habitual del shell se ejecutan directamente en el Codespace. Cuando se inicia Copilot CLI, el lenguaje natural se envía al agente y los comandos de barra controlan la conversación:

1. Escribe `/model`, usa las teclas de flecha para seleccionar **Auto**, pulsa <kbd>Enter</kbd> y vuelve a pulsar <kbd>Enter</kbd> para confirmar.
2. Escribe `/help` para ver los comandos disponibles en la versión instalada y pulsa <kbd>Esc</kbd> para cerrar la ayuda.
3. Haz una pregunta sencilla para comprobar que todo funciona:

   ```plaintext
   ¿Cuáles son los archivos principales de este proyecto?
   ```

4. Lee la respuesta y observa cómo Copilot explora el repositorio antes de responder.
5. Escribe `/mcp list` y confirma que está disponible el servidor MCP integrado de GitHub.
6. Pide a Copilot que encuentre la incidencia de filtrado:

   ```plaintext
   Usa GitHub MCP para buscar en este repositorio la incidencia titulada "Allow users to filter games by category and publisher". Dame su URL y un breve resumen. No hagas cambios.
   ```

7. Abre la URL y lee la incidencia. La usarás después de hacer un primer cambio breve.

> [!TIP]
> Una sesión normal de Copilot CLI trabaja en la rama que está activa en el terminal; no crea automáticamente un árbol de trabajo. Crearás una rama de funcionalidad antes de cada cambio.

## Usa el acceso directo del taller

Normalmente, Copilot CLI pide permiso antes de utilizar herramientas que no estén incluidas en los permisos establecidos. En este taller, lo volverás a iniciar con `--yolo`, un acceso directo aprobado por el usuario que elimina esas solicitudes de aprobación dentro del Codespace para que puedas centrarte en los ejercicios.

> [!CAUTION]
> `--yolo` habilita los permisos automáticos completos (`--allow-all-tools`, `--allow-all-paths` y `--allow-all-urls`). Úsalo solo en un entorno aislado, como un Codespace o una máquina virtual; no lo configures como opción predeterminada para el desarrollo diario. Consulta [Permitir y denegar el uso de herramientas][allow-all-warning].

En este taller, `--enable-all-github-mcp-tools` habilita las herramientas MCP de lectura y escritura de GitHub que se utilizarán en lecciones posteriores para trabajar con incidencias y PR. Aunque el Codespace limita el acceso a tu equipo local, los recursos de GitHub autenticados son reales. Revisa los cambios antes de publicarlos o combinarlos.

1. Sal de Copilot CLI con `/exit`.
2. Vuelve a iniciarlo desde la raíz del repositorio:

   ```bash
   copilot --yolo --enable-all-github-mcp-tools
   ```

3. Haz otra pregunta breve sobre el proyecto para confirmar que la conversación funciona y sal con `/exit`.

Copilot guarda las conversaciones automáticamente. Más adelante, cuando cambies las instrucciones o añadas un agente, utilizarás `copilot --resume` para volver a la misma conversación y rama de la funcionalidad.

## Resumen y pasos siguientes

Has instalado y autenticado GitHub Copilot CLI. En esta lección:

- has instalado Copilot CLI mediante npm.
- te has autenticado con tu cuenta de GitHub.
- has confiado en el repositorio del taller y probado una conversación.
- has localizado la incidencia de filtrado mediante el servidor MCP integrado de GitHub.

A continuación, [iniciarás tu primer cambio centrado][next-lesson] y utilizarás Copilot CLI para mostrar las valoraciones con estrellas en las tarjetas de juegos.

## Recursos

- [Instalar GitHub Copilot CLI][install-cli]
- [Acerca de GitHub Copilot CLI][about-copilot-cli]
- [Referencia de comandos de Copilot CLI][cli-reference]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-add-star-rating/
[install-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[allow-all-warning]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli#allowing-and-denying-tool-use
