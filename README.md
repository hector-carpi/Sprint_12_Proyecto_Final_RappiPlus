📊 RappiPlus: De datos a decisiones de negocio

Proyecto final de análisis de datos — evalúa el desempeño del servicio de suscripción RappiPlus combinando limpieza de datos, KPIs de negocio, SQL, pruebas estadísticas y visualización en Power BI.


🎯 Contexto

RappiPlus es un servicio de suscripción dentro del ecosistema de Rappi pensado para aumentar la frecuencia de compra y el valor generado por usuario. Este proyecto responde 6 preguntas de negocio:

#PreguntaResultado1¿Podemos confiar en los datos?Datasets limpios y validados2¿El negocio es rentable?KPIs de revenue, costo y profit3¿Dónde se pierden los usuarios?Funnel de conversión (SQL)4¿Los usuarios regresan?Retención por cohortes (SQL)5¿Los cambios funcionan?Test A/B del checkout6¿Cómo comunicamos todo?Dashboard interactivo (Power BI)


🗂️ Estructura del repositorio

├── notebooks/
│   ├── paso1_limpieza_rappiplus.py       # Carga y calidad de datos
│   ├── paso2_kpis_rentabilidad.py        # KPIs de revenue, costo y profit
│   ├── paso3_funnel_conversion.py        # Funnel de conversión (SQL)
│   ├── paso4_retencion_cohortes.py       # Retención por cohortes (SQL)
│   └── paso5_test_ab_checkout.py         # Test estadístico A/B
├── data/
│   ├── rappiplus_orders_raw.csv          # Pedidos (crudo)
│   ├── rappiplus_catalog.csv             # Catálogo de productos
│   ├── rappiplus_marketing_spend.csv     # Inversión en marketing
│   ├── experiment_checkout_ui.csv        # Resultados del experimento A/B
│   ├── orders_clean.csv                  # Pedidos (limpio) — generado en Paso 1
│   ├── catalog_clean.csv                 # Catálogo (limpio) — generado en Paso 1
│   └── marketing_clean.csv               # Marketing (limpio) — generado en Paso 1
├── dashboard/
│   └── rappiplus_dashboard.pbix          # Dashboard en Power BI
└── README.md


🔧 Stack técnico


Python (pandas, numpy, scipy) — limpieza de datos y análisis estadístico
SQL (PostgreSQL vía SQLAlchemy) — funnel de conversión y retención por cohortes
Power BI — dashboard ejecutivo y de detalle



1️⃣ Calidad de datos

Se identificaron y corrigieron los siguientes problemas en rappiplus_orders_raw.csv:


100 filas duplicadas exactas
Inconsistencias de mayúsculas/minúsculas en pais (Mexico vs mexico)
Valores de cantidad negativos y outliers extremos (10,000–20,000 unidades) que inflaban el revenue en millones de dólares
Desajuste de acentos entre orders.categoria_producto (Electronica) y catalog.categoria_producto (Electrónica), que rompía el join


📦 Entregable: orders_clean.csv, catalog_clean.csv, marketing_clean.csv


2️⃣ KPIs de rentabilidad

KPIValorRevenue total$9,491,675.14Costo total (COGS)$3,783,478.80Inversión en marketing$2,871,843.53Profit neto$2,836,352.81Margen sobre revenue29.9%Ticket promedio$386.00Ítems promedio por orden1.51

Nota metodológica: marketing_clean.csv no tiene llave a nivel de producto o pedido (solo país y fecha), por lo que el profit se calcula en dos niveles:


Profit neto (Revenue − Costo − Marketing): válido a nivel de negocio total o por mes.
Profit bruto (Revenue − Costo): usado en desgloses por producto o categoría, donde el marketing no puede prorratearse de forma confiable.



3️⃣ Funnel de conversión (SQL)

Construido sobre la tabla events con conteo de usuarios únicos por etapa y tasa de conversión entre pasos consecutivos. Ver paso3_funnel_conversion.py.


4️⃣ Retención por cohortes (SQL)

Cohortes por mes de registro (users.fecha_registro), con retención semanal (semana 1, 2 y 3) calculada sobre user_activity. Ver paso4_retencion_cohortes.py.


5️⃣ Test A/B — nueva UI de checkout

H₀: no hay diferencia en conversión entre control y tratamiento.
H₁: sí hay diferencia.
Test: z-test de proporciones (dos colas), α = 0.05.

VarianteUsuariosConversiónControl4,96515.69%Tratamiento5,03516.29%

p-value = 0.416 → no se rechaza H₀. La diferencia observada (+3.8% relativo) es consistente con ruido muestral; no hay evidencia suficiente para lanzar el cambio de UI a producción con esta muestra.


6️⃣ Dashboard

Dos páginas en Power BI:


Overview ejecutivo: KPIs principales, evolución mensual de revenue/profit, profit bruto por categoría, revenue por producto, gasto de marketing por canal.
<img width="876" height="497" alt="image" src="https://github.com/user-attachments/assets/99a2bbfa-1007-4ae9-96c3-e538642521bd" />


Detalle / drill-through: tabla de pedidos con formato condicional (verde/rojo según profit) y navegación de detalle activada desde el Overview.
<img width="989" height="488" alt="image" src="https://github.com/user-attachments/assets/c98f587d-f42d-435b-8942-8816a6d84470" />


🔗 Link del dashboard publicado: https://drive.google.com/file/d/1Nn57BBIfYRPAVS4xUMH_lmbp8zmS2yHK/view?usp=drive_link


💡 Conclusiones y recomendaciones


El negocio es rentable, con un margen neto de ~30%.
Las categorías Hogar, Electrónica y Moda contribuyen de forma similar al profit — no hay una categoría dominante que concentre el riesgo.
El experimento de checkout no mostró impacto estadísticamente significativo; se recomienda repetirlo con mayor tamaño de muestra antes de tomar una decisión definitiva.



✍️ Autor

Hector Carpinteyro Aguilar
Proyecto final — Bootcamp de Análisis de Datos.
