# Cómo usar este kit

## Requisitos

1. **Visual Studio Code** — descarga en https://code.visualstudio.com
2. **Extensión Claude Code** — búscala en el marketplace de VS Code (Extensions → busca "Claude Code")

---

## Paso 1 — Descarga o clona este repositorio

**Opción A — Descargar ZIP:**
1. Haz clic en el botón verde "Code" en GitHub
2. Selecciona "Download ZIP"
3. Descomprime la carpeta

**Opción B — Clonar con git:**
```bash
git clone https://github.com/[usuario]/kit-vuelos-bilbao
```

---

## Paso 2 — Abre la carpeta en VS Code

1. Abre VS Code
2. Archivo → Abrir carpeta → selecciona la carpeta `kit-vuelos-bilbao`
3. Abre Claude Code desde el panel lateral de VS Code

---

## Paso 3 — Escribe lo que buscas

Escribe en Claude Code algo como:

> "Quiero volar a Tokio en julio, somos 2 personas"

> "¿Cuál es la ruta más barata para llegar a Nueva York desde Bilbao?"

> "Muéstrame los chollos de vuelos desde Bilbao esta semana"

> "¿Está barato ahora el vuelo a Bangkok o debería esperar?"

Claude buscará precios reales, comparará rutas y te dará una recomendación.

---

## Paso 4 — El gráfico de precios

Cada vez que hagas una consulta, la skill guarda el precio en un historial local y genera un gráfico HTML que se abre automáticamente en tu navegador.

El gráfico muestra:
- Evolución del precio a lo largo del tiempo
- Si el precio actual está **por debajo** (verde), **en rango normal** (amarillo) o **por encima** (rojo) de lo habitual

Cuanto más la uses para el mismo destino, más preciso se vuelve el análisis.

Los archivos de historial se guardan en la carpeta `vuelos-historial/` dentro de este proyecto.

---

## Estructura del proyecto

```
kit-vuelos-bilbao/
├── CLAUDE.md                        ← Claude lo lee automáticamente al abrir la carpeta
├── INSTRUCCIONES.md                 ← Este archivo
├── .claude/
│   └── skills/
│       └── vuelos-bilbao.md         ← La skill
└── vuelos-historial/                ← Se crea automáticamente al usar la skill
    └── [destino].json               ← Historial de precios por destino
```

---

## Preguntas frecuentes

**¿Funciona para cualquier destino del mundo?**
Sí. Para destinos lejanos (Asia, América, África...) la skill busca automáticamente las mejores rutas con escala, ya que desde Bilbao no hay vuelos directos a esos lugares.

**¿Los precios son exactos?**
Son precios obtenidos de búsquedas web en tiempo real (Skyscanner, Google Flights, etc.). Pueden variar ligeramente respecto al precio final en la web de la aerolínea, pero son una referencia fiable.

**¿Necesito una cuenta de Claude o una API key?**
Solo necesitas tener instalada la extensión Claude Code en VS Code con tu cuenta de Anthropic.

**¿Se guardan mis búsquedas en algún servidor?**
No. El historial de precios se guarda únicamente en tu ordenador, dentro de la carpeta `vuelos-historial/` de este proyecto.
