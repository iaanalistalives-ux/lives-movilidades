# CLAUDE.md — Movilidades (lives-movilidades)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
Pedidos de movilidad (envíos, recojos y traslados), su programación con choferes, la ruta del chofer y el kilometraje.
- `solicitud_movilidad.html` — **formulario de solicitud**, por link directo (fuera del portal): ENVIO, RECOJO o TRASLADO; varios puntos (cada punto es una solicitud); tipo (CARGA/PAQUETE/SOBRE), peso o cantidad, prioridad y fecha. El área se escribe con autocompletado. Después de las 4 pm avisa que queda "fuera de horario". Incluye un consultor de estado por área y fechas, con fotos de entrega. Una clave `idem` evita duplicar envíos.
- `chofer_entregas.html` — **"Mis Entregas"**, app del chofer por link directo: el chofer elige su nombre de la lista; ve su ruta del día (PROGRAMADO de hoy y EN_ESPERA), marca salida y llegada de base (BARRANCO / CHORRILLOS / OTRO, con GPS para el kilometraje), "Llegué al punto", y cierra como Entregado o "No se pudo" (motivo obligatorio), con GPS y fotos opcionales.
- `sara_programacion.html` — **bandeja de programación** de Logística: filtros por estado, zona, urgente, reprogramado, fuera de horario, tipo, chofer y fecha; programa varias solicitudes con un chofer y una fecha; editar, revertir a pendiente o cancelar con motivo. Excel propio (SheetJS) y Excel LIVES. Desde el 24/11/2026: botones «Catálogo» y «Dashboard» al costado de Exportar Excel, que abren `catalogos.html` y `mov_dashboard.html` en una ventana dentro de la misma página (pedido de Christian; excepción a «sin enlaces a otra herramienta»); y quien no tiene permiso de programar (`mov_puede_programar` responde no) ve la bandeja solo para consultar: sin casillas, sin columna Acciones, sin barra de programar y sin el botón «Catálogo» (el catálogo es solo para quien programa; el Dashboard sí lo ve).
- `mov_dashboard.html` — **dashboard** de solo lectura: KPIs y gráficos por estado, zona, servicio, chofer y tendencia; detalle exportable; kilometraje por chofer; revisión de marcas de jornada sin GPS. Excel propio (SheetJS), PNG, impresión y copia HTML descargable. Desde el 24/11/2026: la tendencia tiene selector Día / Mes / Año y empieza por mes (con más de un año, por mes sale una línea por año); «Solicitudes fuera de horario por área» va en barras horizontales, una fila por área, y une la misma área escrita en mayúsculas o minúsculas, con o sin tilde (los errores de tipeo siguen aparte). La pestaña Kilometraje muestra una tarjeta por chofer (km del período, días, paradas, km por día = km ÷ días con km calculado, días por estado y último registro), de mayor a menor; al tocar una tarjeta se abre debajo el detalle por día de ese chofer.
- `catalogos.html` — **catálogos** de puntos (directorio) y choferes, con historial de cambios. Nombres duplicados bloqueados; los registros del sistema anterior solo se desactivan.

## Base de datos (Supabase lives-datos)
- Solicitud: `mov_areas`, `mov_puntos_formulario`, `mov_nueva_solicitud`, `mov_consulta_area` (tabla `mov_solicitudes`).
- Chofer: `mov_choferes_app`, `mov_bitacora_hoy`, `mov_marcar_base`, `mov_deshacer_base` (tabla `mov_bitacora`), `mov_ruta_chofer`, `mov_llegue`, `mov_entregar`, `mov_registrar_fotos` (`mov_solicitudes`).
- Programación: lee `mov_solicitudes` directo; `mov_programar_lote`, `mov_editar_programacion`, `mov_revertir`, `mov_cancelar`.
- Dashboard: lee directo `mov_solicitudes`, `mov_km_diario` y `mov_bitacora`.
- Catálogos: lee directo `mov_puntos` y `mov_choferes`; `mov_puede_catalogos`, `mov_guardar_punto`, `mov_estado_punto`, `mov_borrar_punto`, `mov_guardar_chofer`, `mov_estado_chofer`, `mov_borrar_chofer`, `mov_cambios_lista` (historial en `mov_catalogo_cambios`). Control de versión: si otra persona cambió el registro, la pantalla recarga.
- Kilometraje: tarea programada `mov_km_diario` en la base, todos los días a las 5:00 (hora de Lima), llama a `mov_km_pedir` y llena `mov_km_diario`.
- Fotos: bucket `movilidades` (`fotos/<id>/…`), lectura pública. Las fotos antiguas de Drive se muestran con su enlace original.

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: 3 personas de Logística (gerente y analistas). Además, choferes y áreas solicitantes por link directo.

## Quién puede registrar
- Solicitud y app del chofer: por link directo, sin usuario (excepción acordada). El chofer elige su nombre de la lista; el área se escribe.
- Programar: la base exige permiso (`mov_puede_programar`).
- Catálogos: la base exige permiso (`mov_puede_catalogos`); sin permiso, modo consulta.
- Regla: a un chofer no se le cambia el nombre; si cambia el chofer, se desactiva y se agrega uno nuevo.
- A Sistemas: `mov_puede_programar` y `mov_puede_catalogos` son las piezas que reemplazan con su seguridad.

## Pendientes vistos al escribir esto (21/10/2026)
- (Resuelto el 24/11/2026) El aviso del dashboard sin kilometraje ya no menciona el robot de Code.gs.
- Los Excel propios del dashboard y de programación salen con SheetJS, sin formato LIVES.
