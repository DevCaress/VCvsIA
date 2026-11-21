# Notas del speaker: Vibe Coding vs Ingeniería asistida por IA

Guía diapositiva por diapositiva. Para cada una: la idea clave, qué explicar y cómo pasar a la siguiente. Los tiempos son orientativos y suman unos 40 minutos sin preguntas.

> Durante la charla, la tecla **S** abre la vista de orador con las notas cortas que ya están dentro del HTML. Este archivo es la versión larga para prepararte.

---

## Parte 1: El problema

### 1. Portada: Vibe Coding vs Ingeniería asistida por IA
⏱ 1 min

**Idea clave:** misma herramienta, resultados muy distintos.

- Abre con dos preguntas a mano alzada: ¿cuántos generaron código con IA esta semana? ¿Cuántos lo llevaron a producción?
- La diferencia entre las dos manos levantadas es el tema de la charla.
- Aclara desde el inicio que no vas a decir que la IA es mala ni que el vibe coding está prohibido. La charla trata del proceso, no de la herramienta.

**Transición:** "Empecemos por ponerle nombre a lo que casi todos hacemos."

---

### 2. Vibe coding
⏱ 2–3 min

**Idea clave:** programar sin mirar el código, solo el resultado.

- **Origen del término:** Andrej Karpathy (cofundador de OpenAI, ex director de IA en Tesla) lo usó en febrero de 2025. La frase completa habla de "entregarse a las vibras, abrazar los exponenciales y olvidar que el código existe". Él lo dijo para proyectos de fin de semana, no para producción.
- **El ciclo:** escribes un prompt, aceptas todo lo que propone, lo ejecutas y, si falla, pegas el error de vuelta en el chat. Repites.
- **Lo bueno:** es rapidísimo para prototipos, demos y para probar una idea.
- **Lo malo:** no hay revisión, ni pruebas, ni arquitectura. Nadie entiende el código, ni siquiera quien lo "escribió".
- "Funciona hasta que deja de funcionar": cuando falla algo serio, no hay nadie que sepa por qué funcionaba.

**Si tienes un ejemplo propio** (un bug, una fuga de un secreto, un proyecto que hubo que reescribir), este es el momento de contarlo.

**Transición:** "Ahora, la otra forma de usar exactamente las mismas herramientas."

---

### 3. Ingeniería asistida por IA
⏱ 2 min

**Idea clave:** la IA es un colaborador dentro de un proceso, no un reemplazo del proceso.

- **El ciclo:** especificar qué quieres, darle el contexto correcto, planear, ejecutar, verificar y revisar.
- La diferencia con el vibe coding no está en el paso de "ejecutar", que es el mismo. Está en todo lo que pasa antes y después.
- Frase para remarcar: **el ingeniero diseña el sistema alrededor del modelo.** El modelo es una pieza; tú armas la máquina.
- Este ciclo también es iterativo: si la verificación falla, vuelves a planear, no a pegar el error a ciegas.

**Transición:** "Pongamos las dos lado a lado."

---

### 4. La diferencia en una tabla
⏱ 2–3 min

**Idea clave:** cada fila es una decisión que el vibe coding deja al azar.

Ve fila por fila (aparecen una a una):

- **Objetivo:** "que funcione" contra "que sea correcto y mantenible". Algo que funciona hoy puede ser imposible de cambiar mañana.
- **Contexto:** lo que haya en el chat contra contexto curado. Lo veremos a fondo en context engineering.
- **Memoria:** en vibe coding cada sesión empieza de cero. En ingeniería, lo aprendido queda escrito y versionado.
- **Validación:** "se ve bien" contra tests, linters y CI. El ojo humano no detecta un caso borde.
- **Control:** el modelo decide contra un humano en los puntos clave.
- **Escala:** un script contra un equipo y un producto.

**Transición:** "Cada una de estas filas tiene conceptos detrás. Vamos a verlos."

---

## Parte 2: Los conceptos

### 5. Portada de la Parte 2
⏱ 30 s

- Lee las etiquetas: context engineering, memoria, tokens, orquestación, harness y human-in-the-loop.
- Avisa que primero vas a definir cuatro palabras que se usan en todo lo que sigue.

---

### 6. Conceptos básicos
⏱ 3–4 min

**Idea clave:** cuatro palabras que vas a escuchar todo el tiempo.

- **Agente:** un LLM que trabaja en un ciclo. Un chat responde una vez y espera; un agente actúa (lee archivos, edita, ejecuta comandos), mira qué pasó y decide el siguiente paso hasta terminar la tarea. Lo que lo convierte en agente es el ciclo y las herramientas.
- **Subagente:** un agente que otro agente lanza para una tarea acotada, por ejemplo "busca dónde se valida el login". Trabaja con su propio contexto, limpio, y devuelve solo la respuesta. Así el agente principal no se llena de ruido. Vuelve a aparecer en orquestación.
- **Skill:** un paquete de instrucciones, un `SKILL.md` y a veces scripts, que enseña al agente a hacer algo concreto: "cómo hacemos los deploys", "cómo revisar un PR". Se carga solo cuando la tarea lo pide, así no gasta tokens mientras no se usa.
- **MCP (Model Context Protocol):** un estándar abierto para conectar agentes con herramientas y datos externos: GitHub, una base de datos, Figma, Slack. Se suele comparar con un USB-C para la IA: escribes el conector una vez y sirve para cualquier agente compatible.

**Distinción útil:** una skill es **conocimiento** (cómo hacer algo); MCP es **conexión** (acceso a algo).

**Transición:** "Con eso, armemos un modelo mental de cómo funciona todo esto por dentro."

---

### 7. Modelo mental
⏱ 2 min

**Idea clave:** el modelo es un CPU sin memoria; tu trabajo es decidir qué entra a su RAM.

- **LLM = CPU:** razona y procesa, pero no recuerda nada entre una llamada y otra. Cada vez empieza de cero.
- **Ventana de contexto = RAM:** todo lo que el modelo "ve" en este momento: tus mensajes, los archivos que leyó, los resultados de los comandos. Lo que no está ahí, para el modelo no existe.
- **Memoria externa = disco:** archivos, documentación, bases de datos. Está disponible, pero hay que cargarlo en la RAM para que sirva.
- Conclusión: hacer ingeniería con IA es, en gran parte, **gestionar qué entra a la RAM**.

**Transición:** "Eso tiene un nombre."

---

### 8. Context engineering
⏱ 3 min

**Idea clave:** no basta con escribir un buen prompt; hay que diseñar todo lo que el modelo ve.

- **Prompt engineering** era buscar la frase perfecta. **Context engineering** es diseñar todas las capas de información que recibe el modelo.
- Recorre las capas (aparecen una a una):
  1. **System prompt e instrucciones:** las reglas base de comportamiento que trae el harness.
  2. **Memoria del proyecto:** archivos como `CLAUDE.md` o `AGENTS.md`, con las reglas del repo.
  3. **Herramientas disponibles:** lo que el agente puede hacer (MCP, comandos de terminal).
  4. **Archivos y datos relevantes:** solo los que sirven para esta tarea, no el repo entero.
  5. **Historial y resultados de herramientas:** lo que ya pasó en la sesión.
  6. **La tarea concreta:** lo que pides ahora. Es solo la última capa.
- Mensaje: cuando el resultado es malo, casi siempre el problema está en las capas de arriba, no en la redacción de la tarea.

**Transición:** "Varias de esas capas son memoria. Veamos los dos tipos."

---

### 9. Memoria de corto plazo
⏱ 2–3 min

**Idea clave:** la ventana de contexto es limitada y se degrada.

- Es la memoria de la **sesión actual**: el historial, los archivos leídos, la salida de los comandos.
- Tiene un límite: hoy está entre 200 mil y un millón de tokens, según el modelo.
- **Context rot (degradación del contexto):** aunque haya espacio, entre más ruido hay, peor presta atención el modelo. Instrucciones del principio se olvidan y se mezclan detalles irrelevantes.
- Técnicas para cuidarla:
  - **Compactar:** resumir la conversación y seguir con el resumen.
  - **Sesiones cortas y enfocadas:** una tarea por sesión.
  - **Limpiar el contexto entre tareas:** no arrastrar lo de la tarea anterior.
  - **Listas de tareas (TODO):** que el agente lleve registro de qué hizo y qué falta.

**Transición:** "¿Y lo que tiene que sobrevivir a la sesión?"

---

### 10. Memoria de largo plazo
⏱ 3 min

**Idea clave:** lo que sobrevive entre sesiones y entre personas se escribe y se versiona.

- **Archivos de instrucciones del proyecto** (`CLAUDE.md`, `AGENTS.md`): el agente los lee al empezar. Muestra el ejemplo de la diapositiva: el stack, cómo correr los tests, qué no tocar nunca, decisiones tomadas.
- **ADR (Architecture Decision Record):** un markdown corto por cada decisión importante, con el contexto, lo que se decidió, por qué y sus consecuencias. Se numeran (ADR-004) y no se borran: si la decisión cambia, se escribe uno nuevo que reemplaza al anterior. Para el agente es muy valioso: en vez de proponer otra vez lo que el equipo ya descartó, lee el ADR y entiende por qué se hizo así.
- **Memorias del agente:** preferencias y correcciones que el agente guarda ("no uses esta librería", "los commits van en español").
- **RAG (Retrieval-Augmented Generation):** buscar los fragmentos relevantes del código o la documentación e insertarlos en el contexto, en vez de cargar todo.
- Punto clave: **se versiona en git como el código.** Si cambia, queda en el historial y se revisa en el PR.

**Transición:** "Hay proyectos que se dedican justamente a esto."

---

### 11. Proyectos de persistencia
⏱ 3 min

**Idea clave:** la diferencia entre ellos es quién decide qué se guarda.

- La diapositiva anterior era el concepto; esta es la caja de herramientas.
- **Engram** (de Gentleman Programming): el agente decide qué vale la pena recordar, como un bugfix o una decisión, y no guarda cada acción. Es un solo binario en Go con SQLite, sin API key. Funciona por MCP con cualquier agente y se puede exportar al repo para commitearlo.
- **claude-mem:** graba la sesión con hooks y la comprime. La siguiente sesión arranca con un índice de lo importante. Recuerda más, pero corre más procesos.
- **Basic Memory:** la memoria son notas en Markdown que tú también lees y editas. Si el equipo no puede leer la memoria, no es documentación.
- **Memory MCP:** el servidor de referencia del protocolo. Un grafo de entidades y relaciones en un archivo local. Sirve para entender cómo funciona MCP.
- **Mem0 y Graphiti** son otra categoría: no son para tu repo, sino memoria **dentro de un producto**. Mem0 extrae datos de los usuarios de una app. Graphiti guarda desde cuándo un dato es cierto y cuándo dejó de serlo.
- **Si preguntan por más:** Letta (antes MemGPT) es un agente que reescribe su propia memoria.
- Criterio de cierre: no hace falta instalar todos. **Si la memoria se puede revisar y versionar, es ingeniería.**

**Transición:** "Todo lo que entra al contexto cuesta. Hablemos de tokens."

---

### 12. Optimización de tokens
⏱ 3 min

**Idea clave:** los tokens son un recurso; afectan al costo, a la velocidad y a la calidad.

- **Qué es un token:** el pedazo de texto que procesa el modelo, aproximadamente tres cuartos de una palabra. Todo lo que entra y sale del modelo se cobra y se mide en tokens.
- Más tokens significa **más costo, más latencia y peor atención** (vuelve al context rot).
- Cuatro técnicas:
  - **Menos es más:** leer solo los fragmentos relevantes, no repositorios enteros.
  - **Prompt caching:** si el inicio del contexto no cambia (system prompt, documentación), el proveedor lo reutiliza y cobra mucho menos. Por eso conviene que lo estable vaya primero y lo variable al final.
  - **Subagentes:** que la búsqueda la haga otro agente y solo vuelva el resultado, no todo el ruido.
  - **Modelo adecuado:** un modelo pequeño y barato para tareas simples, uno grande para razonar.

**Transición:** "Lo de los subagentes merece una diapositiva propia."

---

### 13. Orquestación de agentes
⏱ 2–3 min

**Idea clave:** dividir el trabajo entre agentes especializados, cada uno con su contexto limpio.

- Un **agente orquestador** planea y coordina. No hace todo: delega.
- **Explorador:** busca en el código. **Implementador:** escribe los cambios. **Tester:** corre las pruebas. **Revisor:** hace code review.
- Ventajas: **paralelismo** (varios trabajan a la vez) y **especialización** (cada uno con instrucciones y contexto propios). El revisor, por ejemplo, no está "contaminado" por el razonamiento de quien escribió el código.
- Advertencia honesta: más agentes significa más tokens y más coordinación. Para una tarea chica, un solo agente basta.

**Transición:** "¿Y qué es lo que hace que un modelo pueda ser agente, tener subagentes, usar herramientas? El harness."

---

### 14. El harness
⏱ 3 min

**Idea clave:** el harness es todo lo que rodea al modelo y lo convierte en un agente útil.

- La palabra *harness* significa arnés: el equipo que se le pone a algo para poder dirigirlo.
- Componentes:
  - **Herramientas:** las acciones que el agente puede pedir (leer, editar, ejecutar, buscar). Cada uso es una *tool call*.
  - **Permisos:** qué puede hacer solo y qué necesita tu aprobación.
  - **Hooks:** comandos que se ejecutan solos en ciertos momentos; por ejemplo, correr el linter cada vez que el agente edita un archivo. Son reglas que se cumplen siempre, no sugerencias que el modelo puede olvidar.
  - **Sandbox:** un entorno aislado para que un error del agente no rompa tu máquina.
  - **Skills:** las que vimos al principio.
  - **Loop agente:** el ciclo de actuar, mirar el resultado y decidir.
- Frase de cierre: **el mismo modelo rinde muy distinto según el harness.**

**Transición:** "¿Cuáles usa la gente hoy?"

---

### 15. Harness que usa la gente
⏱ 3 min

**Idea clave:** hay opciones de terminal, de editor, abiertas y cerradas; elige según cómo trabajas.

- **Claude Code** (Anthropic) y **Codex** (OpenAI): los dos grandes de la terminal. Claude Code trae skills, hooks, permisos y subagentes. Codex es una CLI abierta con sandbox y aprobación por acción.
- **OpenCode:** abierto y sin modelo fijo. Es el que se recomienda para no quedar atado a un proveedor.
- **Pi:** minimalista a propósito. Trae pocas herramientas y el resto se agrega con extensiones. Mucha gente lo usa como base para armar el suyo.
- **Gentle Shell:** el harness de Gentleman, construido sobre Pi, con memoria (Engram), su flujo ODD y varios agentes en la misma terminal. Es un ejemplo de "hacerlo tuyo".
- **Cursor:** el editor, con reglas, agentes y el diff al lado del código.
- **Copilot** (GitHub): agente en el IDE y en la CLI, integrado con el repo y los pull requests.
- **Cline:** agente abierto dentro del editor, con el modelo que elijas.
- Aclaración por si preguntan: **gentle-ai** no es un harness; configura los que ya tienes.

**Transición:** "Con tanta autonomía, ¿dónde queda el humano?"

---

### 16. Human-in-the-loop
⏱ 2 min

**Idea clave:** la IA acelera; el humano decide y es responsable.

- *Human-in-the-loop* significa que una persona interviene en puntos definidos del proceso.
- Los cinco puntos de control:
  1. **Aprobar el plan** antes de que escriba código.
  2. **Autorizar acciones riesgosas:** borrar, desplegar, tocar datos.
  3. **Revisar el diff:** leer lo que cambió.
  4. **Validar en CI o staging:** que pase las pruebas en un entorno real.
  5. **Merge y deploy:** la decisión final es humana.
- Regla práctica: **más autonomía donde el riesgo es bajo, más control donde es alto.** Un cambio de texto no necesita lo mismo que una migración de base de datos.

**Transición:** "Juntemos todo en un ejemplo."

---

### 17. Todo junto: un flujo real
⏱ 3 min

**Idea clave:** cada concepto aparece en un paso concreto.

Cuenta el ejemplo como una historia, con un ticket real si puedes:

1. **Memoria:** el agente carga `CLAUDE.md` con las reglas del repo.
2. **Contexto:** recibe un ticket claro y solo los archivos relevantes.
3. **Orquestación:** un subagente explora el código y otro propone un plan.
4. **HITL:** el ingeniero lee y aprueba el plan.
5. **Harness:** el agente implementa y los hooks corren lint y tests solos.
6. **Tokens:** compacta el contexto y sigue con la siguiente tarea.
7. **HITL:** abre un PR, hay code review humano y se hace merge.

**Transición:** "Este proceso se puede improvisar cada vez, o se puede empaquetar. Eso es la Parte 3."

---

## Parte 3: Los flujos

### 18. Portada de la Parte 3: Flujos para trabajar con IA
⏱ 1 min

**Idea clave:** el proceso ya no cabe en un prompt, así que se empaqueta en metodologías.

- Todos comparten una idea: **la intención antes que el código.** Primero se escribe qué se quiere; después se codea.
- **SDD** es el de referencia. Los demás cambian cuánta ceremonia hay y quién la decide.
- *Ceremonia* aquí significa la cantidad de documentos, pasos y aprobaciones antes de escribir código.

---

### 19. Los flujos que más se repiten
⏱ 4–5 min

**Idea clave:** todos separan pensar de ejecutar; difieren en cuánto proceso exigen.

- **SDD (Spec-Driven Development, con Spec Kit de GitHub):** la especificación manda. Se escribe la spec, luego el plan, luego las tareas y después se implementa. Cada documento se aprueba antes de pasar al siguiente. Hay además una "constitución" de principios por repo.
- **ODD (Organic Driven Development, de Gentleman, en gentle-ai):** explora, clasifica el cambio por riesgo y codea con tests. Si el cambio se puede retomar mañana solo mirando el diff, no se escribe documento; si no, se escribe uno solo.
- **OpenSpec:** proponer el cambio, aplicarlo y archivarlo en las specs vivas del repo. Más liviano y pensado para código que ya existe.
- **BMAD:** usa roles como un equipo: analista, PM, arquitecto, dev y QA, cada uno en su propio contexto. El proceso crece con el tamaño del cambio.
- **GSD (Get Shit Done):** pregunta hasta cerrar la idea, planea por fases y ejecuta cada fase en una ventana de contexto nueva. Ataca directamente el context rot.
- **Kiro (IDE de AWS):** tres archivos (`requirements.md`, `design.md`, `tasks.md`). No se codea hasta aprobar cada fase.
- **Task Master:** convierte un PRD (documento de requisitos de producto) en tareas con dependencias que el agente cierra en orden. No es un ciclo completo; encaja dentro de otro flujo.

**Transición:** "¿Cuál elegir?"

---

### 20. Cómo elegir
⏱ 2 min

**Idea clave:** elige según el tamaño y el riesgo del cambio.

- **Siempre la misma ceremonia (SDD, OpenSpec, Kiro):** cuando otra persona va a leer el cambio o cuando equivocarse sale caro.
- **Ceremonia según el cambio (ODD, GSD):** lo chico no necesita documentos; lo grande deja algo escrito para poder retomarlo.
- **Varios roles (BMAD):** cuando quieres planear, diseñar y revisar en contextos separados, como un equipo pequeño.
- Casi todos se instalan sobre el agente que ya usas. Kiro es la excepción: el flujo vive en su propio IDE.
- Cierra preguntando: **¿con qué flujo trabajan hoy?** La respuesta casi siempre es "con el chat". El objetivo no es adoptar todos, sino **tener un flujo escrito, con un punto donde el humano dice que sí.**

---

### 21. ¿Entonces vibe coding es malo?
⏱ 2 min

**Idea clave:** no; es una herramienta para ciertos contextos.

- **Vibe coding sí:** prototipos y pruebas de concepto, scripts que se van a tirar, aprender y explorar una tecnología nueva.
- **Ingeniería sí o sí:** código en producción, trabajo en equipo, y cualquier cosa que toque seguridad, datos personales o dinero.
- Frase de cierre: **no es la herramienta, es el proceso.** El mismo agente sirve para las dos cosas; lo que cambia es lo que pones alrededor.

---

### 22. Ideas para llevarse
⏱ 2 min

Repasa cada idea con una frase que conecte con su diapositiva:

1. **El contexto es el nuevo código: diséñalo.** (Context engineering)
2. **La memoria del proyecto se versiona.** (Memoria de largo plazo, ADRs)
3. **Los tokens son un recurso, no son infinitos.** (Optimización de tokens)
4. **El harness y los agentes son tu infraestructura.** (Harness, orquestación)
5. **El flujo se elige según el tamaño y el riesgo.** (Parte 3)
6. **El humano sigue siendo responsable.** (Human-in-the-loop)

Agradece y abre preguntas.

---

## Glosario rápido

| Término | En una línea |
|---|---|
| LLM | Modelo de lenguaje: genera texto razonando sobre lo que recibe. No recuerda nada por sí solo. |
| Token | Pedazo de texto (~¾ de palabra). La unidad de costo y de límite del modelo. |
| Prompt / system prompt | Lo que le pides al modelo / las instrucciones base que trae el harness. |
| Ventana de contexto | Todo lo que el modelo ve en un momento. Tiene un límite. |
| Context rot | Pérdida de calidad cuando el contexto tiene demasiado ruido. |
| Agente | LLM en un ciclo que usa herramientas hasta terminar una tarea. |
| Subagente | Agente lanzado por otro para una tarea acotada, con contexto propio. |
| Tool call | Una acción concreta que el modelo pide al harness (leer, editar, ejecutar). |
| Skill | Instrucciones empaquetadas que el agente carga cuando las necesita. |
| MCP | Protocolo estándar para conectar agentes con herramientas y datos externos. |
| Hook | Comando que el harness ejecuta solo en un momento definido. |
| Harness | Todo lo que rodea al modelo: herramientas, permisos, hooks, sandbox, loop. |
| HITL | Human-in-the-loop: el humano aprueba en puntos clave. |
| ADR | Architecture Decision Record: un documento por decisión de arquitectura. |
| RAG | Buscar fragmentos relevantes e insertarlos en el contexto. |
| Prompt caching | Reutilizar el inicio fijo del contexto a menor costo. |
| PRD | Product Requirements Document: los requisitos de un producto o feature. |
| Alucinación | Cuando el modelo inventa con seguridad algo que no existe (una API, un paquete). |

---

## Pendiente de verificar antes de presentar

- El paso "converge" de Spec Kit: revisar en el repo cuáles son los comandos actuales.
- Que en gentle-ai el flujo actual sea ODD y que haya reemplazado a su SDD.
- Las descripciones de herramientas que cambian rápido (persistencia, harness y flujos). Los enlaces respondían al 21 de noviembre de 2026.
