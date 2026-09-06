# FAQ técnica de VeriFactu para Dolibarr

Preguntas de instalación, configuración y operación de **VeriFactu para Dolibarr**, el
módulo de EasySoft Tech S.L. Cada respuesta indica el documento del
[manual de la wiki](https://wiki.easysoft.es/s/ffc8ab0f-9399-43c2-aa87-52ce7e938229/doc/verifactu-slEi43xirw)
del que procede. Para síntomas y mensajes de error concretos, ver
[Resolución de problemas](RESOLUCION-DE-PROBLEMAS.md).

## Requisitos e instalación

### ¿Qué necesita el servidor para instalar el módulo?

Dolibarr desde la 10 (recomendado 16 o superior), PHP con las extensiones cURL, JSON,
OpenSSL, XML y SOAP, permiso de administrador y salida HTTPS a Internet. Las extensiones se
comprueban en Inicio > Configuración > Información del sistema > PHP. Si falta alguna, el
módulo puede llegar a activarse pero fallan funciones clave: firma, envío SOAP y validación
XML.

Fuente: Instalación y activación

### ¿Por qué no se activa el módulo?

Por dos causas habituales: que el módulo «RD 1007/2023 - Antifraude», del que VeriFactu
depende, no esté instalado y activado antes, o que la ficha de empresa no tenga el país
relleno (Inicio > Configuración > Empresa/Organización). Si se activa VeriFactu
directamente, intenta activar el antifraude por su cuenta y se detiene si no lo consigue.

Fuente: Instalación y activación

### ¿Qué versión de Dolibarr hace falta de verdad?

El módulo se instala desde Dolibarr 10, pero el listado principal de VERI\*FACTU y su área
de gestión exigen 16 o superior y en versiones anteriores muestran «Requiere Dolibarr 16.0 o
superior». La configuración y la pestaña VeriFactu de la factura funcionan por debajo, con
matices.

Fuente: Requisitos de versión de Dolibarr

### ¿Para qué se usa SOAP y qué ocurre si falta?

SOAP es el protocolo con el que se habla con los servicios web de la AEAT: envío de facturas
en modo VERI\*FACTU, consulta y reconciliación, verificación de NIF, verificación VIES y
entrega de un requerimiento por envío directo. Sin la extensión, la activación se detiene y
esas pantallas quedan bloqueadas. En custodia, validar una factura no la necesita, ni
tampoco la entrega del requerimiento por ZIP.

Fuente: SOAP no disponible o modo incorrecto

## Certificados

### ¿Qué certificado admite cada modo?

En modo envío se sube el certificado del obligado en formato `.pfx` o `.p12` desde la
pestaña Gestión de Certificados: el módulo extrae la clave privada, la guarda cifrada en el
servidor y muestra titular, emisor, vigencia y huellas. En custodia se admiten además `.pem`
siempre que incluyan certificado y clave privada; un `.pem` con solo la parte pública no
sirve. Sin certificado válido no se puede firmar ni enviar.

Fuente: Certificado local para custodia (NO VERI\*FACTU)

### El certificado de la FNMT no se lee, aunque la contraseña es correcta

Los `.p12` de la FNMT vienen con un algoritmo antiguo que OpenSSL 3.x rechaza por defecto,
de modo que la función nativa de PHP falla con un error genérico que parece de contraseña.
El módulo reintenta solo con el binario `openssl` del sistema en modo de compatibilidad, lo
que exige `exec()` habilitado; si funciona, muestra un aviso informativo, no un error.

Fuente: Certificado FNMT con OpenSSL 3.x

### ¿Elijo «Normal» o «Sello» en el tipo de certificado?

«Normal» (valor de fábrica) para certificados personales o de representante; «Sello» solo
con un certificado de sello electrónico de empresa. El ajuste decide el punto de acceso de
la AEAT que se usa, también al responder a un requerimiento por SOAP, y el módulo no lo
detecta por sí mismo. La herramienta Diagnóstico AEAT marca cuál está en uso.

Fuente: Tipo de certificado: normal o de sello

## Registros, huella y fechas

### ¿Cómo se encadena la huella y qué ocurre si dos usuarios validan a la vez?

Cada registro nuevo (alta, subsanación o anulación) encadena con el último generado, aunque
corresponda a una factura más antigua; el módulo mantiene una punta de cadena explícita por
empresa y entorno. Si dos usuarios validan a la vez, los registros se generan de uno en uno
leyendo esa punta con certeza; si en unos segundos no la tiene, la factura queda «Pendiente
(cadena en uso)» y se reintenta sola en vez de encadenar a ciegas.

Fuente: Verificación de la cadena de custodia

### ¿Qué fecha se declara: la de la factura o la del día en que se valida?

Se declara la fecha de expedición (el campo «Fecha facturación» de Dolibarr). La fecha y
hora de generación es un dato distinto, siempre el instante real de la validación con su
huso horario. La cadena de huellas se ordena por el orden de generación de los registros, no
por la fecha de las facturas.

Fuente: La fecha de la factura

### ¿Qué comprobaciones se hacen antes de validar una factura?

Que el cliente tenga NIF/CIF (o el identificador que corresponda si es extranjero), que una
factura simplificada no supere los 3.000 euros con IVA incluido, que los suplidos estén bien
marcados y que la fecha sea admisible: más de un día posterior a hoy o más de veinte años de
antigüedad bloquean la validación. Los avisos empiezan por «VeriFactu - Validación
pre-envío:».

Fuente: Validar varias facturas a la vez

### ¿Subsanación, rectificativa o anulación?

La subsanación corrige datos fiscales de un registro ya enviado sin tocar importes ni número
de factura: tipo de factura, impuesto, clave de régimen, calificación, causa de exención y
fecha de operación. La rectificativa (R1-R5) es una factura nueva para corregir importes. La
anulación cancela el registro de una factura que no debía existir. Las tres se encadenan con
la huella anterior y ninguna borra el registro original.

Fuente: Subsanación: corregir un registro ya enviado

### ¿Cómo se elige el subtipo de rectificativa R1-R5?

En el campo «Tipo de factura» de la pestaña VeriFactu, antes de validar: R1 (error fundado
en derecho y art. 80 Uno, Dos y Seis LIVA), R2 (concurso de acreedores), R3 (créditos
incobrables), R4 (resto de casos) y R5 (facturas simplificadas). Si se deja vacío se declara
R1, y R5 se impone siempre cuando la original es simplificada.

Fuente: Anulaciones y facturas rectificativas

## Impresión y casos de uso

### ¿Por qué el QR lleva un importe distinto del total de la factura?

Porque el QR lleva el importe registrado. Con «Gestión de suplidos» activada, las líneas
marcadas como suplido quedan fuera del registro y del QR (deben ir al 0 % de IVA), mientras
la factura impresa muestra el total completo; la retención de IRPF tampoco se descuenta. Una
vez registrada la factura, el QR queda congelado a ese importe.

Fuente: Suplidos (art. 78.Tres.3.º LIVA)

### ¿Cómo se imprime el QR y con qué plantillas funciona?

El QR se añade al documento ya generado, así que sirve cualquier plantilla PDF activa de
Dolibarr. En la configuración se ajustan tamaño (de 30 a 40 mm), posición horizontal y
vertical, tamaño del texto y si aparece en todas las páginas. El texto «QR tributario:» se
imprime siempre; si el tamaño y la posición no caben en la página, el QR no se imprime y no
hay aviso. En plantillas ODT se inserta con etiquetas de 30, 35 o 40 mm, que no funcionan
dentro de un cuadro de texto de LibreOffice.

Fuente: El código QR en la factura impresa

### ¿Cómo se comporta con TakePOS?

No hay nada que configurar: cada venta se declara como factura simplificada (F2) sin bloque
de destinatario, sus abonos como R5, y el ticket lleva el QR en la cabecera. El módulo
impide validar una venta de TPV de más de 3.000 euros con IVA incluido; los abonos no
tienen ese límite.

Fuente: Punto de venta (TakePOS / TPV)

### ¿Y las facturas recurrentes?

Al activar el módulo, la tarea programada nativa de facturas recurrentes pasa a ejecutar la
versión del módulo, que separa crear, validar y generar el PDF en operaciones distintas para
evitar facturas fantasma; conserva identificador, frecuencia y entidad, y al desactivar el
módulo vuelve al comportamiento nativo. Si una validación falla por datos, la factura queda
en borrador y hay que validarla a mano.

Fuente: Facturas recurrentes

### ¿Cómo se identifica a un cliente extranjero?

Si el país del cliente es España, o no tiene país, se usa el campo CIF/NIF; con cualquier
otro país se usa el campo de identificación extranjera. Si falta el dato que corresponde, la
factura no se valida y queda en borrador con el aviso de validación pre-envío. En la ficha
del tercero puede elegirse el tipo de identificación (NIF-IVA, pasaporte, documento oficial
del país de residencia, certificado de residencia, otro documento probatorio o no censado).

Fuente: Clientes extranjeros

## Operación y control

### ¿Qué permisos hacen falta para usar el módulo?

Un solo permiso, «Permisos para gestionar el módulo de VERI\*FACTU», que se concede por
usuario o por grupo; sin él no se ven el menú ni la pestaña y la URL directa devuelve
«Acceso denegado». Los administradores tienen acceso sin marcarlo. Generar el volcado
completo del art. 8 de la Orden y relanzar la verificación de toda la cadena están
reservados a administradores; descargar el XML firmado de un registro concreto, no.

Fuente: Permisos: quién puede usar VeriFactu

### ¿Cómo se comprueba la firma de un registro custodiado?

Con el botón «Firma» de cada fila del listado de custodia o de la pestaña VeriFactu de la
factura. La comprobación verifica dos cosas: que la firma cuadre con el contenido y que el
NIF del certificado que firmó sea el del obligado del registro. Los resultados posibles son
firma correcta, firma no válida, firmada por otro certificado, no está firmado o no se
encuentra el fichero. El XML también puede validarse en V@lide.

Fuente: Firma XAdES-EPES de los registros

### ¿Cómo se genera el resumen periódico de eventos?

Por dos vías idempotentes: un disparo automático cada vez que se custodia una factura, que
cubre los periodos con actividad, y un trabajo programado cada 6 horas, que cubre además los
periodos sin actividad. Ambos aplican un candado de 6 horas y recuperan las ventanas que
quedaron sin cubrir. Una ventana sin eventos no genera resumen, por diseño, y sin NIF del
obligado no puede generarse ninguno.

Fuente: Resumen periódico de eventos (Art. 9.2)
