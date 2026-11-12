# Process 32515 — Aviso Valor Provisional

Legacy knowledge base. Source of truth for this trámite only. Built from the controller `RegistroSolicitudAvisoValorProvisionalController`.

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. This file records only 32515.

## Identity

- Process number: `32515`
- Entity: `SolicitudAvisoValorProvisional` — `@DiscriminatorValue` is `AvisoValorProvisional.ID_AVISO_VALOR_PROVISIONAL` (`"32515"`). Evidence: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional/model/SolicitudAvisoValorProvisional.java` and `constants/AvisoValorProvisional.java`. Spring `parametrosURL` repeats `solicitud.discriminatorValue=32515` in `tramite-envioavisos-context.xml`
- Package: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional`
- Registro URL: `/avisos-agace/avisoValorProvisional/solicitudAvisoValorProvisional.action`. Binding is `ACTION_PREFIX + "/avisos-agace/avisoValorProvisional/solicitudAvisoValorProvisional" + ACTION_SUFFIX`. `ACTION_PREFIX` is `""` and `ACTION_SUFFIX` is `".action"` in `legacy/vuc_mtto_commons/base-jms-ibm/support/com/softtek/global/constant/ControllerUrlBindingConstants.java`
- Consulta URL: `/avisos-agace/avisoValorProvisional/consultaSolicitudAvisoValorProvisional.action` (`CONSULTA_SOLICITUD_AVISO_VALOR_PROVISIONAL_URL_BINDING`)
- Session key: `AvisoValorProvisional.VALOR_PROV_DTO` = `"ValorProvisionalDto"`. Also written: `CommonAvAConstants.SOLICITUD_SESSION` = `"SOLICITUD_SESSION"`, `CommonAvAConstants.SOLICITANTE_TMP` = `"_solicitanteToSession"` (removed in `inicializarSession`), `JspPagesConstansRegistroSolicitud.ID_DOCTO_OFICIAL` = `"__ID_DOCTO_OFICIAL"`, `JspPagesConstantsMercDesmSinMon.ID_DOCTO_OFICIAL_EXCEL` = `"__ID_DOCTO_OFICIAL_EXCEL"`
- Spring bean (solicitud): `SolicitudAvisoValorProvisional` in `legacy/vuc_mtto_aga2/avisos-agace/WebContent/WEB-INF/config/spring/avisosAgace/tramites/tramite-envioavisos-context.xml`. Discriminator map entry in `tramites/avisosAgace-common-context.xml`
- Module pom base artifact: `base-jms-ibm` (war) and `base-jms-ibm-jar` — commons tree used: `legacy/vuc_mtto_commons/base-jms-ibm`. The same pom also depends on `common-model`. Evidence: `legacy/vuc_mtto_aga2/avisos-agace/pom.xml`

## Controllers

### Registro

- Class: `RegistroSolicitudAvisoValorProvisionalController`
- Path: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional/controller/RegistroSolicitudAvisoValorProvisionalController.java`
- Extends: `RegistroSolicitudController<SolicitudAvisoValorProvisional>`
- Wizard events: `iniciar`, `mostrar`, `buscarDatosSolitante`, `filtrarArchivosGenerales`, `guardar`, `guardarParcial`, `filtrarArchivosEspecificos`, `comboArchivosEspecificos`, `cargarListDoctosEspecificos`, `recibirDatos`, `guardar` (listed twice), `buscarAdace`, `buscarRfcValido`
- Capture JSP: `JspPagesConstantsAvisoValorProvisional.REGISTRO_SOLICITUD_AVISO_VALOR_PROVISIONAL` → `APP_BASE_DIRECTORY + "/avisosAgace/avisoValorProvisional/solicitud/registrarSolicitud.jsp"`. File opened: `legacy/vuc_mtto_aga2/avisos-agace/WebContent/WEB-INF/jsp/avisosAgace/avisoValorProvisional/solicitud/registrarSolicitud.jsp`. That page includes `datosAviso.jsp` and `avisosAgace/common/adace.jsp`, and the script `js/modulos/avisosAgace/avisoValorProvisional/solicitud/registrarSolicitud.js`. `declaraciones.jsp` and `datosAvisoConsulta.jsp` are not included
- AJAX methods on this class:
  - `recibirDatos` — request params listed in **Persistence** — returns JavaScript `"OK"` and stores `ValorProvisionalDto` under `VALOR_PROV_DTO`. Dates are parsed with `dd/MM/yyyy`. A `ParseException` is printed and the date stays null
  - `buscarRfcValido` — request param `rfc` — returns `iDCService.contribuyenteActivo(rfc.toUpperCase())` when `rfc` is not `""`. An empty rfc leaves the boolean false. Any exception is printed and the method still returns false

### Consulta

- Class: `ConsultaSolicitudAvisoValorProvisionalController`
- Path: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional/controller/ConsultaSolicitudAvisoValorProvisionalController.java`
- Extends: `ConsultaAvisosGobMxController` (not the registro chain)
- What it loads: `cargarConsultas` forwards `CONSULTA_FUNCIONARIO` when the profile type is `FUNCIONARIO` or `getParametrosSolicitud()` is null, otherwise `CONSULTAS_TRAMITE`. `showSol` calls `getConsultaServices().iniciar`, `obtenerPersonaSolicitante`, `oirNotificarService.loadPersonas`, and `buscarAdace` (defined on `ConsultaAvisosGobMxController`, not on this class), then forwards `getJspShowSol()`. `showDet` calls `registroSolicitudAvisoValorProvisionalServices.buscar`, then `ObtenerDatos`, sets empresa and direccion, and forwards `CONSULTA_DETALLE_SOLICITUD`

## Service and rules

Confirm both links from code before writing them.

- Service interface: `RegistroSolicitudAvisoValorProvisionalServices`
- Service implementation: `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl`
- Spring bean: `registroSolicitudAvisoValorProvisionalService` (`@Service`, `@Scope("session")`)
- Rules class: `RegistroSolicitudAvisoValorProvisionalRules`
- Spring bean: `registroSolicitudAvisoValorProvisionalRules` (`@Component` with no name)
- Repository: `RegistroSolicitudAvisoValorProvisionalRepository` / `RegistroSolicitudAvisoValorProvisionalRepositoryJPAImpl` (`@Repository("registroSolicitudAvisoValorProvisionalRepository")`)
- Helper: `RegistroSolicitudAvisoValorProvisionalHelper` (`@Component`, default bean name)

Wired by `getRegistroSolicitudServices()` on the registro controller, then `getRegistroSolicitudRules()` and `getRegistroSolicitudRepository()` on the service.

Methods this service declares or overrides (name and one-line effect):

- `getRegistroSolicitudRepository` — returns `registroSolicitudAvisoValorProvisionalRepository`
- `getRegistroSolicitudRules` — returns `registroSolicitudAvisoValorProvisionalRules`
- `guardarProvisional` — `guardarvalorprovisional` then `guardarempresa`
- `ObtenerDatos` — index 0 of `obtenerDatosEmpresa` and of `obtenerDatosDireccionST`. An empty list throws
- `ConsultarDireccionST` / `ConsultarEmpresa` — first row, or a new empty instance
- `guardar` — when `idSolicitud` is set, deletes `DireccionST` and `Empresa`, then `super.guardar`
- `validaReglaDocumentosEspecificos` — see the rule below

Rules class methods this process declares:

- none; class body has no methods (`serialVersionUID` only)

The process-specific rule that does not live on the rules class:

- `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl.validaReglaDocumentosEspecificos`. When `seQuiereObligatorio` is true and `regla` equals `REANX.VPM`, returns true only if `cveRolCapturista` equals `PersonaMoral`, otherwise false. When `seQuiereObligatorio` is false, or the rule id is not `REANX.VPM`, returns true. `cveRolCapturista.equals` assumes the role is non-null. `buscarSolicitud` is assumed non-null. The architecture file records the parent return as false, so this override is what makes `REANX.VPM` obligatory for a persona moral

## Parents and shared beans

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. Do not restate it.

Pom base artifact: `base-jms-ibm` — commons tree: `legacy/vuc_mtto_commons/base-jms-ibm`

Overrides against that chain. One line each. `not overridden` or `overridden — <effect>`.

- `RegistroSolicitudController::getRegistroSolicitudServices` — overridden — returns `registroSolicitudAvisoValorProvisionalService`
- `RegistroSolicitudController::setSolicitud` — overridden — stores the solicitud field
- `RegistroSolicitudController::getJspCaptura` — overridden — adds the verificar message, `inicializarSession` removes `SOLICITANTE_TMP` and `SOLICITUD_SESSION`, reloads `DireccionST` and `Empresa` when `idSolicitud` is set, then returns the capture JSP constant
- `RegistroSolicitudController::getActionForm` — overridden — returns the registro URL binding
- `RegistroSolicitudServicesPOJOImpl::getRegistroSolicitudRepository` — overridden — returns the process repository
- `RegistroSolicitudServicesPOJOImpl::getRegistroSolicitudRules` — overridden — returns the process rules
- `RegistroSolicitudServicesPOJOImpl::iniciar` — not overridden
- `RegistroSolicitudServicesPOJOImpl::guardar` — overridden — deletes `DireccionST` and `Empresa` when `idSolicitud` is set, then `super.guardar`
- `RegistroSolicitudServicesPOJOImpl::firmar` — not overridden on the service. The controller overrides `firmar`: `super.firmar`, then `enviaListaCorreos`. A mail exception is logged as `Error al enviar el mail` and is not rethrown
- `RegistroSolicitudServicesPOJOImpl::buscar` — not overridden
- `RegistroSolicitudServicesPOJOImpl::validaReglaDocumentosEspecificos` — overridden — `REANX.VPM` returns true only for `PersonaMoral` when `seQuiereObligatorio` is true
- `RegistroSolicitudRules::iniciarSolicitud` — not overridden
- `RegistroSolicitudRules::crearDatosIniciales` — not overridden
- `RegistroSolicitudRules::crearMensajeAxway` — not overridden
- `RegistroSolicitudRules::validarEstadoPreFirma` — not overridden
- `RegistroSolicitudRules::verificaSolicitud` — not overridden
- `RegistroSolicitudRules::validaSolicitudGuardada` — not overridden
- `RegistroSolicitudRules::crearSolicitud` — not overridden
- `RegistroSolicitudRules::prepararSolicitud` — not overridden
- `RegistroSolicitudRules::crearFirmaUsuario` — not overridden
- `RegistroSolicitudRules::crearFirmaSistema` — not overridden
- `RegistroSolicitudRules::prepararTramite` — not overridden
- `RegistroSolicitudRules::generarCadenaOriginalSolicitante` — not overridden
- `RegistroSolicitudRules::completarPrellenadoSolictud` — not overridden

Shared beans this controller or service calls, and the method. Omit beans the architecture file already names unless this process calls them for a reason that file does not cover.

- `AdaceServices::obtenerAdace` — `buscarAdace` calls `obtenerAdace(clave).get(0)` when the solicitante entidad is non-null, then sets `unidadAdministrativaRepresentacionFederal` from `param.getClave()` and returns `param.getDescripcion()`. `Sin ADACE` is returned only when `param` is null. An empty list is not handled (`get(0)`). When the entidad is null, `param` stays a new `Parametro` and the method returns that object's description
- `AdaceServices::armaCadenaQR` — PDF parameter `QR_TEXT` in `generaAcuseRecibo`
- `IDCService::contribuyenteActivo` — `buscarRfcValido`. The architecture file's `iniciar` path uses `buscarContribuyente`; this process does not override service `iniciar`, and this call is the extra RFC check
- `ReportesServices::guardarReporteOficial` — PDF as `EstadoDocumentoOficial.ACUSE_RECEPCION` and Excel as `EstadoDocumentoOficial.ACUSE_DETALLE_SOLICITUD`
- `EnviarNotificacionesCommonAvAServices::enviaListaCorreos` — after controller `firmar`, arguments `idSolicitud`, unit clave, `"Nuevo Aviso"`, null
- `OirRecibirNotificacionesServices::loadPersonas` — consulta `showSol` only

## Do not use

- Sibling process numbers seen nearby: `32501`, `32502`, `32503`, `32504`, `32505`, `32506`, `32507`, `32508`, `32509`, `32510`, `32511`, `32512`, `32513`, `32514`, `32516`, `32517`, `6901`, `6902`, `6903`, `6904`, `6905`, `6906`, `6907`, `6908`, `6909`, `6915`, `6920`. Numbers come from sibling `constants` files under `avisosAgace`, or from `DiscriminadorTipoTramiteConstants` in `base-jms-ibm` when the sibling entity points at that constant. Do not open their services or rules
- Unused imports in the registro controller: `JspPagesConstantsAvisoRetornoSeguroVehiculosExtranjeros`, `SolicitudAvisoRetornoSeguroVehiculosExtranjeros`, `ContribuyenteDto`, `DicCompDismContAprovDto`, `ElementoWizard.Estados`. `JspPagesConstantsMercDesmSinMon` is used for `ID_DOCTO_OFICIAL_EXCEL`
- Commons trees that this module’s pom does not depend on: `base`, `base-jms`, `base-jms-ejb-ibm`, `base-jms-noAxway`, `base-jms-noAxway-ibm`, `base-jms-noAxway-gobmx-ibm`, `vu-archetype`, `axwayAdapter`

## Capture flow

Numbered, in call order. Each step names the method and the file.

1. `iniciar`, `mostrar`, and `mostrarCaptura` on `RegistroSolicitudAvisoValorProvisionalController` call `super`, then `buscarAdace`
2. `cargarTiposDocumentos`, `desplegarArchivos`, and `mostrarFirma` add `solicitud.verificar.informacion` (and `solicitud.verificar.informacion.salto` on `cargarTiposDocumentos`) and call `super`
3. `getJspCaptura` returns `registrarSolicitud.jsp`, which includes `datosAviso.jsp` and loads `registrarSolicitud.js`
4. On ready, `registrarSolicitud.js` applies the persona-moral branch, then `ponerRqueridos`. After 1500 ms it copies `estadoHidden`, `mpioAlcaldiaHidden`, and `coloniaHidden` into catalog selectors and binds `input[name="guardar"]`
5. That click sets `#tabs` to index 1, calls `validartamanioCampos`, then `enviarDatos` (AJAX `recibirDatos`). `validartamanioCampos` returns true, so the click does not cancel submit
6. `guardar` calls `super.guardar`, stores the solicitud in `SOLICITUD_SESSION`, reads `VALOR_PROV_DTO` with no null check, sets `idSolicitud`, and calls `guardarProvisional` with `guardarDireccion` and `guardarEmpresa`. A missing session DTO throws on `vp.setIdSolicitud`. The service deletes prior `DireccionST` and `Empresa` when `idSolicitud` is set, before `super.guardar`
7. `firmar` calls `super.firmar`, then `enviaListaCorreos`
8. `generaAcuseRecibo` writes the PDF, then the Excel chosen by `cveRolCapturista.equals("PersonaMoral")`

## Role branches

Each branch that shows, hides, or changes `required`. Quote the condition.

- Condition: `$('#cveRolCapturista').val() == "PersonaMoral"` in `registrarSolicitud.js`
  - Shows: `#mostrarRep` (`display = "block"`)
  - Hides: `#dos` (`display = "none"`)
  - Adds required: `#rfcRL`, `#nombreRL`, `#paternoRL`, `#maternoRL`, `#telefono`, `#email`
  - Removes required: none in this branch
  - Other effect (document, report): `REANX.VPM` returns true from `validaReglaDocumentosEspecificos` when `seQuiereObligatorio` is true; Excel bean is `acuseReporteExcel32515`
- Condition: any other capturista value, including a value that is not `PersonaMoral`
  - Shows: `#dos`
  - Hides: `#mostrarRep`
  - Adds required: none
  - Removes required: `#rfcRL`, `#nombreRL`, `#paternoRL`, `#maternoRL`, `#telefono`, `#email`
  - Other effect (document, report): `REANX.VPM` returns false when `seQuiereObligatorio` is true. Excel uses `acuseReporteExcel32515Fisica` only when `cveRolCapturista` is non-null and not `PersonaMoral`. A null role throws on `equals` before the else branch

## Submit validation

- Action that saves or continues: `input[name="guardar"]`. `encabezadoWizardCaptura.jsp` renders `s:submit` name `guardar` value `Siguiente`. `pieWizardCaptura.jsp` renders `input` name `guardar` value `Continuar`. Wizard event `guardar`
- Tab or panel switch before validation: `$('#tabs').tabs({ active: 1 })` in `registrarSolicitud.js`, before `validartamanioCampos`
- Hidden-field behavior: Not found. This script does not set `ignore`. The click comment says the switch is because of `ignore: ":hidden"`. The platform default is in `.cursor/knowledge/legacy-base/architecture.md`
- Function that can block submit: the `guardar` click returns false only when `validartamanioCampos` returns false. The live function returns true, so this script does not block. `enviarDatos` alerts `Error: ` plus `error.responseText` on AJAX failure and does not cancel the click
- Functions that always return true or are commented out: `validartamanioCampos` returns true. The `#idPatAutAgeAdu` length check inside it is commented out. `validartamanio`, `validarNumeroDecimal`, and `validaFechas` are not called from the ready handler or the click. A second `validaFacAp` with `/^\d{0,3}(\.\d{0,18})?$/` and the message `No cumple con el formato esperado: 999.999999999999999999` is commented out. The live `validaFacAp` is the one under **Fields**

`pieWizardCaptura.jsp` builds a validator for `#formSolicitud`. This capture form id is `formId`. `registrarSolicitud.jsp` still calls `$.fn.stripesValidation` from `s:field-metadata`.

## Fields

One row per rendered input, select, or textarea. `Not found` for a cell the code does not define. Cite the file in Evidence.

Capture inputs are in `datosAviso.jsp`, the hidden inputs in `registrarSolicitud.jsp`, and `adace.jsp`. Label cells are the `s:label` `name` key. No properties file in this repo defines those keys.

Keypress allowlist in `datosAviso.jsp`, cited below as the address allowlist: letters, digits, comma, parentheses, space, accented vowels, and ñ (`\u00f1` / `\u00d1`). Several of those literals also contain a replacement character before `opqrstuvwxyz`. Name allowlist: letters, digits, space, accented vowels, and ñ, without comma or parentheses.

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `solicitud.avisosAgace.ingAcumulables` | `ingAcumulables` | `ingAcumulables` | `solicitud.direccionST.generico2` | Always (`class="required"` and a label asterisk). `ponerRqueridos` does not add it | 50 | address allowlist | Not found | Not found | Not found | Session DTO, then `DireccionStOrigen.generico2`. No second check | `datosAviso.jsp`; helper `guardarDireccion` |
| `solicitud.avisosAgace.capSocial` | `capSocial` | `capSocial` | `solicitud.direccionST.generico3` | Always (`class="required"` and a label asterisk). `ponerRqueridos` does not add it | 50 | address allowlist | Not found | Not found | Not found | Session DTO, then `generico3` | `datosAviso.jsp`; helper |
| `solicitud.avisosAgace.rfc` (representante) | `rfcRL` | `rfcRL` | `solicitud.empresa.rfc` | When `$('#cveRolCapturista').val() == "PersonaMoral"` (JS adds `required`). Label asterisk only in the markup | 13 | keyup strips characters outside `a-zA-Z0-9()&,` | `buscarRfcValidoPBA`: empty check, then `/^[A-ZÑ&]{3,4}\d{6}[A-Z\d]{3}$/`, then `buscarRfcValido` | `Debes ingresar un RFC`; `Existen datos incorrectos que no cumplen con el formato esperado.`; `El RFC no tiene un estado activo y vigente` | Clears the field on empty or bad format. Inactive RFC alerts and does not clear | `IDCService.contribuyenteActivo` after `toUpperCase()`. Stored as `Empresa.rfc` | `datosAviso.jsp`; `registrarSolicitud.js`; controller `buscarRfcValido` |
| `solicitud.avisosAgace.nombre` | `nombreRL` | `nombreRL` | `solicitud.empresa.nombre` | When PersonaMoral | 50 | name allowlist | Not found | Not found | Not found | Session DTO, then `Empresa.nombre` | `datosAviso.jsp` |
| `solicitud.avisosAgace.paterno` | `paternoRL` | `paternoRL` | `solicitud.empresa.apellidoPaterno` | When PersonaMoral | 50 | name allowlist | Not found | Not found | Not found | Session DTO, then `apellidoPaterno` | `datosAviso.jsp` |
| `solicitud.avisosAgace.materno` | `maternoRL` | `maternoRL` | `solicitud.empresa.apellidoMaterno` | When PersonaMoral | 50 | name allowlist | Not found | Not found | Not found | Session DTO, then `apellidoMaterno` | `datosAviso.jsp` |
| `solicitud.avisosAgace.telefono` | `telefono` | `telefono` | `solicitud.direccionST.telefono` | When PersonaMoral | 15 | address allowlist | Not found | Not found | Not found | Session DTO, then `DireccionStOrigen.telefono` | `datosAviso.jsp` |
| `solicitud.avisosAgace.email` | `email` | `email` | `solicitud.empresa.correoElectronico` | When PersonaMoral | 50 | keypress allows letters, digits, `(),.@`, accents, ñ, space | `validaCorreo`: `/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/` | `Debes ingresar un correo`; `Debes ingresar un correo válido` (source `v&aacute;lido`) | Clears the field | Session DTO, then `correoElectronico`. No second check | `datosAviso.jsp`; `validaCorreo` |
| `solicitud.avisosAgace.fecIniVigS` | `fecIniVigS` | `fecIniVigS` | `solicitud.empresa.fechaInicioVigencia` | Always (`ponerRqueridos` adds `required`). Label asterisk. The input has no `class="required"` | 10 | `datepicker-group`, `readonly="true"`, `formatPattern="dd/MM/yyyy"`. No `.datepicker()` call on this id | Not found | Not found | Not found | `recibirDatos` parses `dd/MM/yyyy`. Parse failure leaves null | `datosAviso.jsp`; controller `recibirDatos` |
| `solicitud.avisosAgace.fecFinVigS` | `fecFinVigS` | `fecFinVigS` | `solicitud.empresa.fecFinVigencia` | Always (`ponerRqueridos`). Label asterisk. No `class="required"` on the input | 10 | same markup as the start date | Not found | Not found | Not found | Same parse. No comparison with the start date | `datosAviso.jsp`; controller `recibirDatos` |
| `solicitud.avisosAgace.noPolizaSeg` | `noPolizaSeg` | `noPolizaSeg` | `solicitud.empresa.razonSocial` | Always (`class="required"` and `ponerRqueridos`) | 60 | address allowlist | Not found | Not found | Not found | Session DTO, then `Empresa.razonSocial` | `datosAviso.jsp` |
| `solicitud.avisosAgace.costSegGlobal` | `costSegGlobal` | `costSegGlobal` | `solicitud.empresa.caracterEmpresa` | Always (`class="required"` and `ponerRqueridos`) | 13 | Not found | `validaCostSeg`: `/^\d{0,10}(\.\d{0,2})?$/` | `No cumple con el formato esperado: 9999999999.99` | Clears the input and marks `errorCostSeg` | Session DTO, then `caracterEmpresa`. No second check | `datosAviso.jsp`; `validaCostSeg` |
| `solicitud.avisosAgace.valMerc` | `valMerc` | `valMerc` | `solicitud.empresa.numeroProgramaDGCESE` | Always (`class="required"` and `ponerRqueridos`) | 13 | Not found | `validaValMerc`: `/^\d{0,10}(\.\d{0,2})?$/` | `No cumple con el formato esperado: 9999999999.99` | Clears the input and marks `errorValMerc` | Session DTO, then `numeroProgramaDGCESE` | `datosAviso.jsp`; `validaValMerc` |
| `solicitud.avisosAgace.factorAplicable` | `factorAplicable` | `factorAplicable` | `solicitud.direccionST.fax` | Always (`class="required"` and `ponerRqueridos`) | 22 | Not found | `validaFacAp`: `/^\d{1,3}(\.\d{1,18})?$/` | `No cumple con el formato esperado 999.999999999999999999` | Clears the input and marks `errorFacAp` | Session DTO, then `DireccionStOrigen.fax`. No second check | `datosAviso.jsp`; live `validaFacAp` in `registrarSolicitud.js` |
| `solicitud.avisosAgace.rfc` (compañía) | `rfcC` | `rfcC` | `solicitud.direccionST.generico1` | Always (`class="required"` and `ponerRqueridos`) | 13 | keyup strips characters outside `a-zA-Z0-9()&,` | Not found | Not found | Not found | Session DTO, then `generico1`. No format check and no IDC call | `datosAviso.jsp` |
| `solicitud.avisosAgace.razonsocial` | `razonsocial` | `razonsocial` | `solicitud.direccionST.nombreComercial` | Always (`class="required"` and `ponerRqueridos`) | 50 | address allowlist | Not found | Not found | Not found | Session DTO, then `nombreComercial` | `datosAviso.jsp` |
| `solicitud.envioAvisos.entidadFederativa` | Not found | `entidadFederativa` | `solicitud.direccionST.entidadFederativa.clave` | Always (`styleClass="required"`) | Not found | catalog `estado`. No `parentSelectName` | Not found | Not found | Not found | Session DTO clave on `EntidadFederativa` | `datosAviso.jsp`. `enviarDatos` reads `#solicitud.direccionST.entidadFederativa.clave` |
| `solicitud.avisosAgace.municipioDemarcacion` | Not found | `delegacionMunicipio` | `solicitud.direccionST.delegacionMunicipio.clave` | Always (`styleClass="required"`) | Not found | catalog `municipioMex`, `parentSelectName` `solicitud.direccionST.entidadFederativa.clave` | Not found | Not found | Not found | Session DTO clave on `DelegacionMunicipio` | `datosAviso.jsp`. `enviarDatos` reads `select[name="solicitud.direccionST.delegacionMunicipio.clave"]` |
| `solicitud.envioAvisos.colonia` | Not found | `colonia` | `solicitud.direccionST.colonia.clave` | Always (`styleClass="required"`) | Not found | catalog `colonia`, `parentSelectName` `solicitud.direccionST.delegacionMunicipio.clave` | Not found | Not found | Not found | Session DTO clave on `Colonia` | `datosAviso.jsp`. `enviarDatos` reads `select[name="solicitud.direccionST.colonia.clave"]` |
| `solicitud.avisosAgace.localidad` | `localidad` | `localidad` | `solicitud.direccionST.descripcionUbicacion` | Always (`class="required"` and `ponerRqueridos`) | 15 | address allowlist | Not found | Not found | Not found | Session DTO, then `descripcionUbicacion` | `datosAviso.jsp` |
| `solicitud.avisosAgace.calle` | `calle` | `calle` | `solicitud.direccionST.calle` | Always (`class="required"` and `ponerRqueridos`) | 250 | address allowlist | Not found | Not found | Not found | Session DTO, then `calle` | `datosAviso.jsp` |
| `solicitud.avisosAgace.numExterior` | `numExterior` | `numExterior` | `solicitud.direccionST.numeroExterior` | Always (`class="required"` and `ponerRqueridos`) | 15 | address allowlist | Not found | Not found | Not found | Session DTO, then `numeroExterior` | `datosAviso.jsp` |
| `solicitud.avisosAgace.numeroLetraInterior` | `numeroLetraInterior` | `numeroLetraInterior` | `solicitud.direccionST.numeroInterior` | Always (`class="required"` on the input). No label asterisk. `ponerRqueridos` does not add it | 50 | address allowlist | Not found | Not found | Not found | Session DTO, then `numeroInterior` | `datosAviso.jsp` |
| `solicitud.avisosAgace.codigoPostal` | `codigoPostal` | `codigoPostal` | `solicitud.direccionST.ciudad` | Always (`class="required"` and `ponerRqueridos`) | 50 | address allowlist | Not found | Not found | Not found | Session DTO, then `ciudad` | `datosAviso.jsp` |
| `solicitud.cveRolCapturista` | `cveRolCapturista` | `solicitud.cveRolCapturista` | `solicitud.cveRolCapturista` (`CVE_ROL_CAPTURISTA` on `Solicitud`) | Not required | Not found | hidden | Not found | Not found | Not found | Not posted by `enviarDatos`. Read for the role branch, `REANX.VPM`, and the Excel bean | `datosAviso.jsp`; `common-model` `Solicitud.java` |
| Not found | `idSolicitud` | `solicitud.idSolicitud` | `solicitud.idSolicitud` | Not required | Not found | hidden | Not found | Not found | Not found | Stripes bind. The same id is also rendered by `pieWizardCaptura.jsp` | `registrarSolicitud.jsp`; `pieWizardCaptura.jsp` |
| Not found | `estadoHidden` | `estadoHidden` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Copied onto `#solicitud.direccionST.entidadFederativa` after 1500 ms. Not in `enviarDatos` | `registrarSolicitud.jsp`; `registrarSolicitud.js` |
| Not found | `mpioAlcaldiaHidden` | `mpioAlcaldiaHidden` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Copied onto `#solicitud.direccionST.delegacionMunicipio`. Not in `enviarDatos` | `registrarSolicitud.jsp`; `registrarSolicitud.js` |
| Not found | `coloniaHidden` | `coloniaHidden` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Copied onto `#solicitud.direccionST.colonia`. Not in `enviarDatos` | `registrarSolicitud.jsp`; `registrarSolicitud.js` |
| Not found | `idPersonaSolicitud` | `solicitud.extranjeroAvisoAgace.idPersonaSolicitud` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Not read by this controller, helper, or `enviarDatos` | `registrarSolicitud.jsp` |
| Not found | `idMercanciaST` | `solicitud.mercanciaST.idMercanciaST` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Not read by this controller, helper, or `enviarDatos` | `registrarSolicitud.jsp` |
| Not found | `idPedimentoST` | `solicitud.pedimentoST.idPedimentoST` | Not found | Not required | Not found | hidden | Not found | Not found | Not found | Not read by this controller, helper, or `enviarDatos` | `registrarSolicitud.jsp` |
| Not found | `idDireccionST` | `solicitud.direccionST.idDireccionST` | `solicitud.direccionST.idDireccionST` | Not required | Not found | hidden | Not found | Not found | Not found | Not set by the helper. The helper sets `idSolicitud` on a new `DireccionStOrigen` | `registrarSolicitud.jsp`; helper `guardarDireccion` |
| `ADACE` (literal label) | `solicitud.solicitante.adace` | `adace` | controller field `adace` | Not required | Not found (`size="15"`, `readonly="true"`) | Not found | Not found | Not found | Not found | `buscarAdace` return text | `adace.jsp`; controller `buscarAdace` |
| Not found | Not found | `solicitud.unidadAdministrativaRepresentacionFederal.clave` | `solicitud.unidadAdministrativaRepresentacionFederal.clave` | Not required | Not found | hidden | Not found | Not found | Not found | `buscarAdace` sets the clave from `param.getClave()` | `adace.jsp`; controller `buscarAdace` |

## Dead selectors

JS or JSP ids that are marked required or validated and are not in the capture markup.

`ponerRqueridos` adds `required` to these ids. None of them is an `id` in `registrarSolicitud.jsp`, `datosAviso.jsp`, or `adace.jsp`. `declaraciones.jsp` is not included, so `#manifiesto0` is not rendered.

| Selector | What the script does | Evidence |
| --- | --- | --- |
| `#idRazonSocial` | adds `required` | `registrarSolicitud.js` `ponerRqueridos` |
| `#idRFC` | adds `required` | same |
| `#idTaxID` | adds `required` | same |
| `#solicitud.mercanciaST.cveFraccionArancelaria` | adds `required` | same |
| `#solicitud.mercanciaST.reglaFraccion` | adds `required` | same |
| `#idNICO` | adds `required` | same |
| `#idValorUSD` | adds `required` | same |
| `#idMarca` | adds `required` | same |
| `#idPesoKg` | adds `required` | same |
| `#idFechaInicioVigencia` | adds `required`. `validaFechas` also calls `.datepicker({minDate: new Date()})` on this id and is not called from ready | same |
| `#idDescMerc` | adds `required` | same |
| `#solicitud.direccionST.entidadFederativa` | adds `required`. The restore timeout also sets this id. The catalog `selectName` is `solicitud.direccionST.entidadFederativa.clave` | same |
| `#solicitud.direccionST.delegacionMunicipio` | adds `required`. The restore timeout sets this id. The `selectName` ends in `.clave` | same |
| `#solicitud.direccionST.colonia` | adds `required`. The restore timeout sets this id. The `selectName` ends in `.clave` | same |
| `#idCalle` | adds `required` | same |
| `#idNoExterior` | adds `required` | same |
| `#idCodPostal` | adds `required` | same |
| `#manifiesto0` | adds `required` | same |

## Documents

- Rule id: `REANX.VPM`
- Obligatory when: `seQuiereObligatorio` is true and `cveRolCapturista` equals `PersonaMoral` (returns true). The same rule with another role returns false. Every other rule, and this rule when `seQuiereObligatorio` is false, returns true
- Where it is implemented: `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl::validaReglaDocumentosEspecificos`
- Document names this process’s code lists: Not found. The method checks the rule id only

## Persistence

Helper mapping. One row per persisted property the helper or the controller sets.

| Request param | Entity | Property | Transform | Evidence |
| --- | --- | --- | --- | --- |
| `ingAcumulables` | `DireccionStOrigen` | `generico2` | none | helper `guardarDireccion` |
| `capSocial` | `DireccionStOrigen` | `generico3` | none | helper |
| `telefono` | `DireccionStOrigen` | `telefono` | none | helper |
| `factorAplicable` | `DireccionStOrigen` | `fax` | none | helper |
| `rfcC` | `DireccionStOrigen` | `generico1` | none | helper |
| `razonsocial` | `DireccionStOrigen` | `nombreComercial` | none | helper |
| `entidadFederativa` | `DireccionStOrigen` | `entidadFederativa.clave` | catalog clave | helper |
| `delegacionMunicipio` | `DireccionStOrigen` | `delegacionMunicipio.clave` | catalog clave | helper |
| `colonia` | `DireccionStOrigen` | `colonia.clave` | catalog clave | helper |
| `localidad` | `DireccionStOrigen` | `descripcionUbicacion` | none | helper |
| `calle` | `DireccionStOrigen` | `calle` | none | helper |
| `numExterior` | `DireccionStOrigen` | `numeroExterior` | none | helper |
| `numeroLetraInterior` | `DireccionStOrigen` | `numeroInterior` | none | helper |
| `codigoPostal` | `DireccionStOrigen` | `ciudad` | none | helper |
| `rfcRL` | `Empresa` | `rfc` | none | helper `guardarEmpresa` |
| `nombreRL` | `Empresa` | `nombre` | none | helper |
| `paternoRL` | `Empresa` | `apellidoPaterno` | none | helper |
| `maternoRL` | `Empresa` | `apellidoMaterno` | none | helper |
| `email` | `Empresa` | `correoElectronico` | none | helper |
| `fecIniVigS` | `Empresa` | `fechaInicioVigencia` | `dd/MM/yyyy` | controller `recibirDatos`, helper |
| `fecFinVigS` | `Empresa` | `fecFinVigencia` | `dd/MM/yyyy` | controller `recibirDatos`, helper |
| `noPolizaSeg` | `Empresa` | `razonSocial` | none | helper |
| `costSegGlobal` | `Empresa` | `caracterEmpresa` | none | helper |
| `valMerc` | `Empresa` | `numeroProgramaDGCESE` | none | helper |

`blnActivo` is set true on the address. `idSolicitud` is set from the saved solicitud. `empresa.solicitud` is the solicitud. Evidence: helper. `DireccionStOrigen` extends `DireccionST` (`common-model`).

Repository writes and deletes this process calls:

- `guardarvalorprovisional` — `persist` `DireccionST`, then `flush`. An exception is logged and swallowed
- `guardarempresa` — `persist` `Empresa`, then `flush`. An exception is logged and swallowed
- `eliminarDireccionST` — `delete from DireccionST dst where dst.idSolicitud = ?`
- `eliminarEmpresa` — `delete from Empresa emp where emp.solicitud.idSolicitud = ?`
- `obtenerDatosDireccionST` / `obtenerDatosEmpresa` — lists ordered by id. `ObtenerDatos` uses index 0. An empty result returns an empty list; the service then throws on `get(0)`
- `ConsultarDireccionST` / `ConsultarEmpresa` — first row, or a new empty instance when the list is empty or the query throws

## Reports

- Bean: `acuseRecepcionTramiteQr` → `/WEB-INF/reportes/common/acuseRecepcionTramite/AcuseRecepcionTramite.jasper`. Chosen always in `generaAcuseRecibo`. Written as `EstadoDocumentoOficial.ACUSE_RECEPCION`. Evidence: `reportes-envioavisos-context.xml` and controller `getAcuseReport`
- Bean: `acuseReporteExcel32515` → `/WEB-INF/reportes/common/avisoValorProvisional/formatoDescargaAvisosAgace32515.jasper`. Chosen when `getCveRolCapturista().equals("PersonaMoral")`. Written as `EstadoDocumentoOficial.ACUSE_DETALLE_SOLICITUD`
- Bean: `acuseReporteExcel32515Fisica` → `/WEB-INF/reportes/common/avisoValorProvisional/PersonaFisica/formatoDescargaAvisosAgaceFisica.jasper`. Chosen in the else branch. A null `cveRolCapturista` throws before that branch
- Session key the PDF id is stored under: `JspPagesConstansRegistroSolicitud.ID_DOCTO_OFICIAL` = `"__ID_DOCTO_OFICIAL"`
- Session key the Excel id is stored under: `JspPagesConstantsMercDesmSinMon.ID_DOCTO_OFICIAL_EXCEL` = `"__ID_DOCTO_OFICIAL_EXCEL"`

## Not found

- Resolved label text for every `solicitud.avisosAgace.*` and `solicitud.envioAvisos.*` key. Searched `*.properties` under the repository. The JSP keys are the label cells
- DOM id on the three `stk-web:catalog` tags in `datosAviso.jsp`. The tags set `selectName` and do not set `id`. The catalog tag class was not in `base-jms-ibm` `*.tld`
- Filter cell on `costSegGlobal`, `valMerc`, and `factorAplicable`. Searched `datosAviso.jsp` and `registrarSolicitud.js`. Those fields use a blur regex, not a keypress filter
- Validator and message cells on text fields that only have maxlength, `required`, or a keypress allowlist. Searched `registrarSolicitud.js`. No extra function targets those ids
- End-date versus start-date check. Searched `registrarSolicitud.js` and `recibirDatos`. Neither compares the two dates
- A process `ignore` setting. Searched `registrarSolicitud.js`. The script comments on `ignore: ":hidden"` and does not assign it
- Document titles for `REANX.VPM`. Searched `validaReglaDocumentosEspecificos`. The method returns a boolean for the rule id and does not name a document
- Model path for the hidden names `solicitud.extranjeroAvisoAgace.idPersonaSolicitud`, `solicitud.mercanciaST.idMercanciaST`, and `solicitud.pedimentoST.idPedimentoST`. Searched `SolicitudAvisoValorProvisional` and `SolicitudDatosGenerales`. Those properties are not declared there
- `validaFechas` is not called from the capture ready handler. It targets the dead id `#idFechaInicioVigencia`, not `fecIniVigS`
