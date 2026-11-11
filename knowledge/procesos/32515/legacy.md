# Process 32515 — Aviso Valor Provisional

Legacy knowledge base. Source of truth for this trámite only. Built from the controller `RegistroSolicitudAvisoValorProvisionalController`.

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. This file records only 32515.

## Identity

- Process number: `32515`
- Entity: `SolicitudAvisoValorProvisional` — `@DiscriminatorValue` is `AvisoValorProvisional.ID_AVISO_VALOR_PROVISIONAL` (`"32515"`). Evidence: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional/model/SolicitudAvisoValorProvisional.java` and `constants/AvisoValorProvisional.java`
- Package: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional`
- Registro URL: `ControllerUrlBindingConstantsAvisoValorProvisional.REGISTRO_SOLICITUD_AVISO_VALOR_PROVISIONAL_URL_BINDING` = `ACTION_PREFIX + "/avisos-agace/avisoValorProvisional/solicitudAvisoValorProvisional" + ACTION_SUFFIX`
- Consulta URL: `CONSULTA_SOLICITUD_AVISO_VALOR_PROVISIONAL_URL_BINDING` = same prefix + `consultaSolicitudAvisoValorProvisional` + `ACTION_SUFFIX`
- Session key: `AvisoValorProvisional.VALOR_PROV_DTO` = `"ValorProvisionalDto"`
- Spring bean (solicitud): `SolicitudAvisoValorProvisional` in `legacy/vuc_mtto_aga2/avisos-agace/WebContent/WEB-INF/config/spring/avisosAgace/tramites/tramite-envioavisos-context.xml`. Discriminator map entry in `tramites/avisosAgace-common-context.xml`
- Module pom base artifact: `base-jms-ibm` (war) and `base-jms-ibm-jar` — commons tree used: `legacy/vuc_mtto_commons/base-jms-ibm`. Also depends on `common-model`. Evidence: `legacy/vuc_mtto_aga2/avisos-agace/pom.xml`

## Controllers

### Registro

- Class: `RegistroSolicitudAvisoValorProvisionalController`
- Path: `legacy/vuc_mtto_aga2/avisos-agace/src/mx/gob/ventanillaunica/www/avisosAgace/avisoValorProvisional/controller/RegistroSolicitudAvisoValorProvisionalController.java`
- Extends: `RegistroSolicitudController<SolicitudAvisoValorProvisional>`
- Wizard events: `iniciar`, `mostrar`, `buscarDatosSolitante`, `filtrarArchivosGenerales`, `guardar`, `guardarParcial`, `filtrarArchivosEspecificos`, `comboArchivosEspecificos`, `cargarListDoctosEspecificos`, `recibirDatos`, `guardar` (listed twice), `buscarAdace`, `buscarRfcValido`
- Capture JSP: `JspPagesConstantsAvisoValorProvisional.REGISTRO_SOLICITUD_AVISO_VALOR_PROVISIONAL` → `APP_BASE_DIRECTORY + "/avisosAgace/avisoValorProvisional/solicitud/registrarSolicitud.jsp"`. That page includes `datosAviso.jsp`
- AJAX methods on this class:
  - `recibirDatos` — request params listed in **Persistence** — returns JavaScript `"OK"` and stores `ValorProvisionalDto` in session. Dates parsed with `dd/MM/yyyy`; a parse failure is printed and the date stays null
  - `buscarRfcValido` — request param `rfc` — returns `iDCService.contribuyenteActivo(rfc.toUpperCase())` as a boolean. Empty rfc stays false. Exceptions are printed and the method still returns false

### Consulta

- Class: `ConsultaSolicitudAvisoValorProvisionalController`
- Path: `.../controller/ConsultaSolicitudAvisoValorProvisionalController.java`
- What it loads: `showSol` calls `getConsultaServices().iniciar`, `obtenerPersonaSolicitante`, `oirNotificarService.loadPersonas`, and `buscarAdace`. `showDet` calls `registroSolicitudAvisoValorProvisionalServices.buscar` then `ObtenerDatos`, and forwards `CONSULTA_DETALLE_SOLICITUD`

## Service and rules

- Service interface: `RegistroSolicitudAvisoValorProvisionalServices`
- Service implementation: `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl`
- Spring bean: `registroSolicitudAvisoValorProvisionalService` (`@Service`, `@Scope("session")`)
- Rules class: `RegistroSolicitudAvisoValorProvisionalRules`
- Spring bean: `registroSolicitudAvisoValorProvisionalRules` (`@Component` with no name)
- Repository: `RegistroSolicitudAvisoValorProvisionalRepository` / `RegistroSolicitudAvisoValorProvisionalRepositoryJPAImpl`
- Helper: `RegistroSolicitudAvisoValorProvisionalHelper` (`@Component`, default bean name)

Wired by `getRegistroSolicitudServices()` on the registro controller, then `getRegistroSolicitudRules()` and `getRegistroSolicitudRepository()` on the service.

Methods this service declares or overrides:

- `getRegistroSolicitudRepository` — returns `registroSolicitudAvisoValorProvisionalRepository`
- `getRegistroSolicitudRules` — returns `registroSolicitudAvisoValorProvisionalRules`
- `guardarProvisional` — persists `DireccionST` then `Empresa`
- `ObtenerDatos` — takes index 0 of `obtenerDatosEmpresa` and `obtenerDatosDireccionST`. An empty list throws
- `ConsultarDireccionST` / `ConsultarEmpresa` — first row, or a new empty instance
- `guardar` — when `idSolicitud` is set, deletes `DireccionST` and `Empresa`, then `super.guardar`
- `validaReglaDocumentosEspecificos` — see the rule below

Rules class methods this process declares:

- none; class body has no methods (`serialVersionUID` only)

The process-specific rule that does not live on the rules class:

- `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl.validaReglaDocumentosEspecificos`. When `seQuiereObligatorio` is true and `regla` equals `REANX.VPM`, returns true only if `cveRolCapturista` equals `PersonaMoral`, otherwise false. Every other case returns true. `cveRolCapturista.equals` assumes the role is non-null. The parent method returns false, so this override is what makes `REANX.VPM` obligatory for a persona moral

## Parents and shared beans

Platform contract: `.cursor/knowledge/legacy-base/architecture.md`. Do not restate it.

Pom base artifact: `base-jms-ibm` — commons tree: `legacy/vuc_mtto_commons/base-jms-ibm`

Overrides against that chain:

- `RegistroSolicitudController::getRegistroSolicitudServices` — overridden — returns `registroSolicitudAvisoValorProvisionalService`
- `RegistroSolicitudController::setSolicitud` — overridden — stores the solicitud field
- `RegistroSolicitudController::getJspCaptura` — overridden — reloads `DireccionST` and `Empresa` when `idSolicitud` is set, then returns the capture JSP constant
- `RegistroSolicitudController::getActionForm` — overridden — returns the registro URL binding
- `RegistroSolicitudController::iniciar` — overridden — `super.iniciar`, then `buscarAdace`
- `RegistroSolicitudServicesPOJOImpl::iniciar` — not overridden
- `RegistroSolicitudServicesPOJOImpl::guardar` — overridden — deletes address and company when `idSolicitud` is set, then `super.guardar`
- `RegistroSolicitudServicesPOJOImpl::firmar` — not overridden on the service. The controller overrides `firmar`: `super.firmar`, then `enviaListaCorreos`. Mail failure is logged and does not roll back the signature
- `RegistroSolicitudServicesPOJOImpl::buscar` — not overridden
- `RegistroSolicitudServicesPOJOImpl::validaReglaDocumentosEspecificos` — overridden — `REANX.VPM` is obligatory only for `PersonaMoral`
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

Shared beans this controller or service calls:

- `AdaceServices::obtenerAdace` — `buscarAdace` sets `unidadAdministrativaRepresentacionFederal`. Empty result returns the text `Sin ADACE`
- `AdaceServices::armaCadenaQR` — PDF parameter `QR_TEXT` in `generaAcuseRecibo`
- `IDCService::contribuyenteActivo` — `buscarRfcValido`. The parent `iniciar` path still uses `buscarContribuyente`; this call is extra
- `ReportesServices::guardarReporteOficial` — PDF `ACUSE_RECEPCION` and Excel `ACUSE_DETALLE_SOLICITUD`
- `EnviarNotificacionesCommonAvAServices::enviaListaCorreos` — after `firmar`, arguments `idSolicitud`, unit clave, `"Nuevo Aviso"`, null
- `OirRecibirNotificacionesServices::loadPersonas` — consulta `showSol` only

## Do not use

- Sibling process numbers seen nearby: `32514` and `32516` in `reportes-envioavisos-context.xml`. Do not open their services or rules
- Unused imports in the registro controller: `JspPagesConstantsAvisoRetornoSeguroVehiculosExtranjeros`, `SolicitudAvisoRetornoSeguroVehiculosExtranjeros`, `ContribuyenteDto`, `DicCompDismContAprovDto`. `JspPagesConstantsMercDesmSinMon` is used only for the session key `ID_DOCTO_OFICIAL_EXCEL`
- Commons trees that this module’s pom does not depend on: `base`, `base-jms`, `base-jms-ejb-ibm`, `base-jms-noAxway`, `base-jms-noAxway-ibm`, `base-jms-noAxway-gobmx-ibm`

## Capture flow

1. `iniciar` / `mostrar` / `mostrarCaptura` on the registro controller call `buscarAdace`
2. `getJspCaptura` returns `registrarSolicitud.jsp`, which includes `datosAviso.jsp` and loads `registrarSolicitud.js`
3. On ready, `registrarSolicitud.js` applies the persona-moral branch, then `ponerRqueridos`
4. The Guardar click handler switches `#tabs` to index 1, calls `validartamanioCampos`, then `enviarDatos` (AJAX `recibirDatos`)
5. `guardar` calls `super.guardar`. The service deletes prior `DireccionST` and `Empresa` when `idSolicitud` is set. The controller then maps the session DTO through the helper and calls `guardarProvisional`
6. `firmar` calls `super.firmar`, then `enviaListaCorreos`
7. `generaAcuseRecibo` writes the PDF, then the Excel chosen by `cveRolCapturista`

## Role branches

- Condition: `$('#cveRolCapturista').val() == "PersonaMoral"` in `registrarSolicitud.js`
  - Shows: `#mostrarRep`
  - Hides: `#dos`
  - Adds required: `#rfcRL`, `#nombreRL`, `#paternoRL`, `#maternoRL`, `#telefono`, `#email`
  - Removes required: none in this branch
  - Other effect: `REANX.VPM` returns true from `validaReglaDocumentosEspecificos`; Excel bean is `acuseReporteExcel32515`
- Condition: any other capturista value
  - Shows: `#dos`
  - Hides: `#mostrarRep`
  - Adds required: none
  - Removes required: `#rfcRL`, `#nombreRL`, `#paternoRL`, `#maternoRL`, `#telefono`, `#email`
  - Other effect: `REANX.VPM` returns false when `seQuiereObligatorio` is true; Excel bean is `acuseReporteExcel32515Fisica`

## Submit validation

- Action that saves or continues: `input[name="guardar"]` click, wizard event `guardar`
- Tab or panel switch before validation: `$('#tabs').tabs({ active: 1 })` in `registrarSolicitud.js`. The comment says this is because validation uses `ignore: ":hidden"`
- Hidden-field behavior: the script comment says `ignore: ":hidden"`. The platform file `.cursor/knowledge/legacy-base/architecture.md` records jquery `ignore: []` in `base-jms-ibm`. This trámite’s script still switches tabs. Do not treat the comment as the platform default
- Function that can block submit: `validartamanioCampos` — returns false only in a commented patente-length check — the live function returns true, so it does not block
- Functions that always return true or are commented out: `validartamanioCampos` returns true. The patente length check inside it is commented out

Stripes validation from `s:field-metadata` on `registrarSolicitud.jsp` still applies `class="required"` and the classes `ponerRqueridos` adds. That is the platform contract, not a second process function.

## Fields

Capture page: `datosAviso.jsp`. Address keypress set, cited below as the address charset: letters, digits, comma, parentheses, space, accents, ñ. Name keypress set: letters, digits, space, accents, ñ (no comma, no parentheses).

| UI label | DOM id | Request param | Model path | Required when | Max | Filter | Validator | Message | On failure | Server | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `solicitud.avisosAgace.ingAcumulables` | `ingAcumulables` | `ingAcumulables` | `solicitud.direccionST.generico2` | Always (`class="required"`, asterisk, `ponerRqueridos`) | 50 | address charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp`; `ponerRqueridos` |
| `solicitud.avisosAgace.capSocial` | `capSocial` | `capSocial` | `solicitud.direccionST.generico3` | Always | 50 | address charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp`; `ponerRqueridos` |
| `solicitud.avisosAgace.rfc` (representante) | `rfcRL` | `rfcRL` | `solicitud.empresa.rfc` | When `cveRolCapturista == "PersonaMoral"` | 13 | keyup strips characters outside `a-zA-Z0-9()&,` | `buscarRfcValidoPBA`: empty check, then `/^[A-ZÑ&]{3,4}\d{6}[A-Z\d]{3}$/`, then `buscarRfcValido` | `Debes ingresar un RFC`; `Existen datos incorrectos que no cumplen con el formato esperado.`; `El RFC no tiene un estado activo y vigente` | Clears the field on empty or bad format. Inactive RFC alerts and does not clear | `contribuyenteActivo` | `datosAviso.jsp`; `registrarSolicitud.js`; controller `buscarRfcValido` |
| `solicitud.avisosAgace.nombre` | `nombreRL` | `nombreRL` | `solicitud.empresa.nombre` | When PersonaMoral | 50 | name charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp` |
| `solicitud.avisosAgace.paterno` | `paternoRL` | `paternoRL` | `solicitud.empresa.apellidoPaterno` | When PersonaMoral | 50 | name charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp` |
| `solicitud.avisosAgace.materno` | `maternoRL` | `maternoRL` | `solicitud.empresa.apellidoMaterno` | When PersonaMoral | 50 | name charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp` |
| `solicitud.avisosAgace.telefono` | `telefono` | `telefono` | `solicitud.direccionST.telefono` | When PersonaMoral | 15 | address charset | Not found | Not found | Not found | Stored on the session DTO | `datosAviso.jsp` |
| `solicitud.avisosAgace.email` | `email` | `email` | `solicitud.empresa.correoElectronico` | When PersonaMoral | 50 | keypress allows letters, digits, `(),.@`, accents, ñ, space | `validaCorreo`: `/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/` | `Debes ingresar un correo`; `Debes ingresar un correo válido` | Clears the field | Stored on the session DTO. No second check | `datosAviso.jsp`; `validaCorreo` |
| `solicitud.avisosAgace.fecIniVigS` | `fecIniVigS` | `fecIniVigS` | `solicitud.empresa.fechaInicioVigencia` | Always (`ponerRqueridos` adds `required`) | 10 | datepicker, `readonly`, `formatPattern` `dd/MM/yyyy` | Not found | Not found | Not found | `recibirDatos` parses `dd/MM/yyyy`. Parse failure leaves null | `datosAviso.jsp`; controller `recibirDatos` |
| `solicitud.avisosAgace.fecFinVigS` | `fecFinVigS` | `fecFinVigS` | `solicitud.empresa.fecFinVigencia` | Always (`ponerRqueridos`) | 10 | datepicker, `readonly`, `dd/MM/yyyy` | Not found | Not found | Not found | Same parse as the start date. No order check | `datosAviso.jsp`; controller `recibirDatos` |
| `solicitud.avisosAgace.noPolizaSeg` | `noPolizaSeg` | `noPolizaSeg` | `solicitud.empresa.razonSocial` | Always | 60 | address charset | Not found | Not found | Not found | Stored as `Empresa.razonSocial` | `datosAviso.jsp` |
| `solicitud.avisosAgace.costSegGlobal` | `costSegGlobal` | `costSegGlobal` | `solicitud.empresa.caracterEmpresa` | Always | 13 | Not found | `validaCostSeg`: `/^\d{0,10}(\.\d{0,2})?$/` | `No cumple con el formato esperado: 9999999999.99` | Clears the input and marks `errorCostSeg` | Stored as `Empresa.caracterEmpresa`. No second check | `datosAviso.jsp`; `validaCostSeg` |
| `solicitud.avisosAgace.valMerc` | `valMerc` | `valMerc` | `solicitud.empresa.numeroProgramaDGCESE` | Always | 13 | Not found | `validaValMerc`: `/^\d{0,10}(\.\d{0,2})?$/` | `No cumple con el formato esperado: 9999999999.99` | Clears the input and marks `errorValMerc` | Stored as `Empresa.numeroProgramaDGCESE` | `datosAviso.jsp`; `validaValMerc` |
| `solicitud.avisosAgace.factorAplicable` | `factorAplicable` | `factorAplicable` | `solicitud.direccionST.fax` | Always | 22 | Not found | `validaFacAp`: `/^\d{1,3}(\.\d{1,18})?$/` | `No cumple con el formato esperado 999.999999999999999999` | Clears the input and marks `errorFacAp` | Stored as `DireccionStOrigen.fax` | `datosAviso.jsp`; `validaFacAp` |
| `solicitud.avisosAgace.rfc` (compañía) | `rfcC` | `rfcC` | `solicitud.direccionST.generico1` | Always | 13 | keyup strips characters outside `a-zA-Z0-9()&,` | Not found | Not found | Not found | Stored as `generico1`. No SAT format check and no IDC call | `datosAviso.jsp` |
| `solicitud.avisosAgace.razonsocial` | `razonsocial` | `razonsocial` | `solicitud.direccionST.nombreComercial` | Always | 50 | address charset | Not found | Not found | Not found | Stored as `nombreComercial` | `datosAviso.jsp` |
| `solicitud.envioAvisos.entidadFederativa` | catalog `solicitud.direccionST.entidadFederativa.clave` | `entidadFederativa` | `solicitud.direccionST.entidadFederativa` | Always (`styleClass="required"`, `ponerRqueridos`) | Not found | catalog `estado`. No parent | Not found | Not found | Not found | Stored as catalog clave | `datosAviso.jsp`; `recibirDatos` reads `solicitud.direccionST.entidadFederativa.clave` |
| `solicitud.avisosAgace.municipioDemarcacion` | catalog `solicitud.direccionST.delegacionMunicipio.clave` | `delegacionMunicipio` | `solicitud.direccionST.delegacionMunicipio` | Always | Not found | catalog `municipioMex`, `parentSelectName` = entidad federativa clave | Not found | Not found | Not found | Stored as catalog clave | `datosAviso.jsp` |
| `solicitud.envioAvisos.colonia` | catalog `solicitud.direccionST.colonia.clave` | `colonia` | `solicitud.direccionST.colonia` | Always | Not found | catalog `colonia`, `parentSelectName` = municipio clave | Not found | Not found | Not found | Stored as catalog clave | `datosAviso.jsp` |
| `solicitud.avisosAgace.localidad` | `localidad` | `localidad` | `solicitud.direccionST.descripcionUbicacion` | Always | 15 | address charset | Not found | Not found | Not found | Stored as `descripcionUbicacion` | `datosAviso.jsp` |
| `solicitud.avisosAgace.calle` | `calle` | `calle` | `solicitud.direccionST.calle` | Always | 250 | address charset | Not found | Not found | Not found | Stored as `calle` | `datosAviso.jsp` |
| `solicitud.avisosAgace.numExterior` | `numExterior` | `numExterior` | `solicitud.direccionST.numeroExterior` | Always | 15 | address charset | Not found | Not found | Not found | Stored as `numeroExterior` | `datosAviso.jsp` |
| `solicitud.avisosAgace.numeroLetraInterior` | `numeroLetraInterior` | `numeroLetraInterior` | `solicitud.direccionST.numeroInterior` | Always (`class="required"` on the input). Label has no asterisk and `ponerRqueridos` does not add it | 50 | address charset | Not found | Not found | Not found | Stored as `numeroInterior` | `datosAviso.jsp` |
| `solicitud.avisosAgace.codigoPostal` | `codigoPostal` | `codigoPostal` | `solicitud.direccionST.ciudad` | Always | 50 | address charset | Not found | Not found | Not found | Stored as `ciudad`. Not digits-only | `datosAviso.jsp` |
| `solicitud.cveRolCapturista` | `cveRolCapturista` | Not found | `solicitud.cveRolCapturista` | Not required | Not found | hidden | Not found | Not found | Not found | Drives the role branch, `REANX.VPM`, and the Excel bean. Not posted by `enviarDatos` | `datosAviso.jsp` |

`registrarSolicitud.jsp` also renders hidden inputs `solicitud.idSolicitud`, `estadoHidden`, `mpioAlcaldiaHidden`, `coloniaHidden`, `solicitud.extranjeroAvisoAgace.idPersonaSolicitud`, `solicitud.mercanciaST.idMercanciaST`, `solicitud.pedimentoST.idPedimentoST`, and `solicitud.direccionST.idDireccionST`. None of them is in `ponerRqueridos` or `enviarDatos`.

## Dead selectors

`ponerRqueridos` adds `required` to ids that are not in `datosAviso.jsp` or `registrarSolicitud.jsp`.

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
| `#idFechaInicioVigencia` | adds `required`. `validaFechas` also sets a datepicker `minDate` on this id | same |
| `#idDescMerc` | adds `required` | same |
| `#idCalle` | adds `required` | same |
| `#idNoExterior` | adds `required` | same |
| `#idCodPostal` | adds `required` | same |
| `#manifiesto0` | adds `required` | same |

## Documents

- Rule id: `REANX.VPM`
- Obligatory when: `seQuiereObligatorio` is true and `cveRolCapturista` equals `PersonaMoral`. Otherwise the override returns false for that rule, and true for every other rule
- Where it is implemented: `RegistroSolicitudAvisoValorProvisionalServicesPOJOImpl::validaReglaDocumentosEspecificos`
- Document names this process’s code lists: Not found. The method checks the rule id only. No document title is in this class

## Persistence

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

`blnActivo` is set true on the address. `empresa.solicitud` is set to the solicitud. Evidence: helper.

Repository writes and deletes this process calls:

- `guardarvalorprovisional` — `persist` `DireccionST`
- `guardarempresa` — `persist` `Empresa`
- `eliminarDireccionST` — `delete from DireccionST dst where dst.idSolicitud = ?`
- `eliminarEmpresa` — `delete from Empresa emp where emp.solicitud.idSolicitud = ?`
- `obtenerDatosDireccionST` / `obtenerDatosEmpresa` — lists. `ObtenerDatos` uses index 0
- `ConsultarDireccionST` / `ConsultarEmpresa` — first row, or a new empty instance

## Reports

- Bean: `acuseRecepcionTramiteQr` → URL from `getAcuseReport()` (the bean’s `url` property). Written as `EstadoDocumentoOficial.ACUSE_RECEPCION`. Chosen always in `generaAcuseRecibo`
- Bean: `acuseReporteExcel32515` → `/WEB-INF/reportes/common/avisoValorProvisional/formatoDescargaAvisosAgace32515.jasper`. Chosen when `cveRolCapturista` equals `PersonaMoral`
- Bean: `acuseReporteExcel32515Fisica` → `/WEB-INF/reportes/common/avisoValorProvisional/PersonaFisica/formatoDescargaAvisosAgaceFisica.jasper`. Chosen for every other capturista role
- Session key the PDF id is stored under: `JspPagesConstansRegistroSolicitud.ID_DOCTO_OFICIAL`
- Session key the Excel id is stored under: `JspPagesConstantsMercDesmSinMon.ID_DOCTO_OFICIAL_EXCEL`

## Not found

- Filter cell on `costSegGlobal`, `valMerc`, and `factorAplicable`. Searched `datosAviso.jsp` blur handlers and `registrarSolicitud.js`. Those fields use a blur regex, not a keypress filter
- Validator and message cells on text fields that have maxlength and `required` only. Searched `registrarSolicitud.js`. No extra function targets those ids
- End-date versus start-date check. Searched `registrarSolicitud.js` and `recibirDatos`. Neither compares the two dates
- Document titles for `REANX.VPM`. Searched `validaReglaDocumentosEspecificos`. The method returns a boolean for the rule id and does not name a document
- `validaFechas` is not called from the capture ready handler. It targets the dead id `#idFechaInicioVigencia`, not `fecIniVigS`
