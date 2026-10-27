---
slug: es-es/real-world-development/cloud
title: "Agente de GitHub Copilot en la nube"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

El **[agente de GitHub Copilot en la nube](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent)** permite que GitHub Copilot trabaje de forma asíncrona en la nube. Asignas trabajo en GitHub y el agente en la nube se encarga de él en segundo plano: explora el repositorio, realiza cambios y abre una solicitud de incorporación de cambios mientras tú te ocupas de otras tareas.

En estos ejercicios añadirás instrucciones personalizadas que deberá seguir el agente en la nube y, después, le asignarás una incidencia de GitHub para que la implemente. Revisarás y usarás agentes personalizados para orientar su enfoque, supervisarás y dirigirás las sesiones desde el panel de agentes y, por último, revisarás sus solicitudes de incorporación de cambios e iterarás sobre los resultados.

## Ejercicios

| Ejercicio | Tema | Descripción |
|----------|-------|-------------|
| [0. Requisitos previos][ex0] | Configuración | Crea tu repositorio y Codespace |
| [1. Instrucciones personalizadas][ex1] | Contexto | Añade instrucciones personalizadas que seguirá el agente en la nube |
| [2. Agente en la nube][ex2] | Agente asíncrono | Asigna incidencias al agente de GitHub Copilot en la nube |
| [3. Agentes personalizados][ex3] | Agentes especializados | Revisa y usa agentes personalizados |
| [4. Administración de agentes][ex4] | Supervisión | Supervisa y dirige las sesiones de agente |
| [5. Iteración][ex5] | Revisión | Revisa las solicitudes de incorporación de cambios (PR), itera sobre el trabajo de Copilot y elige los próximos pasos |

## Requisitos previos

Antes de participar en este taller, asegúrate de que dispones de lo siguiente:

- [ ] Una cuenta de GitHub con un plan activo de **Copilot Student, Pro, Pro+, Business o Enterprise**
- [ ] El agente de GitHub Copilot en la nube habilitado para tu cuenta u organización

> [!TIP]
> ¿No tienes un plan de pago? Los estudiantes cuya condición se haya verificado pueden obtener GitHub Copilot gratis a través de [GitHub Education][student-plan]. El plan **Copilot Student** incluye las funciones de agente, Model Context Protocol (MCP), revisión de código y Copilot CLI que utiliza este taller, por lo que puedes completar todos los entornos con él.

> [!NOTE]
> El agente en la nube está disponible en todos los planes excepto Copilot Free: **Copilot Student, Pro, Pro+, Business y Enterprise**. En Copilot Business o Enterprise, un administrador debe habilitarlo para la organización.

> [!NOTE]
> Este entorno no incluye el Model Context Protocol (MCP). Para aprender a usar servidores MCP con Copilot, consulta los entornos de [CLI][cli] o [VS Code][vscode].

## Primeros pasos

**[Empieza por los requisitos previos][ex0]**

[student-plan]: https://github.com/education/students
[cli]: ../cli/
[vscode]: ../vscode/
[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-cloud-agent/
[ex3]: 3-custom-agents/
[ex4]: 4-managing-agents/
[ex5]: 5-iterating/
