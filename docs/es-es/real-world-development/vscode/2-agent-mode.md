---
title: "Ejercicio 2: Añade funcionalidad con el modo de agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso los cambios más sencillos de una aplicación suelen afectar a varios archivos y requieren operaciones como ejecutar pruebas. El flujo habitual de un desarrollador puede incluir localizar los archivos necesarios, hacer cambios, ejecutar pruebas, depurar, descubrir qué archivo falta y volver a modificarlo. Este proceso puede alargarse.

El modo de agente de Copilot puede ayudar. Trabaja de forma más autónoma en el entorno de desarrollo integrado (IDE): explora la estructura del proyecto, realiza cambios coordinados, ejecuta tareas como las pruebas y corrige automáticamente los errores que encuentra. En este ejercicio, lo usarás para añadir una funcionalidad al sitio.

> [!NOTE]
> Aunque los nombres se parecen, el modo de agente y el agente en la nube ofrecen experiencias distintas. El modo de agente trabaja en el IDE y permite ciclos de respuesta rápidos e interactivos. El agente en la nube trabaja de forma asíncrona, como un miembro del equipo, y se comunica contigo mediante incidencias y solicitudes de incorporación de cambios.

En este ejercicio aprenderás cómo:

- el modo de agente de Copilot explora el proyecto, identifica los archivos pertinentes y coordina cambios.
- el modo de agente de GitHub Copilot implementa funcionalidades en la interfaz y la capa de datos.
- revisar los cambios y las pruebas generados por Copilot antes de combinarlos en el código base.

## Escenario

A medida que aumenta la lista de juegos, quieres permitir que los usuarios filtren por categoría y editorial. En el ejercicio anterior añadiste un método auxiliar para las editoriales; ahora completarás con el modo de agente de Copilot el resto del trabajo en la capa de datos, la interfaz y las pruebas.

## Ejecuta el sitio de Tailspin Toys

Antes de hacer cambios, explora el sitio para entender su funcionamiento actual.

El sitio es una plataforma de financiación colectiva de juegos de mesa con temática de desarrollo. Permite mostrar juegos y sus detalles. Es una única aplicación Astro que genera páginas en lenguaje de marcado de hipertexto (HTML) estáticas durante la compilación. Las páginas consultan directamente una base de datos SQLite local mediante Drizzle ORM desde el frontmatter; no hay una interfaz de programación de aplicaciones (API) de backend independiente ni un framework de interfaz en el cliente. Los métodos reutilizables de acceso a datos se encuentran en `src/lib/`, y la interactividad se implementa con pequeños elementos `<script>` de Astro que usan las interfaces estándar del modelo de objetos del documento (DOM).

### Inicia el sitio

La tarea `npm run dev` inicia el único servidor de desarrollo de Astro. Para ejecutarlo en GitHub Codespaces:

1. Vuelve al Codespace. Después del ejercicio anterior, deberías estar en `main`.
2. Abre un terminal con <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Crea una rama para el trabajo de filtrado:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Inicia el sitio:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo de Astro esté listo, debería aparecer un mensaje parecido a este:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo para abrir `http://localhost:4321` en un navegador, ciérralo seleccionando **x**.

5. Abre el sitio manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando `http://localhost:4321` en el terminal.

> [!NOTE]
> En Codespaces, al seleccionar el enlace localhost en el terminal se redirige a una URL como `https://<your-codespace-name>-4321.app.github.dev/`. Es un túnel privado al Codespace, que ahora aloja el servidor web.

### Explora el sitio

Con el sitio en ejecución, explora sus funciones principales:

- **Página de inicio**: muestra juegos de mesa con sus títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre una página con más información, como el título, la descripción, la editorial y la categoría.

## Explora el backlog con Copilot

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar, vuelve al Codespace, abre el panel Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. El modo y el modelo varían según el ejercicio; los pasos correspondientes indicarán qué seleccionar.

La primera versión del sitio funciona, pero queremos añadir capacidades. Empieza por explorar el backlog de incidencias que se creó automáticamente al generar el proyecto a partir de la plantilla:

1. En el menú desplegable de agentes de Chat, selecciona **Agent**. El agente planifica e implementa cambios en varios archivos, ejecuta comandos de terminal y usa herramientas.

   ![Selector de agente en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. En la lista de modelos disponibles, selecciona **Claude Sonnet 4.5**.

> [!CAUTION]
> Los autores no expresan preferencia por un modelo concreto. El taller se creó con Claude Sonnet 4.5 para que las sugerencias fueran relativamente coherentes y la experiencia resultase más predecible. Como los modelos de lenguaje grandes (LLM) son probabilísticos, es normal que las sugerencias varíen.

> [!NOTE]
> Debido al carácter probabilístico de los LLM, Copilot puede utilizar un comando diferente de Model Context Protocol (MCP), pero debería poder completar la tarea.

3. Envía este prompt:

   ```plaintext
   Muéstrame el backlog de incidencias de mi repositorio de GitHub. Ayúdame a priorizarlas según su utilidad para los usuarios.
   ```

4. Selecciona **Continue** para permitir que se ejecute el comando que enumera las incidencias.
5. Revisa la lista generada.

Copilot también habrá priorizado las incidencias según su estimación de la utilidad para los usuarios.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, revisa las instrucciones que proporcionan contexto para el trabajo. Utilizarás el archivo de [instrucciones de interfaz][ui-instructions], que describe cómo añadir funcionalidades al sitio:

1. En el Codespace, abre `.github/instructions/ui.instructions.md`.
2. Lee las pautas generales para añadir funcionalidad, entre ellas:
   - una descripción de la arquitectura.
   - principios para diseñar componentes, hacerlos comprobables y garantizar la accesibilidad.
   - enlaces a instrucciones específicas para Astro, Tailwind CSS y Drizzle.

> [!TIP]
> Los archivos de instrucciones pueden hacer referencia a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas al archivo de instrucciones. Así puedes reutilizar pautas, dividir instrucciones complejas y proporcionar ejemplos y plantillas.

## Implementa el filtrado

Para completar el filtrado, tendrás que modificar al menos tres áreas:

- añadir o ajustar la lógica restante de filtrado en la capa de datos (`src/lib/`).
- añadir o actualizar las pruebas del comportamiento de filtrado.
- actualizar la página de listado de juegos para incluir la interfaz de filtrado.

Además, las pruebas deben superarse antes de combinar los cambios. El modo de agente de Copilot puede realizar este trabajo:

1. Continúa la conversación actual o selecciona **New Chat** para iniciar otra.
2. Selecciona **Add Context**, **Instructions** y **ui** para añadir el archivo de instrucciones de interfaz.

   ![Ejemplo de selección del archivo de instrucciones de interfaz](../../../_images/ex3-select-instructions-file.png)

3. Confirma que **Agent** sigue seleccionado en el menú desplegable de agentes de Chat.

   ![Selector de agente en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Confirma que sigue seleccionado el modelo **Claude Sonnet 4.5**.
5. Envía este prompt para que Copilot implemente la funcionalidad conforme a la incidencia:

   ```plaintext
   Actualiza el sitio para incluir el filtrado por editorial y categoría conforme a los requisitos de la incidencia correspondiente del backlog de GitHub. Ya existe un método auxiliar para las editoriales; conserva y ajusta ese trabajo según sea necesario mientras completas el resto de la capa de datos, la interfaz y las pruebas. Asegúrate de que todas las pruebas se superan antes de terminar. El servidor ya está en ejecución; no hace falta iniciarlo.
   ```

6. Observa cómo Copilot explora el proyecto y localiza los archivos pertinentes de la capa de datos, la interfaz y las pruebas. Después, modifica los archivos y ejecuta las pruebas.

   ![Copilot explora los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot puede tardar varios minutos en explorar, modificar archivos y ejecutar pruebas. Durante el proceso quizá aparezcan los botones **Keep** y **Undo** en el editor. Al terminar, tendrás la opción de conservar o deshacer todos los cambios; no es necesario seleccionarlos mientras el agente sigue trabajando.

7. Cuando Copilot lo solicite, selecciona **Continue** para ejecutar las pruebas.
8. Es posible que haya pausas y que algunas pruebas fallen durante el proceso. Copilot alternará entre la generación de código y las pruebas hasta terminar y no detectar errores.
9. Explora el código generado para detectar posibles problemas.

   ![Sesión completa de Copilot en modo de agente](../../../_images/ex3-agent-mode-proposed-changes.png)

> [!CAUTION]
> Revisa siempre el código generado por Copilot o cualquier otra herramienta de IA.

10. Vuelve al navegador con el sitio en ejecución y explora la nueva funcionalidad.
11. Cuando hayas confirmado que funciona y revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y pasos siguientes

Has explorado cómo usar el modo de agente de GitHub Copilot para añadir funcionalidades al sitio de Tailspin Toys. Has aprendido cómo:

- el modo de agente de GitHub Copilot implementa funciones en la interfaz y la capa de datos.
- el modo de agente de Copilot explora el proyecto, identifica archivos y coordina cambios.
- revisar los cambios y las pruebas generados antes de combinarlos en el código base.

Ahora [probarás la funcionalidad con el servidor Playwright MCP][next-lesson] y abrirás una solicitud de incorporación de cambios.

### Ejercicio adicional: implementa la paginación

A medida que crezca la lista de juegos, será necesario habilitar la paginación. Aplica lo aprendido y pide a Copilot que añada esta función. Ten en cuenta lo siguiente:

- sigue las prácticas recomendadas existentes, incluidos los archivos de instrucciones.
- decide cómo quieres que funcione la paginación: por ejemplo, si el usuario podrá elegir el tamaño de página o si será fijo.
- incluye en el prompt el contexto necesario para que Copilot cree la implementación deseada.
- itera con GitHub Copilot, pide cambios y proporciona contexto adicional. Es el flujo normal de trabajo con Copilot.

## Recursos

- [Copilot: modos ask, edit y agent; para qué sirven y cuándo usarlos][choose-mode]
- [Modo de agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
