# Legacy base architecture

Platform contract for a VUCEM trámite. A process knowledge base cites this file and records only that trámite’s overrides. It does not restate this chapter.

Paths are relative to the repository root.

## Trees

| Tree | Path | Role |
| --- | --- | --- |
| Trámites AGA | `legacy/vuc_mtto_aga2` | Process modules (one folder per module, such as `avisos-agace`) |
| Commons | `legacy/vuc_mtto_commons` | Shared parents, model, and the trámite archetype |
| Guber | `legacy/vuc_mtto_guber` | Separate product tree. Do not read it unless the controller path is under it |

Commons variants on disk, each a sibling directory:

- `base`
- `base-jms`
- `base-jms-ibm`
- `base-jms-ejb-ibm`
- `base-jms-noAxway`
- `base-jms-noAxway-ibm`
- `base-jms-noAxway-gobmx-ibm`
- `common-model`
- `infrastructure`
- `axwayAdapter`
- `axway-fix-model`
- `vu-archetype`

Open only the variant the module `pom.xml` depends on. Do not describe a method from another variant.

## Which parent a trámite uses

The module `pom.xml` `artifactId` selects the commons tree.

`legacy/vuc_mtto_aga2/avisos-agace/pom.xml` depends on `mx.gob.sat.vucem` artifacts `base-jms-ibm` (type `war`) and `base-jms-ibm-jar` (scope `provided`), and also on `common-model`. For a controller in that module, the parent chain below is `legacy/vuc_mtto_commons/base-jms-ibm`. Another module may declare a different artifact. Record the artifact the process module actually declares.

`vu-archetype` (`legacy/vuc_mtto_commons/vu-archetype`) scaffolds a trámite with a `RegistroSolicitud` service, rules, and repository, plus separate task types (evaluar, dictamen, requerimiento, notificación). Those task controllers are not the registro chain. Use the chain in this file only when the named controller extends `RegistroSolicitudController`.

## Registro layering (`base-jms-ibm`)

Documented chain for a controller that extends `RegistroSolicitudController`. Other commons trees are listed above so they are not opened by mistake.

### Controller

`legacy/vuc_mtto_commons/base-jms-ibm/src/mx/gob/ventanillaunica/www/common/controller/RegistroSolicitudController.java`

- `abstract class RegistroSolicitudController<S extends Solicitud> extends BaseController`
- Abstracts the process must implement: `getRegistroSolicitudServices()`, `setSolicitud()`, `getJspCaptura()`, `getActionForm()`
- Wizard steps are `ElementoWizard` values this class sets: `CAPTURAR`, `TIPODOCUMENTO`, `ANEXAR_DOCUMENTOS`, `FIRMAR`, `NOTIFICACIONES`

### Service

`legacy/vuc_mtto_commons/base-jms-ibm/src/mx/gob/ventanillaunica/www/common/service/impl/RegistroSolicitudServicesPOJOImpl.java`

- `abstract class RegistroSolicitudServicesPOJOImpl<S extends Solicitud> extends BaseSerializableBusinessServices implements RegistroSolicitudServices<S>`
- Abstracts: `getRegistroSolicitudRepository()`, `getRegistroSolicitudRules()`
- `iniciar` loads the solicitante through `idcService.buscarContribuyente` and then `getRegistroSolicitudRules().iniciarSolicitud`
- `guardar` calls `getRegistroSolicitudRules().crearSolicitud` for a new solicitud, then the repository
- `firmar` calls `getRegistroSolicitudRules().validarEstadoPreFirma`, then `prepararTramite`, `crearFirmaUsuario`, `generarCadenaOriginalSolicitante`, and `crearFirmaSistema`
- `buscar` loads `getRegistroSolicitudRepository().buscarSolicitud` and sets `discriminatorValue` from `tipoTramite.idTipoTramite`
- `validaReglaDocumentosEspecificos` returns `false`. A document rule is obligatory only when the process service overrides this method and returns true

### Rules

`legacy/vuc_mtto_commons/base-jms-ibm/src/mx/gob/ventanillaunica/www/common/rule/RegistroSolicitudRules.java`

- `abstract class RegistroSolicitudRules<S extends Solicitud> extends BaseSerializableBusinessRules`
- Methods a process may override: `iniciarSolicitud`, `crearDatosIniciales`, `crearMensajeAxway`, `validarEstadoPreFirma`, `verificaSolicitud`, `validaSolicitudGuardada`, `crearSolicitud`, `prepararSolicitud`, `crearFirmaUsuario`, `crearFirmaSistema`, `prepararTramite`, `generarCadenaOriginalSolicitante`, `completarPrellenadoSolictud`
- An empty process rules class inherits these. Record `not overridden`. Do not paste the parent body

### What the process module supplies

- Entity subclass of `Solicitud` with its own `@DiscriminatorValue` (the process number)
- Repository and helper
- Spring solicitud bean, discriminator map, and URL binding in that module’s Spring XML

## Solicitud identity

`legacy/vuc_mtto_commons/common-model/src/mx/gob/ventanillaunica/www/common/domain/model/Solicitud.java`

- `@Inheritance(strategy = InheritanceType.SINGLE_TABLE)` on table `VUC_SOLICITUD`
- `@DiscriminatorColumn(name = "ID_TIPO_TRAMITE", discriminatorType = DiscriminatorType.INTEGER)`
- Base `@DiscriminatorValue("1")`. The process entity replaces that value with the process number

## Capture contract

These rules live in the base. Field rows still come from the process JSP and JS.

- Wizard capture pages call stripes from `s:field-metadata`. Example: `legacy/vuc_mtto_commons/base-jms-ibm/WebContent/WEB-INF/support/gobmx/patterns/registroSolicitud/capturar.jsp` invokes `$.fn.stripesValidation`.
- `legacy/vuc_mtto_commons/base-jms-ibm/WebContent/js/gobmx/jquery/validation/stripes.jquery.validation.js` adds class `required` when field metadata says `required`, and sets `maxlength` from metadata. A mask becomes a jquery method whose message is `El valor capturado no es válido.`
- `class="required"` on the element is what that validator enforces. A label asterisk (`span` with class `require`) is not, by itself, a validator.
- Both jquery builds in this tree default `ignore` to `[]` (validate hidden fields too): `WebContent/js/gobmx/jquery/validation/jquery.validate.min.js` and `WebContent/js/jquery/validation/jquery.validate.min.js`. A process script that switches tabs before Guardar is process behavior. Record it in the process knowledge base. Do not treat a script comment about `ignore: ":hidden"` as this platform default.
- `stk-web:catalog` with `parentSelectName` loads a child catalog from the parent select. Example: `WebContent/WEB-INF/jsp/gobmx/common/solicitud/personaRecibirNotificaciones.jsp`. When a process page sets `parentSelectName`, the process knowledge base records the parent field. Independent lists are a process delta.

## What stays out of each process file

Do not copy into `.cursor/knowledge/procesos/<number>/legacy.md`:

- This variant list
- Parent method bodies
- The stripes, `required` class, and `parentSelectName` contract

The process file links here, names the pom artifact, and lists each method on the chain as `not overridden` or `overridden — <effect>`. Shared beans appear only when this controller or service calls them.
