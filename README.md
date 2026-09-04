# VeriFactu para Dolibarr

Documentación pública del módulo **VeriFactu para Dolibarr** de EasySoft Tech S.L.

Este repositorio contiene **solo documentación**. El módulo se distribuye desde
[Dolistore](https://www.dolistore.com/product.php?id=2848) y su página oficial es
<https://easysoft.es/es/verifactu-dolibarr/>.

## En breve

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

## Ficha rápida

| | |
|---|---|
| Fabricante | EasySoft Tech S.L. |
| Tipo | Módulo estándar de Dolibarr (ZIP, sin tocar el núcleo) |
| Compatibilidad | Dolibarr 10+ (recomendado 16+) |
| PHP | 7.4–8.3 |
| Modos | VERI\*FACTU y NO VERI\*FACTU |
| Conexión AEAT | SOAP directa con tu certificado |
| Actualizaciones | 1 año incluido |
| Idiomas del módulo | Español, inglés, gallego |
| Instalación y parametrización | Incluidas |
| Versión actual | 2.8.1 |
| Contacto | info@easysoft.es |

## Requisitos

- Dolibarr 10 o superior (recomendado 16 o superior).
- PHP 7.4 a 8.3.
- Certificado digital propio (persona jurídica o representante) en formato PKCS#12.
- Salida HTTPS desde el servidor hacia los servicios de la AEAT.

## Preguntas frecuentes

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
VERI\*FACTU los registros se firman con XAdES y se custodian localmente, y se entregan solo
ante requerimiento. Los dos son válidos.

### ¿Modifica el núcleo de Dolibarr?

No. Se instala como módulo estándar, de modo que las actualizaciones de Dolibarr siguen
siendo posibles.

### ¿Sirve para los tickets del punto de venta?

Sí. Los tickets son facturas simplificadas (tipo F2) y también son registros de facturación
a efectos del RD 1007/2023.

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
