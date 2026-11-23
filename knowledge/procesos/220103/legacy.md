# Process 220103 — Requisitos de importación acuícola

Legacy knowledge base. Source of truth for this trámite only. Built from the controller `RegistroSolicitudRequisitosImportacionAcuicolaController`.

Open the files in **Code map**. Do not search `old/`, `legacy/`, or `old code/` for another class with a similar name. A path under **Do not open** is the wrong implementation.

## Identity

- Process number: `220103`
- Surface: `registration`
- Entity: `SolicitudSagarpaRequisitosImportacionAcuicola` — `@DiscriminatorValue` evidence: `DiscriminadorTipoTramiteConstants.SOLICITUD_SAGARPA_REQUISITOS_IMPORTACION_ACUICOLA` (`"220103"`) on `legacy/vuc_mtto_guber/sagarpa/src/mx/gob/ventanillaunica/www/gubernamentales2/sagarpa/requisitosImportacion/model/SolicitudSagarpaRequisitosImportacionAcuicola.java` and `legacy/vuc_mtto_guber/gubernamentales-common/src/mx/gob/ventanillaunica/www/gubernamentales2/common/constants/DiscriminadorTipoTramiteConstants.java`
- Package: `legacy/vuc_mtto_guber/sagarpa/src/mx/gob/ventanillaunica/www/gubernamentales2/sagarpa/requisitosImportacion`
- Registro URL: `/gubernamentales2/sagarpa/requisitosImportacion/registrarSolicitudAC.action` (`REGISTRAR_SOLICITUD_REQUISITOS_IMPORTACION_ACUICOLA_URL_BINDING`)
- Consulta URL: `/gubernamentales2/sagarpa/requisitosImportacion/consultarSolicitud.action` (`CONSULTAR_SOLICITUD_URL_BINDING`)
- Session key: commons `CommonConstants.VARIABLE_SESION_TRAMITE` = `"tramite"` (`legacy/vuc_mtto_commons/base-jms-ibm/.../CommonConstants.java`). Process also stores `ID_TRAMITE` = discriminator string and guber `CommonConstants.LISTA_MERCANCIAS_GUBERNAMENTALES` = `"_listaMercanciaGubernamentales"`
- Spring bean (solicitud): `SolicitudSagarpaRequisitosImportacionAcuicola` in `legacy/vuc_mtto_guber/sagarpa/WebContent/WEB-INF/config/spring/sagarpa/sagarpa-requisitoImportacion-context.xml` (imported from `sagarpa-tramites-context.xml`)
- Module pom base artifact: `gubernamentales-common` (war/jar) and `base-jms-ibm` (war/jar) — commons tree used: `legacy/vuc_mtto_commons/base-jms-ibm`; guber shared: `legacy/vuc_mtto_guber/gubernamentales-common`. Evidence: `legacy/vuc_mtto_guber/sagarpa/pom.xml`

## Code map

One row per behavior this process runs. **File** is the implementation for this process number. **Do not open** is a sibling or a parent that looks alike.

| Behavior | Class | Method | File | Do not open |
| --- | --- | --- | --- | --- |
| Registro wizard entry / URL | `RegistroSolicitudRequisitosImportacionAcuicolaController` | `@UrlBinding` / `getActionForm` / `getSolicitud` / `setSolicitud` / `mostrar` | `legacy/vuc_mtto_guber/sagarpa/src/.../requisitosImportacion/controller/RegistroSolicitudRequisitosImportacionAcuicolaController.java` | `RegistroSolicitudRequisitosImportacionFitosanitarioController` / `...ZoosanitarioController` |
| Shared registro guardar / capture JSP / service wire | `RegistroSolicitudRequisitosImportacionController` | `iniciar` / `mostrar` / `guardar` / `getJspCaptura` / `getRegistroSolicitudServices` / `validarSolicitud` | `.../controller/RegistroSolicitudRequisitosImportacionController.java` | sibling fitosanitario/zoosanitario controllers |
| Wire rules / repository / acuicola transporte load | `RegistroSolicitudRequisitosImportacionServicesPOJOImpl` | `getRegistroSolicitudRules` / `getRegistroSolicitudRepository` / `completarDatosSolicitud` / `obtenerClasificacionProducto` | `.../service/impl/RegistroSolicitudRequisitosImportacionServicesPOJOImpl.java` | sibling sagarpa registro services |
| Persist guber solicitud (mercancias, aduanas, transportes) | `RegistroSolicitudSagarpaServicesPOJOImpl` | `guardarGubernamentales` | `legacy/vuc_mtto_guber/sagarpa/src/.../sagarpa/common/service/impl/RegistroSolicitudSagarpaServicesPOJOImpl.java` | `RegistroSolicitudGubernamentalesServicesPOJOImpl.guardarGubernamentales` (different tree; this process service extends Sagarpa impl) |
| Build transporte list for save | `TransporteSagarpaHelper` | `prepararSolicitudTransportes` | `legacy/vuc_mtto_guber/sagarpa/src/.../sagarpa/common/helper/TransporteSagarpaHelper.java` | unused `RegistroSolicitudRequisitosImportacionHelper.prepararSolicitudAcuicolaTransportes` (never called) |
| Aduanas from claves | `RegistroSolicitudGubernamentalesHelper` | `crearAduanasPorClave` | `legacy/vuc_mtto_guber/gubernamentales-common/.../RegistroSolicitudGubernamentalesHelper.java` | Not found |
| Zoosanitario-only tipo mercancia helper | `RegistroSolicitudRequisitosImportacionHelper` | `preparaTipoMercancia` / `regresarTipoMercancia` | `.../helper/RegistroSolicitudRequisitosImportacionHelper.java` | called only when discriminator is zoosanitario (`220101`), not for `220103` |
| Cadena original / Axway inicio | `RegistroSolicitudRequisitosImportacionRules` | `generarCadenaOriginalSolicitante` / `crearDatosIniciales` | `.../rule/RegistroSolicitudRequisitosImportacionRules.java` | sibling proceso rules |
| Cadena body for 220103 | `CadenasRequisitosImportacionRules` | `cadenaComun` / `cadenaDatosSolicitud` (branches on `220103`) | `.../rule/CadenasRequisitosImportacionRules.java` | Not found |
| Repository create / dictamen mercancia delete | `RegistroSolicitudRequisitosImportacionRepositoryJPAImpl` | `crearSolicitud` / `eliminarMercanciaDictamen` | `.../repository/impl/RegistroSolicitudRequisitosImportacionRepositoryJPAImpl.java` | sibling repos |
| Mercancia grid session | `SendGridToSessionController` | `setGridMercanciasToSession` | `legacy/vuc_mtto_guber/gubernamentales-common/.../SendGridToSessionController.java` | listed on Acuicola `@Wizard` but method body is this class |
| Mercancia dialog sections for 220103 | `MercanciaGridSeccionesHelper` | `obtenerSeccionesDisponibles` | `.../mercancia/helper/MercanciaGridSeccionesHelper.java` | `220101` / `220102` branches in same method |
| Mercancia dialog markup | JSP tag include | `datosMercanciaTag.jsp` | `legacy/vuc_mtto_guber/gubernamentales-common/WebContent/WEB-INF/jsp/gubernamentales2/common/mercanciaGubernamental/datosMercanciaTag.jsp` | `datosMercancia220202.jsp` / `datosMercancia220203.jsp` |
| Mercancia dialog open / save JS | JSP | `abrirCapturaMercancia` / grid validate | `.../mercanciaGubernamental/capturaMercanciaJS.jsp` and `grid2201JS.jsp` | `grid2202AJS.jsp` and other `grid*.jsp` |
| Capture page / tabs / submit hooks | JSP | capture | `legacy/vuc_mtto_guber/sagarpa/WebContent/WEB-INF/jsp/gubernamentales2/sagarpa/requisitosImportacion/solicitud/capturarSolicitud.jsp` | sibling certificadoExportacion captura |
| Datos trámite / aduana / transporte fields | JSP | `datosTramite.jsp` | `.../solicitud/datosTramite.jsp` | `220101` tipo-mercancia radios in same file |
| Instalación procedencia tab | JSP | `instalacionProcedencia.jsp` | `.../solicitud/instalacionProcedencia.jsp` | Not found |
| Client grid / tooltips for 220103 | `sagarpa.js` | `mostrarBotones` / `agregarMercanciaGrid` / aduana restore / `obtenerClasificionProductoxTipoMercancia` | `legacy/vuc_mtto_guber/sagarpa/WebContent/js/modulos/gubernamentales2/sagarpa/sagarpa.js` | `sagarpa04.js` / `sagarpa2205.js` |
| Terceros grids tab | JSP | `tercerosSG.jsp` | `.../sagarpa/common/terceros/tercerosSG.jsp` | common non-SG terceros JSPs |
| Terceros dialog form | JSP | `tercerosRelacionadosSG.jsp` → `datosGeneralesSG.jsp` / `datosPersonalesSG.jsp` | `.../sagarpa/common/terceros/` | non-SG tercero dialogs |
| Terceros dialog JS | `tercerosComponenteSG.js` | `abrirDialogoAgregarTercero` / `guardarTercero` / validators | `.../js/modulos/gubernamentales2/sagarpa/common/terceros/tercerosComponenteSG.js` | non-SG terceros JS |
| Consulta | `ConsultaRequisitosImportacionController` | `showDet` / `obtenerClasificacionProducto` (AJAX used by JS) | `.../controller/ConsultaRequisitosImportacionController.java` | sibling consulta controllers |
| Terceros require flag | `RegistroSolicitudGubernamentalesController` | `getRequiereTerceros` | `.../common/controller/RegistroSolicitudGubernamentalesController.java` | Not found |

## Controllers

### Registro

- Class: `RegistroSolicitudRequisitosImportacionAcuicolaController`
- Path: `legacy/vuc_mtto_guber/sagarpa/src/mx/gob/ventanillaunica/www/gubernamentales2/sagarpa/requisitosImportacion/controller/RegistroSolicitudRequisitosImportacionAcuicolaController.java`
- Extends: `RegistroSolicitudRequisitosImportacionController` → `RegistroSolicitudSagarpaController` (`sagarpa/controller`) → `RegistroSolicitudGubernamentalesController` → `RegistroSolicitudController` (`base-jms-ibm`)
- Wizard events: `iniciar`, `mostrar`, `setGridAutorizacionToSession`, `guardar`, `mostrarCaptura`, `filtrarArchivosGenerales`, `filtrarArchivosEspecificos`, `comboArchivosEspecificos`, `cargarListDoctosEspecificos`, `setGridMercanciasToSession`
- Capture JSP: parent `getJspCaptura` → `JspPagesConstantsRequisitosImportacion.MOSTRAR_REGISTRO_SOLICITUD` → `/WEB-INF/jsp/gubernamentales2/sagarpa/requisitosImportacion/solicitud/capturarSolicitud.jsp`. Includes `datosTramite.jsp`, for `220103` also `instalacionProcedencia.jsp`, conditional `tercerosSG.jsp`, gobmx `solicitante.jsp` / wizard encabezado/pie, scripts `sagarpa.js`, `utilerias.js`, `FormDataComSc.js`, `breadcrump.js`
- AJAX methods on this class: Not found (empty body beyond getters/setters/`mostrar`/`getActionForm`). Mercancia session event is implemented on `SendGridToSessionController`. Classification AJAX hits `ConsultaRequisitosImportacionController`
- Stripes nested validate (on `solicitud`): `identificacionTransporte`, `numEstablecimientoAprobado`, `coordenadasGeograficas` — all `required=false`, maxlength 20/20/80. JSP posts `solicitud.transporte.identificacionTransporte`, `solicitud.numeroEstablecimiento`, `solicitud.coordenadasGeograficas` (property-name mismatch for establecimiento)

### Consulta

- Class: `ConsultaRequisitosImportacionController`
- Path: `legacy/vuc_mtto_guber/sagarpa/src/mx/gob/ventanillaunica/www/gubernamentales2/sagarpa/requisitosImportacion/controller/ConsultaRequisitosImportacionController.java`
- What it loads: `showDet` loads solicitud via `buscarSolicituGubernamentales2` + `completarDatosSolicitud`; for Acuicola sets aduana clave and session `ID_TRAMITE`. Also exposes `obtenerClasificacionProducto` used by capture JS

## Service and rules

Confirm both links from code before writing them.

- Service interface: `RegistroSolicitudRequisitosImportacionServices`
- Service implementation: `RegistroSolicitudRequisitosImportacionServicesPOJOImpl`
- Spring bean: default `@Service` name `registroSolicitudRequisitosImportacionServicesPOJOImpl` (`@Scope("session")`)
- Rules class: `RegistroSolicitudRequisitosImportacionRules`
- Spring bean: `registroSolicitudRequisitosImportacionRules` (`@Component`)
- Repository: `RegistroSolicitudRequisitosImportacionRepository` / `RegistroSolicitudRequisitosImportacionRepositoryJPAImpl` (`@Repository("registroSolicitudRequisitosImportacionRepository")`)
- Helper: `RegistroSolicitudRequisitosImportacionHelper` (`@Component`); save path also uses `TransporteSagarpaHelper` and `RegistroSolicitudGubernamentalesHelper`

Methods this service declares or overrides (name and one-line effect):

- `getRegistroSolicitudRules` — returns process rules
- `getRegistroSolicitudRepository` — returns process repository
- `completarDatosSolicitud` — for Acuicola loads transportes and sets first as `solicitud.transporte`
- `obtenerDeclaracionesPorIdSolicitud` — declaraciones repository lookup
- `obtenerClasificacionProducto` — catalog `obtenerClasificacionProductos2201` by tipo mercancia (used mainly by zoosanitario UI)

Rules class methods this process declares:

- `generarCadenaOriginalSolicitante` — builds cadena via `CadenasRequisitosImportacionRules` + repository reload
- `crearDatosIniciales` — wraps `super.crearDatosIniciales` in `InicioTramiteGubernamentales`

The process-specific rule that does not live on the rules class:

- Not found for documents. `validaReglaDocumentosEspecificos` is not overridden on this service; Spring `tipoDocumentoOrigen` = `DEFAULT_SIN_DOCTOS`

## Parents and shared beans

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. Do not restate it.

Pom base artifact: `base-jms-ibm` — commons tree: `legacy/vuc_mtto_commons/base-jms-ibm`. Also depends on `gubernamentales-common`.

Overrides against that chain. One line each. `not overridden` or `overridden — <effect>`.

- `RegistroSolicitudController::getRegistroSolicitudServices` — overridden — parent requisitos controller returns `registroSolicitudRequisitosImportacionServices`
- `RegistroSolicitudController::setSolicitud` / `getSolicitud` — overridden — on Acuicola controller
- `RegistroSolicitudController::getJspCaptura` — overridden — shared requisitos parent returns `MOSTRAR_REGISTRO_SOLICITUD` and stores `ID_TRAMITE`
- `RegistroSolicitudController::getActionForm` — overridden — Acuicola returns `registrarSolicitudAC.action` binding
- `RegistroSolicitudController::iniciar` — overridden — requisitos parent `@DefaultHandler` + skip page to capture JSP, then `super.iniciar`
- `RegistroSolicitudController::mostrar` — overridden — requisitos parent; zoosanitario-only helper branch; Acuicola also overrides calling `super.mostrar`
- `RegistroSolicitudController::guardar` — overridden — requisitos parent maps mercancias/aduanas/certificado then `guardarGubernamentales` + `guardarTercerosRelacionados` + `super.guardar`
- `RegistroSolicitudServicesPOJOImpl::getRegistroSolicitudRepository` — overridden — process repository
- `RegistroSolicitudServicesPOJOImpl::getRegistroSolicitudRules` — overridden — process rules
- `RegistroSolicitudServicesPOJOImpl::iniciar` — not overridden on process service
- `RegistroSolicitudServicesPOJOImpl::guardar` — not overridden on process service (persist path is `guardarGubernamentales` on Sagarpa service)
- `RegistroSolicitudServicesPOJOImpl::firmar` — not overridden on process service
- `RegistroSolicitudServicesPOJOImpl::buscar` — not overridden on process service (`buscarSolicituGubernamentales2` is separate)
- `RegistroSolicitudServicesPOJOImpl::validaReglaDocumentosEspecificos` — not overridden
- `RegistroSolicitudRules::iniciarSolicitud` — not overridden
- `RegistroSolicitudRules::crearDatosIniciales` — overridden — wraps in `InicioTramiteGubernamentales`
- `RegistroSolicitudRules::crearMensajeAxway` — not overridden
- `RegistroSolicitudRules::validarEstadoPreFirma` — not overridden
- `RegistroSolicitudRules::verificaSolicitud` — not overridden
- `RegistroSolicitudRules::validaSolicitudGuardada` — not overridden
- `RegistroSolicitudRules::crearSolicitud` — not overridden
- `RegistroSolicitudRules::prepararSolicitud` — not overridden
- `RegistroSolicitudRules::crearFirmaUsuario` — not overridden
- `RegistroSolicitudRules::crearFirmaSistema` — not overridden
- `RegistroSolicitudRules::prepararTramite` — not overridden
- `RegistroSolicitudRules::generarCadenaOriginalSolicitante` — overridden — process cadena
- `RegistroSolicitudRules::completarPrellenadoSolictud` — not overridden

Shared beans this controller or service calls, and the method. Omit beans the architecture file already names unless this process calls them for a reason that file does not cover.

- `TransporteRepository::obtenerTransportesPorSolicitud` / `guardarTransportes` — load/save transporte for Acuicola
- `MercanciaGubernamentalRepository::guardarMercanciasGubernamentales` / `eliminarMercanciasGubernamentales` — mercancia persist via Sagarpa `guardarGubernamentales`
- `DeclaracionesRepository` — declaraciones by tramite / by solicitud
- `TercerosServices::obtenerTiposTerceroPorModalidad` — drives `requiereTerceros` and grid types
- `CatalogoSagarpaRepository::obtenerClasificacionProductos2201` — classification AJAX (zoosanitario-oriented; JS skips eval for 220103)

## Do not use

- Sibling process numbers seen nearby: `220101` (zoosanitario), `220102` (fitosanitario) in the same package; also `CVE_NUM_COMBINACIONES_*` siblings in URL constants
- Unused imports in the registro Acuicola controller: none observed beyond used types
- Commons trees that this module’s pom does not depend on: `base`, `base-jms`, `base-jms-ejb-ibm`, `base-jms-noAxway`, `base-jms-noAxway-ibm`, `base-jms-noAxway-gobmx-ibm`, `vu-archetype`, `axwayAdapter`
- Do not open sibling modalidad controllers `RegistroSolicitudRequisitosImportacionFitosanitarioController` / `...ZoosanitarioController` to fill this file
- Do not treat `RegistroSolicitudRequisitosImportacionHelper.prepararSolicitudAcuicolaTransportes` as the live save path (never referenced)

## Capture flow

Numbered, in call order. Each step names the method and the file.

1. `iniciar` on `RegistroSolicitudRequisitosImportacionController` (inherited) runs commons registro start; `getJspCaptura` stores `ID_TRAMITE` and loads declaraciones
2. Capture renders `capturarSolicitud.jsp`: tabs Solicitante, Datos del trámite, and for `220103` Instalación de procedencia; if `requiereTerceros` appends terceros tab via `tercerosSG.jsp`
3. `sagarpa.js` on ready initializes mercancia grid buttons and for `220103` restores `aduanas` / medio transporte from hidden fields
4. User adds mercancias via `agregarMercanciaGrid` → `abrirCapturaMercancia` (dialog `dialogoMercanciaGubernamental` / form `frmMercancias`); `grid2201JS.jsp` applies 220103 validate rules; sections from `MercanciaGridSeccionesHelper` for `220103`
5. Mercancias live in session under `_listaMercanciaGubernamentales` (`setGridMercanciasToSession` / parent session stores)
6. For `220103`, terceros dialog `#dialogoTercero` uses `tercerosRelacionadosSG.jsp`; `sendGridTercerosSagarpa` posts grids on submit
7. Form `onsubmit` runs `sendGridTercerosSagarpa` then `validarCampos` then `validateAllTables` (see Submit validation)
8. `guardar` on requisitos parent attaches session mercancias, builds aduanas from `aduanas` select, sets certificado from user profile, calls `guardarGubernamentales`, then `guardarTercerosRelacionados`, then `super.guardar`
9. `guardarGubernamentales` (Sagarpa service) persists solicitud, mercancias, aduanas, and transportes via `TransporteSagarpaHelper.prepararSolicitudTransportes`

## Role branches

- Condition: `${sessionScope.ROL.name=='CapturistaGubernamental'}`
  - Shows: include of `personasAutorizadasParaNotificaciones.jsp` inside tab Datos del trámite
  - Hides: Not found
  - Adds required: Not found in process JSP
  - Removes required: Not found
  - Other effect: Not found

- Condition: `${actionBean.requiereTerceros=='true'}` (from `getRequiereTerceros` / DB tipos tercero for modalidad)
  - Shows: terceros tab (`tercerosSG.jsp`) and `#dialogoTercero`
  - Hides: Not found
  - Adds required: asteriscos on `TIPERS_DES` / `TIPERS_INSPR` when config `numeroMinimoDePersonas > 0`
  - Removes required: Not found
  - Other effect: `sendGridTercerosSagarpa` enabled only for `220103`

- Condition: `$('#idTipoTramite').val() == 220103` / discriminator JSP tests
  - Shows: aduana/transporte fields, instalación tab, mercancia 220103 grid/validate branch, ComScore homoclave `SENASICA-01-045.Solicitud`
  - Hides: `220101` tipo-mercancia radios
  - Adds required: `aduanas` (`styleClass="... required"`); mercancia JS required on nombre comun/cientifico/paises; description asterisk via `mostrarDescripcion`
  - Removes required: Not found
  - Other effect: tooltip on `aduanas`

## Submit validation

- Action that saves or continues: wizard Continuar / `guardar` (gobmx `pieWizardCaptura.jsp` → `guardarSolicitud`); form `onsubmit` on `#formId`
- Tab or panel switch before validation: `validarTablas` switches to tab 1 if mercancias empty, tab 3 if terceros empty; also resets to tab 0 at end of that function. `validateAllTables` does not switch tabs
- Hidden-field behavior: Not found (no process `ignore: ":hidden"`)
- Function that can block submit: `validarCampos` — reads `#tipoMercanciaA.checked` (element not rendered for `220103`; see Dead selectors); `validateAllTables` / `validateTable` — blocks empty tables with class `require` (message `Este campo es obligatorio`); stripesValidation from field-metadata; mercancia dialog validate on `#frmMercancias` for 220103
- Functions that always return true or are commented out: `verificarNumeroMercancias` returns `true` for non-`220101`; empty `sendGridTercerosSagarpa` stub when discriminator != 220103

## Fields

One row per rendered input, select, or textarea for this process’s capture surface and dialogs. Shared gobmx `solicitante.jsp` fields are the common solicitante contract (evidence: include in `capturarSolicitud.jsp`); they are not re-listed here. `Not found` when the code does not define the cell.

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Hidden solicitud id | `idSolicitud` | `solicitud.idSolicitud` | `solicitud.idSolicitud` | Not required | Not found | hidden | Not found | Not found | Not found | Stripes bind | `capturarSolicitud.jsp` / `datosTramite.jsp` |
| Hidden discriminator | `idDiscriminatorValue` / `idTipoTramite` | `solicitud.discriminatorValue` | discriminator | Not required | Not found | hidden | Not found | Not found | Not found | session `ID_TRAMITE` | `capturarSolicitud.jsp`; `datosTramite.jsp` |
| `solicitud.claveAduana` | catalog select `aduanas` (+ hidden `aduanasEnt`) | `aduanas` | entrada aduana claves → `solicitud.aduanasEntrada` | Always (`styleClass="form-control required"` + label `span.require`) | Not found | catalog `aduanas` | stripes / class required | platform required | Marks error | `crearAduanasPorClave` in `guardar` | `datosTramite.jsp` (`220103`); parent controller `guardar` |
| `certificado.medioTransporte` | catalog `solicitud.transporte.ideMedioTransporte` (+ hidden `transporte`) | `solicitud.transporte.ideMedioTransporte` | `solicitud.transporte.ideMedioTransporte` | Not required (no `required` class) | Not found | catalog `medioTransporteGubernamentales` criteria `ID_TRAMITE` | Not found | Not found | Not found | `TransporteSagarpaHelper.prepararSolicitudTransportes` | `datosTramite.jsp`; `TransporteSagarpaHelper.java` |
| `solicitud.identificacionTransporte` | Not found on text (name-bound) | `solicitud.transporte.identificacionTransporte` | `solicitud.transporte.identificacionTransporte` | Not required | 20 | Not found | Stripes nested validate `identificacionTransporte` maxlength 20 optional | Not found | Not found | transporte persist | `datosTramite.jsp`; Acuicola `@ValidateNestedProperties` |
| `solicitud.numEstablecimientoAprobado` | Not found | `solicitud.numeroEstablecimiento` | `SolicitudDatosGenerales.numeroEstablecimiento` | Not required | 20 | Not found | Controller annotates `numEstablecimientoAprobado` (name mismatch vs JSP) | Not found | Not found | Stripes bind on solicitud | `instalacionProcedencia.jsp`; `SolicitudDatosGenerales.java` |
| `solicitud.coordenadasGeograficas` | Not found | `solicitud.coordenadasGeograficas` | `SolicitudDatosGenerales.coordenadasGeograficas` | Not required | 80 | Not found | Stripes maxlength 80 optional | Not found | Not found | Stripes bind | `instalacionProcedencia.jsp` |
| Mercancías grid | `gridMercanciasTram2201` | session list (not direct form fields) | `solicitud.mercancias` | At least one (label `span.require` + table `class="require"` + alert text) | Not found | jqGrid | `validateTable` / `validarTablas` | `Este campo es obligatorio` / `Faltan campos por capturar` | Marks table error / switches tab | `guardar` reads `LISTA_MERCANCIAS_GUBERNAMENTALES` | `datosTramite.jsp`; `capturarSolicitud.jsp`; tag `MercanciaGubernamental` |
| Hidden tipo mercancia | `enumTipoMercancia` | `solicitud.tipoMercancia` | transient `tipoMercancia` | Not required for 220103 | Not found | hidden | Not found | Not found | Not found | zoosanitario helper only | `datosTramite.jsp` |
| Fracción arancelaria (dialog) | `fraccionArancelaria` | `mercanciaGrid.fraccionArancelaria.clave` | mercancia fraccion | Label asterisk only when `mostrarRequiredFraccion` (false for 220103) | 8 | digits blur `obtenerDescripcionFraccion` | JS `minlength/maxlength 8`, `digits` | jquery validate | Marks error | session mercancia → repository | `datosMercanciaTag.jsp` (2201 block); `grid2201JS.jsp` 220103 rules |
| Descripción fracción (dialog) | `descFraccionArancelaria` | `mercanciaGrid.fraccionArancelaria.descripcion` | mercancia | Not required (`readonly`) | Not found | readonly | Not found | Not found | Not found | filled from fraccion lookup | `datosMercanciaTag.jsp` |
| Descripción mercancía (dialog) | `idDenominacionComunInternacional` | `mercanciaGrid.descripcionMercancia` | mercancia descripcion | Always (label `span.require` + JS `required`) | Not found (`cols="5000"`) | Not found | `#frmMercancias` required; also `validarDatos220103` | `¡Error de Registro! Faltan campos por capturar.` (`#dvMessage`) | Shows `#dvMessage` | session mercancia | `datosMercanciaTag.jsp` `mostrarDescripcion`; `grid2201JS.jsp`; `capturarSolicitud.jsp` / `tercerosSG.jsp` |
| Cantidad UMT (dialog) | `cantidadUMT` | `mercanciaGrid.cantidadUMT` | mercancia | Not required (`mostrarRequiredCantidadUMT=false`) | 20 | Not found | `threedecimals` only | Not found | Marks error | session mercancia | `datosMercanciaTag.jsp`; `MercanciaGridSeccionesHelper` 220103; `grid2201JS.jsp` |
| UMT descripción (dialog) | `descripcionUMT` (+ hidden `claveUMT`) | `mercanciaGrid.unidadMedidaTarifa.*` | mercancia UMT | Not required (`mostrarRequiredUMT=false`) | 100 | readonly | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp` |
| Cantidad UMC (dialog) | `cantidadUMC` | `mercanciaGrid.cantidadUMC` | mercancia | Not required in markup (`mostrarRequiredCantidadUMC` unset); JS rule overwritten to `threedecimals` only | 20 | Not found | `threedecimals` | Not found | Marks error | session mercancia | `datosMercanciaTag.jsp`; `grid2201JS.jsp` 220103 (duplicate key) |
| UMC (dialog) | catalog / text per tag | `mercanciaGrid.unidadMedidaComercial.clave` | mercancia UMC | Not required (`mostrarRequiredUMC` unset) | Not found | catalog | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp` `mostrarUnidadMedidaComercial` |
| Nombre común (dialog) | `nombreComun` | `mercanciaGrid.nombreComun` | mercancia | Always (label `span.require` + JS required) | 250 | Not found | `#frmMercancias` required; `validarDatos220103` | `#dvMessage` | Shows error banner | session mercancia | `datosMercanciaTag.jsp` `mostrarNombreComun`; `grid2201JS.jsp` |
| Nombre científico (dialog) | `nombreCientifico` | `mercanciaGrid.nombreCientifico` | mercancia | Always for 220103 (`mostrarRequiredNombreCientifico` + JS) | 40 | Not found | `#frmMercancias` required | `#dvMessage` | Shows error banner | session mercancia | `datosMercanciaTag.jsp`; helper 220103 |
| Fase de desarrollo (dialog) | `faseDesarrollo` | `mercanciaGrid.faseDesarrollo` | mercancia | Not required (label only) | 50 | Not found | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp` `mostrarFaseDesarrollo` |
| Uso (dialog) | catalog `mercanciaGrid.idUso` (+ hidden `idUso`) | `mercanciaGrid.idUso` | mercancia uso | Not required for 220103 (`mostrarRequiredUso` only for 220101) | Not found | catalog `usosMercancia` criteria `ID_TRAMITE` | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp`; helper |
| Otro uso (dialog) | `otroUso` | `mercanciaGrid.otroUso` | mercancia | Not required | 50 | Not found | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp` `mostrarUsoOtro` |
| Origen (dialog) | catalog `mercanciaGrid.idOrigen` (+ hidden `idOrigen`) | `mercanciaGrid.idOrigen` | mercancia | Not required | Not found | catalog `origenMercancia` | Not found | Not found | Not found | session mercancia | `datosMercanciaTag.jsp` `mostrarOrigen` |
| País origen (dialog) | catalog select | `mercanciaGrid.clavesPaisesOrigen` | mercancia paises | Always (label `span.require` + JS required) | Not found | catalog `pais` | `#frmMercancias` required | `#dvMessage` | Marks error | session mercancia | `datosMercanciaTag.jsp` `mostrarPaisOrigenCombo` |
| País procedencia (dialog) | catalog select | `mercanciaGrid.clavesPaisesProcedencia` | mercancia paises | Always (label `span.require` + JS required) | Not found | catalog `paisesSinMexico` | `#frmMercancias` required | `#dvMessage` | Marks error | session mercancia | `datosMercanciaTag.jsp` `mostrarPaisProcCombo` |
| Mercancia id (dialog) | `idMercancia` | `mercanciaGrid.idMercancia` | mercancia | Not required | Not found | hidden | Not found | Not found | Not found | grid id | `datosMercanciaTag.jsp` |
| Tipo persona (terceros dialog) | `terceros_tipoPersonaFisica` / `terceros_tipoPersonaMoral` | `terceros.tipoPersona` | tercero | Always (`class="required"`) | Not found | radio | jquery validate | generated error label | Marks error | terceros session → `guardarTercerosRelacionados` | `datosGeneralesSG.jsp` — dialog `#dialogoTercero` |
| RFC (terceros dialog) | `terceros_rfc` | `terceros.rfc` | tercero | When física/moral section shown (label asterisk) | 15 | `rfcPersonaFisica` / `rfcPersonaMoral` methods | format validators | `msgFormatoIncorrecto` | Marks error | terceros persist | `datosGeneralesSG.jsp`; `tercerosRelacionadosSG.jsp` validators |
| Nombre (terceros dialog) | `terceros_nombre` | `terceros.nombre` | tercero | Always when física (`class="required"`) | 200 | `persona` method | required + persona | format / required | Marks error | terceros persist | `datosPersonalesSG.jsp` |
| Apellido paterno (terceros dialog) | `terceros_apellidoPaterno` | `terceros.apellidoPaterno` | tercero | Always when física (`class="required"`) | 200 | `persona` | required | required | Marks error | terceros persist | `datosPersonalesSG.jsp` |
| Apellido materno (terceros dialog) | `terceros_apellidoMaterno` | `terceros.apellidoMaterno` | tercero | Not required | 200 | Not found | Not found | Not found | Not found | terceros persist | `datosPersonalesSG.jsp` |
| Razón social (terceros dialog) | `terceros_rSocial` | `terceros.denominacionRazonSocial` | tercero | Always when moral (`class="required"`) | 250 | Not found | required | required | Marks error | terceros persist | `datosPersonalesSG.jsp` |
| País (terceros dialog) | `terceros_selectPais` | `terceros_selectPais` | domicilio pais | Always (`requiredPais` / asterisk) | Not found | catalog `pais`; parent for estado | `requiredPais` | `tercerosEsteCampoEsObligatorio` | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Código postal (terceros dialog) | `terceros_codPostal` | `terceros.codigoPostal` | domicilio | Not required (no required class) | 12 | Not found | Not found | Not found | Not found | terceros persist | `datosGeneralesSG.jsp` |
| Estado (terceros dialog) | `terceros_selectEstado` | `terceros_selectEstado` | domicilio entidad | Always (`class="required"`) | Not found | catalog `estado` `parentSelectName=terceros_selectPais` | required | required | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Municipio (terceros dialog) | `terceros_selectMunicipio` | `terceros_selectMunicipio` | domicilio | Not required | Not found | catalog `municipio` parent estado | Not found | Not found | Not found | terceros persist | `datosGeneralesSG.jsp` |
| Colonia (terceros dialog) | `terceros_selectColonia` | `terceros_selectColonia` | domicilio | Not required | Not found | catalog `colonia` parent municipio | Not found | Not found | Not found | terceros persist | `datosGeneralesSG.jsp` |
| Calle (terceros dialog) | `terceros_calle` | `terceros.calle` | domicilio | Always (`class="required"`) | 100 | Not found | required | required | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Número exterior (terceros dialog) | `terceros_numExterior` | `terceros.numLetraExterior` | domicilio | Always (`class="required alfanumericoEspacio"`) | 55 | `alfanumericoEspacio` | required + filter | format / required | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Número interior (terceros dialog) | `terceros_numInterior` | `terceros.numLetraInterior` | domicilio | Not required | 55 | `alfanumericoEspacio` | filter | format | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Domicilio extranjero (terceros dialog) | `terceros_domicilioExtranjero` | `terceros.domicilio` | domicilio | Always when extranjero section (`class="required direccion"`) | 4000 (JS maxlength) | `imposeMaxLength` / `validarlongitud` | required | required | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Teléfono (terceros dialog) | `terceros_telefono` | `terceros.telefono` | tercero | Not required | 30 | `telefono` method | format | `msgFormatoIncorrecto` | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Correo (terceros dialog) | `terceros_correoElec` | `terceros.correoElectronico` | tercero | Not required | 320 | `email` class | email | email | Marks error | terceros persist | `datosGeneralesSG.jsp` |
| Tipo actividad (terceros dialog) | `terceros_selectTipoActividad` | `terceros_selectTipoActividad` | tercero | When `#divTipoDeActividadEstablecimiento` shown (`styleClass="... required"`) | Not found | catalog `tipoActividadEstablecimiento` | required when visible | required | Marks error | terceros persist | `tercerosRelacionadosSG.jsp` |
| Otra actividad (terceros dialog) | `terceros_tipoActividadOtro` | `terceros.tipoActividadOtro` | tercero | When select value `ACTPG.OTR` (JS adds `required`) | 120 | Not found | JS toggles required | required | Marks error | terceros persist | `tercerosRelacionadosSG.jsp` |
| Número identificación (terceros dialog) | `terceros_numeroIdentificacion` | `terceros.numeroIdentificacion` | tercero | Label asterisk when `#divContenidoDatosAdicionales` shown | 100 | Not found | Not found in validate block | Not found | Not found | terceros persist | `tercerosRelacionadosSG.jsp` |
| Entidad expide ID (terceros dialog) | `terceros_entidadExpideIdentificacion` | `terceros.entidadExpideIdentificacion` | tercero | Label asterisk when adicionales shown | 200 | Not found | Not found | Not found | Not found | terceros persist | `tercerosRelacionadosSG.jsp` |
| Número certificado (terceros dialog) | `terceros_numeroCertificado` | `terceros.numeroRegistro` | tercero | Not required | 25 | Not found | Not found | Not found | Not found | terceros persist | `tercerosRelacionadosSG.jsp` |
| Terceros grids DES / INSPR | `gridTerceros_TIPERS_DES` / `gridTerceros_TIPERS_INSPR` | grid session | terceros | When min personas > 0 (asterisco shown); `validarTablas` expects both non-empty for 220103 | Not found | jqGrid `class="require"` | `validarTablas` / `validateTable` | `Faltan campos por capturar` / `Este campo es obligatorio` | Switches to tab 3 / marks asterisco red | `guardarTercerosRelacionados` | `tercerosSG.jsp`; `capturarSolicitud.jsp` |

## Dead selectors

| Selector | What the script does | Evidence |
| --- | --- | --- |
| `tipoMercanciaA` / `tipoMercanciaA1` | `validarCampos` requires radio checked and `marcarErrorInput("tipoMercanciaA1")` on submit | `capturarSolicitud.jsp` — radios only rendered for `220101` in `datosTramite.jsp` |
| `tipoMercanciaP` / `tipoMercanciaQ` | `sagarpa.js` reads checked state for classification load | same — not in 220103 markup |
| `guardarSolicitud1` | click handler calls `validateAllTables` | referenced in `capturarSolicitud.jsp` / `tercerosSG.jsp`; Continuar button id in gobmx pie is `guardarSolicitud` |

## Documents

- Rule id: Not found
- Obligatory when: Not found. Spring `tipoDocumentoOrigen` = `DEFAULT_SIN_DOCTOS`. Service does not override `validaReglaDocumentosEspecificos` (parent returns false)
- Where it is implemented: Not found for a process-specific document rule
- Document names this process’s code lists: Not found

## Persistence

Helper mapping. One row per persisted property the helper or the controller sets.

| Request param | Entity | Property | Transform | Evidence |
| --- | --- | --- | --- | --- |
| `aduanas` | `AduanaSolicitud` list on solicitud | `aduanasEntrada` | clave → aduana objects via `crearAduanasPorClave` | `RegistroSolicitudRequisitosImportacionController.guardar` |
| `solicitud.transporte.ideMedioTransporte` / `identificacionTransporte` | `Transporte` | `ideMedioTransporte` / `identificacionTransporte` | `TransporteSagarpaHelper.prepararSolicitudTransportes` then `transporteRepository.guardarTransportes` | Sagarpa `guardarGubernamentales` |
| `solicitud.numeroEstablecimiento` | `Solicitud` / `SolicitudDatosGenerales` | `numeroEstablecimiento` | none | Stripes bind; `instalacionProcedencia.jsp` |
| `solicitud.coordenadasGeograficas` | `SolicitudDatosGenerales` | `coordenadasGeograficas` | none | Stripes bind |
| session `_listaMercanciaGubernamentales` | `MercanciaGubernamentales2` | mercancias + partida index | `mercanciaRepository.guardarMercanciasGubernamentales` | controller `guardar` + Sagarpa service |
| terceros grids | `TerceraPersona` maps | by tipo tercero | `guardarTercerosRelacionados` on Sagarpa controller | `RegistroSolicitudSagarpaController.guardarTercerosRelacionados` |
| user profile certificado | solicitud | `certificado` | from `VuUserProfile` | requisitos `guardar` |

Repository writes and deletes this process calls:

- `crearSolicitud` — flush after super create
- `eliminarMercanciaDictamen` — native delete by folio (workflow/dictamen oriented; not called from registro Acuicola controller)
- via Sagarpa `guardarGubernamentales`: eliminate/recreate aduanas and mercancias; `guardarTransportes`

## Reports

- Bean: Not found on the registro Acuicola controller (`getAcuseReport` not overridden). Workflow/dictamen beans `oficioResAutorizacionyRechazo2201RequisitosImportacion` / `oficioResAutorizacionORechazo220102RequisitosImportacion` live in `sagarpa-reportes-context.xml` and are used by dictamen/autorizar controllers, not this registration pass
- Chosen when: Not found for registration
- Session key the id is stored under: Not found for process-specific acuse override

## Not found

- `setGridAutorizacionToSession` method body on the Acuicola / requisitos registro controllers — listed in `@Wizard` only; searched controller hierarchy for requisitos registro
- Process-specific `getAcuseReport` / registration jasper for 220103 — searched requisitos registro controllers and Spring report beans usage
- Document rule id / obligatory anexo names — Spring `DEFAULT_SIN_DOCTOS`; no `validaReglaDocumentosEspecificos` override
- Live call to `prepararSolicitudAcuicolaTransportes` — only definition exists
- `numEstablecimientoAprobado` model property matching JSP `numeroEstablecimiento` — annotation name vs posted name disagree
- Controller field `transporte` on Acuicola (separate from `solicitud.transporte`) used by capture JSP — JSP binds under `solicitud.transporte.*`
- `ignore: ":hidden"` process override — not in capture scripts
- Classification combo populate for 220103 from `obtenerClasificacionProducto` — JS skips `eval` for 220103/220102; 220103 mercancia UI does not show clasificación sections
