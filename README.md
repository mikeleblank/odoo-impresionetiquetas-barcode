# 🏷️ Etiquetas 50×25 mm (doble) en **Odoo Online 19+ (Enterprise)** con **TSC TE200** (PDF/QWeb)

Esta guía documenta **de principio a fin** cómo dejar funcionando la impresión de etiquetas térmicas desde **Odoo Online** usando un **reporte PDF (QWeb)** y una impresora **TSC TE200**, para etiquetas **dobles**:

- ✅ **2 etiquetas por tirada**
- ✅ Etiquetas **50 mm × 25 mm**
- ✅ Separación real entre columnas (**gap**) **3 mm**
- ✅ Total de página: **103 mm × 25 mm** (50 + 3 + 50)
- ✅ Código de barras **Code128**
- ✅ Texto: **número del código** + **nombre del producto**
- ✅ **Sin servidor**, **sin módulo custom**
- ✅ **Sin IoT / ZPL** (omitido a propósito)
- ✅ **Sin imprimir por cantidades** (omitido a propósito)

> 📌 Importante: Odoo genera el PDF con **wkhtmltopdf**, por eso hay algunas “reglas” para que no salga deformado, cortado o en 2 páginas.

---

## 📚 Índice

1. ✅ Requisitos previos
2. 🧾 Medidas exactas (tu rollo)
3. 🧩 Parte A — Configurar Odoo (habilitar modo desarrollador)
4. 🧩 Parte B — Crear **Formato de Papel** (Paperformat) 103×25
5. 🧩 Parte C — Crear **Reporte PDF** para `product.template`
6. 🧩 Parte D — Crear/editar la **Plantilla QWeb**
7. 🧪 Verificaciones (PDF correcto antes de imprimir)
8. 🖨️ Configurar el **driver** (Windows) con tamaño 103×25
9. 🧯 Problemas comunes y soluciones rápidas

---

## ✅ 1) Requisitos previos

### 🧰 En Odoo
- Odoo **19.0+ Enterprise Online**
- App **Inventario** instalada
- Acceso a **Ajustes** con permisos de administrador
- (Recomendado) Tener algunos productos con:
  - **Nombre** (obligatorio)
  - **Código de barras** (barcode) (obligatorio para el barcode)

### 🖨️ En tu PC
- Impresora instalada: **TSC TE200**
- Driver oficial TSC instalado
- Etiquetas físicas: **dobles 50×25** con separación **3 mm**

---

## 🧾 2) Medidas exactas del rollo / etiqueta

Tus etiquetas son dobles, por lo tanto se arma una “página” horizontal:

| Elemento | Medida |
|---|---:|
| Etiqueta izquierda | 50 mm × 25 mm |
| Espacio (gap) | 3 mm |
| Etiqueta derecha | 50 mm × 25 mm |
| **TOTAL** | **103 mm × 25 mm** |

✅ El PDF debe generarse en **103×25 mm**.

---

## 🧩 3) Parte A — Activar **Modo Desarrollador** en Odoo

> Necesario para acceder a: **Formatos de papel**, **Reportes** y **Vistas QWeb**

### Opción 1 (rápida)
1. Ir a **Ajustes**
2. Buscar “**Activar modo desarrollador**”
3. Hacer clic en **Activar modo desarrollador**

### Opción 2 (URL)
1. Estar logueado en Odoo
2. Agregar a la URL:
   - `?debug=1`
3. Enter

✅ Si activó bien, vas a ver menús “Técnico” y más opciones.

---

# 🧩 4) Parte B — Crear el **Formato de Papel** (Paperformat) 103×25

📍 Ruta exacta:

1. Ir a **Ajustes**
2. Entrar a **Técnico**
3. Ir a: **Informes → Formatos de papel**

> Si no ves “Técnico”, revisá que el modo desarrollador esté activo.

---

## ➕ Crear un nuevo formato

1. Clic en **Nuevo**
2. Completar así:

### 🔧 Campos recomendados (exactos)

- **Nombre**: `Etiqueta 103x25 (doble 50x25)`
- **Formato**: `Personalizado` (o “Custom”)
- **Ancho**: `103` mm
- **Alto**: `25` mm
- **Orientación**: `Horizontal` (Landscape)  
  > Si no te deja elegir, no importa: con ancho>alto ya queda horizontal.

### 📌 Márgenes (fundamental)
- **Margen superior**: `0`
- **Margen inferior**: `0`
- **Margen izquierdo**: `0`
- **Margen derecho**: `0`

### ⚠️ Encogimiento inteligente (CRÍTICO)
- **Encogimiento inteligente / Smart shrinking**: **DESACTIVADO** ❌

> ✅ Esto evita que Odoo “encuadre” la etiqueta dentro de un área más chica o la centre con márgenes raros.

3. Clic en **Guardar**

---

## ✅ Verificación rápida del Paperformat
- El formato debe quedar con:
  - 103×25
  - márgenes 0
  - sin smart shrinking

---

# 🧩 5) Parte C — Crear el **Reporte PDF** (para Productos)

📍 Ruta exacta:

1. **Ajustes**
2. **Técnico**
3. **Informes → Informes** (Reports)

---

## ➕ Crear el reporte

1. Clic en **Nuevo**
2. Configurar los campos:

### 🧠 Campos clave

- **Nombre del reporte**: `Etiquetas productos 50x25 doble (PDF)`
- **Tipo de reporte**: `PDF`
- **Modelo**: `product.template`

> `product.template` es el producto “modelo”, el que ves en Inventario → Productos.

### 🧾 Papel / Layout (crítico)
- **Formato de papel**: seleccionar el creado:
  - `Etiqueta 103x25 (doble 50x25)`

### 📌 (Opcional) Mostrar en menú de impresión
- Activar: **Mostrar en el menú de impresión** ✅  
Esto lo hace aparecer en:
- Inventario → Productos → seleccionar → **Imprimir**

3. Clic en **Guardar**

---

# 🧩 6) Parte D — Crear / editar la **Plantilla QWeb** (contenido del reporte)

Un reporte PDF necesita una **plantilla QWeb**.

📍 Desde el mismo reporte que acabás de crear:
1. Abrí el reporte `Etiquetas productos 50x25 doble (PDF)`
2. Entrá a la sección **Vistas** (o “Views”)
3. Buscá / creá la vista QWeb asociada al reporte

> En Odoo Online suele existir un vínculo “Nombre de plantilla” o “Vista QWeb”.

---

## ✅ Template QWeb recomendado (estable en wkhtmltopdf)

📌 Copiá y pegá esto como arquitectura QWeb.  
Este template:
- usa página 103×25
- arma 2 etiquetas (50 + gap 3 + 50)
- imprime barcode + número + nombre
- evita desbordes
- NO usa tablas internas con alturas “raras”
- NO imprime por cantidad (a pedido)

> 🔁 Este template imprime **1 página por producto** seleccionado.

```xml
<t t-name="studio_report_document">
  <style>
    @page { size: 103mm 25mm; margin: 0 !important; }

    html, body { margin: 0 !important; padding: 0 !important; }

    #wrapwrap, main, .o_body_html, .container, .container-fluid, .article {
      margin: 0 !important;
      padding: 0 !important;
    }

    /* Tolerancia anti “2da hoja” (wkhtmltopdf puede redondear) */
    .label-page{
      width: 103mm !important;
      height: 24.5mm !important;
      margin: 0 !important;
      padding: 0 !important;
      overflow: hidden !important;
      page-break-after: always;
      page-break-inside: avoid;
    }

    table.lbl{
      width: 103mm !important;
      height: 24.5mm !important;
      border-collapse: collapse !important;
      border-spacing: 0 !important;
      table-layout: fixed !important;
    }

    /* Quitar cualquier borde visible */
    table, tr, td {
      border: none !important;
      outline: none !important;
    }

    td.cell{
      width: 50mm !important;
      height: 24.5mm !important;
      padding: 0 !important;
      margin: 0 !important;
      vertical-align: top !important;
      overflow: hidden !important;
    }

    td.gap{
      width: 3mm !important;
      padding: 0 !important;
      margin: 0 !important;
    }

    .box{
      width: 50mm !important;
      height: 24.5mm !important;
      box-sizing: border-box !important;
      padding: 0.8mm 1mm !important;
      font-family: "DejaVu Sans", Arial, sans-serif !important;
      text-align: center !important;
      overflow: hidden !important;
    }

    .bc-wrap{
      height: 13mm !important;
      overflow: hidden !important;
      display: flex !important;
      align-items: center !important;
      justify-content: center !important;
    }

    .barcode-img{
      height: 13mm !important;
      width: 48mm !important;
      object-fit: contain !important;
      display: block !important;
      margin: 0 auto !important;
    }

    .barcode-text{
      height: 3mm !important;
      font-size: 8px !important;
      line-height: 1 !important;
      margin-top: 0.2mm !important;
    }

    .product-name{
      height: 8mm !important;
      font-size: 9px !important;
      font-weight: bold !important;
      line-height: 1.1 !important;
      overflow: hidden !important;
      display: flex !important;
      align-items: center !important;
      justify-content: center !important;
    }

    table, tr, td, .box { page-break-inside: avoid !important; }
  </style>

  <!-- Odoo manda los registros como 'docs' -->
  <t t-foreach="docs" t-as="doc">
    <div class="label-page">
      <table class="lbl">
        <tr>

          <!-- IZQUIERDA -->
          <td class="cell">
            <div class="box">
              <div class="bc-wrap">
                <t t-if="doc.barcode">
                  <img class="barcode-img"
                       t-att-src="'/report/barcode/?barcode_type=Code128&amp;value=%s&amp;width=380&amp;height=90' % doc.barcode"/>
                </t>
              </div>

              <div class="barcode-text">
                <t t-if="doc.barcode"><span t-esc="doc.barcode"/></t>
              </div>

              <div class="product-name">
                <span t-esc="doc.name"/>
              </div>
            </div>
          </td>

          <!-- GAP -->
          <td class="gap"></td>

          <!-- DERECHA -->
          <td class="cell">
            <div class="box">
              <div class="bc-wrap">
                <t t-if="doc.barcode">
                  <img class="barcode-img"
                       t-att-src="'/report/barcode/?barcode_type=Code128&amp;value=%s&amp;width=380&amp;height=90' % doc.barcode"/>
                </t>
              </div>

              <div class="barcode-text">
                <t t-if="doc.barcode"><span t-esc="doc.barcode"/></t>
              </div>

              <div class="product-name">
                <span t-esc="doc.name"/>
              </div>
            </div>
          </td>

        </tr>
      </table>
    </div>
  </t>
</t>
