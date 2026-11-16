# Funcionario workflow

This file is the shared map of the funcionario task flow. It starts at `/bandeja-tareas-pendientes`. The registro wizard (Capturar solicitud, Anexar requisitos, Firmar solicitud) is a different flow and is not described here.

Every procedure in the list uses this same task flow. The department in the URL comes from `tramiteList.json` for the row. The pages below are the shared task shells on that department. The form inside a choice is that procedure’s own component. A sentence that names a procedure number is only that procedure. Do not copy it onto another number.

**Department: agricultura** marks a behavior read on that remote’s task shell. **Procedure: `<number>`** marks a behavior read for that procedure only.

## Procedures in scope

Every number below is a `tramite` in `Project/frontend/libs/shared/theme/assets/json/tramiteList.json`. The department is the `department` field on that row. None of these numbers is missing from that file.

| Procedure | Department | `linkDashboard` in `tramiteList.json` |
| --- | --- | --- |
| 40101 | aga | `/aga/transportista-terrestre/solicitante` |
| 40102 | aga | `/aga/transportista-renovacion/solicitante` |
| 40103 | aga | `/aga/modificar-caat-terrestre/solicitante` |
| 570101 | aga | `/aga/cancelacion-servicios-extraordinarios/cancelacion-extraordinarios` |
| 570102 | aga | `/aga/registro-solicitud/solicitud` |
| 420102 | aga | `/aga/concluir-relacion-de/solicitud` |
| 6001 | aga | `/aga/registro-cuentas-bancarias/todospasos` |
| 303 | aga | `/aga/despacho-mercancias/registro/registro` |
| 32514 | agace | `/agace/retorno-seguro-vehiculos-extranjeros/solicitud` |
| 32515 | agace | `/agace/aviso-opcion-seguro-global/solicitante` |
| 32516 | agace | `/agace/acta-de-hechos/solicitud` |
| 31803 | agace | `/agace/registro/solicitud` |
| 317 | agace | `/agace/aviso-unico-renovacion/pantallas` |
| 220102 | agricultura | `/agricultura/peticion-requisitos-fitosanitarios/invocar-modulo` |
| 220103 | agricultura | `/agricultura/sanidad-acuicola-importacion/solicitud` |
| 221601 | agricultura | `/agricultura/zoosanitario/solicitante` |
| 221602 | agricultura | `/agricultura/fitosanitario/solicitante` |
| 221603 | agricultura | `/agricultura/sanidad/solicitante` |
| 220701 | agricultura | `/agricultura/inspeccion-fisica/inspeccion-fisica-zoosanitario` |
| 220702 | agricultura | `/agricultura/inspeccion-fitosanitario/fitosanitario` |
| 220703 | agricultura | `/agricultura/acuicola-fisica/acuicola` |
| 220404 | agricultura | `/agricultura/desistimiento/desistimiento-page` |
| 290101 | amecafe | `/amecafe/cafe-exportadores/solicitud` |
| 290201 | amecafe | `/amecafe/registrar-solicitud/solicitud` |

Task routes, from each remote’s `app-routing.module.ts`:

- Agricultura declares `evaluar`, `verificar-dictamen`, `autorizar-dictamen`, `autorizar-requerimiento`, `confirmar-notificacion`, `proceso-requerimiento`, `atender-requerimiento`, `autorizar`, and `seleccion-tramite`. File: `Project/frontend/apps/agricultura/src/app/application/app-routing.module.ts`.
- Aga declares the same task paths, including `autorizar-requerimiento`. File: `Project/frontend/apps/aga/src/app/application/app-routing.module.ts`.
- Agace declares the same task paths, including `autorizar-requerimiento`. File: `Project/frontend/apps/agace/src/app/application/app-routing.module.ts`.
- Amecafe declares `evaluar`, `autorizar`, `autorizar-dictamen`, `confirmar-notificacion`, `proceso-requerimiento`, `verificar-dictamen`, and `seleccion-tramite`. Its route file does not declare `autorizar-requerimiento`. File: `Project/frontend/apps/amecafe/src/app/application/app-routing.module.ts`.

The dashboard loads the remotes `aga`, `agace`, `agricultura`, and `amecafe` with `loadRemoteModule` in `Project/frontend/apps/dashboard/src/app/app.routes.ts`.

## Shared components

A file inside `tramites/<procedure>/` may be edited directly.

These six folders may also be edited directly. The parent evaluar page around them still needs Allow:

- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220102`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_220103`
- `Project/frontend/apps/agricultura/src/app/application/evaluar/components_221601`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen317`
- `Project/frontend/apps/agace/src/app/application/evaluar/dictamen-31803`
- `Project/frontend/apps/aga/src/app/application/evaluar/dictamen-303`

Ask Allow before editing a component common to every procedure. That tree is `Project/frontend/libs/shared`, including the inbox, `ConsultaioStore`, `app-reviewers-tabs`, `solicitante`, `app-generar-dictamen`, `firma-electronica`, and the wizard buttons.

Ask Allow before editing a component common to a department. Those are the task pages outside `tramites/<procedure>/`: `evaluar`, `verificar-dictamen`, `autorizar-dictamen`, `autorizar-requerimiento`, `confirmar-notificacion`, `proceso-requerimiento`, `atender-requerimiento`, and `tramiteConfig.service.ts`. The parent evaluar page is one of those shells. It is not one of the six folders.

## 1 Bandeja de tareas pendientes

Route `bandeja-tareas-pendientes` loads `BandejaTareasPendientesComponent` with `authGuard` (`Project/frontend/apps/dashboard/src/app/app.routes.ts`, `Project/frontend/apps/dashboard/src/app/bandeja-tareas-pendientes/layout.component.ts`). Its template hosts `lib-bandeja-tareas-pendientes` with title `Bandeja de tareas pendientes` (`layout.component.html`). The host passes `[tieneBandeja]="true"` and `listaRoles` `Dictaminador`, `Autorizador`, `Verificador`, `EmisorOpinion` (`layout.component.ts`).

`LibBandejaTareasPendientesComponent` is `Project/frontend/libs/shared/data-access-user/src/tramites/components/lib-bandeja-tareas/lib-bandeja-tareas.component.ts`. Selector: `lib-bandeja-tareas-pendientes`.

The search form is `BANDEJA_TAREAS_PENDIENTES_FORMA` in `Project/frontend/libs/shared/data-access-user/src/core/enums/bandeja-solicitude-forma.enum.ts`: Folio, Fecha inicial, Fecha final.

Folio. `obtenerDepartamento` calls `aplicarReglaFolioVsFechas` when the field is `folio`. A non-empty trimmed folio sets `fechaInicial` and `fechaFinal` to `null` and sets both fields’ `habilitado` to false. An empty folio sets `habilitado` back to true.

Buscar. The button calls `enviarDatos()` (`lib-bandeja-tareas.component.html`). For `tipo_login() === "funcionario"`, `enviarDatos` builds a body with `numero_folio` equal to the folio control, `fecha_inicio`, `fecha_fin`, and `roles` from `listaRoles`, then calls `consultarTareas`. `consultarTareas` calls `postBandejaTareas` and uses that response as the table. It does not run a second lookup by folio. `revisarTipoDeSolicitud`, called from `enviarDatos` before the post, sets `configuracionTabla` to `TABLADECONFIGUACIONFUNCIONARIO` for a funcionario (`Project/frontend/libs/shared/data-access-user/src/core/enums/bandeja-de-solicitudes-funcionario-solicitante.enum.ts`).

The table is rendered only when `configuracionTablaDatos.length > 0` (`lib-bandeja-tareas.component.html`). `consultarTareas` also sets `tieneConfiguracionTablaDatos` from that same length.

Funcionario column headers in `TABLADECONFIGUACIONFUNCIONARIO`: Folio Trámite, Tipo Trámite, Nombre tarea, Fecha de Asignación de la Tarea, Estado Trámite, Información Adicional, Fecha inicio trámite, Días hábiles transcurridos, Requerimiento, Observación, Opinión.

## 2 Edit icon

`tablaAcciones` is `[TablaAcciones.EDITAR]`. `TablaAcciones.EDITAR` is the string `'EDITAR'` (`Project/frontend/libs/shared/data-access-user/src/core/enums/tabla-seleccion.enum.ts`).

`app-tabla-dinamica` receives `[acciones]="tablaAcciones"` and `(filaClic)="onFilaClick($event)"`. The bandeja does not bind `alternarValor`.

The pencil is the `EDITAR` case in `Project/frontend/libs/shared/data-access-user/src/tramites/components/tabla-dinamica/tabla-dinamica.component.html`: `<i class="bi bi-pencil color-icon-edit">`. That click emits `alternarValor` and does not call `stopPropagation`. The row `(click)` still runs `onFilaClic`. `desactivarEmitirEvento` defaults to `true`, so `onFilaClic` emits `filaClic` with the row (`tabla-dinamica.component.ts`).

`onFilaClick` calls `activarTarea` when the row has an `action_id` path (it returns early to `onFilaSolicitudClick` only when there is no `action_id` and there is an `id_solicitud` and a procedure number). `activarTarea` returns without posting when the row has no procedure number or no folio. Otherwise it calls `postActivarTarea`. Navigation runs only in the `next` handler when `response.codigo === '00'`, and that call is `onFilaClickTareas`.

`onFilaClickTareas` calls `consultaioStore.establecerConsultaio` before any task `navigate`. The signature in `Project/frontend/libs/shared/data-access-user/src/core/estados/consulta.store.ts` is `procedureId`, `parameter`, `department`, `folioTramite`, `tipoDeTramite`, `estadoDeTramite`, `readonly`, `create`, `update`, then action, user, and `id_solicitud`. From this inbox (`tieneBandeja` true) the call passes `readonly` true, `create` false, `update` true. `parameter` is the row origin (`ORIGIN`), after the 32401 rewrite below.

The row is matched in `tramiteList.json` by procedure number only (`v.tramite === PROCEDURE`). The task URL uses `tramiteData[0].department`.

Origin to URL, in source order inside `onFilaClickTareas`. Procedure-specific branches for 300105, 32401, 420101, and 130118 stay in this shared method. They are not copied into a process file. Those IDs are not in the owned list.

- 32401: when the uppercased origin with spaces and underscores removed contains `AUTORIZARDICTAMEN`, the method rewrites `ORIGIN` to `AutorizarRequerimiento` before the rest of the chain.
- `FLUJO_FUNCIONARIO_ATENDER_REQUERIMIENTO` and `AtenderRequerimiento` go to `/${department}/atender-requerimiento` only when the procedure is 220201, 220202, 220203, 220401, 220402, 220403, 220501, 220502, or 220503. Every other procedure, including every owned agricultura procedure, goes to `/${department}/proceso-requerimiento`.
- `CONFIRMAR_NOTIFICACION_RESOLUCION && tramite === 130118 || ConfirmarNotificacionRes`. `&&` binds before `||`, so `ConfirmarNotificacionRes` always goes to `/${department}/confirmar-notificacion`, and `CONFIRMAR_NOTIFICACION_RESOLUCION` does so only when the procedure is 130118. There is no later branch for `CONFIRMAR_NOTIFICACION_RESOLUCION`.
- The next condition has the same precedence: `CONFIRMAR_NOTIFICACION_REQUERIMIENTO` with 130118, or `ConfirmarNotificacionReq`, also goes to `/${department}/confirmar-notificacion`. A later `else if` sends `CONFIRMAR_NOTIFICACION_REQUERIMIENTO` and `ConfirmarNotificacionReq` to that same URL for a procedure that did not match earlier.
- `FLUJO_FUNCIONARIO_CONFIRMAR-NOTIFICACION` goes to `/confirmar-notificacion` with no department.
- `REGISTRAR_OPINION`, `RegistrarOpinion`, and `SolicitarOpinion` go to `/${department}/registrar-opinion` when the procedure is in the opinion list in that condition: 80101, 80102, 80103, 80104, 80105, 80202, 80203, 80205, 130113, 120601, 120602, 120603, 80210, 80211, 120401, 120403, 120404, 120402, 80208, 90101, 130202, 130121, 90102, 90202, 80308, 90201, 80314, 90305, 140201, 130108, 130201, 130204, 250101, 250102, 250103, 230401. None of the owned procedures is in that list.
- 300105 has its own paths, tested before the general evaluar branch: `AsignarTramite` / `Asignar Tramite` → `/${department}/evaluar`; `VerificarAsignacion` / `Verificar Asignacion` → `/${department}/autorizacion-de-rayos-x/verificar-asignacion`; `VerificarDictamen` / `VERIFICAR_DICTAMEN` → `.../autorizacion-de-rayos-x/verificar-dictamen`; `VerificarRequerimiento` / `VERIFICAR_REQUERIMIENTO` → `.../autorizacion-de-rayos-x/verificar-requerimiento`; `AutorizarRequerimiento` / `AUTORIZAR_REQUERIMIENTO` → `.../autorizacion-de-rayos-x/autorizar-requerimiento`.
- `FLUJO_FUNCIONARIO_EVALUAR`, `EvaluarSolicitud`, and `RegistrarOpinion` go to `/${department}/evaluar` when an earlier branch did not match. `RegistrarOpinion` for a procedure that is not on the opinion list falls into this branch. `REGISTRAR_OPINION` is not in this condition.
- `FLUJO_FUNCIONARIO_AUTORIZACION` goes to `/${department}/autorizar`. That page is a different route from `autorizar-dictamen`.
- `FLUJO_FUNCIONARIO_VERIFICAR-REQUERIMIENTO-RESOLUCION`, `VERIFICAR_DICTAMEN`, and `VerificarDictamen` go to `/${department}/verificar-dictamen`. A later duplicate of this same condition is in the file and is not reached. Verification mode on the page requires `parameter` exactly `VerificarDictamen`. The other two origin strings open the URL and do not enter that mode. See section 4.
- `SUBSECUENTES` goes to `/subsecuentes`.
- `AUTORIZAR_DICTAMEN` and `AutorizarDictamen` go to `/${department}/autorizar-dictamen`. The page fills `tramite` and folio only when `parameter` is exactly `AutorizarDictamen`. `AUTORIZAR_DICTAMEN` opens the URL and `ngOnInit` redirects to `/${department}/seleccion-tramite` because `tramite` stays unset. See section 5.
- `AUTORIZAR_REQUERIMIENTO` and `AutorizarRequerimiento` go to `/${department}/autorizar-requerimiento`, except the 300105 branch above.
- `VERIFICAR_REQUERIMIENTO` and `VerificarRequerimiento` go to `/${department}/verificar-requerimiento`, except the 300105 branch above.
- `tareas_usuario === 'CONFIRMAR_NOTIFICACION_ESTRADOS'`, `CONFIRMAR_NOTIFICACION_ESTRADOS`, and `ConfirmarNotificacionEstrados` go to `/${department}/confirmar-notificacion`.
- `AUTORIZAR_OPINION` and `AutorizarOpinion` go to `/${department}/autorizar-opinion`.
- 420101, as a final branch, stores `consultaio420101` and navigates to `/${department}/confirmar-aviso` when `tareas_usuario` is `CONFIRMAR_NOTIFICACION_SOLICITUD`, or to `/${department}/evaluar-solicitud` when `tareas_usuario` is `EVALUAR_SOLICITUD_SOLICITANTE`.

## Task sequence

The same task names apply to every procedure in the list. The department comes from the table above.

Dictamen, after choosing Generar dictamen on Evaluar: `evaluar` → `verificar-dictamen` only when the process assigns a verificar origin (`VerificarDictamen` is the origin that enters verification mode) → `autorizar-dictamen` (`AutorizarDictamen` is the origin that fills the page) → `confirmar-notificacion`.

Requerimiento, after choosing Generar requerimiento: `evaluar` → `autorizar-requerimiento` → the same `confirmar-notificacion` with a requerimiento notification (`tipo_acuse` other than `Resolucion`) → `proceso-requerimiento`.

Generar Observación on autorizar-requerimiento sets `mensajeExito` to `REQUERIMIENTO` and navigates to `bandeja-tareas-pendientes`. That success path does not open `confirmar-notificacion`. **Department: agricultura.** File: `apps/agricultura/.../autorizar-requerimiento/autorizar-requerimiento.component.ts`, `generarObservacion`.

## 3 Evaluar

URL: `/${department}/evaluar`. Every procedure in the list opens this page on its own department. Agricultura, aga, agace, and amecafe each have an `EvaluarComponent` at `apps/<department>/src/app/application/evaluar/evaluar.component.ts`. Each one calls `postOpcionesEvaluacion` and can prepare `GENERAR_REQUERIMIENTO`. The choice body under the tabs is the procedure’s own component. This section is the shell around that body.

The page starts once `ConsultaioStore` has `procedureId` and `folioTramite` (`iniciarEvaluacion`). **Department: agricultura.** That method calls `opcionesEvaluacion`, then `getEvaluacionTramite`, then `getTabs`. `getEvaluacionTramite` calls `opcionesEvaluacion` again when its response codigo is success. The heading is `Evaluar`. The line under it is `Por favor seleccione que tipo de evaluación requiere generar.` The folio header is `lib-encabezado-requerimiento` (`evaluar.component.html`). `indice` starts at `0`, so no option body is shown until a choice is clicked.

### Choice tabs

`postOpcionesEvaluacion` returns the labels. Each label is shown only when `tieneOpcion` finds it in that list. The click uses the position in the list, not the words. `opcionesEvaluacion` maps `response.datos` to `tabsOpcionEvaluacion` with `id: i + 1`. A failed call shows a toastr and leaves the choices empty.

| Position | Prepares | Body |
| --- | --- | --- |
| 1 | `GENERAR_DICTAMEN` | Dictamen. Calls `iniciarDictamen`, `getPrepararEvaluacion('GENERAR_DICTAMEN')`, and `getSentidosDisponibles`. A procedure may then call its own iniciar method |
| 2 | `GENERAR_REQUERIMIENTO` | Requerimiento. Calls `iniciarRequerimiento` only when `dataIniciarRequerimiento` is still unset |
| 3 | `SOLICITAR_OPINION` | `app-solicitar-opinion` |

Evidence: `opcionesEvaluacion` and `seleccionaTab`.

Other department evaluar pages also call `postOpcionesEvaluacion` from `opcionesEvaluacion` after `getEvaluacionTramite` succeeds:

- Aga skips that call when `esRegistrarOpinionEnEvaluar()` is true. That list is 105 and 324, outside this procedure list. File: `apps/aga/.../evaluar/evaluar.component.ts`.
- Agace: `apps/agace/.../evaluar/evaluar.component.ts`.
- Amecafe: `apps/amecafe/.../evaluar/evaluar.component.ts`.

Sections 4 to 6 continue Generar dictamen. Generar requerimiento continues at section 7, then section 6, then section 8.

### Generar dictamen

The shared body is `app-generar-dictamen` (`libs/shared/.../generar-dictamen/`). `TramiteConfigService.getConfig` for the current procedure decides whether fechas, antecedentes, and anexo are on. The title is `Generar Dictamen` when the page is not verificar or autorizar. The buttons on that component, when the origin is not `VerificarDictamen`, are **Guardar y firmar**, **Guardar**, **Cancelar**.

Sentido Dictamen is required. `mostrarSentido` defaults to true. The radios are `opcionesSentidosDisponibles` when that list has rows. Otherwise they are Aceptado (`SEDI.AC`) and Rechazado (`SEDI.RZ`). An empty value shows `Este campo es obligatorio.`

Justificación del dictamen is required and rejects a value that is only spaces. The message is `Este campo es obligatorio.` or `No se permiten solo espacios en blanco.` The length limit and its message come from `maxLengthJustificacionDictamen` for the current procedure. The line `Por favor, no escriba más de 2000 caracteres.` is only the branch `isTramite220202 || isTramite220402 || esTramite2216` (`221601`, `221602`, `221603`).

Antecedentes and fechas render only when `getConfig` turns them on. Atención del requerimiento is filled when iniciar returns `desc_atencion_requerimiento`, and that textarea is created disabled.

**Guardar** marks the dictamen form touched. A valid form emits `guardar`. An invalid form emits a validation event and does not post. **Guardar y firmar** uses the same form check, then the page heading becomes `Firmar` with `firma-electronica`. **Cancelar** stays on this choice.

A procedure may mount a second component under this form. That second component belongs to that procedure. It is not part of this shell.

#### Procedure: `220102`

`seleccionaTab(1)` calls `iniciarDictamen`, then `iniciar220102`. `iniciar220102` posts `postIniciarDictamen`. `muestraHojaTrabajo` becomes true in `finalize` even when that call fails, so the form still renders. A success stores `dataIniciarDictamen` and `historial_observaciones`.

`getConfig(220102)` is `{ habilitarFechas: false, isAntecedentes: false, anexo222se: false, descargaSolicitud: false }` in `apps/agricultura/.../shared/services/tramiteConfig.service.ts`. Antecedentes and fechas stay off. The motivos de rechazo (Cancelado para Retorno, Cancelado para Destrucción, Rechazado para Acondicionamiento) are rendered for `220501`, `220502`, and `220503`, not for `220102`.

The body is `app-generar-dictamen` and then `app-generar-dictamen-220102`. Buttons, in this order, and only when the origin is not `VerificarDictamen`: **Guardar y firmar**, **Guardar**, **Cancelar**. The later button block in `generar-dictamen.component.html` excludes `idTramite === 220102`, so that second copy is not rendered.

`maxLengthJustificacionDictamen` is 2000. The visible limit line is `No puede exceder los 2000 caracteres.` Choosing a sentido emits `cumplimientoChanged`. `onCumplimientoChanged220102` clears the selected mercancía rows and passes the clave and the description to `setCumplimientoSeleccionado`.

**Mercancías** (`components_220102/generar-dictamen_220102`). Heading `Mercancías`. Columns: checkbox, Resolución, Partida, Fracción Arancelaria, Descripción, Nombre común, Nombre científico. An empty table says `SIN DATOS`. Tipo Resolución, Número de Oficio, and Texto para la Resolución appear only after a sentido is chosen. Before a tipo is stored, a row shows a red cross. After a tipo is stored, the Resolución cell shows a green check.

| Action | When | What it does |
| --- | --- | --- |
| Definir Resolucion | At least one row is checked and not every checked row has a tipo | Opens the resolution form. Loads the tipo catalog with the sentido description in upper case. Does nothing to the catalog when that description is still empty |
| Modificar Resolucion | Every checked row already has a tipo | Opens the same form with the first selected row’s tipo, oficio, and texto |
| Guardar Datos | The form is open and valid | Writes tipo, oficio, and texto onto the checked rows, hides the form, and clears the checkboxes |
| Select-all checkbox | While generating | Checks or clears every row |

Tipo `223` makes **Número oficio caso especial** required. Any other tipo clears that value and its validator. Changing the tipo clears the texto, then `getTextoResolucion('220102', id)` fills it. A non-`00` response is a danger toastr.

**Guardar** is `guardar220102`. It marks the form touched. A valid form emits `guardar`. An invalid form emits `validarResolucion`. Both run `validarResolucion220102`. The save is refused unless the table has rows and every row has tipo and texto. The message is `Debe capturar la resolución de las mercancías`, and the page scrolls to it. **Guardar y firmar** uses the same check, then calls `firmarMostrarDictamen220102` and sets `firmar` true. An HTTP error toastr says `Error preparar tramite.` **Cancelar** sets `indice` to `1`, so this choice stays selected.

`guardarDictamen220102` posts `guardarDatos220102`. On any response it scrolls to the top. When codigo is `00` it calls `getTabs()`. That subscribe does not set a toastr.

### Generar requerimiento de información

This is the capture on Evaluar. The next inbox task after a saved requerimiento is Autorizar requerimiento, section 7. It does not go to Verificar dictamen.

`TipoRequerimiento` in `apps/agricultura/.../core/enum/agricultura-core-enum.ts`: `INFREQ.D` datos, `INFREQ.DR` datos y documentos, `INFREQ.R` documentos. `getRequerimientoConfig` for the current procedure turns tipo, área, justificación, and the second document table on or off.

| Index | Label | Component |
| --- | --- | --- |
| 1 | Capturar requerimiento | The department capture component. On agricultura that is `app-capturar-requerimiento-agricultura` |
| 2 | Seleccionar documentos | `app-solicitar-documentos` |

The page opens on Capturar requerimiento. Seleccionar documentos gets `disabled-tab` (`pointer-events: none`, opacity `.5`) when the alcance from iniciar is `INFREQ.D` or missing, and again when the capture form’s tipo is `INFREQ.D` or missing. `INFREQ.DR` and `INFREQ.R` leave the tab clickable. `seleccionaTabRequerimiento` does not read that flag. Opening tab 2 while a saved `id_requerimiento` exists and `isSegundaTabla` is true calls `postDocumentosIniciales`.

Buttons are **Guardar y Firmar**, **Guardar**, **Cancelar**. `221601` uses Cancelar, Guardar, Guardar y Firmar (`evaluar.component.html`).

#### Capturar requerimiento

On agricultura the heading is `Requerimiento` and the line `* Campos obligatorios` is shown. `esSoloLectura` is false on Evaluar, so the form stays enabled. Tipo, área, and justificación follow `getRequerimientoConfig` for the current procedure. Tipo is a required catalog. Área is a required multi-select whose stored value is the option `clave`. Justificación is required. The length rule and any special-character pattern are set for that procedure, not for the whole list.

If iniciar returns `observaciones`, the table **Observaciones al requerimiento** is shown: Fecha de generación, Fecha de atención, Generada por, Estatus, Detalle.

Each value change emits `formChanged`. `ngOnDestroy` writes tipo, justificación, and áreas into `RequerimientosStates`.

#### Seleccionar documentos

Heading `Seleccione los documentos que van a ser requeridos` (`solicitar-documentos-evaluacion.component.html`).

The first table columns are Requerir, Tipo de documento, Nombre del archivo, Estatus, and an eye (`bi-eye`, title `Ver Documento`). The rows are `iniciarResponse.documentos`. The eye opens the file. Checking a row emits that document as requerido.

A procedure listed in `TRAMITES_SIN_DOCUMENTOS_ESPECIFICOS` does not call `getDocumentosEspecificos`, so the Tipo de Documento list stays empty unless another input fills it.

When `isSegundaTabla` is true, the second block **Documentos adicionales** is shown, with Eliminar and Agregar nuevo. The instruction is `Para solicitar documentos adicionales, seleccione el documento y presione "Agregar Nuevo".` If a save or a firma sets `validacionDocumentos` and no document is checked, the line is `Debe seleccionar al menos un documento.`

#### Guardar and firmar

**Guardar** calls `validarFormulariosRequerimiento`. An invalid capture form sets `validacionesActivas`, forces Capturar requerimiento, marks every control touched, and does not post. A valid form posts `postGuardarRequerimiento`. When the tipo is `INFREQ.D`, `documentos` and `documentos_especificos` are sent empty. `rechazarGuardarSinDocumento221602` runs only for `220501`, `221601`, `221602`, and `221603`. Every other procedure, including the rest of this list, can Guardar without a checked document. Success sets `requerimientoGuardado`, shows a success toastr with `resp.mensaje`, scrolls to the top, and calls `getTabs()`. Failure shows an error toastr.

**Guardar y Firmar** uses the same capture check. When the tipo is not `INFREQ.D`, it stops unless a document is checked or a specific document was added, switches to Seleccionar documentos, and shows the document message. `INFREQ.D` skips that check. Success sets `firmar`, shows the heading `Firmar`, and fills `cadenaOriginalRequerimiento`.

**Cancelar** sets the choice index to `1` and the inner index to `1` (`cancelar()`).

#### Procedure: `220102`

`getRequerimientoConfig(220102)` turns on tipo, área, justificación, and the second document table. Justificación `MAX_CHARS` is 10000. `220102` is not in the special-character list (`220401` is). The messages are `Este campo es obligatorio.` and `Por favor, no escriba más de 10000 caracteres.` `onKeydown` blocks typing once the length is already greater than 10000. Navigation keys and Ctrl+A/C/V/X/Z still work. `onPaste` cuts a paste to the remaining characters. `221601` may type past the limit. `221602` also skips the paste cut. The observaciones detalle cell uses `scroll-td-expanded`.

`220102` is in `TRAMITES_SIN_DOCUMENTOS_ESPECIFICOS`, so this page does not call `getDocumentosEspecificos`. **Guardar** does not require a document. **Guardar y Firmar** does, when the tipo is not `INFREQ.D` and nothing is selected. The firmar payload for `220102` and `220103` also sends `usuario_perfil`.

### Reviewer tabs

`app-reviewers-tabs` sits under the choice when `mostrarReviewersTabs` is true (`evaluar.component.html`). It stays on screen for every procedure except `221601`, `221602`, and `221603` while `indice === 1` and `muestraHojaTrabajo` (`mostrarReviewersTabs`).

The bar starts from `libs/shared/theme/assets/json/shared/lista-trimites-tabs.json`: Solicitud, Documentos, Dictamen, Requerimientos, Opiniones, Acuses y resoluciones, Tareas del trámite, Envío digital, Estado de la transmisión. `getTabs` then shows, hides, or disables each one. A service value `null` drops the tab. `false` leaves it visible and disabled (`reviewers-tabs.component.ts`). A disabled tab ignores the click. The page opens on Solicitud.

| Id | Title | What opens | Loaded when |
| --- | --- | --- | --- |
| `solicitud` | Solicitud | The procedure capture component | On open |
| `documentos` | Documentos | `lib-documentos` | First click, `getDocumentosSolicitud` |
| `dictamen` | Dictamen | `tab-dictamen` | First click, `getDictamenes` |
| `requerimientos` | Requerimientos | `tab-requerimiento` | First click, `getRequerimientos` |
| `opiniones` | Opiniones | `tab-opinion` | First click, `getOpiniones` |
| `resoluciones` | Acuses y resoluciones | `lib-bandeja-acuses` | First click |
| `tarea_tramite` | Tareas del trámite | `lib-tareas-tramite` | First click, `getTareasSolicitud` |
| `enviodigital` | Envío digital | `lib-envio-digital` | First click, `getEnvioDigital` |
| `estadoTransmision` | Estado de la transmisión | Empty in the tab list | — |

`onTabSeleccionado` clears the dictamen action buttons until the Dictamen tab is the one selected.

### Solicitud tab

`LISTA_TRIMITES` maps `solicitud` to that procedure’s capture component. `find` uses the first entry for the procedure number. `reviewers-tabs` creates it in the child container and may set `attenterDisableFlow` and `formProcHabilitado` on the instance. The lock the person sees is `ConsultaioStore`, not those two fields.

The fieldset around the child is not disabled on Evaluar: `isDatos` is not passed, and its default is false.

#### What the inbox writes

Before navigate, `establecerConsultaio` stores the row. On this inbox `tieneBandeja` is true. These flags are the same for every procedure:

| Field | Value on Evaluar |
| --- | --- |
| `procedureId` | Procedure number from the row |
| `parameter` | Task origin, such as `EvaluarSolicitud` |
| `department` | Department from `tramiteList.json` |
| `folioTramite` | Folio from the row |
| `tipoDeTramite` / `estadoDeTramite` | From the row |
| `readonly` | `true` |
| `create` | `false` |
| `update` | `true` |
| `id_solicitud` | Id from the row |
| `action_id` / `current_user` | From the row |

Switching between Generar dictamen and Generar requerimiento does not reset the store, so the Solicitud tab stays in this mode.

When `id_solicitud` is already set, the evaluar constructor writes `consultaioSolicitante` with the folio, the row’s estado and tipo, and the stub date `FECHA_DE_INICIO`.

#### Capture child

The child is the component `LISTA_TRIMITES` registered for this procedure. It subscribes to `ConsultaioQuery`. While `readonly` is true the capture stays locked. Shared `solicitante` loads the person with `id_solicitud` through `getDatosSolicitanteEvaluar` (`sat-t${TRAMITE}/tramite/${IDSOLICITUD}/solicitud/solicitante`) when the procedure is not in `TRAMITES_SOLICITANTE_POR_FOLIO`. `puedeUsarPadronSesionComoRespaldo` returns false when `readonly` is true. `esConsultaFuncionario` is true when `update` or `readonly` is true, so the logged-in funcionario RFC is not used. The one update path that still uses the session RFC is update true, create false, and an empty folio. This inbox sets readonly true, update true, and a folio.

If the folio is set and `id_solicitud` is still empty, the component waits on `ConsultaioQuery` and then calls the same service. A procedure that is not in `TRAMITES_SOLICITUD_DESDE_CONSULTAIO` does not refresh this form when fecha or estado later changes in `consultaioSolicitante`. That list is the INBAL procedures plus `240123`. None of the procedures in this file is on that list.

#### Procedure: `220102`

`LISTA_TRIMITES` maps `solicitud` to `PasoUnoComponent` at `apps/agricultura/.../tramites/220102/pages/paso-uno`. `find` uses the first `220102` entry. Paso uno does not read `attenterDisableFlow` or `formProcHabilitado`.

Both panels stay mounted. `[hidden]` switches them. Index 1 is Solicitante (`mostrarRfcCompleto` true). Index 2 is `Datos de la solicitud` while capturing and `Datos generales` while `readonly` is true (`actualizarTituloPestana`).

`220102` is not in `TRAMITES_SOLICITANTE_POR_FOLIO`, so the person loads by `id_solicitud`. That load sets `banEvaluar` true.

`datos-mercancia` disables `formMercancia` and the catalog selects when `readonly` is true. With a folio set and `parameter` other than `AtenderRequerimiento`, `mostrarAccionesMercancia` is false, so the alert and the grid buttons Modificar, Eliminar, and Agregar stay hidden. Limpiar sits inside the mercancía modal. The grid still loads through `obtenerDatosParaGrid`. The person can read the rows and cannot add or edit a mercancía from Evaluar. `guardar220102$` is the Atender save. Evaluar does not emit it, so an empty mercancía table on this screen does not raise `Debe agregar al menos una mercancía`.

## 4 Verificar dictamen

This step is not for every process. The inbox opens `/${department}/verificar-dictamen` for `VerificarDictamen`, `VERIFICAR_DICTAMEN`, and `FLUJO_FUNCIONARIO_VERIFICAR-REQUERIMIENTO-RESOLUCION`. `300105` is handled earlier in `onFilaClickTareas` and goes to `/${department}/autorizacion-de-rayos-x/verificar-dictamen`. The frontend does not keep a list of procedure numbers that include or skip this step. The task appears because the process assigned that origin.

Route: each department that declares `verificar-dictamen` in `app-routing.module.ts`. On agricultura the component is `VerificarDictamenComponent`. Heading `Verificar dictamen`. The folio line is `lib-encabezado-requerimiento`. Buttons, in this order: **Dar Vo. Bo.**, **Observar**.

On agricultura, `220102` and `220103` render the dictamen that was generated on Evaluar. Every other agricultura procedure that lands on this URL gets the shared disabled body: radios Aceptado, Rechazado, and Parcial, and the disabled fields from `inicializaFormTramite` (Fundamento, Justificación, Plazo, Requisito que debe cumplir, Tipo de análisis, Número de muestras, plus número de trámite, tipo de solicitud, siglas del dictaminador, and asignar autorizador). **Vista Preliminar Aceptado** on that shared body calls `observacion()`, which navigates to `/${department}/detalle-v-dictamen`. Aga, agace, and amecafe open the same URL on their own `verificar-dictamen` component.

On the page, `isVerificarDictamen` is `origin === 'VerificarDictamen'`. `aplicarAjustesInicialesFormulario` calls `dictamenForm.disable()` only when `origin === 'VerificarDictamen'`. `VERIFICAR_DICTAMEN` and `FLUJO_FUNCIONARIO_VERIFICAR-REQUERIMIENTO-RESOLUCION` open this URL and do not enter that mode. The constructor runs `getConfig` for the current procedure. `getSentidosDisponibles` runs once the folio is in the store. `getTabs` runs once `id_solicitud` is in the store.

While `isDictamen`, the template shows Dar Vo. Bo. (`firmarMostrarVerificar`) and Observar (`getObservar`). In verification mode the generar buttons are hidden and the title `Generar Dictamen` is not rendered. The person sees the sentido and the justificación from iniciar and cannot change them.

Dar Vo. Bo. posts the mostrar-firma call for this task. Sentido and justificación come from the iniciar data. A success stores `cadena_original`, hides the dictamen, and shows the heading `Firmar` with `firma-electronica`. The signature uses `clave_rol` `Autorizador`. A second click is ignored while `firmando` is true. When `codigo` is `00` and `datos` is present, the page sets `mensajeExito` to `DICTAMEN` and navigates to `bandeja-tareas-pendientes`. Any other result shows the alert `Error al firmar el dictamen.`

When iniciar returned `historial_observaciones`, the table **Observaciones al dictamen** is shown: Fecha de generación, Fecha de atención, Generada por, Estatus, Detalle. Dar Vo. Bo. hides that table.

`app-reviewers-tabs` stays under the dictamen. The bar and the Solicitud child are the ones in section 3. The inbox flags stay `readonly` true, `create` false, `update` true. `parameter` is the verificar origin, so a capture that hides its edit actions unless `parameter` is `AtenderRequerimiento` stays locked.

Observar hides the dictamen and shows **Justificación de la observación**. The control is required, the textarea `maxlength` is 2000, and the visible error when invalid and touched is `Este campo es obligatorio.` **Generar Observación** marks the form touched and returns without posting when it is invalid. A valid form posts `postObservacionGuardar`. Success sets `mensajeExito` to `OBSERVACION` and calls `getRegresar`. `getRegresar` navigates to `bandeja-tareas-pendientes` and does not post. The Regresar button on the observation form calls that same method.

#### Procedure: `220102` on agricultura

`verificarIniciar220102` runs only when `tramite === 220102` and `origin === 'VerificarDictamen'`. It posts `postVerificarIniciar220102` with `current_user` and `action_id` and sets `muestraHojaTrabajo` true in `finalize`. The verificar template never reads `muestraHojaTrabajo`. `getConfig(220102)` still turns fechas, antecedentes, and anexo 222 off.

The page mounts `app-generar-dictamen` and `app-generar-dictamen-220102`. The Resolución cell is a green check on every row. Tipo, oficio, and texto columns stay hidden. Select-all, Definir, and Modificar stay hidden. A check keeps only that row and opens **Detalle de Resolución**. Tipo, oficio, and texto are disabled. Oficio and texto appear only when that row has a value. Clearing the check empties the detail.

**Vista preliminar de la resolución** calls `getVistaPreviaResolucion220102`. An empty folio returns without calling. A `00` response with `contenido` opens the PDF. Anything else is a warning toastr. `VerificarDictamenComponent.vistaPreliminarResolucion` is not bound by the template.

Dar Vo. Bo. posts `postVerificarMostrar220102`. Sentido and justificación come from `dataIniciarDictamen`. The payload sends `usuario_perfil` from the session. The signature posts `firmarVerificar220102`.

## 5 Autorizar dictamen

Route: each department that declares `autorizar-dictamen`. On agricultura the component is `AutorizarDictamenComponent`.

`AutorizarDictamenComponent` sets `this.tramite` and `folioTramite` only inside `if (seccionState.parameter === 'AutorizarDictamen')`. `ngOnInit` navigates to `/${department}/seleccion-tramite` when `this.tramite` is falsy (`autorizar-dictamen.component.ts`). `AUTORIZAR_DICTAMEN` therefore opens the route and leaves `tramite` unset, so `ngOnInit` redirects. This check is the same for every procedure.

The page renders only when `LISTA_TRIMITES` has an entry for the procedure (`slectTramite`). The heading `Autorizar dictamen` comes from `lib-encabezado-requerimiento` with `isAturorizar`. The constructor also writes `consultaioSolicitante` with the folio, the stub date `FECHA_DE_INICIO`, and the row’s estado and tipo. The Solicitud tab stays locked the same way as on Evaluar.

`ngOnInit` calls `iniciarDictamenAutorizar`, `getSentidosDisponibles`, and `getTabs`. Iniciar posts `postIniciarDictamen`. A failure of iniciar shows the toastr `Error al iniciar la autorización del dictamen`.

Sentido dictamen uses the claves from `getSentidosDisponibles` when that list has rows. Otherwise the template shows Aceptado (`SEDI.AC`) and Rechazado (`SEDI.RZ`). The selected value is `ide_sent_dictamen` from iniciar. Justificación is the text from iniciar. Both controls are created disabled. When iniciar returned `historial_observaciones`, the table **Observaciones al dictamen** is shown. **Autorizar** hides that table.

`getModeloConfig` for the current procedure decides whether `app-generar-dictamen` is mounted. When `actualizarVista` is true the page skips that component and shows the disabled sentido and justificación directly. Fechas from `getConfigAutorizarDictamen` are an input of the component that mount skips.

Desistir and Corregir are inside `@if(tramite === 220401)`. Every other procedure, including every number in this list, uses **Autorizar** (`firmarMostrarAutorizarDictamen`) and **Observar** (`getObservar`).

A procedure may also mount its own read-only detail under those controls. That detail belongs to that procedure.

Autorizar calls `postFirmarMostrar`. Sentido and justificación come from the disabled form, which holds the iniciar values. The payload also sends `usuario_perfil` and `id_dictamen`. It hides the observaciones table before the call returns. When `firma_certificado.cadena_original` is present, `cadenasAFirmar` is oficio then certificado. Otherwise it is the oficio chain only. A failure shows an error toastr and stays on the dictamen. Firma calls `firmarAutorizar` with `clave_rol: 'Autorizador'`. Missing cadena or certificate data shows `Faltan datos para completar la firma.` A second click is ignored while `firmando` is true. Success toastr text is `La firma del dictamen se ha realizado correctamente.` Then, if `sentido_dictamen === 'Aceptado'` (that text, not the clave `SEDI.AC`), it calls `postOficioResolucionAutorizacion` and, on success, `postOficioCertificado`. The person sees **Acuses y resoluciones**, table **Resolución(es)**. The certificado row is added after the oficio. Any other sentido calls `postOficioResolucionRechazado` and the same table shows the rechazo document. A failed oficio or certificado call shows an error toastr. The download calls `getDescargarAcuse` and opens the PDF when `codigo` is `00` and `contenido` is present.

`app-reviewers-tabs` is wrapped in `*ngIf="!isDocumento"`, so the reviewer bar is hidden while that table is on screen.

Observar. `observacion` is required and max 2000. The textarea `maxlength` is 2000. The required message is `Este campo es obligatorio.` The maxlength message is `No escribas más de 2000 caracteres.` Generar Observación posts `postObservacionGuardar`, sets `mensajeExito` to `OBSERVACION`, and navigates to `bandeja-tareas-pendientes`. `getRegresar` stays on the page: it clears the observation panel, sets `isDictamen` true, and calls `iniciarDictamenAutorizar` and `getSentidosDisponibles`. The method comment says it returns to the inbox. The body does not.

#### Procedure: `220102` on agricultura

`getConfigAutorizarDictamen(220102)` has `habilitarFechas` true. `getModeloConfig(220102)` is `{ actualizarModelo: true, actualizarVista: true }`, so `app-generar-dictamen` is not mounted and those fechas are not shown. The page does mount `app-generar-dictamen-220102`. Mercancías match Verificar dictamen: no select-all, no Definir or Modificar, a green check on every row, and one checked row at a time. **Vista preliminar de la resolución** uses `getVistaPreviaResolucion220102`.

## 6 Confirmar notificación

Route: each department that declares `confirmar-notificacion`. On agricultura the component is `ConfirmarNotificacionComponent`. Aga, agace, and amecafe register the same path.

Dictamen and requerimiento both open `/${department}/confirmar-notificacion` (see section 2). An estrados notification uses the same URL. The page does not look at the origin to choose a layout. There is no `app-reviewers-tabs` (`confirmar-notificacion.component.html`). The page reads `procedureId`, `folioTramite`, and `action_id` from `ConsultaioStore`.

`getConfirmarNotificacion` calls `getIniciarNotificacion`. A code other than `00` shows `Error al consultar la notificacion` and calls `location.back()`. An HTTP error shows `Error inesperado al consultar notificacion.` and stays on the page. `obtenerAcusesRecibos` runs only for `220202` and `220203`. Every other procedure skips that call.

Heading `Confirmar notificación`. `app-detalles-folio` shows Folio del trámite (`num_folio_tramite`) and Tipo de solicitud (`desc_modalidad`) once iniciar has returned. Then `app-notificacion-acto-administrativo`, heading `Notificación de acto administrativo`. `nombre`, `rfc`, and `fechaHora` are created disabled for every procedure. The values are `nombre_completo`, `rfc`, and `fecha_envio_notificacion`. The RFC label is `RFC`, except `220103`, which uses `Registro federal de contribuyentes`. The only action is **Firmar**. It sets the step to `FIRMAR` and calls `obtenerCadenaOriginal`.

`postCadenaOriginal` sends `fecha_firma`. A `00` response whose `datos` is a string becomes `cadenaOriginal`. Any other code shows `Error al generar la cadena original.` An HTTP error shows `Error inesperado al iniciar trámite.` Missing cadena or certificate data shows `Faltan datos para completar la firma.` A response that is not `00`, or that has no `datos`, shows `Error al firmar la solicitud` and stays on Firmar. A failed guardar shows an error toastr and puts the step back on Firmar.

Heading on the next screen is `Acuses y resoluciones`. **Acuse(s)** uses No. and Documento, plus Descargar. The row comes from `postResolucionGuardar` or `postRequerimientoGuardar` (`nombre_archivo`, `llave_archivo`). **Resolución(es)** uses the same columns and appears when `getAcusesRecibidosNotificación` returned at least one `documentos_oficiales` row (`desc_documento`, `documento_minio`). Descargar calls `getDescargarAcuse`. A `00` response with `contenido` downloads the PDF. **Cerrar** returns to `/bandeja-tareas-pendientes`.

Firmar calls `alContinuar`, which reaches `obtenerCadenaOriginal` → `postCadenaOriginal`. `postCadenaOriginal` sends `fecha_firma`. When `getModeloConfig` for the current procedure has `actualizarModelo` true, the payload also sends `id_notificacion`, `fecha_envio_notificacion`, and `tipo_notificacion`. The `usuario` block in that request is the same for every procedure on this shell: nombre `José`, apellido paterno `Hernández`, apellido materno `Pérez`, rfc `MAVL621207C95`.

`obtieneFirma` posts `postFirma` with `clave_rol: 'Solicitante'`. When `tipo_acuse === 'Resolucion'` it calls `postResolucionGuardar` and `acuse-recibo` shows `La notificación de la resolución para el trámite con número ${folio} ha sido confirmada.` Any other `tipo_acuse` calls `postRequerimientoGuardar` and the alert is `La notificación de Requerimiento de Información para el trámite con número ${folio} ha sido confirmada.` (`acuse-recibo.component.ts`). The dictamen walk expects `Resolucion`. The requerimiento walk expects a `tipo_acuse` other than `Resolucion`.

Cerrar is the button on the acuses step unless the procedure is in `tramitesSinBotonCerrar`. That array is `221601`, `221602`, `221603`, `220701`, `220702`, `220703`. Every other procedure in this list gets Cerrar, and those six also hide `* Campos obligatorios` on the firmar step.

#### Procedure: `220102` on agricultura

`getModeloConfig(220102).actualizarModelo` is true, so the cadena payload adds `id_notificacion`, `fecha_envio_notificacion`, and `tipo_notificacion`. The parent does not pass `procedimiento`, so the RFC label stays `RFC`. `220102` is not in `tramitesSinBotonCerrar` and is not in `tramitesDocumentosResolucion`, so Cerrar is shown and `obtenerAcusesRecibos` is not called.

## 7 Autorizar requerimiento

Route: `autorizar-requerimiento` on agricultura, aga, and agace. Component on agricultura: `AutorizarRequerimientoComponent`. Amecafe’s route file does not declare this path, so `290101` and `290201` do not open this page.

`AutorizarRequerimientoComponent` sets `this.tramite` from `Number(this.guardarDatos?.procedureId)`. It does not branch on the origin string (`autorizar-requerimiento.component.ts`). When that id is missing, `ngOnInit` sends the person to `/${department}/seleccion-tramite`. The page renders only when `LISTA_TRIMITES` has the procedure (`slectTramite`). The folio line is `lib-encabezado-requerimiento`.

`ngOnInit` calls `requerimientosStates.resetStore()`, then `iniciarRequerimiento` (`postIniciarAutorizarRequerimiento` with `current_user` and `action_id`), `getEvaluacionTramite`, and `getTabs`. A success stores `dataIniciarRequerimiento` and opens the Requerimiento tab. An HTTP error shows `err.error.error` or `Error al obtener los criterios`. The constructor writes `consultaioSolicitante` with the folio, `FECHA_DE_INICIO`, and the row’s estado and tipo.

Heading in the template: `Autorizar Requerimiento`. **Descargar datos de la solicitud** is shown only when `getConfig` for the current procedure has `descargaSolicitud` true.

Tab 1 is labeled Requerimiento and passes `[esSoloLectura]="true"` and `[esAtenderRequerimiento]="true"` for every procedure. Because `esAtenderRequerimiento` is true, the heading `Requerimiento`, the line `* Campos obligatorios`, and the plural área label are not used. The área label is `Área solicitante*:`. The disabled fields show `alcance_requerimiento`, the claves from `areas_dependencias` (or `areas_solicitantes` when that first list is empty), and `justificacion`. Tab 2 is labeled Documentos Requeridos and gets `disabled-tab` when `deshabilitarSolicitarDocumentos` is true. That flag is set when `alcance_requerimiento` is `INFREQ.D` or undefined. The same CSS as evaluar applies (`pointer-events: none`, opacity `.5`). `seleccionaTabRequerimiento` does not read that flag. The visible columns are Tipo de documento, Nombre del archivo, and Ver.

The table rendered on tab 2 is `dataIniciarRequerimiento.documentos`. Opening tab 2 when `requerimientoConfig.isSegundaTabla` is true calls `postDocumentosEspecificos`, which posts `postDocumentosAdicionales` and stores the response on `documentosAdicionales`. The template does not bind `documentosAdicionales`. The row action in the template is `bi-archive`, which calls `verDetalleAcuse` → `getDescargarDoc`. The file opens when `data.codigo === "UPSER00"` and `data.datos.content` is present. The template does not render an eye icon on this table.

Autorizar calls `mostrarFirmarRequerimiento` directly. That method does not call a form re-validation. Buttons are **Autorizar**, then **Observar**. `postMostrarFirma` sends `usuario_perfil` with nombre `Javier`, apellido_paterno `Chávez`, apellido_materno `Barrios`, and `rfc: this.guardarDatos.current_user`. Firma posts `postFirmarAutorizarRequerimiento` with `clave_rol: 'Dictaminador'` and `requiere_autorizador: false`. Missing cadena or certificate data shows `Faltan datos para completar la firma.` A second click is ignored while `firmando` is true. A response whose `codigo` is not `00` shows `Error al firmar la solicitud`. The success toastr text is `La firma del dictamen se ha realizado correctamente.` It then sets `mensajeExito` to `REQUERIMIENTO` and navigates to `bandeja-tareas-pendientes`. The next inbox task on this path is Confirmar notificación, then Atender requerimiento.

Observar sets `esObservacion` true and stays on the page. The heading is `Observación`. The justificación control is required and `maxLength(2000)`, the textarea has `maxlength="2000"`, and the error text is `Este campo es obligatorio.` The muted line `Máximo 2000 caracteres` is shown for every procedure except `221601`. Button order is Regresar, then Generar Observación, except `221601`, which uses the opposite order. Generar Observación success sets `mensajeExito` to `REQUERIMIENTO` and navigates to the inbox. That observation does not open Confirmar notificación. `regresar()` sets `esObservacion` false and stays on the page. `app-reviewers-tabs` stays under the requerimiento, under Firmar, and under Observación.

#### Procedure: `220102` on agricultura

`getConfig(220102).descargaSolicitud` is false, so **Descargar datos de la solicitud** is not shown. The observaciones detalle cell uses `scroll-td-expanded`. Button order is Regresar, then Generar Observación.

## 8 Atender requerimiento

Every procedure in this list uses `/${department}/proceso-requerimiento` (section 2). None of them is in the `atender-requerimiento` exception list. On agricultura the component is `ProcesoRequerimientoComponent`. Aga, agace, and amecafe register the same path. The page renders only when `LISTA_TRIMITES` has the procedure (`slectTramite`).

`iniciarAtenderRequerimiento` calls `getIniciarAtenderRequerimiento` with the folio and sets the wizard from `tipo_requerimiento` (`TipoRequerimiento` in `agricultura-core-enum.ts`, step titles in `libs/shared/.../core/enums/constantes-alertas.enum.ts`). A code other than `00` shows `Error al consultar la notificacion` and calls `location.back()`. An HTTP error shows `Error inesperado al consultar notificacion.` and stays on the page.

- `INFREQ.D` → `PASOS_REQUERIMIENTOS_DATOS`: Requerimiento de información, Requerimiento de datos, Firmar promoción.
- `INFREQ.DR` → `PASOS_AMBOS_REQUERIMIENTOS`: Requerimiento de información, Requerimiento de datos, Anexar documentos, Firmar promoción.
- anything else, including `INFREQ.R` → `PASOS_REQUERIMIENTOS_DOCUMENTOS`: Requerimiento de información, Anexar documentos, Firmar promoción.

The page title is `Atender requerimiento`. When `indice === 3` and `pasos.length === 4` (the document step of `INFREQ.DR`) the title is `Anexar requisitos`. The folio line is `lib-encabezado-requerimiento` until the firma step (`proceso-requerimiento.component.html`).

A procedure on `TRAMITES_FLUJO_BOTONES_ATENDER_COMO` uses the atender button layout on this page. `tramitesCargaDocumentosInfreqR` is only `220703`, so every other procedure leaves `usarSoloDocumentosAdicionales` off.

### Requerimiento de información

`lib-requerimiento-informacion`. Fecha de generación and Justificación del requerimiento are disabled. Fecha is `fecha_generacion` passed through `formatearFechaDdMmYyyy`, which returns `dd/MM/yyyy` from a `yyyy-MM-dd` string. Justificación is `justificacion`. The step uses `btn-continuar`. Continuar moves to the next step and does not save.

### Anexar documentos

Shown with `app-paso-carga-documento`, `esAtenderRequerimiento` true, instructions off, the optional-document selector hidden, and `documentos` from iniciar. When the procedure uses the atender button layout and iniciar returned no documents, **Continuar** advances without a file. While the upload section is active the button label is `Cargar archivos` and it stays disabled until a file is selected or a previous document is chosen. After the section is no longer the upload section the same button reads `Continuar`. **Anterior** is shown on that step. On the firma step the heading is `Firmar`. **Anterior** is hidden for `220701`, `220702`, and `220703` (`tramitesSinAnteriorFirma`). The line `* Campos obligatorios` is shown for every other procedure. Those three hide it (`tramitesSinCamposObligatorios`). A `00` firma response hides the wizard and shows **Acuse de recibo**.

`isDatosCalculated` does not appear in the agricultura proceso-requerimiento or evaluar templates. The step-2 fieldset is not driven by that name.

### Requerimiento de datos

This step exists for `INFREQ.D` and `INFREQ.DR`. Buttons are **Guardar** and **Continuar**. Both ask the procedure’s capture component to save before the wizard moves on. `INFREQ.R` does not show this step and does not call that save.

On the datos step, `establecerConsultaIO(false)` writes `readonly` false, `create` false, and `update` true, and keeps `parameter` `AtenderRequerimiento`. The capture child then unlocks. On every other step `establecerConsultaIO(true)` writes `readonly` true, `create` false, and `update` false, so the capture locks again. Edit actions that depend on `parameter === 'AtenderRequerimiento'` can stay available while their controls stay disabled for as long as `readonly` is true.

A save that is not `00` stays on this step. A `00` response writes the returned `id_solicitud` back into `ConsultaioStore`. The resolver message is not rendered by `getValorIndice2` except the special value `PC01`. **Guardar** can show the success alert while the step is still open. **Continuar** leaves the step, so an alert that renders only while `indice === 2` leaves the screen. `INFREQ.D` goes to Firmar. `INFREQ.DR` goes to Anexar documentos. The alert text is the procedure’s own message.

On step 2 the reviewer bar is hidden with `[ocultarPestanasConservarContenido]="indice === 2"`. `mostrarSolicitudSiOcultaPestanas` in `reviewers-tabs.component.ts` keeps the solicitud child mounted and switches to it if another tab was active. `getTabs` is called with `ProcesoSolicitud.ATENDER_REQUERIMIENTO`.

An empty required table on the datos step stays on that step. The visible line is the capture component’s own required message. `getValorIndice2` does not display the resolver sentence except `PC01`.

Firma. `firmarAtenderRequerimiento` sends `clave_rol: 'CapturistaGubernamental'` and `documentos_requeridos` from the firma store. Entering the step calls `postFirmarMostrar`. Missing cadena or certificate data shows `Faltan datos para completar la firma.` A second click is ignored while `firmando` is true. A response whose codigo is not `00` shows `Error al firmar la solicitud`. On success, `txtAlerta` is `El requerimiento se registró exitosamente con folio de requerimiento ${folio}.` (`TXT_ALERTA_ACUSE_RECIBO` in `constantes-alertas.enum.ts`). `postAcuseRecibo` fills the document table from `nombre_archivo` and `llave_archivo`. A failed acuse call shows `Error al obtener oficio.`

`mostrarAlertaArchivosRequeridos` is initialized false and every assignment in this component sets it false. The template `@if(mostrarAlertaArchivosRequeridos)` therefore does not open.

#### Procedure: `220102` on agricultura

`220102` is in `TRAMITES_FLUJO_BOTONES_ATENDER_COMO`, `TRAMITES_WITH_BOTONES_GUARDAR_CONTINUAR_PASO_2`, and `TRAMITES_MENSAJE_EXITO_GUARDADO`. It is not in `tramitesCargaDocumentosInfreqR`.

On the datos step, **Guardar** calls `guardarDatosFormulario220102` and **Continuar** calls `ejecutarGuardado220102`. Both ask Paso uno to save. An empty mercancía table sets `mostrarErrorTabla`. The visible line is `Este campo es obligatorio`. The resolver sentence `Debe agregar al menos una mercancía` is not shown by `getValorIndice2`. A response that is not `00` resolves `Ocurrió un error al guardar la solicitud.` or the service message. An HTTP error resolves `Error al guardar la solicitud.` A `00` response writes `id_solicitud` with `setIdSolicitud`.

The success alert is `MENSAJE_DE_EXITO_ETAPA_UNO` from `tramites/220102/constantes/fitosanitario.enum.ts`, with `_folio_` replaced by `id_solicitud`: `La solicitud ha quedado registrada con el número temporal {id_solicitud}. Éste no tiene validez legal y sirve solamente para efectos de identificar tu solicitud. Un folio oficial le será asignado a la solicitud al momento en que ésta sea firmada.` It renders only while `indice === 2`. The save payload sets `cve_unidad_administrativa` to `1016`.

With `readonly` false the second inner tab is **Datos de la solicitud** and `datos-mercancia` shows Agregar, Modificar, Eliminar, and Limpiar. With `readonly` true the title goes back to **Datos generales** and the form is disabled. `* Campos obligatorios` is shown on Firmar. **Anterior** is shown.

## Traps confirmed in this pass

- Verification mode is the exact string `VerificarDictamen`. The other verificar origins open the URL and do not disable the dictamen form. **Department: agricultura.**
- `AUTORIZAR_DICTAMEN` opens `autorizar-dictamen` and then redirects to `seleccion-tramite` because the page fills the procedure only for `AutorizarDictamen`. **Department: agricultura.**
- **Department: amecafe.** The route file does not register `autorizar-requerimiento`, so `290101` and `290201` do not open that page.
- **Department: agricultura.** The autorizar-requerimiento document table shows `bi-archive`. The Evaluar document selector shows an eye. They are different tables.
- **Department: agricultura.** `mostrarAlertaArchivosRequeridos` on proceso-requerimiento is never set true. `muestraHojaTrabajo` on verificar is assigned and the template never reads it.
- **Procedure: `220501`, `221601`, `221602`, `221603`.** Guardar on Generar requerimiento requires a document. Every other procedure can Guardar without one. Guardar y Firmar requires a document when the tipo is not `INFREQ.D` and nothing is selected.
- **Procedure: `221601`, `221602`, `221603`.** The dictamen limit line is `Por favor, no escriba más de 2000 caracteres.` **Procedure: `220102`.** The dictamen limit line is `No puede exceder los 2000 caracteres.` The requerimiento justificación is a different control, max 10000.
- **Procedure: `220102`.** `guardarDictamen220102` scrolls to the top and calls `getTabs()` when codigo is `00`. That subscribe does not set a toastr.
- **Department: agricultura.** `getRegresar` on Autorizar dictamen stays on the page. The comment on that method says it returns to the inbox.
