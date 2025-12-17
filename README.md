# 🏷️ Impresión de Etiquetas 50×25 mm en Odoo Online (Enterprise 19+)
### Impresora TSC TE200 · PDF · QWeb · Odoo Studio

Esta guía explica **paso a paso** cómo configurar **Odoo Online (Enterprise)** para imprimir **etiquetas térmicas de productos** usando una **impresora TSC TE200**, con etiquetas **dobles 50×25 mm**, mediante **reportes PDF (QWeb)**.

> ✅ Sin acceso al servidor  
> ✅ Sin módulos custom  
> ✅ Sin Python  
> ✅ Sin IoT / ZPL (omitido)  
> ✅ Sin impresión por cantidades (omitido)  
> ✅ 100% compatible con Odoo Online + Studio  

---

## 📦 Resultado final

✔️ Dos etiquetas por tirada  
✔️ Etiquetas reales **50 × 25 mm**  
✔️ Separación física (gap) **3 mm**  
✔️ Código de barras **Code128**  
✔️ Número del código visible  
✔️ Nombre del producto en negrita  
✔️ PDF limpio (sin bordes, sin cortes)  
✔️ Impresión correcta en **TSC TE200**

---

## 🧾 Medidas físicas del rollo

| Elemento | Medida |
|---|---:|
| Etiqueta izquierda | 50 mm × 25 mm |
| Gap (separación) | 3 mm |
| Etiqueta derecha | 50 mm × 25 mm |
| **Total página PDF** | **103 mm × 25 mm** |

📌 El PDF **NO debe ser A4**. Debe ser **exactamente 103 × 25 mm**.

---

## 🧰 Requisitos

### En Odoo
- Odoo **Enterprise Online 19+**
- App **Inventario**
- **Odoo Studio**
- Permisos de administrador
- Productos con:
  - Nombre
  - Código de barras (`barcode`)

### En la PC
- Impresora **TSC TE200**
- Driver oficial TSC instalado
- Etiquetas térmicas dobles 50×25 mm (gap 3 mm)

---

## 🧩 PARTE A — Activar Modo Desarrollador

Necesario para acceder a **Reportes**, **Formatos de papel** y **QWeb**.

### Opción rápida
1. Ir a **Ajustes**
2. Buscar **Activar modo desarrollador**
3. Clic en **Activar**

### Opción por URL
Agregar `?debug=1` a la URL de Odoo y recargar.

---

## 🧩 PARTE B — Crear Formato de Papel (Paperformat)

📍 Ruta:
```
Ajustes → Técnico → Informes → Formatos de papel
```

### Crear nuevo formato

| Campo | Valor |
|---|---|
| Nombre | Etiqueta 103×25 (doble 50×25) |
| Formato | Personalizado |
| Ancho | 103 mm |
| Alto | 25 mm |
| Orientación | Horizontal |
| Márgenes | 0 / 0 / 0 / 0 |
| Encogimiento inteligente | ❌ DESACTIVADO |

⚠️ **Crítico:** si el encogimiento inteligente está activado, el PDF se centra, se achica o se deforma.

Guardar el formato.

---

## 🧩 PARTE C — Crear el Reporte PDF

📍 Ruta:
```
Ajustes → Técnico → Informes → Informes
```

### Crear reporte

| Campo | Valor |
|---|---|
| Nombre | Etiquetas productos 50×25 doble |
| Tipo | PDF |
| Modelo | product.template |
| Formato de papel | Etiqueta 103×25 (doble 50×25) |
| Mostrar en imprimir | ✅ |

Guardar.

---

## 🧩 PARTE D — Plantilla QWeb del Reporte

Entrar al reporte → **Vistas / Views** → plantilla QWeb.  
Pegar **TODO este template** (recomendado y estable para wkhtmltopdf):

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
    }

    table.lbl{
      width: 103mm !important;
      height: 24.5mm !important;
      border-collapse: collapse !important;
      table-layout: fixed !important;
    }

    table, tr, td { border: none !important; outline: none !important; }

    td.cell{
      width: 50mm !important;
      height: 24.5mm !important;
      padding: 0 !important;
      overflow: hidden !important;
      vertical-align: top !important;
    }

    td.gap{ width: 3mm !important; }

    .box{
      width: 50mm !important;
      height: 24.5mm !important;
      padding: 0.8mm 1mm !important;
      font-family: "DejaVu Sans", Arial, sans-serif !important;
      text-align: center !important;
      box-sizing: border-box !important;
      overflow: hidden !important;
    }

    .bc-wrap{
      height: 13mm !important;
      display: flex !important;
      align-items: center !important;
      justify-content: center !important;
      overflow: hidden !important;
    }

    .barcode-img{
      height: 13mm !important;
      width: 48mm !important;
      object-fit: contain !important;
      display: block !important;
      margin: 0 auto !important;
    }

    .barcode-text{
      font-size: 8px !important;
      line-height: 1 !important;
      margin-top: 0.2mm !important;
    }

    .product-name{
      font-size: 9px !important;
      font-weight: bold !important;
      line-height: 1.1 !important;
      height: 8mm !important;
      display: flex !important;
      align-items: center !important;
      justify-content: center !important;
      overflow: hidden !important;
    }
  </style>

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
              <div class="barcode-text"><t t-if="doc.barcode"><span t-esc="doc.barcode"/></t></div>
              <div class="product-name"><span t-esc="doc.name"/></div>
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
              <div class="barcode-text"><t t-if="doc.barcode"><span t-esc="doc.barcode"/></t></div>
              <div class="product-name"><span t-esc="doc.name"/></div>
            </div>
          </td>
        </tr>
      </table>
    </div>
  </t>
</t>
```

---

## 💾 Guardar cambios del reporte (QWeb)

1. 📋 Pegá el template QWeb completo en la vista del reporte  
2. 💾 Hacé clic en **Guardar**  
3. 🔄 Reabrí el reporte desde:
   - Ajustes → Técnico → Informes → Informes  
4. ✅ Confirmá que no se muestre ningún error

---

## 🧪 Verificaciones antes de imprimir (OBLIGATORIO)

### ✅ Verificar tamaño real del PDF
1. Desde Odoo, ejecutá:
   - Inventario → Productos → seleccionar producto → **Imprimir**
2. Descargá el PDF
3. Abrilo con tu visor (recomendado: Adobe Reader)
4. Ir a **Propiedades del documento**
5. Confirmar:
   - ✅ **Tamaño de página:** `103 × 25 mm`
   - ❌ Si dice **A4** → el reporte **NO está usando** tu Paperformat

### ✅ Verificar que NO se generen “2 páginas”
Si te sale una segunda hoja en blanco o el contenido cortado:

- Bajá la altura:
  ```css
  .label-page { height: 24mm; }
  ```
- O reducí padding interno:
  ```css
  padding: 0.6mm 0.8mm;
  ```

> ℹ️ Esto ocurre porque **wkhtmltopdf redondea milímetros** y puede “pasarse” del alto por ~0.5mm.

---

## 🖨️ PARTE E — Configurar tamaño de papel en el Driver (Windows)

⚠️ **Lo más importante:** si el driver imprime como A4, vas a desperdiciar etiqueta y quedará corrido aunque el PDF esté perfecto.

### 8.1 Crear tamaño personalizado en Windows
1. Abrir **Panel de control**
2. Ir a **Dispositivos e impresoras**
3. Clic derecho en **TSC TE200**
4. Elegir **Preferencias de impresión** (*Printing Preferences*)

### 8.2 Definir “Papel / Tamaño”
En el driver de TSC (puede variar por versión):
1. Buscar sección **Paper / Stock / Media**
2. Elegir **Custom / User Defined / Personalizado**
3. Crear nuevo tamaño:
   - **Width / Ancho:** `103 mm`
   - **Height / Alto:** `25 mm`

Si lo pide en pulgadas:
- 103 mm = **4.055 in**
- 25 mm = **0.984 in**

### 8.3 Parámetros críticos del driver

#### ✅ Escala
- Scaling / Fit to page / Ajustar: **NO** ❌
- Scale: **100%** ✅

#### ✅ Orientación
- Horizontal ✅ (Landscape)
- Rotación automática: **desactivada** ❌

#### ✅ Tipo de papel
- Tipo: **Etiqueta / Label**
- Gap / separación: si hay opción, indicar **gap físico (3 mm)**

#### ✅ Velocidad / densidad (calidad)
- Densidad media/alta si barcode sale claro
- Velocidad media para mejor definición

---

## 🧯 Problemas comunes (y solución directa)

### ❌ Sale como A4
✅ Solución:
- Revisar que el reporte tenga seleccionado el **Paperformat 103×25**
- Revisar que el PDF descargado sea realmente **103×25**

### ❌ Deja mucho margen o se centra
✅ Causa típica:
- “Encogimiento inteligente / Smart shrinking” activado

✅ Solución:
- Desactivarlo en el formato de papel

### ❌ Sale en 2 páginas
✅ Causa:
- wkhtmltopdf redondea mm y se pasa por 0.5mm

✅ Solución:
- usar:
  ```css
  .label-page { height: 24.5mm; }
  ```
- si insiste:
  ```css
  .label-page { height: 24mm; }
  ```

### ❌ El texto desaparece
✅ Causa:
- layouts con tablas internas y filas con alturas en mm (wkhtmltopdf lo calcula mal)

✅ Solución:
- usar el template recomendado (divs con alturas fijas)

### ❌ Barcode deformado o muy chico
✅ Solución:
- aumentar parámetros del endpoint:
  - `width=420&height=100`
- mantener `.barcode-img` con altura fija

---

## ✅ Resultado esperado

Cuando esté bien configurado, el resultado será:

- Dos etiquetas por “página” (tirada)
- Barcode centrado
- Número debajo
- Nombre en negrita
- Sin bordes
- Sin segunda hoja

---
