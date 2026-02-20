# Reporte de Ingeniería: Ajustes y Optimización de Procesos
## Proyecto: Sistema de Credencialización Digital Jalisco (v3.0)

---

## 1. Identificación del Documento
* **Asunto:** Corrección de flujo lógico y optimización de diagramación BPMN.
* **Fecha de Modificación:** 20 de febrero de 2026.
* **Referencia Normativa:** Manual de Usuario Operativo FSW2023 (Cignuz/Secretaría de Administración).
* **Archivo de Proceso:** `credencializacion_completo.bpmn`

---

## 2. Antecedentes y Contexto
Tras una auditoría técnica del modelo v2.0 comparado con el **Manual de Usuario FSW2023**, se identificó una restricción de tiempo arbitraria en el nodo de validación de identidad. El manual establece que el ciudadano debe recibir un código de 6 dígitos para activar su cuenta, pero no estipula un límite de caducidad de 30 minutos, por lo que se procedió a la reingeniería del proceso para alinearlo a la normativa oficial.

---

## 3. Registro de Errores y Hallazgos (Debilidades v2.0)

### **3.1. Error de Regla de Negocio (Lógica de Proceso)**
* **Hallazgo:** Configuración de un temporizador rígido de 30 minutos (`PT30M`) en el evento de recepción de correo.
* **Evidencia Técnica:** `<timeDuration xsi:type="tFormalExpression">PT30M</timeDuration>`.
* **Impacto:** Según el manual (pág. 9), el sistema depende del remitente **IDJalisco**. Si este servicio presenta latencia, el temporizador bloqueaba el trámite, obligando al usuario a reiniciar el registro y generando saturación innecesaria en la base de datos de pre-registros.

### **3.2. Deficiencia en la Interoperabilidad BPMN**
* **Hallazgo:** Coordenadas de flujo amontonadas y falta de espaciado en los carriles de "Brigadista" y "Sistema".
* **Consecuencia:** Errores de renderizado y congelamiento en **Bizagi Modeler**.

---

## 4. Diagramación del Flujo Correcto (Propuesta v3.0)

El nuevo diseño garantiza que el ciudadano pueda completar la verificación incluso si el correo electrónico tarda en llegar, permitiendo el uso de la función **"Reenviar código"** que menciona el manual.

### **4.1. Esquema Lógico de Verificación (BPMN Flow)**

```mermaid
graph TD
    A[Sistema envía código de 6 dígitos] --> B(Esperar recepción de correo)
    B --> C{¿Código recibido?}
    C -- NO / Reenviar --> B
    C -- SÍ --> D[Usuario ingresa código en el portal]
    D --> E[Validar integridad del código]
    E -- Válido --> F[Continuar a Verificación Biométrica]
    E -- Inválido --> D