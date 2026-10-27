# StowIQ 3D — Brief de construcción y auditoría

**Motor:** GranelFlow Engine  
**Versión:** 1.1.0 · 26 de octubre de 2026  
**Estado documental:** revisión canónica R2, lista para iniciar Fase A y construir contra estos contratos. Sustituye el cierre prematuro de v1.0.0; incorpora correcciones documentales de las auditorías recibidas. La reevaluación externa de R2 y todas las pruebas de producto están pendientes. No significa software implementado, validación física, certificación de transporte ni aprobación de auditores externos.  
**Propietario de producto:** Heroldo Escobedo. **Constructor previsto:** ChatGPT/Codex, según el encargo actual. El usuario aportará auditorías multicasa; no se atribuyen dictámenes anticipados.  
**Tipo:** brief de producto + especificación de comportamiento + plan de aceptación. Perfil: aplicación local con datos persistentes, interfaz web y artefacto descargable.  
**Autoridad:** propuesta de implementación derivada del mini brief del usuario. No instala reglas globales ni modifica Intake. Si posteriormente se lleva al parque Lolo-code, entra mediante Intake y sus reglas vigentes.  
**Snapshot de referencia:** mini brief de esta conversación y «Brief Final - WMS Simulator y Stress-Tester - READY TO USE.md», versión documental 3.3. Se reutilizan sus principios, no su modelo de pedidos ni su stack Python.

## 0. Qué se está construyendo

Una herramienta para **planear y explicar cómo acomodar bultos individuales de distintos productos en un camión rígido con acceso trasero**, comprobar restricciones modeladas y reconciliar la preparación de una ruta contra su manifiesto.

La pregunta central es: «¿Qué bulto va dónde, en qué orden se carga y descarga, qué restricciones cumple y qué queda pendiente?». El 3D es una vista verificable del plan. No inventa posiciones, no decide restricciones ni acredita lo que sucedió físicamente.

«A granel» se usa aquí en el sentido operativo de carga suelta sin tarimas. No incluye líquidos, granos vertidos ni material sin unidades discretas. El nombre comercial Thorton/Torton no determina dimensiones, capacidad ni configuración de ejes: todo se declara por vehículo.

### Resultado de negocio buscado, todavía no demostrado

Reducir decisiones improvisadas de estiba, reacomodos y errores de selección; hacer visibles problemas de capacidad y descarga antes de cargar. No existen en este encargo mediciones de merma, tiempos, adopción ni ahorro. Son hipótesis a contrastar en un piloto, no beneficios garantizados.

### Premisas heredadas del WMS 3D

1. Motor headless antes de riqueza visual; una sola fuente de verdad.
2. Datos sintéticos identificados y resultados reproducibles.
3. Reconciliación de todas las unidades; nada desaparece por no caber.
4. Invariantes comprobados independientemente del renderer.
5. UI, contratos, trazas, exportaciones y documentación coherentes.
6. Fases con puertas de salida y artefactos de evidencia; ninguna fecha sustituye las pruebas.
7. Alcance publicable menor separado de producto completo y uso operativo.
8. Ningún resultado de demo se presenta como integración WMS o seguridad física validada.

## 1. Correcciones explícitas al mini brief

| Idea original | Decisión de este brief |
|---|---|
| Cada caja 3D vinculada por SKU | SKU + presentación identifican artículo; `unit_id` identifica cada bulto lógico. La vinculación física individual requiere etiqueta única o captura equivalente. |
| Clase A/B/C previene aplastamiento | Se conserva como regla de compatibilidad y se añade carga admisible por orientación. No se infiere resistencia desde peso o clase. |
| LIFO por ordenar paradas | Se verifica secuencia de extracción y dependencias de apoyo, además de separar paradas. |
| Centro de gravedad 60/40 evita multas | Se elimina esa promesa. Se calcula centro de masa de carga y, con datos suficientes, reacción estática por grupo de apoyo; no certifica límites legales. |
| Costales como cajas exactas | Se admiten solo como envolventes medidas; en la demo se declaran aproximadas y no sirven como soporte. |
| JSON / API WMS | JSON local es obligatorio en MVP. API real es fase posterior con contrato y autorización propios. |
| Monolito en archivo único | Código fuente modular; distribución compilada a un HTML autónomo, sin CDN obligatorio. |
| «Tiempo real» | Reacción a entradas y confirmaciones locales. No hay sensores ni observación automática del interior. |
| Bloquear o advertir | Violación dura bloquea el plan válido. Información desconocida produce estado no evaluado; nunca éxito silencioso. |

## 2. Alcances y exclusiones

### S0 — motor verificable

CLI, importación JSON, expansión de unidades, empaquetado heurístico, verificador independiente, secuencias de carga/descarga, métricas y exportación. S0 es hito técnico, **no satisface la entrega 3D solicitada**.

### S1 — demo 3D publicable, objetivo de la primera construcción

S0 + interfaz en español, vistas supervisor/estibador, planta/alzado/3D, corte de altura, selección por SKU y unidad, lector tipo teclado, confirmaciones locales, recuperación, comparador de escenarios y HTML autónomo descargable. Datos sintéticos por defecto. Debe permitir completar un recorrido de principio a fin sin usar consola.

### S2 — piloto asistido con datos reales

S1 + vehículo medido, perfiles de embalaje verificados, procedimiento de identificación, dispositivo y entorno de muelle probados, observación física y contraste con báscula cuando corresponda. Sin escritura automática en WMS. Su aceptación se documenta separadamente; S1 no la concede.

### S3 — integración productiva futura

Adaptador WMS, autenticación, permisos, conciliación transaccional, operación de varios usuarios y eventual respaldo central. Fuera de la primera construcción. No diseñar infraestructura SaaS ni desplegar bases de datos para resolver S1.

### Exclusiones

Optimización global exacta; garantía de aprovechamiento máximo; asignación de flota y ruteo; empaque interno de cajas; pallets mixtos; refrigeración y mercancías peligrosas; física de deformación; cálculo de amarres, frenado, vuelco o resistencia estructural; lectura por cámara obligatoria; AR/VR; IA generativa durante ejecución; conexión permanente a internet; acreditación regulatoria. Las cargas incompatibles con este alcance se rechazan, no se simulan como mercancía genérica.

## 3. Actores y recorridos

**Supervisor:** importa, corrige datos, configura vehículo, calcula, compara, inspecciona restricciones, congela una revisión y exporta. **Estibador:** consulta el siguiente bulto, identifica producto/lote, confirma colocación, informa excepción y consulta descarga. Son modos de interfaz en S1, no roles de seguridad.

Recorrido feliz: abrir demo → importar manifiesto → resolver errores → calcular → revisar pendientes y condiciones → congelar plan completo válido → abrir guía → escanear/identificar → confirmar colocado → repetir → reconciliar → exportar sesión.

Recorridos obligatorios alternativos: no cabe; dato desconocido; lector sin coincidencia; código ambiguo; SKU equivocado; bulto dañado; faltante; recarga; almacenamiento lleno; cancelación del cálculo; cambio de ruta después de empezar; pérdida de WebGL. Cada uno tiene mensaje accionable y conserva la evidencia histórica. Un cambio de manifiesto no transfiere confirmaciones a la nueva sesión (§11).

## 4. Contrato de datos y unidades

El implementador generará `contracts/manifest.schema.json` y `contracts/plan.schema.json` en JSON Schema, versión fijada y soportada por el validador elegido. Este brief define la semántica; los esquemas deben reflejarla. Los ejemplos JSON no sustituyen el esquema.

### Entidades obligatorias

| Entidad | Campos y reglas |
|---|---|
| Manifiesto | `schema_version`, `trip_id`, `manifest_revision`, `data_mode` (`SYNTHETIC`/`IMPORTED`), `vehicle`, `stops`, `lines`, `packing_profiles`, `config`. |
| Vehículo | `vehicle_id`, dimensiones interiores útiles, abertura de puerta, `max_payload_g`, obstáculos rectangulares, procedencia y estado de medición. No confundir carga útil con peso bruto. |
| Parada | `stop_id`, `stop_order` entero único positivo, `display_name`. Orden 1 se atiende primero. Órdenes discontinuas se admiten y ordenan numéricamente. |
| Línea | `line_id` único, `order_id`, `stop_id`, `sku`, `description`, `packaging_id`, `quantity` entera positiva, `packing_profile_id`, `lot`, `lot_status`, `lot_requirement`, `expiry_date`, `identifiers`, `source_refs`. |
| Perfil | `packing_profile_id`, dimensiones exteriores de bulto, `unit_mass_g`, `shape_model`, `fragility_class`, orientaciones permitidas, carga superior admisible por orientación, procedencia, estado de medición. |
| Unidad | `unit_id`, `line_id`, índice ordinal, identidad física opcional, posición y orientación del plan, estado de sesión. Una unidad representa exactamente un bulto de esa presentación. |
| Identificador | `type`, `value` string, `scope` (`ARTICLE`/`UNIT`), presentación y, si aplica, lote. Nunca convertir códigos a números ni perder ceros iniciales. |

`lot` y `expiry_date` existen con valor `null` cuando se desconoce o no aplica; `lot_status` distingue `KNOWN`, `UNKNOWN`, `NOT_APPLICABLE`. `lot_requirement` es obligatorio por línea: `REQUIRED` u `OPTIONAL`. `KNOWN` exige string no vacío; `UNKNOWN`/`NOT_APPLICABLE` exigen `lot=null`; `REQUIRED + NOT_APPLICABLE` se rechaza. `REQUIRED + UNKNOWN` permite planeación exploratoria pero bloquea congelamiento para guía y selección/colocación hasta una nueva revisión con lote conocido. `OPTIONAL + UNKNOWN` permite guía con limitación visible. No inferir la exigencia desde descripción o SKU. FEFO no se optimiza: se respeta el lote asignado por el manifiesto.

`source_refs` conserva sistema, referencia de exportación y fecha con offset si se conoce. No se inventa fecha ni autoridad. `data_mode=IMPORTED` significa importado, no medido ni certificado.

### Representación numérica

Internamente longitudes en **milímetros enteros**, masas en **gramos enteros**. Coordenadas enteras y orientaciones de 90°. El importador legado convierte cm/kg a mm/g; rechaza precisión no representable en lugar de redondear silenciosamente. Rechaza negativos, ceros en dimensiones/peso, NaN, infinitos y números fuera del rango entero seguro. Cálculos agregados verifican desbordamiento; volúmenes se pueden serializar como enteros decimales en string si exceden ese rango.

La UI muestra cm, kg y m³ sin usar su redondeo para validar restricciones. Tolerancia geométrica del kernel: cero en la rejilla entera; tocar una cara está permitido, interpenetrar no. Las holguras son parámetros geométricos explícitos, no tolerancias ocultas.

### Identidad estable

`unit_id` usa `uid-v1:` seguido de base64url sin padding de los bytes UTF-8 de la tupla JSON canónica `[trip_id, manifest_revision, manifest_hash, line_id, ordinal]`. El ordinal inicia en 1 y pertenece exclusivamente a su línea. La codificación de tupla es reversible y no usa concatenación ambigua ni hashes truncados. Cambiar manifiesto genera un nuevo universo de unidades. Un reintento del mismo manifiesto no crea otras identidades. Una etiqueta externa única se conserva como alias, no reemplaza arbitrariamente la identidad interna.

`manifest_hash` es SHA-256 del manifiesto normalizado, excluyendo solo su propio campo hash; incluye procedencia y configuración. El par `(trip_id, manifest_revision)` es inmutable dentro del registro conocido: misma huella se reutiliza; huella distinta produce `REVISION_CONTENT_CONFLICT`. Una instalación sin historia no puede saber que existe otra versión externa: incluir la huella en `unit_id` evita identidad compartida entre contenidos distintos aun en ese caso. Importar una sesión debe registrar/verificar también su manifiesto. Cambios de configuración para comparar planes exigen nueva revisión; la comparación usa además una huella de demanda que excluye configuración, revisión y metadatos de captura, pero incluye líneas, cantidades, perfiles, paradas y vehículo.

Para `scope=UNIT`, cada identificador declara `ordinal` entre 1 y `quantity`. S1 admite un solo identificador UNIT por ordinal; ordinal repetido o valor de escaneo repetido en cualquier alias UNIT se rechaza. Los identificadores ARTICLE no tienen ordinal y pueden compartir código entre líneas compatibles. Un mismo valor normalizado no puede significar UNIT y ARTICLE a la vez. S1 compara valores exactamente como strings Unicode válidos, sensibles a mayúsculas, sin trim ni conversión numérica; rechaza controles, cadenas vacías y sustitutos Unicode inválidos. Solo el adaptador lector elimina el terminador de transporte Enter, nunca caracteres del identificador. Conservar `raw_value` en el evento. Si se añade normalización por simbología, requiere versión nueva y prueba de colisiones; no se ejecuta implícitamente.

Una línea de 15 cajas de 12 botellas representa 15 unidades de carga, no 180. La UOM/presentación se valida antes de expandir. Dos líneas con igual SKU y distinto lote/parada no se fusionan.

### Aceptación del manifiesto

Importación atómica: errores estructurales o referencias inexistentes impiden calcular. Se muestran ruta JSON, valor y motivo. Datos opcionales ausentes producen limitaciones explícitas. Cantidad expandida máxima S1: 2,000 bultos, 200 líneas y 20 paradas; límites de producto propuestos, no capacidad ya medida. Archivo máximo: 10 MiB. Rechazar antes de expandir entradas que excedan límites; no truncar.

El formato original sin identidad de línea, códigos ni resistencia se puede convertir a borrador con informe de campos faltantes. No se completan medidas ni resistencia con valores «típicos». Permitir simulación exploratoria con perfiles sintéticos elegidos explícitamente, conservando el original y marcando `assumption_status=SYNTHETIC_ASSUMPTIONS`. `data_mode` conserva el origen SYNTHETIC/IMPORTED. Cada perfil/vehículo declara `provenance= SYNTHETIC|DECLARED|MEASURED|UNKNOWN`; `assumptions[]` enumera ruta de campo, valor sustituido, valor usado y motivo. El estado derivado es `NONE_DECLARED` o `SYNTHETIC_ASSUMPTIONS`; este último aplica si cualquier perfil/vehículo usado es sintético o se sustituyó un dato por supuesto. Se propaga a plan, sesión, métricas, UI y reporte sin perder el origen. `NONE_DECLARED` no significa medido.

## 5. Modelo espacial y carga representada

Ejes canónicos: `x=0` en mampara, `x=L` en puerta trasera; `y=0` piso, `y=H` techo; `z=0` pared izquierda vista desde mampara hacia puerta, `z=W` derecha. Dominio interior `[0,L] × [0,H] × [0,W]`. Posición es esquina mínima; dimensiones orientadas son `dx,dy,dz`. La UI muestra flechas y puerta para evitar invertir la secuencia.

Cajas rígidas: cuboides exteriores. Costales/bultos deformables: `ENVELOPE_ONLY`, envolvente exterior conservadora declarada, orientación permitida y `max_top_load_g=0`; no sostienen otras unidades en S1. Peso sigue siendo real o sintético según procedencia, nunca se infiere del volumen.

Obstáculos son cuboides fijos no utilizables como soporte: pasos de rueda, zonas prohibidas y reservas de espacio. Se comprueba colisión y acceso. Geometría distinta exige envolvente conservadora o se declara no soportada. Capacidad volumétrica usa volumen interior menos unión de obstáculos; obstáculos no pueden solaparse en S1 para simplificar y validar ese cálculo. Importación rechaza intersección de volumen positivo entre obstáculos, fuera de límites o dimensiones no positivas; contacto de caras sí se admite. Rechaza también `stop_gap_mm` negativo/no entero. La abertura de puerta declara intervalo lateral y vertical explícitos, contenidos en la cara trasera.

Unidades requieren al menos una orientación permitida. No asumir que «esta cara arriba» permite acostar una caja. La apertura real de puerta se verifica en la trayectoria de entrada/salida, no solo contra el interior.

## 6. Reglas duras del motor

| ID | Regla | Comprobación y efecto |
|---|---|---|
| R01 | Cada unidad se asigna a una sola posición o queda no colocada | Conjuntos disjuntos y unión igual al universo; cualquier fallo invalida resultado. |
| R02 | Dentro del volumen útil y sin colisiones | AABB y obstáculos, incluyendo holguras declaradas. |
| R03 | Orientación autorizada | Enumeración del perfil. |
| R04 | Apoyo completo y conservador | En piso o sobre una única unidad cuya cara superior contiene totalmente la base; mismo nivel exacto. No puentes ni voladizos. ENVELOPE_ONLY jamás genera una superficie de apoyo candidata, independientemente de clase o valor de resistencia. |
| R05 | Compatibilidad A/B/C | Política sintética de demostración: pares soporte→superior válidos A→A/B/C y B→B/C. C no soporta ninguna unidad. Todo par permitido queda además sujeto a resistencia acumulada. |
| R06 | Resistencia acumulada | Masa total de descendientes en árbol de apoyo ≤ carga superior admisible de cada soporte en su orientación. Dato desconocido: perfil no apilable. Verificador reconstruye árbol desde geometría, rechaza ciclos y acumula masas post-order desde hojas; incluye todos los descendientes, no solo hijos directos. |
| R07 | Carga útil | Suma de masa colocada ≤ `max_payload_g`. |
| R08 | Paradas y extracción | Bandas de parada ordenadas y secuencia de descarga comprobada sin retirar bultos de otra parada. |
| R09 | Apertura y recorrido de extracción | Volumen barrido de cada bulto hasta fuera de puerta no colisiona y cabe por abertura. |
| R10 | Identidad y lote | Toda colocación conserva línea, presentación, parada y lote; selección ambigua no confirma. |
| R11 | Revisión inmutable | No confirmar eventos contra una revisión distinta ni editar posiciones del plan congelado. |
| R12 | Resultado explicable | Todas las unidades no colocadas tienen motivo y conteo; no convertir búsqueda agotada en imposibilidad demostrada. |

R05 usa clases declaradas, no deduce «pesado = A». R06 se evalúa incluso si el par de clases está permitido. Estas reglas son decisiones conservadoras de producto, no normas de embalaje. No se permite desactivarlas desde un toggle para conseguir un plan verde. Cambiar política exige versión/configuración nueva y revalidación; nunca cambia una sesión comenzada.

**Modelo de resistencia:** carga vertical estática distribuida en apoyo completo, sin rigidez, humedad, vibración ni pandeo. No estima presión local ni resistencia del piso. La evaluación de esas condiciones queda `NOT_EVALUATED` en S1 y no se vende como prevención garantizada de merma.

## 7. Heurística cerrada para la primera implementación

Objetivo prioritario: plan completo verificable, reproducible y comprensible. No prometer resolver todas las configuraciones físicamente posibles. El verificador independiente decide validez aunque el generador diga éxito.

### Estrategia S1: bandas longitudinales por parada

1. Expandir unidades y validar perfiles.
2. Procesar paradas en orden descendente: última entrega cerca de mampara.
3. Cada parada ocupa una banda longitudinal exclusiva; la siguiente comienza después del máximo `x+dx` ocupado por la anterior más `stop_gap_mm` (predeterminado 0).
4. En cada banda generar exactamente el conjunto de candidatos definido en §7.1, sin búsqueda continua ni candidatos ocultos.
5. Probar solo orientaciones permitidas y respetar R01–R10. Apilar exclusivamente en la misma parada. Orden estable por clase A/B/C, masa descendente, volumen descendente y `unit_id` ascendente.
6. Ordenar posiciones factibles por `x+dx` menor, altura `y` menor, distancia del centro de bulto al centro lateral menor, `x`, `z`, y código de orientación ascendente. Las reglas duras siempre prevalecen sobre puntajes.
7. Ejecutar dos variantes deterministas: orden anterior y orden por volumen descendente dentro de clase y `unit_id` ascendente para empates. Elegir por unidades colocadas descendente, masa colocada descendente, extensión longitudinal ascendente y hash canónico ascendente.
8. Cada intento tiene tope de 200,000 evaluaciones de candidato y conserva su configuración. Una evaluación es una tupla única `(unit_id,x,y,z,orientation)` sometida al validador incremental, contada una vez aunque falle la primera regla. Comprobar varias reglas no incrementa el contador. Los candidatos geométricamente fuera de límites se filtran antes y no se cuentan. Generación de candidatos se contabiliza aparte; el contador no se reinicia por bulto ni parada. Al llegar al tope se conserva el prefijo evaluado y se termina esa variante; no se inicia otra unidad. El tope es propuesto y se mide. No usar tiempo de pared para decidir qué solución canónica gana. Una cancelación manual no genera plan completo.
9. Verificar geometría, árboles de apoyo, masa y secuencias. Un fallo del verificador termina `INVALID_INTERNAL_RESULT` y conserva diagnóstico; no publica el plan como apto.

La restricción de bandas y apoyo único puede dejar espacio desaprovechado. Es una limitación deliberada del MVP. Una versión futura puede usar soportes múltiples y restricciones de precedencia más flexibles, pero necesita modelo mecánico, pruebas y contrato nuevos.

### 7.1 Enumeración canónica `bands-v1`

S1 fija holguras generales en cero; solo `stop_gap_mm` puede ser entero no negativo. Una holgura general distinta se rechaza como no soportada. Orientaciones se identifican por las permutaciones `XYZ,XZY,YXZ,YZX,ZXY,ZYX` de dimensiones originales longitud/altura/ancho; el perfil enumera las permitidas y la evaluación usa ese orden ASCII. Orientaciones con dimensiones idénticas no se fusionan porque sus resistencias pueden diferir.

Para cada unidad y orientación `(dx,dy,dz)`, con inicio de banda `b`, definir cajas B = obstáculos + unidades colocadas. `edges_x` contiene ambas caras x de B y los límites b,L; `edges_z` contiene ambas caras z de B y límites 0,W.

```text
X = sorted_unique({e, e-dx : e in edges_x})
Z = sorted_unique({e, e-dz : e in edges_z})
Y = sorted_unique({0} + {top(u): u colocada en esta parada,
                                   u.shape_model == RIGID_BOX})
C = todas las combinaciones X × Y × Z para esta orientación
filtrar x>=b, y>=0, z>=0, x+dx<=L, y+dy<=H, z+dz<=W
```

No se usa la parte superior de obstáculos como Y. Soporte/colisión se resuelven después; alineaciones derivadas de caras distintas sí se combinan. Unir candidatos de orientaciones, deduplicar por tupla completa y enumerar por `(x+dx,y,abs(2*z+dz-W),x,z,orientation)` ascendente. Es la misma prioridad del paso 6 expresada sin flotantes. Evaluar hasta hallar el primero factible; si ninguno lo es, marcar esa unidad no colocada y continuar. Una parada sin colocados no consume longitud ni gap. Una con colocados fija el siguiente b en su máximo x+dx más gap. Bultos de paradas anteriores siguen como obstáculos y no como soportes.

La validación incremental comprueba geometría, soporte, resistencia y carga útil; la prueba de secuencias y ejes de la variante terminada es obligatoria antes de seleccionarla. Entre variantes que superan verificador elegir por §7 paso 7. Una variante con fallo geométrico interno termina la corrida completa en INVALID_INTERNAL_RESULT; una variante que falla extracción/ejes queda como diagnóstico `CONSTRAINT_FAILED`, no es elegible como plan válido. El conjunto vacío no es una solución utilizable. Si no hay ninguna variante verificada no vacía, devolver `NO_SOLUTION_FOUND` y las causas. El presupuesto cuenta candidatos de colocación; no incluye la verificación final, cuya ejecución es obligatoria para publicar cualquier plan.

Para evitar explosión de memoria, enumerar C por iteradores/heap, sin materializar su producto cartesiano completo. Pausas cooperativas del worker no cambian orden ni contador. El manifiesto de ejecución registra evaluaciones, candidatos generados y motivo de terminación por variante.

### 7.2 Serialización y hashes `canonical-v1`

Normalizar objetos de dominio antes de serializar: paradas por `(stop_order,stop_id)`, líneas por `line_id`, perfiles por ID, obstáculos por ID, identificadores por `(scope,value,ordinal)`; arrays de pasos/eventos conservan orden semántico. Rechazar IDs duplicados. Identificadores ordenados por unidades UTF-16, sin locale. Normalizar `-0` a 0 y rechazar números no finitos o no enteros en artefactos canónicos; fracciones se guardan como numerador/denominador de enteros decimales en string.

Serializador: objetos con claves ordenadas por unidades UTF-16; arrays en su orden normalizado; UTF-8 sin BOM; sin espacios; strings con comillas y backslash escapados, controles como `\u00xx` minúsculos, resto Unicode válido sin escapes opcionales; enteros decimales sin exponente, ceros iniciales ni signo +; null/boolean según JSON. SHA-256 completo, hexadecimal minúsculo. La implementación incluye vectores golden de bytes y digest; no depende de orden incidental de propiedades.

`candidate_hash` para desempatar usa exclusivamente lista de posiciones ordenada por `unit_id`, no nombre de variante, tiempos ni el propio hash. Si hashes y posiciones coinciden, gana variante 1. `plan_hash` incluye entrada normalizada, versión del algoritmo, configuración, posiciones, secuencias y resultados canónicos; excluye únicamente su propio campo, duración, fecha de cálculo y datos de presentación. Esos metadatos se guardan aparte. UI, reportes y sesiones incluyen el mismo `plan_hash`. Un hash detecta diferencias respecto de una referencia conocida; no prueba autoría ni impide que alguien altere archivo y hash juntos.

### Resultados del plan

`COMPLETE_VALID`: todas las unidades colocadas y verificador geométrico/estático simplificado conforme. `PARTIAL_VALID`: subconjunto válido no vacío, pendientes explícitos. `NO_SOLUTION_FOUND`: búsqueda finalizada sin solución utilizable dentro de estrategia/presupuesto. `INVALID_INPUT`, `INVALID_INTERNAL_RESULT`, `CANCELLED`: sin autorización de guía de carga.

`COMPLETE_VALID` **no equivale a apto para transporte**. Además se publican dimensiones de evaluación independientes: geometría, resistencia modelada, extracción, ejes, identidad y validación física. Valores `PASS`, `FAIL`, `NOT_EVALUATED`, con causas.

Un plan parcial se puede inspeccionar y exportar como diagnóstico, pero no iniciar guía. Para cargar menos, el supervisor crea revisión de manifiesto ajustada con faltantes explícitos y vuelve a calcular. No se cambia la cantidad original para ocultar pendientes.

## 8. Secuencias de carga y descarga

**Descarga:** paradas ascendentes; dentro de cada una, solo unidades sin descendientes apoyados encima y con trayectoria recta libre hacia `+x`. Desempate: `x+dx` descendente, `y` descendente, `unit_id` ascendente. El movimiento mantiene orientación y altura hasta salir de puerta. Después de retirar cada unidad, revalidar el conjunto restante. Si no hay siguiente unidad elegible, declarar bloqueo y lista de obstáculos.

**Carga:** inversa exacta de la secuencia de descarga verificada. Validar que al introducir cada bulto están presentes sus soportes y libre el recorrido inverso. El modelo no afirma que una persona pueda levantarlo o alcanzarlo: ergonomía, equipo de manipulación y zonas de acceso humano son verificación del piloto.

Simular cada estado de carga y descarga para masa, apoyos y, si existe modelo de ejes, reacciones. No basta comprobar el camión lleno: la descarga puede desplazar el centro de masa restante.

Se exportan `load_steps[]`, `unload_steps[]` y dependencias. El visor anima esas transiciones; no construye una secuencia distinta. Cambiar cámara o velocidad no cambia un solo paso.

## 9. Centro de masa y ejes: modelo y límites

Centro de masa de carga: `Cx=Σ(mi*xi)/Σmi`, y análogos `Cy,Cz`, usando centros de los cuboides. Si no hay carga: `null`, nunca NaN ni 0 interpretado como medición. La división se redondea solo para mostrar; exportar numerador/denominador o precisión fijada para reproducción.

No existe regla universal 60/40 en el producto. Distribución por mitades es un indicador descriptivo, no límite por eje.

### Modelo opcional de dos apoyos equivalentes

Requiere coordenadas longitudinales `xf,xr` respecto de la misma mampara, `xr>xf`, carga en vacío por apoyo `Tf,Tr` medida/configurada, capacidades por apoyo y datos de procedencia. El grupo trasero puede representar un tándem; **no se reparte automáticamente 50/50 entre sus ejes**.

En kg equivalentes de reacción estática:

`Rr = Tr + Σ[mi*(xi-xf)/(xr-xf)]`  
`Rf = Tf + Σmi - Σ[mi*(xi-xf)/(xr-xf)]`

Internamente usar unidades coherentes g/mm; exportar unidades. Conservación: `Rf+Rr=Tf+Tr+Σmi`. Reacción negativa o capacidad excedida produce alerta de modelo y bloquea la calificación de ejes. Cargas fuera de la distancia entre apoyos no se recortan: sus momentos se conservan.

Si faltan datos, `axle_status=NOT_EVALUATED`. S1 permite demo geométrica con ese estado visible. Con modelo completo, el verificador lo evalúa en todos los pasos; un exceso impide `COMPLETE_VALID` dentro de esa configuración. Con modelo completo, **cualquier `axle_status=FAIL`**, incluida reacción negativa, impide `COMPLETE_VALID` y guía; esta regla aplica en todos los estados intermedios y vacío. Valores no finitos o denominador no positivo producen `INVALID_INPUT`, no NOT_EVALUATED. Validar `xr>xf` semánticamente al importar: JSON Schema estándar no compara por sí solo dos propiedades. Un objeto de ejes parcial con valores presentes válidos queda NOT_EVALUATED; valores presentes inválidos se rechazan. La UI siempre muestra `axle_status` junto a reacciones. Si solo hay dos grupos, la UI jamás muestra «cada eje conforme». No se codifican límites legales mexicanos ni se promete evitar multas.

**Golden case sintético:** xf=0, xr=4,000 mm; tara 2,000 kg delante y 1,000 kg atrás; bulto de 1,000 kg con centro x=2,000 mm → 2,500/1,500 kg equivalentes. El caso prueba aritmética del modelo, no un vehículo real.

## 10. Identificación y escaneo

S1 soporta lector USB/Bluetooth configurado como teclado y entrada manual. El lector entrega una cadena terminada en Enter; la app no presume cámara, permisos o compatibilidad universal de escáneres.

Resolución en orden: identificador único de unidad → identificador de artículo/presentación → SKU exacto manual. Si hay varios lotes, líneas o paradas compatibles, pedir selección explícita y mostrar cantidades. Nunca asignar el primer resultado en silencio.

Un GTIN/código de artículo repetido no identifica una caja particular. En ese caso solo puede seleccionarse la unidad del siguiente `load_step` si pertenece al grupo seleccionado; el orden de la secuencia prevalece sobre ordinal y se registra `identity_level=ARTICLE_CONFIRMED`. Una etiqueta única contrastada permite `UNIT_CONFIRMED`. Lote capturado manualmente se registra como tal. No atribuir trazabilidad individual a un código compartido.

S1 acepta identificadores normalizados del manifiesto y códigos propios. Parsing GS1 complejo no es obligatorio en S1; un código no soportado informa el motivo. No emitir SSCC ni afirmar conformidad GS1 sin implementación y pruebas de sus reglas.

Consultar cualquier caja en el visor no cambia su estado de sesión. Solo el siguiente paso global puede transicionar a SELECTED; un escaneo de una unidad futura muestra «aún no corresponde». Una única unidad puede estar SELECTED por sesión. Escanear selecciona/verifica; **no confirma colocación por sí solo**. El usuario pulsa «Colocado» después de moverlo. Repetir lectura no duplica carga. Repetir etiqueta única ya colocada muestra «ya registrado». Para códigos compartidos, cada nuevo bulto requiere nuevo ciclo explícito de selección y colocación.

El sistema prueba coherencia entre entrada y manifiesto. Sin etiqueta única o sensor no demuestra posición física ni evita que se escanee una caja y se cargue otra.

## 11. Sesión, eventos y recuperación

Estados de plan: `DRAFT → CALCULATING → COMPLETE_VALID/PARTIAL_VALID/...`; solo completo válido puede congelarse `FROZEN`. Recalcular crea revisión nueva. Estados de sesión: `NOT_STARTED → LOADING → LOADED_RECONCILED → UNLOADING → COMPLETED`, con `PAUSED` y `ABORTED` explícitos. S1 puede ensayar descarga; no confundirla con entrega al cliente.

Estado por unidad: `PENDING → SELECTED → PLACED → UNLOADED`. `SELECTED → PENDING` cancela selección. Daño/faltante abre incidencia, pausa avance y conserva manifiesto. No equivale a unidad cargada. Revertir una colocación exige la política de último paso definida más abajo, confirmación explícita y evento compensatorio; nunca borrar historial.

Evento mínimo: `event_id`, `session_id`, `plan_hash`, `sequence`, `event_type`, `unit_id` si aplica, `command_id`, `payload`, fecha con offset en bitácora operativa. Secuencia estricta por sesión. La clave de idempotencia es `(session_id,command_id)`. Persistir huella del comando normalizado (tipo, unidad/payload, plan_hash y revisión esperada), ack completo y unidad asignada. Mismo ID y misma huella devuelve exactamente el ack previo, incluida asignación de unidad; mismo ID y huella distinta devuelve `COMMAND_ID_REUSED` sin éxito ni mutación. Reintentos se resuelven antes de comprobar si la revisión esperada quedó antigua. Una intención nueva exige ID nuevo.

Cada SELECT/PLACE/UNLOAD incluye `expected_sequence`. Una transacción comprueba esa secuencia, plan_hash, estado, siguiente paso autorizado, lote, identidad, soportes y trayectoria modelada; actualiza evento, snapshot y ack juntos. Si cambió la secuencia devuelve `STALE_STATE`; no reasigna otro bulto silenciosamente. Dos comandos SELECT concurrentes no reservan dos unidades: uno gana y el otro se rechaza o informa selección existente, sin nuevo evento. Después de PLACE, un comando nuevo puede seleccionar el siguiente paso compatible. Reversión S1 solo de la última colocación no revertida antes de iniciar descarga, con trayectoria inversa libre; devuelve exactamente al prefijo anterior. UNLOAD sigue estrictamente `unload_steps`; no admite saltos.

Cambio de ruta/manifiesto tras comenzar: pausar y archivar la sesión anterior sin borrar ni alterar eventos. Crear revisión y sesión nuevas con todos los bultos PENDING; no migrar PLACED ni convertir un resumen histórico en estado actual. La UI advierte que el estado físico previo debe reconciliarse aparte. La nueva sesión solo puede arrancar como ensayo S1 desde vacío; replanear físicamente un camión parcialmente cargado queda fuera de S1.

Separar artefactos:

- `plan.json`: determinista, sin reloj real, entrada normalizada, versión/configuración, posiciones y secuencias.
- `verification.json`: reglas, estado y evidencias calculadas.
- `session.json`: snapshot + eventos operativos y fechas; no promete hash idéntico entre operaciones humanas.
- `metrics.json` y `report.html`: derivados del mismo plan/sesión, con alcance visible.

Persistencia HTTPS: IndexedDB con evento, registro idempotente y snapshot en una transacción; mostrar «guardado» solo tras commit. Fallo/cuota bloquea confirmaciones, conserva memoria y permite exportar respaldo. El log completo de eventos aceptados es autoritativo; el snapshot contiene `snapshot_sequence` y es una proyección regenerable.

Restaurar = validar manifiesto/plan/hash, eventos contiguos desde 1 y comandos; plegar log con el mismo reducer y guardas desde estado inicial; comparar por unidad, selección, paso, incidencias, estado y contadores contra snapshot. No basta igualdad de cantidades. Discrepancia bloquea aplicar snapshot; permitir reconstrucción explícita desde log íntegro y conservar el archivo discrepante. Log inválido bloquea continuación y permite exportar diagnóstico. Reintentos después de restaurar conservan acks e idempotencia; fechas operativas no deciden orden.

En HTTPS y mismo origen/partición: mantener Web Lock exclusivo `stowiq:<session_id>` durante escritura y una comprobación transaccional de revisión/sequence por comando. Segunda pestaña solo lectura. Botón «Solicitar control» espera liberación; el cierre de dueña permite adquirirlo, recargar/reproducir estado y continuar. No robar control por heartbeat de cinco segundos: pestañas suspendidas pueden seguir vivas. Si el lock no se libera, informar y pedir cerrar la pestaña dueña; no aplicar una escritura de respaldo. Si Web Locks/IndexedDB no están disponibles, solo consulta de sesiones durables y modo ensayo separado. BroadcastChannel puede actualizar vistas, no otorga autoridad.

`file://` conserva cálculo, 3D, ensayo interactivo y exportación/importación offline, pero **no comparte una sesión escritora durable entre pestañas**. Cada apertura crea una sesión volátil de ensayo nueva; importar eventos los abre en solo lectura. «Continuar como ensayo» crea sesión hija con ID nuevo, `parent_session_id`, hash del historial importado y eventos de inicialización validados; no sobrescribe ni fusiona la sesión original. Dos ensayos derivados son ramas distintas y no se suman como una carga. Mostrar siempre «ensayo local volátil; exporta para conservar». Sin prometer exclusión de pestañas con origen nulo, timestamps ni memoria local. Continuar la misma sesión durable exige HTTPS en origen/partición verificados. Esta es una acotación explícita respecto de v1.0.0.

## 12. Interfaz y diseño de interacción

### Supervisor

Cinco áreas claras: Datos · Plan · Verificación · Comparar · Exportar. Encabezado compacto con viaje, revisión y estado; no panel fijo que ocupe gran parte de la pantalla. KPI siempre distingue planeado/confirmado. Resumen de fallos con enlace al bulto, regla y campo afectado.

Vista principal: camión 3D, lista de paradas y panel contextual plegable. Opciones planta, lateral, isométrica, cámara libre y restablecer. Techo/paredes removibles visualmente. Selección por lista y por geometría produce el mismo `unit_id`.

Corte por altura `y_cut`: oculta unidades cuya base está por encima del corte y recorta visualmente las que lo atraviesan. `y_cut` se limita a [0,H]; una entrada fuera de rango muestra el valor efectivo y aviso. No altera selección por lista, estado ni métricas. El panel indica que son **cortes continuos**, no capas discretas cuando las alturas difieren. Bultos ocultos siguen en el plan y métricas. «Solo parada», «solo pendientes» y búsqueda SKU no borran entidades del motor.

Colores por parada; estado por contorno/icono/texto para no mezclar dos significados en un solo color. Tooltip/panel: SKU, descripción, presentación, lote, `unit_id`, parada, peso, resistencia conocida/desconocida, coordenadas y paso. Soporte de teclado equivalente al clic.

### Estibador

Prioridad visual: SKU/nombre/lote y acción antes que 3D; el visor es apoyo plegable. Una acción principal por paso: «Verificar bulto» → «Colocado». Mostrar nombre/SKU legibles, lote si corresponde, destino, dibujo con referencia puerta/mampara, posición resaltada y cantidad pendiente. Botones «No coincide», «Dañado/faltante», «Pausar». No permitir arrastrar cajas para saltarse el verificador.

Objetivos UI propuestos: controles táctiles ≥48×48 CSS px, texto principal ≥16 px, sin interacción exclusiva por hover ni gestos complejos. A 360×800 px, encabezado persistente ≤20% de altura visible y acción principal accesible sin quedar tapada; paneles secundarios plegados. A 768×1024 y 1280×800, visor y lista aprovechables. Probar orientación vertical/horizontal y teclado de pantalla.

WebGL no disponible: planta/alzado 2D y tabla con información completa; aviso «3D no disponible». Esta degradación conserva consulta y exportación. La aceptación de S1 3D exige además WebGL probado en el entorno declarado.

## 13. Métricas, comparación y honestidad del reporte

| Métrica | Fórmula/semántica |
|---|---|
| Bultos planeados | Conteo de posiciones válidas; no son cargados. |
| Bultos confirmados | Eventos PLACED menos reversiones aplicables; no son sensor físico. |
| Pendientes de planeación | Universo − colocados; desglose por motivo. |
| Ocupación cúbica | Suma de volumen de envolventes colocadas / volumen útil interior. No «porcentaje de producto sólido». |
| Uso de carga útil | Masa colocada / carga útil configurada. |
| Completitud de manifiesto | Unidades colocadas / unidades solicitadas. |
| Completitud de carga | Unidades confirmadas / unidades del plan congelado. |
| CoG y reacciones | Modelo de §9, con estado de datos. |
| Bloqueos de descarga | Número y lista de pasos sin extracción válida. Para plan completo válido: cero. |
| Identidad | Conteos UNIT_CONFIRMED, ARTICLE_CONFIRMED y captura manual. |

Denominador cero → `NOT_APPLICABLE`, no porcentaje inventado. No crear KPI de ahorro, merma evitada o minutos ganados sin mediciones.

Comparar dos planes del mismo manifiesto/vehículo y configuraciones distintas. Mostrar entrada/hash, estrategia, colocados, pendientes, peso, ocupación, restricciones y limitaciones. Si cambia demanda, marcar comparación no equivalente y no atribuir diferencia al algoritmo. Recomendación determinista usa el mismo orden de prioridad de §7 y explica cada criterio; no usa texto generado como prueba.

## 14. Arquitectura y distribución

Decisión propuesta para S1: TypeScript, motor puro sin DOM, validador JSON Schema, Three.js para visor, UI DOM/CSS ligera sin framework obligatorio y esbuild con script explícito de inyección para generar HTML único. Conservar TypeScript y Three.js; no adoptar Tailwind CDN ni renderer custom por una preferencia histórica no verificada. Versiones exactas se fijan al comenzar implementación, con lockfile, inventario de licencias y comprobación de compatibilidad. No declarar dependencias ya instaladas.

Módulos: `domain`, `import`, `packing`, `verification`, `sequencing`, `session`, `reporting`, `ui`. El verificador no llama a la función «es válido» del generador como única prueba: recalcula invariantes desde posiciones y datos. UI solo emite comandos y lee snapshots.

Cálculo en Web Worker para no bloquear interacción; worker clásico autocontenido empaquetado como string dentro del HTML, creado mediante Blob de JavaScript y URL.createObjectURL, sin imports ni fetch externos. Probar handshake de inicio y liberar URL al terminar. No hay fallback silencioso al hilo principal: fallo de inicio deshabilita cálculo y explica entorno no compatible. AC17 exige que funcione en Chromium declarado tanto file:// como HTTPS. CLI y worker usan el mismo kernel. La distribución debe incluir CSS, JS, fuentes necesarias y geometría procedural, sin descargas obligatorias. Restricciones CSP del host deben permitir la estrategia de worker escogida y quedar verificadas; no desactivar políticas globales del servidor para hacer funcionar una demo.

Fuente mantenible en repositorio; `dist/stowiq.html` es salida generada, no archivo editado a mano. Tamaño objetivo inicial ≤8 MiB, medido sin compresión; excederlo requiere causa y decisión documentada, no ocultar dependencias externas.

Publicación prevista: ruta estática en `heroldo.org`, una vez disponible acceso y autorización de despliegue. Este brief no afirma que se haya publicado ni que exista un repo nuevo. No reutilizar el repo del WMS por suposición.

## 15. Privacidad y límites de exposición

El HTML público contiene solo datos sintéticos. Los archivos elegidos por el usuario se procesan localmente; cero llamadas de red de negocio en S1. No almacenar tokens WMS en HTML, query strings o almacenamiento local. Importaciones y descripciones se tratan como datos: evitar `innerHTML`, scripts, URLs ejecutables y fórmulas peligrosas en CSV exportado.

Validar bytes antes de leer; revisar profundidad (máximo 32) y tamaño de strings (máximo 16,384 caracteres) con escáner léxico consciente de escapes antes de JSON.parse dentro del worker; capturar errores y validar conteos/esquema antes de expandir. Rechazar claves duplicadas en objetos JSON; errores no incluyen contenido sensible completo en consola. No cargar modelos remotos ni telemetría. Reportes y sesiones exportados pueden contener datos del manifiesto: indicar su contenido antes de descargarlos. Borrar sesión local es acción explícita del usuario; no borrar automáticamente evidencias para liberar cuota.

## 16. Rendimiento: metas a verificar

Perfil de referencia propuesto: laptop Intel i5-8250U, 8 GB RAM, SSD, Chromium estable con versión registrada, WebGL2 cuando esté disponible. Segundo perfil de aceptación táctil: tablet Android real elegida antes de S2; si no está disponible, reportar «táctil emulado», no «tablet validada».

Fixture medio: 500 bultos, 50 líneas, 8 paradas. Fixture límite: 2,000 bultos, 200 líneas, 20 paradas. Dimensiones/masas sintéticas fijadas y empaquetabilidad conocida para el fixture básico. Una ejecución de calentamiento y cinco medidas, con hashes de dataset, versión, navegador, hardware, tiempos y memoria disponible.

Metas iniciales: fixture medio mediana ≤5 s; fixture límite ≤20 s; selección de bulto p95 ≤200 ms en 100 interacciones; cancelación visible ≤1 s; escena media ≥30 FPS mediana durante recorrido de cámara de 30 s. Son metas de producto propuestas, no resultados.

Límite de trabajo determinista manda sobre duración. Si rendimiento no cumple, perfilado y reducción de costo sin omitir verificaciones. S1 puede publicarse como demo funcional con rendimiento no cumplido visible y límite operativo reducido explícitamente; no puede afirmar capacidad de 2,000 bultos validada si no pasó. No hay excepción a identidad, reconciliación, restricciones ni recuperación.

Render usar InstancedMesh o equivalente probado y selección por ID. Agrupar geometrías/materiales compatibles, no exigir un grupo por SKU ni prometer un número fijo de draw calls. No dibujar menos bultos silenciosamente para mejorar FPS; si se usa agregación visual, publicar conteos y mantener selección individual. La evaluación S1 se hace sobre el conjunto dibujado real.

## 17. Matriz de aceptación

Cada AC exige resultado `PASS`, `FAIL`, `NOT_RUN` o `NOT_APPLICABLE` con razón. Ninguno está ejecutado al escribir este brief.

| ID | Prueba mínima / resultado esperado | Hito |
|---|---|---|
| AC01 | Manifiesto válido, inválido, referencias rotas, exceso de límites y UOM: rechazo atómico con campo y motivo. | S0 |
| AC02 | Expansión repetida: mismos IDs, total exacto, SKU/lote/parada conservados. | S0 |
| AC03 | Casos de paredes, obstáculos, contacto exacto y solape de 1 mm: verificador detecta cada violación. | S0 |
| AC04 | Orientación prohibida y puerta menor que bulto: rechazo, aunque interior alcance. | S0 |
| AC05 | Voladizo, puente, soporte inexistente y perfil deformable como soporte: rechazo. | S0 |
| AC06 | Matriz A/B/C y resistencia acumulada: carga indirecta sobre abuelo excedida debe fallar. | S0 |
| AC07 | Tres paradas, bloqueo geométrico y soporte entre paradas: secuencia válida o motivo observable. Carga inversa también verificada. | S0 |
| AC08 | Exceso de carga útil, golden de §9, datos de ejes incompletos y exceso solo después de descarga: estados correctos. | S0 |
| AC09 | No cabe, presupuesto agotado y cancelado: ninguna unidad desaparece ni se declara imposible sin prueba. | S0 |
| AC10 | Cinco repeticiones CLI y worker: mismo plan y hash con idénticos datos/versiones; cámara no cambia resultado. | S1 |
| AC11 | Clic/lista/lector apuntan al mismo ID; SKU repetido, lote ambiguo, código desconocido y etiqueta repetida no cargan automáticamente. | S1 |
| AC12 | Confirmar, reintentar comando, revertir, pausar y reabrir: conteos y eventos reconciliados. | S1 |
| AC13 | Recarga tras guardar, cuota agotada, snapshot corrupto y dos pestañas: sin pérdida silenciosa ni doble confirmación. | S1 |
| AC14 | Selección 3D, corte, filtros y vistas: IDs/posiciones dibujadas coinciden con plan; métricas no cambian por ocultación. | S1 |
| AC15 | 360×800, 768×1024, 1280×800: sin acción tapada, navegación teclado, táctil emulado, contraste y estados legibles. | S1 |
| AC16 | JSON/CSV/reporte y sesión reimportados: cantidades, versiones y limitaciones iguales; texto malicioso no se ejecuta. | S1 |
| AC17 | HTML distribuido abre offline sin red; file:// calcula y exporta; HTTPS persiste según contrato; ausencia WebGL conserva alternativa. | S1 |
| AC18 | Benchmark con mediciones, hardware y hashes; no mezclar éxito funcional con cumplimiento de metas. | S1 |
| AC19 | Recorrido completo de demo sintética y caso negativo; instrucciones reproducibles desde descarga limpia. | S1 |
| AC20 | Plan, reportes y UI distinguen planeado/confirmado, sintético/importado, validado/no evaluado; ninguna promesa de legalidad. | S1 |
| AC21 | Vehicle/perfiles medidos, identidad física, método de carga, observación y báscula cuando aplique; desviaciones registradas. | S2 |
| AC22 | Contrato real WMS, permisos y reconciliación externa probados contra sistema autorizado. | S3 |
| AC23 | Escanear/confirmar caja superior antes de base por UNIT/ARTICLE: rechazo sin PLACED; secuencia correcta sí completa. | S1 |
| AC24 | Reacción negativa conserva masa pero bloquea plan completo; verificar función aritmética y un caso geométrico realizable. | S0 |
| AC25 | Alias UNIT resuelve ordinal explícito; repetidos, ambiguos y fuera de rango se rechazan atómicamente. | S0 |
| AC26 | Igual revisión/contenido conserva identidad; revisión conocida con cambio de 1 g/lote/parada falla; revisión nueva no hereda eventos. | S0 |
| AC27 | Reintento de SELECT compartido devuelve mismo unit_id/ack; ID con payload diferente falla; dos SELECT simultáneos no duplican selección. | S1 |
| AC28 | Cambio de ruta tras dos colocaciones conserva historial y abre nueva sesión con cero colocados; rechaza comandos cruzados. | S1 |
| AC29 | Goldens de candidatos con alineaciones cruzadas, obstáculos, soportes, empate de variantes, bytes/hash y presupuesto: CLI=worker. | S1 |
| AC30 | Lote OPTIONAL desconocido muestra límite; REQUIRED desconocido impide congelar/seleccionar; contradicción NOT_APPLICABLE se rechaza. | S1 |
| AC31 | Importado con un perfil sintético conserva origen y supuestos en cada artefacto, ida/vuelta incluida. | S1 |
| AC32 | Snapshot con IDs colocados cambiados pero conteos iguales es rechazado; replay de log íntegro recupera estado exacto y acks. | S1 |

Pruebas adicionales de propiedades: permutar orden de líneas sin cambiar semántica normalizada conserva resultado; duplicar `line_id` se rechaza; modificar 1 mm/1 g en frontera puede cambiar aceptación y debe propagarse. Generar casos pequeños aleatorios con semilla fija y validar invariantes independientemente. Casos golden calculados a mano para geometría, resistencia, masa y centro de carga.

Evidencia por AC: versión/commit, fecha, entorno, fixture, comando/procedimiento, esperado/observado, resultado y hash de artefactos. Playwright captura UI, errores y red; los píxeles no prueban invariantes mecánicos. Prueba manual táctil se identifica como manual. Guardar en `artifacts/acceptance/<run_id>/acceptance_manifest.json`.

## 18. Plan de construcción y puertas

**Fase A — contrato y fixtures:** esquemas, ejemplos válidos/negativos, coordenadas, perfil sintético y comandos reales del proyecto. Salida: contratos validables y pruebas AC01–02. Antes de crear archivos, revisar instrucciones del repositorio si existe.

**Fase B — núcleo S0:** motor, verificador, secuencias, métricas y CLI; AC03–09, AC24–26 verdes. Benchmark temprano para detectar explosión combinatoria. No invertir todavía en texturas ni animaciones.

**Fase C — recorrido S1:** interfaz 2D/3D funcional, identificación, eventos, persistencia/exportación y worker; AC10–17, AC23 y AC27–32. Primero un recorrido vertical de 12 bultos/3 paradas; después datasets medios/límite.

**Fase D — cierre S1:** AC18–20, guion demo, paquete autónomo, README y manifiesto. Publicación solo si se dispone del destino y la autorización de tarea. Si falta acceso, entregar HTML y registrar despliegue pendiente; no declarar URL verificada.

**Fase E — piloto S2:** datos medidos y protocolo físico; medir utilidad. No se inicia automáticamente por terminar S1.

Asignación inicial orientativa: 50% motor/contratos/pruebas, 25% recorrido/persistencia, 25% 3D/cierre. No es cotización ni calendario. Se avanza por evidencia. Si una auditoría descubre defecto crítico, corregir su causa y regresiones antes de declarar cierre; no reescribir aceptación para legitimarlo.

## 19. Paquete de entrega

Repositorio con fuente modular, lockfile, scripts reproducibles y contratos; HTML autónomo; fixtures sintéticos válidos/negativos; CLI; pruebas y evidencias; planes/reportes de ejemplo; README breve con inicio, alcance, límites y recuperación; este brief como especificación canónica; inventario de dependencias/licencias; manifiesto de aceptación y benchmark.

Comandos previstos a implementar: `npm ci`, `npm run dev`, `npm run test`, `npm run test:e2e`, `npm run bench`, `npm run build:single`, `npm run verify:dist`, `npm run plan -- <manifest>`. Son contrato deseado, no comandos comprobados hoy; el README final solo mostrará los realmente implementados y probados.

Estructura sugerida, adaptable al repo existente: `src/domain`, `src/packing`, `src/verification`, `src/session`, `src/ui`, `contracts`, `fixtures`, `tests`, `scripts`, `docs`, `artifacts/acceptance`, `dist`. No crear carpetas vacías ni duplicar reglas en varias fuentes.

La entrega S1 debe incluir AC01–20 y AC23–32 conformes funcionalmente; AC18 permite rendimiento documentado no cumplido bajo §16. AC21–22 se declaran fuera de alcance. El motor solo, una captura o un video no satisfacen S1.

## 20. Piloto y criterio de valor

Hipótesis de valor se contrasta con operaciones comparables: tiempo de planeación, tiempo de carga, reacomodos observados, errores de selección y completitud. Registrar mezcla, volumen, vehículo, personal y condiciones. No atribuir causalidad a una única corrida ni interpretar ausencia de daño en una prueba pequeña como garantía.

Antes del piloto, Heroldo/operación define casos y umbral de mejora que justificaría continuar; no se inventa ROI. El sistema conserva excepciones y tiempo dedicado a capturar datos. Si el costo de mantener dimensiones/perfiles supera utilidad observada, simplificar alcance o detener expansión.

Condiciones para detener guía física: medidas no confiables, envase deformado, soporte no previsto, imposibilidad de acceso/manipulación, identidad ambigua o discrepancia con carga real. El demo sigue siendo usable para análisis; el estado físico no se resuelve cambiando un color.

## 21. Auditoría multicasa y control de versiones

Esta versión es base R2. Se recibieron auditorías pegadas de DeepSeek y un dictamen S1, además de Qwen y una Radiografía ejecutiva adjuntas; ChatGPT/Codex consolidará hallazgos sin asumir que una calificación sustituye evidencia. No se solicita delegación automática ni se atribuyen aprobaciones.

Formato de hallazgo: ID, sección/regla afectada, contradicción o caso reproducible, impacto, severidad, corrección propuesta y prueba necesaria. Severidad crítica: permite falso plan válido, pérdida/duplicación de unidades o una afirmación física no sustentada. Alta: impide recorrido/recuperación o deja contrato crítico ambiguo. Media/baja: mejoras sin invalidar integridad.

Resolver por evidencia: confirmado, rechazado con razón, duplicado o pendiente. Cada corrección cambia versión y registra impacto en contratos, fixtures y aceptación. No acumular anexos contradictorios: actualizar la sección canónica y conservar changelog.

Cierre documental READY TO USE: implementación decidible, exclusiones visibles, estados definidos, pruebas reproducibles y sin contradicciones críticas conocidas. Cierre del producto: manifiesto del alcance con pruebas ejecutadas. Las auditorías recibidas revisaron v1.0.0, no v1.1.0. Su resolución documental se registra en §24; ningún veredicto externo se transfiere automáticamente a R2. Cualquier hallazgo crítico confirmado reabre el cierre documental.

### Encargo sugerido para auditores

> Audita este brief como especificación ejecutable, no como presentación comercial. Busca contraejemplos en identidad de bulto, UOM, resistencia acumulada, extracción LIFO, carga inversa, ejes en estados intermedios, datos desconocidos, persistencia, concurrencia, determinismo y HTML autónomo. Distingue simplificación declarada de defecto. Por cada hallazgo cita sección, construcción del contraejemplo, resultado incorrecto permitido y corrección mínima comprobable. No supongas código ni pruebas existentes. Dictamina READY, REVISE o REWRITE para construcción S1, y separa ese dictamen de uso físico S2. No agregues funcionalidades ajenas para elevar la nota.

## 22. Fuentes, límites y pendientes

**Fuentes internas:** mini brief aportado por Heroldo; brief WMS v3.3 leído en esta sesión, especialmente separación kernel/renderer, reconciliación, evidencia y alcance publicable. No se ha inspeccionado código de StowIQ porque no se suministró un repositorio.

**Referencias técnicas consultadas por búsqueda el 26/10/2026:**

- IMO/ILO/UNECE CTU Code, referencia general de empaque y distribución de carga: https://unece.org/transport/intermodal-transport/imoilounece-code-practice-packing-cargo-transport-units-ctu-code
- CTU Code, principios de packing y distribución: https://wiki.unece.org/spaces/TransportSustainableCTUCode/pages/23102046/3%2BPrinciples%2Bof%2Bpacking
- GS1 Logistic Label Guideline: https://www.gs1.org/tr/standards/gs1-logistic-label-guideline/current-standard
- GS1 Albania, elementos GTIN y SSCC en etiqueta logística: https://www.gs1al.org/en/news/create-logistic-labels-gs1-print

Consulta limitada a resultados indexados; el acceso directo a varias páginas devolvió 403. No se realizó revisión íntegra de normas ni se acredita conformidad. La identidad, reglas conservadoras, algoritmo y pruebas de este documento son decisiones propuestas del proyecto. El CTU Code sirve de contexto, no convierte este algoritmo en cálculo de aseguramiento de carga.

Pendientes que **no bloquean construir S1 sintético**: repo/destino final, vehículo medido, perfiles reales de resistencia, códigos/lotes reales, tablet/lector de muelle y contrato WMS. Sí bloquean afirmar S2/S3 según corresponda. No usar los 750×240×240 cm y 14,000 kg del mini brief como valores universales: son un ejemplo sin validación física.

### Changelog v1.0.0

Se completa la idea inicial con identidad de unidad, perfiles y procedencia; se sustituye 60/40 por modelo condicionado; se especifican geometría, apoyo único, resistencia acumulada, extracción/carga, heurística determinista, estados, persistencia y excepciones; se separan demo/piloto/integración; se incorpora UI táctil, benchmark, aceptación y ciclo de auditoría.

## 23. Fixture mínimo de arranque y balances esperados

El constructor debe materializar `fixtures/demo-12.json` con 12 bultos: tres paradas, una línea por parada y cuatro unidades por línea. Identificadores `STOP-1..3`, `LINE-1..3`, `SKU-DEMO-1..3`; lote `DEMO-LOT`, `lot_status=KNOWN`; sin vencimiento aplicable. Todos los valores de este fixture son sintéticos.

Vehículo: interior 3,000×2,000×2,000 mm en orden longitud/altura/ancho; puerta de 2,000 mm de ancho por 2,000 de alto; sin obstáculos; carga útil 1,000,000 g. Bultos rígidos: 500×500×500 mm, masa 10,000 g, clase A, orientación única, carga superior admisible 0 g. Holguras cero, sin modelo de ejes. Así solo se colocan en piso y el algoritmo puede agrupar cuatro unidades en una fila transversal de 500 mm de profundidad por parada.

Balances esperados: 12 unidades, 120 kg, 1.5 m³ de envolventes, 12 m³ interiores, ocupación cúbica 12.5%, carga útil utilizada 12%, longitud ocupada 1,500 mm con la política fijada, cero pendientes. La última parada queda junto a mampara; la primera hacia la puerta. Ejes `NOT_EVALUATED`, identidad inicialmente sin confirmar. Estos son resultados aritméticos esperados del fixture, no una corrida ejecutada.

Variantes negativas independientes: carga útil 100,000 g → no puede completarse; puerta de 400 mm de ancho → ningún bulto sale; cantidad 0 → entrada inválida; repetir `line_id` → entrada inválida; etiqueta de unidad repetida → error de identidad. Fixture independiente `resistance-chain-invalid.json`, sin modificar `demo-12.json` ni su carga superior 0 g: tres cajas de 10 kg apiladas con base que admite 15 kg; la base recibe 20 kg de descendientes y debe fallar aunque cada contacto directo pese solo 10 kg.

## 24. Resolución de auditorías R1 → R2

Estado de todas las correcciones aceptadas: **incorporadas documentalmente, pruebas de software pendientes**. No cerrar hallazgos como ejecución verificada.

| Fuente / hallazgo | Resolución / ubicación |
|---|---|
| DeepSeek H01 | Aceptado: worker Blob sin imports, prueba file/HTTPS en §14, AC17. |
| DeepSeek H02/H04/H05 y observación empate; STQ07 | Aceptados: candidatos, orden, contador, serialización y SHA-256 explícitos §7.1–7.2, AC29. |
| DeepSeek H03/H10; STQ01/STQ05 | Aceptados con ajuste: manda siguiente load_step, no ordinal libre; un único SELECTED, transacción y request hash §10–11, AC23/27. No aceptar dos escaneos simultáneos como dos colocaciones. |
| DeepSeek H06/H07 | Aceptados: obstáculos solapados y gap negativo se rechazan §5, AC01. |
| DeepSeek H08 y observación corte/ejes | Aceptados: clamp visual y estado ejes siempre visible §9/12, AC14/20. |
| DeepSeek H09 y observación hash fixture | Aceptados: fixture exacto materializado y hash lateral antes de benchmark; nunca hash autorreferente. §25, AC18/29. |
| Dictamen pegado H01 | Problema aceptado; remedio de timestamp/memoria rechazado por no garantizar exclusión. Contrato de ensayo separado file:// y locks HTTPS §11, AC13/17. |
| Dictamen pegado H02 | Contradicción rechazada: prueba de resistencia ya era separada. Nombrar archivo independiente §23; base conserva 0 g y balances. |
| Dictamen pegado H03 | Refuerzo aceptado: ENVELOPE_ONLY no genera soporte §6–7; ya era prohibido semánticamente. |
| Dictamen pegado H04; STQ04/STQ06 | Riesgo de revisión aceptado. Ordinales de otras líneas no se desplazan, pero toda revisión crea universo nuevo. Huella y sesión sin migración §4/11, AC26/28. |
| STQ02 | Aceptado: cualquier FAIL de ejes bloquea plan; golden corregido geométricamente §9/25, AC24. |
| STQ03 | Aceptado: alias UNIT→ordinal, unicidad y normalización exacta §4, AC25. |
| STQ08/STQ09 | Aceptados: lot_requirement y procedencia/supuestos explícitos §4, AC30/31. |
| STQ10 | Aceptado: replay completo y equivalencia por unidad/estado §11, AC32. |
| Qwen: stack/memoria histórica | No aceptar preferencias históricas ni complejidad como defecto demostrado; concretar build ligero y mantener 3D solicitado §14. 2D primero como secuencia de trabajo, no sustituto del entregable. |
| Qwen: xr>xf/denominadores | Ya exigidos; reforzar rechazo semántico y pruebas §9. No afirmar que JSON Schema estándar compara campos. |
| Qwen: toma de control | Aceptado con Web Locks y reload; rechazar robo por timeout de 5 s §11. |
| Qwen: resistencia/identidad | Acumulación post-order y tupla reversible; rechazar hash truncado como garantía sin colisiones §4/6. |
| Qwen: datos/instancing/hashes | Incorporados §7/14–16; hash no acredita autoría y 2,000 unidades no implican ~10 draw calls. |
| Qwen: licencias | Inventario y revisión por dependencia ya exigidos; no incorporar comando npx sin pin ni allowlist arbitraria MIT/Apache/BSD. Incompatibilidad confirmada sí bloquea distribución. |
| Qwen: PMF válido / superior al 95% | No sustentado por datos; se mantiene hipótesis y piloto §20. |

La Radiografía adjunta dice fecha de corte 27/10/2026; se conserva como fecha declarada por su fuente, no como fecha comprobada de ejecución. No se recibió su archivo StowIQ.grafo.json: no se verifica la afirmación «0 errores» del registro ni se utiliza para aceptar producto.

### Changelog 1.1.0

Reemplaza reglas ambiguas en sus secciones canónicas; añade AC23–32 y los contratos de candidatos/hash/identidad/lote/procedencia, confirmación secuenciada, reintentos, recuperación y entorno. Acota file:// a ensayo independiente y consulta de historial; la continuación durable de una misma sesión exige HTTPS probado. Conserva base, stack acotado, 3D obligatorio y exclusiones S2/S3. No contiene aprobaciones multicasa de R2 ni evidencia de software.

## 25. Precisiones para fixtures y pruebas de entorno

Fase A materializa `demo-12.json`, `resistance-chain-invalid.json`, `bench-medium.json`, `bench-limit.json` y goldens de candidatos. Guardar SHA-256 de los bytes de cada archivo en `fixtures/manifest.json`; el propio archivo nunca incluye su hash en los bytes hasheados. La huella semántica de §7.2 se conserva además para equivalencia de entradas con distinto formato.

Bench-medium: 50 líneas de 10 unidades; línea i de 1..50 asignada a parada `1+((i-1) mod 8)`. Bench-limit: 200 líneas de 10 unidades, misma regla con módulo 20. Perfil único RIGID_BOX de 100×100×100 mm, masa 1,000 g, clase A, resistencia superior 0 g, orientación única. Vehículo 7,500×2,400×2,400 mm (longitud/altura/ancho), puerta completa, carga útil 14,000,000 g, sin obstáculos/ejes, gap cero. Lotes no aplicables y OPTIONAL, códigos ARTICLE `DEMO-` más índice decimal de línea sin padding; IDs de líneas con índice a tres dígitos, configuración bands-v1. Son pruebas sintéticas de rendimiento, no mix operativo representativo. La comparación de benchmark exige mismos archivos/bytes/versión; datos heterogéneos se prueban aparte en AC29.

AC24 conserva el caso puntual de auditoría x=0 como prueba unitaria de fórmula (Rf=2,600 kg, Rr=-400 kg). No usarlo como caja de volumen positivo centrada en x=0 dentro del camión. Golden geométrico: bulto de dx=200 mm situado en x=0, centro x=100 mm, masa 2,000 kg; xf=1,000 mm, xr=5,000 mm, Tf=Tr=100 kg, capacidad de cada grupo 3,000 kg → Rf=2,550 kg, Rr=-350 kg. Dimensiones restantes y vehículo deben alojarlo; carga útil ≥2,000 kg. Conservación 2,200 kg, sin sobrepasar capacidad positiva, pero FAIL por reacción negativa.

AC13 añade cierre de pestaña dueña/toma de control, pestaña suspendida sin robo y archivo file:// que solo deriva ensayos distintos. AC17 verifica el worker realmente creado, cálculo CLI=worker, cero red, fallo de arranque visible y exportación offline. AC29 publica los conjuntos candidatos exactos y hashes esperados antes de comparar implementaciones; no generar esperado llamando al mismo código bajo prueba. Las pruebas de regresión aún están por implementarse.

Referencias de plataforma consultadas por búsqueda el 26/10/2026: https://www.w3.org/TR/web-locks/ y https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers . Sustentan coordinación por origen y creación de workers desde Blob; la compatibilidad del HTML distribuido sigue exigiendo AC13/17 en los navegadores declarados.

**Entrega actual:** brief documental. **No entregado todavía:** aplicación, código, pruebas ejecutadas, publicación, integración WMS o validación de carga física.
