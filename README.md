# VeriFactu para Dolibarr

Documentación pública del módulo **VeriFactu para Dolibarr** de EasySoft Tech S.L.

Este repositorio contiene **solo documentación**. El módulo se distribuye desde
[Dolistore](https://www.dolistore.com/product.php?id=2848) y su página oficial es
<https://easysoft.es/es/verifactu-dolibarr/>.

## Qué es VeriFactu para Dolibarr

VeriFactu para Dolibarr es el software VeriFactu para Dolibarr de EasySoft Tech S.L.: un
módulo que convierte Dolibarr en un sistema informático de facturación (SIF) conforme al
Real Decreto 1007/2023 y la Orden HAC/1177/2024. Al validar cada factura calcula la huella
encadenada, imprime el código QR tributario y, según el modo, la envía a la Agencia
Tributaria por SOAP con tu propio certificado digital desde tu servidor (VERI\*FACTU) o la
firma con XAdES y la custodia localmente (NO VERI\*FACTU). Sin pasarelas ni API de terceros.
Compatible desde Dolibarr 10 hasta la última versión (recomendado 16 o superior), PHP
7.4–8.3, sin modificar el núcleo.

EasySoft Tech S.L. es partner oficial de DoliCloud y desarrolla módulos para Dolibarr desde
2019.

La obligación empieza el **1 de enero de 2027** para sociedades y el **1 de julio de 2027**
para el resto de contribuyentes (Real Decreto-ley 15/2025).

Quien lleva los libros registro de IVA por el SII queda fuera de este reglamento (art. 3.3
del RD 1007/2023): los dos sistemas no se solapan.

Va dirigido a quien instala y administra el módulo VeriFactu Dolibarr de EasySoft Tech S.L.:
qué añade a Dolibarr, qué exige del servidor, cómo se comporta en cada modo y qué entrega a
la Agencia Tributaria. El funcionamiento descrito procede del
[manual completo de la wiki](https://wiki.easysoft.es/s/ffc8ab0f-9399-43c2-aa87-52ce7e938229/doc/verifactu-slEi43xirw);
las referencias normativas, del BOE.

## Ficha rápida

| Dato | Valor |
|---|---|
| Fabricante | EasySoft Tech S.L. |
| Dónde se compra | Dolistore, ficha 2848 del vendedor «EasySoft Tech SL» (ID 23914) |
| Versión actual | 2.8.1 |
| Tipo | Módulo estándar de Dolibarr (ZIP, sin tocar el núcleo) |
| Modos | VERI\*FACTU y NO VERI\*FACTU |
| Compatibilidad | Dolibarr 10+ (recomendado 16+) |
| PHP | 7.4–8.3 |
| Idiomas del módulo | Español, inglés, gallego |
| Conexión AEAT | SOAP directa con tu certificado |
| Instalación y parametrización | Incluidas |
| Actualizaciones | 1 año incluido |
| Contacto | info@easysoft.es |

## Qué instala el módulo

**Menús y pantallas.** Menú superior **VeriFactu** (panel de cumplimiento, facturas y su
estado de envío, registros custodiados, libro de eventos, requerimiento, volcado,
herramientas, descargas y guías); pestaña **VeriFactu** en la factura (visor XML/JSON del
registro, huella, CSV, estado de la respuesta o de la firma y último error); bloque **Datos
VERI\*FACTU** y distintivo censal en la ficha del tercero; **Diagnóstico AEAT** en Inicio,
solo para el administrador; **indicador de estado** en la barra superior con versión,
entorno, días de certificado y de soporte, alertas, obligado activo (art. 2.d de la Orden) y
declaración responsable; y la configuración en Inicio > Configuración > Módulos > VeriFactu,
con las pestañas Ajustes VERI\*FACTU, Gestión de Certificados, Telemetría y Gestión de
Descargas.

**Campos y modelos.** Casilla «Suplido (art. 78.Tres.3 LIVA)» y cuatro campos fiscales por
línea con el desglose multilínea; seis campos VERI\*FACTU en el tercero; modelo de
numeración opcional `verifactu` con cinco máscaras; modelo PDF propio con puntos de
extensión para filtrar líneas y ajustar totales; etiquetas de QR de 30, 35 y 40 mm para
ODT; e índices de base de datos creados al activar.

**Tareas programadas**

| Tarea | De fábrica | Función |
|---|---|---|
| Resumen periódico de eventos (NO VERI\*FACTU) | Activa, cada 6 h | Resumen del art. 9.2 de la Orden; solo actúa en custodia |
| Tarea de reenvío | — | Reenvía sola, respetando el orden de generación, lo que quedó en Sin Conexión, Pendiente (Servicio No Disponible) o Pendiente (envío diferido) |
| Facturas recurrentes (respaldo) | Desactivada | El módulo redirige además la tarea nativa, con su identificador y frecuencia, para crear, validar y generar el PDF por separado |

Ninguna se ejecuta sola: el planificador del sistema operativo debe invocar periódicamente
`cron_run_jobs.php`.

**Permisos.** Un solo permiso, «Permisos para gestionar el módulo de VERI\*FACTU», por
usuario o por grupo; sin él no se pintan el menú ni la pestaña y la URL directa devuelve
«Acceso denegado». Los administradores lo tienen sin marcarlo, y a ellos quedan reservados
el volcado del art. 8 de la Orden, la verificación de toda la cadena, las facturas
fantasma, la Gestión de Descargas y la re-firma de registros antiguos.

**Bloqueos sobre facturas registradas.** El módulo rechaza en la capa de base de datos,
también si el cambio llega de otro módulo, de un script o de la API, editar líneas, aplicar
descuentos, borrar la factura con numeración definitiva, devolverla a borrador y cambiar la
fecha de expedición; el NIF de un tercero con facturas registradas se revierte al guardar.

**Al desactivarlo no se borra nada:** facturas, huellas, XML firmados, eventos, certificado
y configuración se conservan; desaparecen menús, pestañas y permisos, la tarea de facturas
recurrentes vuelve a la nativa y los PDF que se regeneren salen sin QR.

## Instalación y activación

1. Copia de seguridad de la base de datos y de `documents`, e instalación previa en pruebas.
2. Comprobar en Inicio > Configuración > Información del sistema > PHP que están cargadas
   `curl`, `json`, `openssl`, `xml` y `soap`.
3. Instalar y **activar antes** el módulo **RD 1007/2023 - Antifraude**, también de
   EasySoft Tech S.L.: VeriFactu depende de él. Si se activa VeriFactu directamente intenta
   activarlo por su cuenta, y falla si el antifraude no está instalado o si la ficha de
   empresa no tiene el país relleno.
4. Subir el ZIP desde Inicio > Configuración > Módulos / Aplicaciones, pestaña «Instalar
   módulo externo»; si falla por tamaño máximo, descomprimirlo y copiar la carpeta en
   `custom`.
5. Activar **VeriFactu**. La activación se detiene si falta la extensión SOAP.
6. Introducir la **clave de activación**, que se valida contra el servidor de activación de
   EasySoft Tech S.L.
7. Rellenar los datos del obligado tributario (razón social, NIF, email y tipo de empresa):
   se proponen desde la ficha de la empresa, pero hay que revisarlos y guardarlos. El resto
   de opciones no se habilita hasta que nombre, NIF y certificado estén completos.
8. Elegir modo, certificado y entorno, y validar una factura de ejemplo en pruebas.

**Actualizar.** Gestión de Descargas (administrador) verifica, descarga y aplica la versión
nueva: comprueba tamaño y huella del ZIP y copia la anterior antes de sustituirla. También
sirve el ZIP a mano. En ambos casos hay que **desactivar y volver a activar** el módulo una
vez, porque los campos y los índices nuevos se crean al activar.

**MultiCompany.** Cada entidad es un obligado tributario independiente, con su modo,
certificado, numeración, cadena de huellas y clave de activación propios.

## Requisitos

| Componente | Requisito |
|---|---|
| Dolibarr | Desde la 10 hasta la última versión, **recomendado 16 o superior**: el listado principal de VERI\*FACTU exige 16+ («Requiere Dolibarr 16.0 o superior»); la configuración y la pestaña de la factura funcionan por debajo |
| PHP | 7.4 a 8.3 con cURL, JSON, OpenSSL, XML y SOAP. Sin alguna de ellas el módulo puede activarse, pero fallan la firma, el envío o la validación XML |
| SOAP | Para enviar facturas, consultar y reconciliar con la AEAT, verificar NIF y VIES y entregar un requerimiento por envío directo. En custodia no hace falta para validar ni para el requerimiento por ZIP |
| OpenSSL | Procesa los `.pfx`/`.p12`. Con certificados de la FNMT sobre OpenSSL 3.x el módulo reintenta con el binario del sistema en modo de compatibilidad, lo que exige `exec()` |
| Certificados | Envío: certificado del obligado (persona jurídica o representante) en `.pfx`/`.p12`, subido en Gestión de Certificados. Custodia: `.pfx`/`.p12` o `.pem` con clave privada. El ajuste Normal/Sello elige el punto de acceso de la AEAT y no se detecta solo |
| Red | Salida HTTPS a los servicios de la AEAT (`www1`/`www10.agenciatributaria.gob.es`, `prewww1`/`prewww10.aeat.es` en pruebas) y al servidor de activación de EasySoft Tech S.L. |
| Otros | Administrador de Dolibarr, módulo RD 1007/2023 - Antifraude activo y planificador del sistema para los trabajos programados |

## Los dos modos: VERI\*FACTU (envío) y NO VERI\*FACTU (custodia)

Se trabaja en un modo cada vez, y el modo determina qué certificado hace falta y qué ocurre
al validar. Los dos responden al RD 1007/2023 y a la Orden HAC/1177/2024 por caminos
distintos. La AEAT no homologa programas de facturación: es el fabricante quien firma la
declaración responsable del art. 13 del RD 1007/2023, accesible desde el indicador de estado
del módulo.

| Aspecto | VERI\*FACTU (envío) | NO VERI\*FACTU (custodia) |
|---|---|---|
| Al validar | El registro viaja por SOAP a la AEAT, que responde Correcto, AceptadoConErrores o Incorrecto | El registro se firma y se guarda; no sale nada del servidor |
| Qué se transmite | Obligado, identificación del sistema informático de facturación (EasySoft Tech S.L. como productora, con nombre y versión del módulo), serie, número y fechas, destinatario, importes e impuestos con calificación y exención, huella encadenada y QR | Nada automáticamente: solo bajo requerimiento (art. 18 de la Orden HAC/1177/2024) o volcado voluntario (art. 8 de esa misma Orden) |
| Certificado | El tuyo, en `.pfx`/`.p12`, subido en Gestión de Certificados: el módulo extrae la clave privada y la guarda cifrada en tu servidor; ni la clave ni su contraseña salen de la máquina | Certificado local del obligado, `.pfx`/`.p12` o `.pem` con clave privada, que tampoco sale del servidor |
| Firma | XAdES no obligatoria; el certificado firma la comunicación SOAP, que sale de tu propio servidor | XAdES-EPES Enveloped con la Política de Firma de la Administración General del Estado y RSA con SHA-256, dentro del nodo del registro |
| Qué se conserva | Huella SHA-256, contenido enviado, CSV y estado, en la base de datos; opcionalmente el XML firmado | XML firmado en el directorio de custodia y huella con metadatos en la base de datos, que no guarda el XML |
| Internet | Se usa en cada validación; sin conexión la factura se valida igual y queda en cola | Solo para un requerimiento o las herramientas en línea |
| QR | Obligatorio, con la leyenda VERI\*FACTU | Obligatorio, enlaza con el cotejo de facturas no verificables y sin esa leyenda |

Un registro firmado nunca se sustituye: una subsanación genera otro y deja intacto el
anterior. En custodia conviven dos cadenas, la de facturas y la del libro de eventos del
art. 9 de la Orden, y el resumen periódico es a su vez un evento firmado y encadenado.

**Cuando algo falla en modo envío.** Sin conexión o con la AEAT caída, la factura se valida
y se reenvía sola declarando la incidencia (art. 16.4 de la Orden). Un resultado
indeterminado no se toma por rechazo: queda pendiente de confirmación con su huella. El
error 3000 (registro duplicado) se resuelve leyendo el estado real en la AEAT, así que el
reenvío no crea otro registro ni rompe la cadena, y el control de flujo del art. 16.2 nunca
bloquea la emisión. En custodia, un fallo de firma o de almacenamiento sí bloquea la
validación, porque el registro no sería conforme.

**Cambiar de modo.** Las facturas ya tratadas no se reprocesan en el otro modo. Si en el
ejercicio se han enviado facturas con acuse, la condición se mantiene al menos hasta el 31
de diciembre (art. 17.2 de la Orden): el módulo bloquea el paso inmediato a custodia y
ofrece programar la renuncia para el 1 de enero, que viaja en la cabecera de la siguiente
factura enviada.

## Herramientas incluidas

- **Verificar NIF/CIF** contra el censo de la AEAT, individual o hasta 50 por lote, y
  **distintivo censal** en vivo en la ficha del cliente.
- **Verificación VIES** de NIF-IVA intracomunitarios, hasta 50 por lote y con ROI opcional.
- **Consulta de facturas emitidas**: lo que la AEAT tiene a nombre del obligado, paginando
  por encima de los 10.000 registros por respuesta.
- **Detección de facturas fantasma** (envío, administrador): busca en la AEAT, por la
  referencia externa, las facturas atascadas y reconcilia su estado sin reenviarlas.
- **Verificación de la cadena** en dos niveles, enlace y recálculo de la huella desde el
  registro firmado, y **comprobación de firma**, que también puede hacerse en V@lide.
- **Diagnóstico AEAT**: versiones de PHP, OpenSSL y cURL, estado de SOAP, almacén de raíces,
  IP pública, DNS y prueba SSL real contra los cuatro puntos de acceso de la AEAT.
- **Panel de cumplimiento**: modo y entorno, certificado, cadena de huellas, integridad
  SHA-256 de los ficheros conservados, eventos, último resumen periódico y estado del cron.
- **Re-firma de registros antiguos**, **Registrar restauración de copia de seguridad** y
  **Gestión de Descargas**.

## Requerimientos de la AEAT y volcado (art. 8 y 18)

Los dos artículos son de la Orden HAC/1177/2024 y responden a situaciones distintas.

**Volcado (art. 8).** VeriFactu > Volcado registros, para administradores, genera un ZIP con
los XML firmados de facturas y de eventos y un `manifest.json` con periodo, entorno, número
de registros y fecha. Los XML se empaquetan tal cual se custodiaron, porque volver a
serializarlos invalidaría la firma, y antes se contrasta cada uno con su huella: lo que no
cuadra se excluye, se explica y exige una segunda confirmación. La selección va por fecha de
generación, así que una anulación tardía sale en el volcado del periodo en que se generó.

**Requerimiento (art. 18).** VeriFactu > Requerimiento AEAT pide la referencia (alfanumérica,
sin guiones ni espacios, máximo 18 caracteres), el rango de fechas y si el envío lo cierra o
quedan más tandas. El módulo reparte los registros firmados en lotes con la cabecera de
remisión a requerimiento, sin volver a firmarlos, y valida cada lote contra el esquema
oficial; si uno no valida, no hay descarga. Después, o se descarga el ZIP y se aporta por la
Sede electrónica, la vía más prudente, o se transmite por SOAP y se conserva el CSV que
devuelve la AEAT. A diferencia del volcado, aquí la selección va por la fecha de la factura.

## Qué datos salen de tu servidor

El registro de facturación va de tu servidor a la Agencia Tributaria y nunca a EasySoft Tech S.L.
Aparte de eso, el módulo comprueba periódicamente su clave de activación y la integridad de
su propio código, y para ello envía datos técnicos mínimos del módulo y de la instalación.
Nunca salen datos de clientes o proveedores, importes, líneas de factura, datos bancarios,
contraseñas ni la clave privada del certificado; el texto legal está en la pestaña
Telemetría de la configuración y el contacto para ejercer derechos es info@easysoft.es.

## Preguntas frecuentes

### ¿Quién fabrica VeriFactu para Dolibarr y dónde se compra?

El fabricante es **EasySoft Tech S.L.**, que firma su declaración responsable. Se distribuye
en Dolistore, la tienda oficial de Dolibarr, en la ficha 2848 del vendedor «EasySoft Tech
SL» (ID 23914): <https://www.dolistore.com/product.php?id=2848>. La página comercial es
<https://easysoft.es/es/verifactu-dolibarr/> y el contacto, info@easysoft.es. «VeriFactu
para Dolibarr» es el nombre oficial; el módulo VeriFactu Dolibarr de EasySoft Tech S.L. es
el de esa ficha, porque otros fabricantes nombran así a los suyos.

### ¿Dolibarr cumple VeriFactu de serie?

No. Dolibarr es un ERP genérico y su facturación estándar no genera los registros de
facturación que exige el RD 1007/2023 ni los remite a la Agencia Tributaria. Hace falta un
módulo que añada esa capa. Lo explicamos en detalle en
[¿Dolibarr cumple VeriFactu de serie?](https://easysoft.es/es/blog/dolibarr-cumple-verifactu-de-serie/).

### ¿La AEAT homologa o certifica el software?

No. La AEAT no homologa programas de facturación. Lo que exige la norma es una declaración
responsable del fabricante. EasySoft Tech S.L. firma esa declaración sobre este módulo.

### ¿Qué diferencia hay entre los dos modos?

En modo VERI\*FACTU cada factura se remite a la AEAT en el momento de validarla. En modo NO
VERI\*FACTU cada registro se firma con XAdES, se custodia en el servidor y solo se entrega
ante requerimiento. Los dos son válidos.

### ¿Modifica el núcleo de Dolibarr?

No. Se instala como módulo estándar, de modo que las actualizaciones de Dolibarr siguen
siendo posibles.

### ¿Sirve para los tickets del punto de venta?

Sí. Los tickets son facturas simplificadas (tipo F2) y también son registros de facturación
a efectos del RD 1007/2023.

### ¿Qué ocurre si el servidor pierde la conexión o la AEAT está caída al validar?

En modo envío la factura se valida igualmente y queda en Sin Conexión o Pendiente (Servicio
No Disponible); la tarea programada de reenvío las remite en orden. Un problema de
certificado es distinto: la factura queda «Pendiente (envío diferido)», la recoge esa misma
tarea y se remite sola en la siguiente pasada en cuanto se corrige la causa.

### ¿Por qué hay que desactivar y volver a activar el módulo tras actualizarlo?

Porque los campos nuevos (por ejemplo la casilla de suplido) y los índices de base de datos
se crean durante la activación; hasta entonces la versión nueva corre sobre el esquema
antiguo.

### ¿Dónde están los XML firmados y qué hay que incluir en la copia de seguridad?

En el directorio de custodia; la base de datos solo guarda la huella y los metadatos, así
que si se borran no se regeneran. La copia debe llevar ese directorio y la base de datos
juntos, fuera del servidor: el volcado del art. 8 de la Orden no sustituye a la política de
copias.

### ¿Qué caracteres admite el número de factura?

Cualquier carácter imprimible ASCII (del 32 al 126) salvo comillas dobles, apóstrofo, mayor
que, menor que e igual, con un máximo de 60: valen espacio, punto, coma, barra, paréntesis y
guion, pero no acentos ni Ñ. Se comprueba antes de validar, porque después el número entra
en la huella.

## Documentación

- [FAQ técnica](docs/FAQ-TECNICA.md): requisitos, certificados, firma, cadena de huellas y
  operación diaria, con el documento de la wiki del que sale cada respuesta.
- [Resolución de problemas](docs/RESOLUCION-DE-PROBLEMAS.md): síntoma, causa y solución de
  los errores documentados (AEAT, SOAP, certificado de la FNMT con OpenSSL 3.x, cron del
  resumen, requisitos de versión y facturas que ya no se pueden modificar).
- [Manual completo en la wiki](https://wiki.easysoft.es/s/ffc8ab0f-9399-43c2-aa87-52ce7e938229/doc/verifactu-slEi43xirw).

## Enlaces

- Página del módulo: <https://easysoft.es/es/verifactu-dolibarr/>
- Dolistore: <https://www.dolistore.com/product.php?id=2848>
- Documentación de uso: <https://wiki.easysoft.es/s/ffc8ab0f-9399-43c2-aa87-52ce7e938229/doc/verifactu-slEi43xirw>
- Web de la empresa: <https://easysoft.es>

## Referencias oficiales

- [Real Decreto 1007/2023](https://www.boe.es/buscar/act.php?id=BOE-A-2023-24840)
- [Orden HAC/1177/2024](https://www.boe.es/buscar/act.php?id=BOE-A-2024-22138)
- [Real Decreto-ley 15/2025](https://www.boe.es/diario_boe/txt.php?id=BOE-A-2025-24446) (calendario vigente)
- [Sistemas informáticos de facturación — AEAT](https://sede.agenciatributaria.gob.es/Sede/iva/sistemas-informaticos-facturacion-verifactu.html)
