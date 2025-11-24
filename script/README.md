# 🍽️ ASE242S2_T04_db

**Diseño y Desarrollo de Base de Datos - Sistema SUMAK MIKUY**

---

## 📌 Descripción General

Este repositorio contiene **toda la información del diseño y desarrollo** de la base de datos del proyecto **SUMAK MIKUY**, un sistema de gestión integral para un restaurante andino que administra usuarios, mesas, menú, reservas y pedidos.

---

## 📂 Estructura del Proyecto

```
ASE242S2_T04_db/
│
├── 📋 case/
│   └── description.md          → Descripción del caso y alcance del proyecto
│
├── 🛠️ dveloment/
│   └── info-development.md     → Documentación técnica de la base de datos
│
├── 📚 resources/
│   └── (archivos adicionales)
│
├── 📖 script/
│   ├── README.md               → Este archivo
│   └── (scripts de implementación)
│
└── 📄 LICENSE, .gitignore, etc.
```

---

## 📑 Contenido del Repositorio

### 1️⃣ **case/description.md**
   
   Descripción del caso de estudio que incluye:
   - Objetivo del proyecto
   - Alcance del sistema
   - Beneficios esperados
   - Funcionalidades principales

### 2️⃣ **dveloment/info-development.md**

   Documentación técnica completa con:
   - Scripts SQL para crear la base de datos
   - Definición de todas las tablas (maestras y transaccionales)
   - Explicación de campos y tipos de datos
   - Relaciones entre entidades
   - Datos de ejemplo (inserts)
   - Características de seguridad e integridad

### 3️⃣ **script/README.md**

   Este archivo - información general del repositorio

### 4️⃣ **resources/SumaqMikuy_Documentacion.pdf**

   Documento PDF con el manual técnico y documentación adicional del proyecto.

---

## 🗄️ Tablas de la Base de Datos

### **Tablas Maestras** (Datos estáticos)

| Tabla | Descripción |
|-------|-------------|
| `usuarios` | Gestión de usuarios (clientes, admin, mesero, cocina) |
| `mesas` | Información de mesas del restaurante |
| `categorias_menu` | Categorías de platos |
| `platos` | Catálogo de platos disponibles |

### **Tablas Transaccionales** (Operaciones)

| Tabla | Descripción |
|-------|-------------|
| `reservas` | Registro de reservas de mesas |
| `pedidos` | Registro de pedidos realizados |
| `detalle_pedidos` | Detalles de items en cada pedido |

---

## 🔗 Relaciones Principales

```
usuarios ──→ reservas
       └──→ pedidos

mesas  ──→ reservas
      └──→ pedidos

categorias_menu ──→ platos

platos ──→ detalle_pedidos

pedidos ──→ detalle_pedidos
```

---

## 🚀 Cómo Usar Este Repositorio

### **Paso 1: Consultar el Caso**
   Revisa `case/description.md` para entender el contexto y objetivos del proyecto.

### **Paso 2: Revisar la Documentación Técnica**
   Consulta `dveloment/info-development.md` para ver:
   - Estructura de tablas
   - Scripts SQL
   - Datos de ejemplo

### **Paso 3: Implementar la Base de Datos**
   Ejecuta los scripts SQL en tu servidor MySQL:
   
   ```bash
   # Desde la línea de comandos
   mysql -u usuario -p < script_database.sql
   ```

---

## 🔐 Características de Seguridad

- ✅ **UTF-8mb4:** Soporta caracteres especiales y emojis
- ✅ **Motor InnoDB:** Transacciones y integridad referencial
- ✅ **Claves Foráneas:** Mantiene consistencia de datos
- ✅ **Índices:** Optimiza búsquedas frecuentes
- ✅ **Roles y Permisos:** Diferenciación de usuarios

---

## 📊 Capacidades del Sistema

| Capacidad | Descripción |
|-----------|-------------|
| **Gestión de Usuarios** | Registrar clientes y personal con roles diferenciados |
| **Control de Mesas** | Disponibilidad, ubicación y capacidad de mesas |
| **Administración de Menú** | Categorías, platos, precios e ingredientes |
| **Gestión de Reservas** | Reservar mesas con fecha, hora y notas |
| **Registro de Pedidos** | Capturar pedidos con detalles de platos |
| **Reportes** | Consultas rápidas y análisis de datos |
| **Integración** | Base para aplicaciones web, móviles o POS |

---

## 💡 Tecnología Utilizada

- **Base de Datos:** MySQL 8.0+
- **Codificación:** UTF-8mb4
- **Motor:** InnoDB
- **Lenguaje:** SQL
- **Versionamiento:** Git

---

## 📝 Notas Importantes

1. **Modularidad:** Las tablas maestras son independientes y reutilizables
2. **Escalabilidad:** La estructura está preparada para crecimiento
3. **Integridad:** Relaciones garantizan consistencia de datos
4. **Auditabilidad:** Timestamps registran cambios importantes
5. **Performance:** Índices estratégicos en campos críticos

---

## 🔄 Ciclo de Vida de Datos

```
Usuarios crean Reservas → Generan Pedidos → Contienen Detalles de Pedidos
                                      ↓
                              Incluyen Platos
                                      ↓
                          (Categorizados en Categorías)
```

---

## 📞 Información de Contacto
**Proyecto:** SUMAK MIKUY 2025  
**Repositorio:** ASE242S2_T04_db  
**Rama:** main  
**Última actualización:** 24 de Noviembre, 2025
