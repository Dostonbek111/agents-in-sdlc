---
title: "Ejercicio 4: Supervisión y administración de agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los últimos ejercicios has pedido al agente de GitHub Copilot en la nube que realice tres tareas distintas para mejorar la experiencia de usuario y añadir funcionalidades. Aunque trabaja de forma autónoma y asíncrona, sigue siendo importante supervisar estas tareas.

Hay varias herramientas para administrar las tareas asignadas al agente, como la [página de agentes][agents-page] de GitHub.com. Desde este centro puedes consultar todas las tareas con solicitudes de incorporación de cambios (PR) abiertas, revisar las operaciones realizadas y orientar una sesión que todavía esté en curso.

En esta lección:

- explorarás la página de agentes para supervisar las tareas del agente en la nube.
- orientarás una sesión en curso para solicitar una funcionalidad adicional.

## Escenario

Después de asignar al agente la tarea de crear un modo de alto contraste, el equipo se dio cuenta de que también sería conveniente añadir un modo claro. Como el agente ya está actualizando los estilos del sitio y añadiendo la funcionalidad para cambiar de modo, parece razonable pedirle que incluya también el modo claro. Le indicarás que lo añada junto con el modo de alto contraste.

## Revisa las tareas del agente en la nube

Consulta el estado de las tareas que tiene asignadas el agente:

1. Ve a la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Revisa las tareas que aparecen en el panel principal y en el panel izquierdo. Deberías encontrar las que has asignado a Copilot, entre ellas:
   - actualizar la documentación del código base.
   - generar interfaces de programación de aplicaciones (API) para modificar juegos.
   - añadir un modo de alto contraste al sitio web.
3. Selecciona una tarea en curso y revisa las operaciones realizadas por Copilot. Pueden incluir:
   - obtener el código del repositorio.
   - preparar el entorno de trabajo.
   - configurar servidores MCP.
   - realizar varias operaciones para completar la tarea.

> [!NOTE]
> Los pasos concretos varían según el estado del trabajo de Copilot y el enfoque que haya seguido.

4. Fíjate también en el panel de PR, que aparece a la derecha y permite consultar la PR y los archivos modificados.

## Dirige al agente en la nube

Ya has visto las tareas activas. Ahora pide a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste:

1. Selecciona la sesión relacionada con el modo de alto contraste. El título exacto dependerá del nombre que le haya dado Copilot y del estado del trabajo.

   ![Sesión de accesibilidad en el panel de agentes](../../../_images/ex6-accessibility-session.png)

2. Espera unos minutos hasta que la sesión indique que ha terminado de prepararse y ha empezado a trabajar. Lo notarás cuando aparezcan mensajes similares a los que se muestran en el taller.
3. En el cuadro de diálogo **Steer active session while Copilot is working**, escribe este prompt:

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![Tarea del agente en la nube en la página de agentes con el cuadro para orientar la sesión activa resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar el prompt.
5. Comprueba que Copilot lo recibe y lo incorpora al flujo de trabajo.

## Deja que Copilot trabaje

Copilot incorporará la nueva solicitud al flujo cuando termine el paso que estaba ejecutando al recibir el mensaje. El proceso tardará varios minutos; puedes aprovechar ese tiempo para repasar lo que has aprendido.

## Resumen y pasos siguientes

Has explorado la página de agentes de Copilot, el centro para supervisar y orientar las tareas del agente de GitHub Copilot en la nube. Puedes consultar las tareas activas y completadas, revisar el trabajo y redirigir una sesión en curso para ajustar el alcance o aportar instrucciones.

Has aprendido a:

- explorar el centro y la página de agentes para supervisar las tareas del agente en la nube.
- redirigir una sesión en curso para solicitar funcionalidad adicional.

Mientras Copilot termina el trabajo sobre las funciones de accesibilidad, pasarás a [iterar sobre las solicitudes de incorporación de cambios que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Lección anterior: Agentes personalizados][previous-lesson] | [Siguiente lección: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
