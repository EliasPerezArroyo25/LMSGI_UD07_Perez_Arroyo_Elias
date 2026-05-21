# Explotación Tecnológica en Odoo 

Versión 1.0  
21/05/2026  
WillmanTech S.L.

[INTRODUCCIÓN](#introducción)

[Arquitectura](#arquitectura)

[1.Guia de Instalación](#1.guia-de-instalación)

[2.Seguridad y control de Acceso](#2.seguridad-y-control-de-acceso)

[3.Procedimiento de Backup y Restauración](#3.procedimiento-de-backup-y-restauración)

[4.Flujo Operativo de Facturación e Informes](#4.flujo-operativo-de-facturación-e-informes)

[Fase 1: Extracción y Enlazado de Datos (Motor QWeb)](#fase-1:-extracción-y-enlazado-de-datos-\(motor-qweb\))

[Fase 2: El Motor de Renderizado Gráfico (wkhtmltopdf)](#fase-2:-el-motor-de-renderizado-gráfico-\(wkhtmltopdf\))

[Fase 3: Salida Binaria y Almacenamiento](#fase-3:-salida-binaria-y-almacenamiento)

[Apéndices](#apéndices)

[Trabajo desarrollado con IA](#trabajo-desarrollado-con-ia)

# INTRODUCCIÓN {#introducción}

La empresa andaluza de servicios tecnológicos **"WillmanTech S.L."** acaba de finalizar la implantación de su infraestructura ERP/CRM para optimizar sus flujos de ventas y facturación en Odoo y busca la personalización del diseño de sus facturas de clientes y la habilitación de un pipeline de interoperabilidad y exportación.

## Arquitectura {#arquitectura}

En este caso se está utilizando la plataforma Docker con el despliegue mediante Docker Compose de los módulos de Odoo y base de datos de Postgres. 

En Odoo se han activado los módulos:

* Ventas  
* Facturación  
* Inventario

# 1.Guia de Instalación {#1.guia-de-instalación}

# 2.Seguridad y control de Acceso {#2.seguridad-y-control-de-acceso}

En este momento el sistema cuenta con estos roles cada uno con su contraseña:

**Comercial:**

* **Permiso:** Puede crear, modificar y ver **sólo sus propias cotizaciones y pedidos**. No puede ver las ventas realizadas por sus compañeros. 

**Contable :**

* **Permiso:** Puede ver y gestionar las cotizaciones de todo su equipo o departamento, además de crear las suyas propias. 

**Administrador:**

* **Permiso:** Tiene acceso total. Puede ver todos los registros, aprobar descuentos especiales, gestionar tarifas, y modificar la configuración principal del módulo (como los ajustes de cotizaciones). 


# 3.Procedimiento de Backup y Restauración {#3.procedimiento-de-backup-y-restauración}

Para hacer un respaldo de la base de datos de Postgres optando por usar comando se usa el comando *pg\_dump* con esta estructura:   
***sudo \-u postgres pg\_dump NOMBRE\_BD \> /ruta/de/respaldo/NOMBRE\_BD.sql***

Para restaurar la base de datos se usa el comando *createdb* con esta estructura:  
sudo su \- postgres  
createdb \-O tu\_usuario\_odoo nombre\_nueva\_bd  
psql nombre\_nueva\_bd \< /ruta/al/archivo/dump.sql

(También se dispone de estas funciones desde la Interfaz de Odoo)

# 4.Flujo Operativo de Facturación e Informes {#4.flujo-operativo-de-facturación-e-informes}

Con un presupuesto y venta hecha , volviendo a **Ventas** podremos confirmar la entrega seleccionando **crear factura**.Tras esto, se selecciona la opción de **Factura normal** y con los datos se podrá **confirmar** la factura. El sistema de generación es el siguiente:

## Fase 1: Extracción y Enlazado de Datos (Motor QWeb) {#fase-1:-extracción-y-enlazado-de-datos-(motor-qweb)}

El motor de plantillas nativo de Odoo, QWeb, toma como entrada los registros puros de la base de datos. QWeb procesa el archivo XML de diseño (report\_invoice\_willmantech) aplicando las directivas lógicas especificadas:

* t-foreach: Recorre iterativamente cada registro hijo de las líneas de detalle (doc.invoice\_line\_ids).  
* t-if: Evalúa en tiempo de compilación si existen descuentos globales en el documento para decidir si renderiza las etiquetas estructurales de las celdas \<td\> e \<th\> correspondientes a descuentos.  
* t-field: Realiza el enlazado dinámico de datos (Data Binding), sustituyendo las variables en código por los textos y valores monetarios reales de la factura.


El resultado de esta primera fase es un documento de texto plano.

## Fase 2: El Motor de Renderizado Gráfico (wkhtmltopdf) {#fase-2:-el-motor-de-renderizado-gráfico-(wkhtmltopdf)}

El servidor de aplicaciones Odoo transfiere el código HTML completo generado por QWeb a un subproceso del sistema que invoca al componente binario ejecutable wkhtmltopdf.

1. wkhtmltopdf inicializa una instancia sin interfaz gráfica (headless) basada en el motor de renderizado WebKit.  
2. El motor WebKit abre virtualmente el código HTML y procesa los estilos CSS, las fuentes tipográficas y las tablas, calculando la disposición exacta de los píxeles y las rupturas de página físicas necesarias para el formato de impresión de hojas A4.

## Fase 3: Salida Binaria y Almacenamiento {#fase-3:-salida-binaria-y-almacenamiento}

Una vez que el motor WebKit ha dibujado el documento en la memoria virtual, wkhtmltopdf vectoriza los textos, imágenes y tablas, exportándolos en un flujo de datos binario estructurado bajo las especificaciones del formato PDF (Portable Document Format).  
Este archivo final es capturado por Odoo, almacenado temporalmente en el caché del filestore y enviado de vuelta al navegador web del usuario, quien visualiza de manera inmediata el informe listo para su descarga, impresión física o remisión automatizada por correo electrónico al cliente de WillmanTech S.L.

# Apéndices {#apéndices}

## Trabajo desarrollado con IA {#trabajo-desarrollado-con-ia}

* PROMPT:Configuración de roles en Odoo ejemplo  
* PROMPT: Comando para respaldar y restaurar la base de datos relacional y los almacenes de datos asociados en odoo  
* PROMPT: Explica el Flujo Operativo de Facturación e Informes en Odoo y cómo un usuario genera una factura en la interfaz y cómo el sistema renderiza el informe final a PDF.

