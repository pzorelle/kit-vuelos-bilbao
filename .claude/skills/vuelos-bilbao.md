---
name: vuelos-bilbao
description: "Analiza vuelos desde o hacia Bilbao (BIO), busca las rutas más baratas incluyendo conexiones, rastrea variación de precios y da recomendaciones. Triggers: 'busca vuelos desde bilbao', 'cómo llegar a X desde bilbao', 'vuelos baratos bilbao', 'qué vuelos hay desde bilbao', 'precio vuelo bilbao a X', 'ruta más barata desde bilbao', 'vuelos con escala desde bilbao', 'chollos de vuelos bilbao', 'recomendaciones vuelos bilbao', 'analiza vuelos bilbao'."
---

# Vuelos Bilbao — Cómo llegar a cualquier parte del mundo desde una ciudad pequeña

Bilbao es un aeropuerto pequeño. Madrid tiene cientos de vuelos directos a cualquier destino; Bilbao, no. Esta skill resuelve exactamente ese problema: encuentra la ruta más inteligente y barata para llegar a destinos lejanos o grandes ciudades desde un aeropuerto con pocas conexiones directas, combinando tramos cortos + hubs + vuelos de largo radio.

**Regla fundamental: busca precios reales con WebSearch antes de dar cualquier recomendación. Nunca inventes precios.**

---

## Paso 1 — Entender qué busca el usuario

Pregunta de forma conversacional, en un solo mensaje:

- **¿Tienes destino en mente o buscas inspiración?**
  - Si tiene destino: pregunta las fechas aproximadas (o si es flexible) y cuántas personas viajan
  - Si busca inspiración: pregunta si prefiere Europa, larga distancia, playa, ciudad, etc. y si tiene un presupuesto máximo orientativo

Si el usuario ya menciona un destino en su mensaje inicial, no preguntes de nuevo, ve directamente al paso 2.

---

## Paso 2 — Entender el problema de conectividad

Antes de buscar, clasifica el destino:

**Tipo A — Destino europeo cercano** (menos de 3h de vuelo directo):
Puede haber vuelo directo desde BIO. Compruébalo primero.

**Tipo B — Destino europeo lejano o ciudad grande** (Londres, París, Roma, Berlín...):
Desde Bilbao hay vuelos directos a algunos. Para los que no, la ruta inteligente es BIO → hub → destino. Busca también si volar a un aeropuerto alternativo cercano sale mejor.

**Tipo C — Destino de larga distancia** (América, Asia, África, Oceanía, Oriente Medio):
No hay vuelos directos desde Bilbao. La estrategia es siempre: BIO → hub europeo o nacional → vuelo de largo radio. Avisa al usuario con naturalidad:
> "Desde Bilbao no hay vuelo directo a [destino] — lo normal para aeropuertos pequeños. La clave es elegir bien el hub intermedio. Voy a buscar las combinaciones más baratas."

---

## Paso 3 — Buscar rutas inteligentes por capas

Lanza búsquedas en paralelo cubriendo estas tres capas:

### Capa 1: ¿Hay vuelo directo desde BIO?
```
vuelos directo bilbao [destino] precio [mes]
"BIO" "[IATA destino]" vuelo directo
```

### Capa 2: Rutas vía hubs nacionales (suelen ser más baratas)
Bilbao tiene buenas conexiones con Madrid y Barcelona. Desde ahí hay vuelos a casi todo.
```
vuelo bilbao madrid + madrid [destino] precio combinado
vuelo bilbao barcelona + barcelona [destino] precio
ruta bilbao [destino] via madrid precio total
```
Busca también si el tren/bus BIO→MAD puede ser mejor opción que el vuelo corto:
```
bilbao madrid avion vs tren precio tiempo
```

### Capa 3: Rutas vía hubs europeos (mejor para destinos muy lejanos)
Desde Bilbao hay conexiones directas o frecuentes con: París CDG, Ámsterdam AMS, Londres LGW/STN, Frankfurt FRA, Lisboa LIS. Estos hubs tienen vuelos de largo radio a casi todo el mundo.
```
bilbao [hub] + [hub] [destino] precio vuelo [mes]
ruta bilbao [destino] via [hub europeo]
```
Hubs a probar según destino:
- América del Norte/Sur → Madrid MAD, Londres LHR, Lisboa LIS, París CDG
- Asia/Pacífico → Frankfurt FRA, Ámsterdam AMS, París CDG, Dubai DXB (vía vuelo a hub europeo)
- África → Madrid MAD, París CDG, Lisboa LIS
- Oriente Medio → Frankfurt FRA, París CDG, Londres LHR

### Capa 4: Chollos actuales desde Bilbao (sin destino fijo)
```
vuelos baratos desde bilbao [mes actual] ofertas chollos
skyscanner vuelos baratos BIO bilbao
```

---

## Paso 4 — Comparar y presentar resultados

Con los datos obtenidos, construye una comparativa ordenada de menor a mayor precio:

### Formato del resultado

```
✈ BILBAO → [DESTINO]  |  Consulta: [fecha]
══════════════════════════════════════════════

🥇 OPCIÓN MÁS BARATA
Ruta: BIO → [HUB] → [DESTINO]
Precio: ~XXX€/persona  |  Duración total: Xh Xmin
Aerolíneas: [nombre] + [nombre]
Escala: Xh en [ciudad del hub]
Dónde comprar: Skyscanner / Google Flights

🥈 SEGUNDA OPCIÓN
Ruta: BIO → [HUB] → [DESTINO]
Precio: ~XXX€/persona  |  Duración total: Xh Xmin
...

🥉 TERCERA OPCIÓN
...

──────────────────────────────────────────────
⚠️ CONTEXTO CIUDAD PEQUEÑA
Si vivieras en Madrid: el mismo destino costaría ~XXX€ en directo.
Desde Bilbao la diferencia es de ~XX€ [más/menos].
[Si sale más barato desde Bilbao: destacarlo — pasa a veces con ciertas rutas low-cost]
[Si sale más caro: mencionar si merece la pena ir en coche/tren a MAD o BCN]
──────────────────────────────────────────────

💡 RECOMENDACIÓN
[Cuál elegir y por qué: precio, comodidad, tiempo de escala aceptable]

📉 MOMENTO DE COMPRA
[Si el precio está por encima/debajo de lo habitual según el historial]
[Consejo sobre cuándo suelen bajar los precios para este destino]

🔥 CHOLLOS AHORA DESDE BILBAO
[3-5 destinos con buen precio esta semana, con precio estimado y ruta]
```

El apartado "Contexto ciudad pequeña" es clave: le dice al usuario si le sale a cuenta ir a un aeropuerto grande, o si por el contrario Bilbao tiene una buena ruta para ese destino concreto.

---

## Paso 5 — Guardar historial y generar gráfico de precios

### Guardar el precio en el historial

Cada vez que se ejecuta la skill, guarda el precio encontrado en un archivo JSON de historial:

**Ruta:** `vuelos-historial/[destino-código].json`

Formato del JSON:
```json
{
  "destino": "Tokio",
  "codigo": "TYO",
  "registros": [
    {
      "fecha_consulta": "2026-04-24",
      "fecha_viaje": "2026-07-15",
      "precio_min": 420,
      "precio_medio": 510,
      "ruta": "BIO-MAD-TYO",
      "aerolinea": "Iberia + JAL"
    }
  ]
}
```

Si el archivo ya existe, añade el nuevo registro al array `registros`. Si no existe, créalo.

### Generar gráfico HTML

Genera un archivo `vuelos-grafico-[destino].html` con un gráfico interactivo usando Chart.js (cargado desde CDN, sin instalación):

**El gráfico debe mostrar:**
1. **Eje X:** fechas de consulta (evolución en el tiempo)
2. **Eje Y:** precio en euros
3. **Línea de precio:** puntos con el precio encontrado en cada consulta
4. **Banda de referencia:** zona sombreada que indica el rango de precio "normal" (calculado como media ± 20% de todos los registros históricos)
5. **Indicador de color por punto:**
   - Verde: precio ≥ 15% por debajo de la media histórica → "Buen momento para comprar"
   - Amarillo: precio dentro del rango normal (±15% de la media)
   - Rojo: precio ≥ 15% por encima de la media → "Precio alto, espera si puedes"

**Panel lateral de resumen (junto al gráfico):**
```
📊 ANÁLISIS DE PRECIO
Destino: [nombre]
Precio hoy: XXX€
Media histórica: XXX€
Mínimo registrado: XXX€ ([fecha])
Máximo registrado: XXX€ ([fecha])

🟢 / 🟡 / 🔴 VEREDICTO
[Texto claro: "Precio por debajo de lo habitual — buen momento para comprar" 
 / "Precio en rango normal" 
 / "Precio alto respecto a lo habitual — considera esperar"]

📅 MEJOR ÉPOCA DETECTADA
[Si hay suficientes datos: "Los precios más bajos para este destino 
suelen aparecer en [mes/trimestre]"]
```

**Diseño del HTML:**
- Fondo oscuro elegante (dark mode)
- Gráfico principal grande y legible
- Panel de resumen a la derecha o debajo según el espacio
- Título con destino y aeropuerto de origen (BIO)
- Nota al pie: "Datos basados en X consultas desde [primera fecha]"

Si solo hay un registro (primera vez que se consulta ese destino), genera el gráfico igualmente con ese único punto y el mensaje:
> "Primera consulta registrada. Vuelve a consultar en los próximos días para ver la evolución del precio."

Después de generarlo, abre el HTML automáticamente con:
```bash
start vuelos-grafico-[destino].html   # Windows
open vuelos-grafico-[destino].html    # Mac
xdg-open vuelos-grafico-[destino].html # Linux
```

---

## Paso 6 — Dar recomendaciones inteligentes

Después de mostrar la tabla, añade siempre:

**Recomendación de cuándo comprar:**
- Si las fechas son flexibles: indica qué días de la semana o meses suelen ser más baratos para ese destino
- Si tiene fechas fijas: indica si el precio actual es normal, caro o una buena oferta comparado con lo habitual

**Rutas alternativas a considerar:**
- Si hay un aeropuerto cercano que tenga más opciones (Vitoria VIT, San Sebastián EAS, Santander SDR), mencionarlo si la diferencia de precio justifica el desplazamiento

**Herramientas para seguir el precio:**
- Recomendar activar alertas en Google Flights o Skyscanner para ese destino
- Mencionar que los precios varían mucho día a día

---

## Paso 7 — Presentar y ofrecer más

Al terminar, muestra:
- El gráfico generado (ruta del archivo)
- El veredicto claro: precio bajo / normal / alto
- Si hay historial, cuántas consultas anteriores tiene ese destino

Luego pregunta:

> ¿Quieres que busque más fechas, otro destino, o quieres ver el gráfico comparando varios destinos a la vez?

---

## Notas de comportamiento

- **Si WebSearch no devuelve precios concretos:** indica explícitamente "No he podido obtener el precio exacto" y proporciona el enlace directo a Skyscanner o Google Flights para que el usuario lo compruebe
- **Si el destino tiene muchas opciones:** muestra las 3-4 mejores, no hagas una lista interminable
- **Moneda:** usa siempre euros (€) salvo que el usuario pida otra moneda
- **Fechas:** adapta la búsqueda a las fechas que dé el usuario; si son flexibles, busca para el mes o trimestre más próximo
- **Aeropuerto de origen:** usa siempre Bilbao BIO como origen por defecto salvo que el usuario diga lo contrario
