---
title: "Lección 5: Personaliza y usa una habilidad de comprobaciones de calidad"
description: "Explora la habilidad quality-checks existente, personaliza el formato del informe y úsala para validar el filtrado."
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

Programar no consiste únicamente en escribir código. Ya has comprobado manualmente que funciona y has utilizado archivos de instrucciones para que siga los estándares del proyecto. Pero ¿qué ocurre con las pruebas, lint y el resto de la integración continua (CI)?

Las **habilidades de agente** son adecuadas para estas tareas. Ayudan a Copilot a entender cómo debe ejecutar operaciones repetibles.

En esta lección:

- explorarás la habilidad existente `quality-checks`.
- personalizarás el formato de los resultados.
- volverás a cargar la habilidad y la ejecutarás.

## Escenario

Tailspin Toys utiliza la habilidad `quality-checks` para ejecutar pruebas unitarias, lint y comprobaciones de tipos. El equipo quiere mejorar el informe para que los resultados sean más fáciles de leer.

## Instrucciones, scripts y recursos

Las habilidades de agente agrupan instrucciones de tareas reutilizables, scripts ejecutables y recursos auxiliares que el agente carga cuando los necesita. Una habilidad se guarda en una carpeta con su nombre y un archivo Markdown llamado `SKILL.md`. Este archivo contiene metadatos con un nombre y una descripción, una explicación de la función de la habilidad e indicaciones sobre cuándo debe utilizarse. La carpeta también puede incluir subcarpetas con scripts y otros recursos.

> [!NOTE]
> Las habilidades no necesitan archivos ni carpetas adicionales. La habilidad `quality-checks` de Tailspin Toys solo contiene `SKILL.md`, porque utiliza los comandos existentes del proyecto.

Las habilidades pueden residir en `.github/skills` para compartirse y reutilizarse en el repositorio, o en la carpeta de habilidades del usuario, `~/.copilot/skills`.

## Explora la habilidad

Explorarás la habilidad `quality-checks`, que el equipo de Tailspin Toys creó para ejecutar pruebas unitarias, lint y comprobaciones de tipos:

1. Vuelve al Codespace y abre `.github/skills/quality-checks/SKILL.md` en el editor de Codespaces.
2. Lee el `name` y la `description` de la parte superior. La descripción ayuda a Copilot a decidir cuándo utilizar la habilidad.
3. Lee las instrucciones y observa cómo guían a Copilot durante las pruebas y el análisis de lint.
4. Comprueba que todavía no hay una sección **Results output formatting**.

## Ejecuta la habilidad antes de cambiarla

Las habilidades pueden invocarse directamente desde Copilot CLI o mediante lenguaje natural. Pide a Copilot que ejecute las tres comprobaciones:

1. Vuelve a la conversación de filtrado en modo Interactive.
2. Envía este prompt:

   ```plaintext
   Ejecuta la habilidad quality-checks para las pruebas unitarias, lint y las comprobaciones de tipos.
   ```

3. Lee el informe que presenta al final.

## Personaliza el informe

Quieres que el informe indique qué se ha ejecutado, si se ha superado y qué han comunicado las herramientas. Actualiza la habilidad:

1. Vuelve a `.github/skills/quality-checks/SKILL.md`.
2. Añade esta sección al final del archivo:

   ```markdown
   ## Formato de los resultados

   Al terminar, indica cada comando ejecutado y si se ha superado, ha fallado o se ha bloqueado. Incluye el número de pruebas, la duración, los errores, las advertencias y otras métricas solo si la herramienta las proporciona. Indica el siguiente paso ante cualquier error o bloqueo y nunca presentes como superada una comprobación omitida o incompleta.
   ```

3. Guarda el archivo.

## Ejecuta la habilidad actualizada

Copilot CLI puede volver a cargar las habilidades editadas sin reiniciar la conversación. Comprueba el cambio:

1. Escribe:

   ```plaintext
   /skills reload
   ```

2. Envía exactamente el mismo prompt de antes:

   ```plaintext
   Ejecuta la habilidad quality-checks para las pruebas unitarias, lint y las comprobaciones de tipos.
   ```

3. Lee el informe final y compáralo con el primero.

## Resumen y pasos siguientes

Has personalizado y utilizado una habilidad de agente existente. En esta lección:

- has explorado la habilidad `quality-checks` para pruebas unitarias, lint y comprobaciones de tipos.
- has personalizado el formato de los resultados.
- has vuelto a cargar y ejecutado la habilidad.

El cambio acompañará al filtrado en la solicitud de incorporación de cambios (PR) de la funcionalidad. A continuación, dejarás que Copilot interactúe directamente con el sitio mediante Playwright y Model Context Protocol (MCP) en la [siguiente lección][next-lesson].

## Más ejemplos de habilidades

Estos ejemplos de la comunidad sirven de referencia; no son tareas adicionales:

- [Especificación de Agent Skills][skill-spec]
- [Flujo de contribución: `make-repo-contribution`][contribution-example]
- [Documentos de requisitos: `prd`][prd-example]
- [Diagramas y script de exportación incluido: `drawio`][drawio-example]
- [Pruebas en navegador: `webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing
