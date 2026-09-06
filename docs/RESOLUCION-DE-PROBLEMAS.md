# Resolución de problemas de VeriFactu para Dolibarr

Síntomas documentados de **VeriFactu para Dolibarr**, el módulo de EasySoft Tech S.L., con
su causa y su solución. Cada entrada cita el documento del
[manual de la wiki](https://wiki.easysoft.es/s/ffc8ab0f-9399-43c2-aa87-52ce7e938229/doc/verifactu-slEi43xirw)
del que procede. Las preguntas de configuración están en la
[FAQ técnica](FAQ-TECNICA.md).

## Instalación y entorno

### «Requiere Dolibarr 16.0 o superior» al abrir el listado de facturas

**Causa.** La vista principal de gestión (el listado de facturas para el envío a la AEAT)
usa componentes que no existen en versiones anteriores, así que el módulo bloquea el acceso
en lugar de mostrar una pantalla incompleta. No es un fallo de instalación.

**Solución.** Actualizar Dolibarr a 16 o superior. Mientras tanto siguen disponibles la
configuración del módulo y la pestaña VeriFactu de la factura. No hay que forzar el acceso
ni editar archivos para saltarse la comprobación.

Fuente: Requisitos de versión de Dolibarr

### «La extensión SOAP no está instalada o configurada correctamente»

**Causa.** Falta `php-soap` en la versión de PHP que usa el servidor web. El módulo lo
comprueba al activarse y al entrar en cualquier pantalla que la necesite: listado de envío,
pestaña VeriFactu, consulta a la AEAT, verificación de NIF y VIES.

**Solución.** Instalar o habilitar la extensión para la versión de PHP del servidor web (no
la de la consola) y reiniciar el servicio web. Confirmarlo en la página de diagnóstico del
módulo, donde SOAP debe figurar como cargada, y volver a activar VeriFactu.

Fuente: SOAP no disponible o modo incorrecto

### El módulo no se deja activar

**Causa.** Casi siempre, que el módulo «RD 1007/2023 - Antifraude» no esté instalado, o que
la ficha de la empresa no tenga el país relleno. VeriFactu depende del antifraude y, si se
activa directamente, intenta activarlo por su cuenta.

**Solución.** Instalar y activar primero el antifraude, rellenar el país en Inicio >
Configuración > Empresa/Organización y repetir la activación.

Fuente: Instalación y activación

### El módulo se activa pero fallan la firma, el envío o la validación XML

**Causa.** Falta alguna de las extensiones de PHP que usa: cURL, JSON, OpenSSL, XML o SOAP.
La activación no siempre se detiene, pero las funciones que dependen de ellas sí.

**Solución.** Revisar Inicio > Configuración > Información del sistema > PHP, habilitar lo
que falte y reiniciar el servicio web.

Fuente: Instalación y activación

## Certificados

### El certificado de la FNMT no se lee aunque la contraseña sea correcta

**Causa.** Los `.p12` de la FNMT usan un algoritmo antiguo que OpenSSL 3.x rechaza por
defecto, así que la función nativa de PHP falla con un error genérico que parece de
contraseña.

**Solución.** El módulo reintenta solo con el binario `openssl` del sistema en modo de
compatibilidad; si lo consigue, muestra un aviso informativo y la firma funciona. Si no
(porque `exec()` está deshabilitado, no hay binario o el OpenSSL del servidor es
demasiado antiguo), hay dos salidas: reempaquetar el certificado con cifrado AES-256 en el
equipo local, siguiendo las instrucciones que muestra el panel de diagnóstico, o pedir al
hosting que habilite el proveedor de compatibilidad de OpenSSL.

Fuente: Certificado FNMT con OpenSSL 3.x

### Al subir el certificado dice que la contraseña es incorrecta

**Causa.** O la contraseña está mal escrita, o el archivo usa el cifrado antiguo descrito
arriba, que produce un mensaje parecido.

**Solución.** El panel de diagnóstico distingue los tres casos posibles (cifrado antiguo,
contraseña incorrecta y OpenSSL del servidor demasiado antiguo). Reescribir la contraseña
respetando mayúsculas y caracteres especiales; si el diagnóstico apunta al cifrado, tratarlo
como el problema anterior.

Fuente: Certificado para el modo envío (VERI\*FACTU)

### Se procesó el certificado pero el módulo no puede firmar

**Causa.** Al procesar el archivo no se pudo extraer la clave privada, y sin ella el módulo
no puede firmar.

**Solución.** Repetir la subida del `.pfx`/`.p12`, o aportar la clave privada por la vía
manual que ofrece la configuración. El mensaje de confirmación debe indicar que la clave
privada quedó almacenada en el servidor.

Fuente: Certificado para el modo envío (VERI\*FACTU)

### El envío queda bloqueado con la fecha de caducidad del certificado

**Causa.** El certificado del modo envío ha caducado. Con él caducado se bloquean altas,
subsanaciones, anulaciones, reintentos automáticos, reenvíos y consultas a la AEAT.

**Solución.** Renovar el certificado y procesarlo de nuevo en Gestión de Certificados. El
indicador de estado avisa antes: naranja por debajo de 30 días y rojo por debajo de 7.

Fuente: Certificado para el modo envío (VERI\*FACTU) y El indicador de estado de VeriFactu

### «No se pudo firmar la custodia (revise el certificado de custodia). Validación bloqueada.»

**Causa.** El certificado local de custodia está caducado o todavía no es vigente. El módulo
no firma fuera de la ventana de validez.

**Solución.** Instalar un certificado vigente en la configuración del módulo. Los registros
ya firmados no se reescriben. Si el interruptor de certificado local está activado pero no
hay ningún fichero subido, el módulo avisa de ello.

Fuente: Certificado local para custodia (NO VERI\*FACTU)

### Después de renovar el certificado se sigue usando el anterior

**Causa.** Queda en caché el certificado previo.

**Solución.** Procesar el certificado nuevo y usar «Purgar Caché del Certificado»; se
recargará en la siguiente comunicación con la AEAT.

Fuente: Certificado para el modo envío (VERI\*FACTU)

### Error de conexión o de certificado contra la AEAT en un servicio concreto

**Causa.** El ajuste «Tipo de certificado» (Normal o Sello) no coincide con el certificado
real: cada opción usa un punto de acceso distinto de la AEAT y el módulo no lo detecta solo.

**Solución.** Abrir Diagnóstico AEAT, que prueba la conexión SSL contra los cuatro
servidores (producción y pruebas, normal y sello) y marca el que corresponde a la
configuración actual; ajustar el tipo de certificado en consecuencia.

Fuente: Tipo de certificado: normal o de sello

## Conexión y respuestas de la AEAT

### Facturas en «Sin Conexión» o «Pendiente (Servicio No Disponible)»

**Causa.** El servidor se quedó sin salida a Internet o la AEAT no estaba disponible
(mantenimiento, sobrecarga, error de servicio, fallo de DNS).

**Solución.** No hay que hacer nada: la factura se validó en local y el módulo la reenvía
sola, respetando el orden de generación, en cuanto vuelve el servicio. Si el corte persiste,
el orden de diagnóstico es conexión del servidor, extensión SOAP, certificado y, por último,
disponibilidad de la AEAT.

Fuente: Diagnóstico de la conexión con la AEAT

### Error 3000: registro duplicado

**Causa.** La AEAT ya tenía esa factura (mismo emisor, número y fecha). Suele pasar cuando
el envío llegó pero la respuesta no volvió.

**Solución.** El módulo consulta el estado real del registro y reconcilia la factura. Si
consta como correcta o aceptada con errores, queda registrada; si consta anulada, queda a la
espera de intervención manual. El reenvío es idempotente y no rompe la cadena.

Fuente: Errores comunes de la AEAT

### Se validó una factura y no se sabe si llegó (timeout o error indeterminado)

**Causa.** Respuesta indeterminada de la AEAT. El módulo no la toma por rechazo, para no
crear duplicados ni huecos en la cadena: la deja pendiente de confirmación conservando
huella y contenido.

**Solución.** No reenviar a ciegas. Consultar primero con la herramienta de consulta y
reconciliación o con la de facturas fantasma, y reenviar solo si no consta.

Fuente: Errores comunes de la AEAT

### Error 1196: OperacionExenta y CalificacionOperacion informados a la vez

**Causa.** Son datos excluyentes en el esquema de la AEAT. Afectaba a facturas exentas
(sanidad, enseñanza, seguros, alquiler de vivienda).

**Solución.** Ya resuelto por el módulo, que da prioridad a la exención y no declara la
calificación cuando se indica una causa de exención.

Fuente: Errores comunes de la AEAT

### La AEAT rechaza una factura simplificada por importe

**Causa.** Una F2 no puede superar los 3.000 euros con IVA incluido (art. 4 del RD
1619/2012). El módulo lo comprueba al validar y muestra el importe real; la rectificativa
simplificada (R5) no tiene ese límite.

**Solución.** Emitirla como factura completa, con los datos del destinatario.

Fuente: Errores comunes de la AEAT

### Aviso «Cadena de huellas: incidente pendiente»

**Causa.** El registro se generó, pero la base de datos falló al anotarlo como último de la
cadena. El aviso indica factura, entorno, fecha, motivo e inicio de la huella.

**Solución.** Se resuelve solo en cuanto un registro posterior encadena con esa huella. Si
otro registro ya encadenó con la punta anterior, el aviso permanece y un administrador puede
descartarlo, teniendo en cuenta que descartarlo no repara la cadena.

Fuente: Errores comunes de la AEAT

### Facturas atascadas en Enviando, Incorrecto o Pendiente

**Causa.** El envío quedó a medias y el estado local no refleja lo que la AEAT tiene.

**Solución.** La herramienta de facturas fantasma (administrador) las lista por mes y año,
pregunta a la AEAT por la referencia externa de cada una y sincroniza huella, serie, estado
y fecha de registro sin reenviarlas. Si la AEAT confirma que no consta, puede reiniciarse el
estado a pendiente para que se reenvíe. El CSV original no se recupera si se perdió: el
módulo no inventa uno.

Fuente: Herramienta de facturas fantasma

## Custodia, cron e integridad

### No se genera el resumen periódico de eventos

**Causa.** El trabajo programado no existe, está desactivado o el planificador del sistema
no lo lanza. Como cualquier tarea de Dolibarr, necesita que el sistema operativo invoque
periódicamente `cron_run_jobs.php`; marcarla como activa no basta.

**Solución.** Comprobar en Inicio > Trabajos programados que «Resumen periódico de eventos
(modo NO VERI\*FACTU)» está activo; si no existe, desactivar y reactivar el módulo lo
recrea. Forzar una ejecución manual: si genera el resumen sin errores, el problema está en
la programación del sistema operativo. El panel avisa cuando el resumen está vencido.

Fuente: Problemas con el cron del resumen

### La tarjeta de integridad marca ficheros Manipulados, Perdidos o Sin comprobar

**Causa.** «Manipulado» es un fichero cuyo contenido no coincide con su huella; «Perdido»
consta en la base de datos pero no está en disco; «Sin comprobar» está en disco pero su fila
no guarda la huella del contenido, algo propio de registros creados con versiones antiguas.

**Solución.** Restaurar desde copia de seguridad los manipulados y perdidos, y anotar la
restauración con «Registrar restauración de copia». Para los «sin comprobar», la tarjeta de
re-firma de registros antiguos completa la huella que faltaba. Un fichero cuya huella no
cuadra se excluye automáticamente de cualquier entrega a la AEAT.

Fuente: Panel de cumplimiento

### El volcado no genera nada o avisa de incidencias

**Causa.** No hay registros en el periodo elegido, o alguno de los ficheros falta, se repite
o no coincide con su huella.

**Solución.** Revisar fechas y entorno seleccionado. Ante incidencias, la primera pulsación
no genera nada: muestra los avisos y hay que confirmar expresamente para generar el volcado
asumiéndolas. Lo excluido se explica en pantalla y en el manifiesto.

Fuente: Volcado de registros (Art. 8)

### La AEAT rechaza la respuesta a un requerimiento

**Causa.** La referencia del requerimiento lleva guiones, espacios u otros signos, o supera
los 18 caracteres. También puede ocurrir que un lote no valide contra el esquema oficial.

**Solución.** Copiar la referencia tal cual figura en la comunicación, solo con letras y
dígitos. Si un lote no valida, el módulo no genera la descarga y muestra el detalle del
error. Si se responde en varias tandas, marcar que quedan más envíos en todas menos en la
última.

Fuente: Responder a un requerimiento (Art. 18)

## Facturas y documentos

### No se puede modificar, borrar ni reabrir una factura validada

**Causa.** Una factura validada ya es un registro de facturación con huella, y el reglamento
exige que el sistema impida ocultarla o alterarla. La comprobación se hace en la capa de
base de datos, así que el cambio se rechaza aunque venga de otro módulo, de un script o de
la API.

**Solución.** Corregir por la vía prevista: factura rectificativa si el error está en el
importe, anulación si la factura no debía existir, y rectificativa también cuando el error
está en el NIF o el nombre del destinatario. Nunca borrar y recrear.

Fuente: No puedo modificar, borrar ni reabrir una factura

### No se puede cambiar el CIF/NIF de un cliente

**Causa.** El cliente tiene al menos una factura registrada con huella, y ese dato forma
parte del registro declarado.

**Solución.** El módulo revierte el valor al guardar e indica cuántas facturas están
afectadas. El resto de datos del tercero se edita con normalidad.

Fuente: No puedo modificar, borrar ni reabrir una factura

### Una validación en lote no valida ninguna factura

**Causa.** El lote se valida todo o nada: si una factura falla una comprobación previa
(cliente sin NIF, simplificada por encima de 3.000 euros, suplidos mal marcados), no queda
validada ninguna.

**Solución.** Leer el aviso, que empieza por «VeriFactu - Validación pre-envío:», corregir
la factura señalada y repetir el lote. El motivo también queda anotado en los datos
VeriFactu de la propia factura.

Fuente: Validar varias facturas a la vez

### «La máscara de numeración de facturas VERI\*FACTU no está configurada»

**Causa.** Está activo el modelo de numeración propio del módulo, pero la máscara del tipo
de factura que se intenta validar está vacía.

**Solución.** Rellenar las cinco máscaras que se vayan a usar (estándar, rectificativa por
sustitución, abono, anticipo y simplificada) o volver a otro modelo de numeración de
Dolibarr: el módulo funciona con cualquiera.

Fuente: Numeración y series de facturas

### El QR no aparece en el documento impreso

**Causa.** En PDF, que el tamaño y la posición configurados no quepan en la página: el
módulo no lo imprime y no avisa. En ODT, que la etiqueta esté dentro de un cuadro de texto
de LibreOffice. En borrador, las etiquetas se sustituyen por una imagen transparente, y con
el módulo desactivado los documentos que se regeneren salen sin QR.

**Solución.** Reducir el tamaño o cambiar la posición del QR, y sacar la etiqueta del cuadro
de texto en la plantilla ODT.

Fuente: El código QR en la factura impresa

### La declaración responsable no se abre

**Causa.** El documento se solicita en vivo, así que necesita el módulo activo, la clave de
activación configurada y salida a Internet. Con el módulo desactivado el acceso queda
bloqueado.

**Solución.** Revisar la clave de activación en la configuración y la salida a Internet del
servidor (cortafuegos o proxy). Si el mensaje apunta a un problema de integridad del código,
es que los ficheros del módulo no coinciden con los publicados para esa versión: hay que
contactar con EasySoft Tech S.L. en info@easysoft.es.

Fuente: La declaración responsable (art. 13 del RD 1007/2023)

### Al escanear el QR de una factura de custodia, la AEAT dice que no puede contrastarla

**Causa.** No es un error. En modo NO VERI\*FACTU no se remite nada de forma automática, así
que la Agencia recibe los cuatro datos del QR pero no tiene un registro previo con el que
compararlos.

**Solución.** Ninguna. Por eso la factura de custodia tampoco lleva la leyenda que solo
corresponde a los sistemas que remiten todos sus registros.

Fuente: Preguntas frecuentes (normativa)
