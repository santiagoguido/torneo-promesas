# Torneo Infantil Promesas Del Real — sitio y panel

Sitio estático + panel de administración privado. Hospedaje gratuito, sin servidor y sin base de datos que mantener.

| Archivo | Para qué sirve |
|---|---|
| `index.html` | La página pública. Lee `data.json` al abrirse. |
| `admin.html` | Tu panel privado. Escribe `data.json` y sube imágenes al repositorio. |
| `data.json` | Todos los datos del torneo. Es el único archivo que cambia día a día. |
| `assets/logo.png`, `assets/logo.webp` | El escudo del torneo. |
| `assets/og.jpg` | La imagen que Facebook muestra al compartir el enlace. |

---

## 1. Publicar el sitio (una sola vez, ~10 minutos)

1. Crea una cuenta en [github.com](https://github.com) si no tienes.
2. Crea un repositorio **público** llamado por ejemplo `torneo-promesas`.
3. Sube estos archivos al repositorio, respetando la carpeta `assets`.
4. Entra a **Settings → Pages**. En *Source* elige `Deploy from a branch`, rama `main`, carpeta `/ (root)`. Guarda.
5. En un minuto tendrás la dirección: `https://TU-USUARIO.github.io/torneo-promesas/`

Esa es la liga que compartes en Facebook. El hospedaje de GitHub Pages es gratuito y sin límite de tiempo.

### Para que Facebook muestre bien la vista previa

En `index.html` busca esta línea y ponle la dirección completa:

```html
<meta property="og:image" content="https://TU-USUARIO.github.io/torneo-promesas/assets/og.jpg">
```

Si cambias la imagen después, pásala por el [depurador de Facebook](https://developers.facebook.com/tools/debug/) para que refresque el caché.

---

## 2. Darte acceso de administrador (y a nadie más)

El panel escribe en tu repositorio usando un token personal. Solo tú lo tienes, así que solo tú puedes modificar lo que se ve.

1. En GitHub ve a **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
2. Configúralo así:
   - **Repository access:** *Only select repositories* → elige únicamente `torneo-promesas`.
   - **Permissions → Repository permissions → Contents:** `Read and write`.
   - **Expiration:** la que prefieras (si la pones a un año, tendrás que renovarlo).
3. Copia el token (empieza con `github_pat_`). GitHub solo te lo muestra una vez.
4. Abre `https://TU-USUARIO.github.io/torneo-promesas/admin.html`, llena usuario, repositorio, rama y token, y presiona **Conectar y cargar**.

El token queda guardado únicamente en el navegador donde lo escribiste. Si alguien más abre `admin.html` ve el panel vacío y no puede guardar nada.

> Ten presente que `admin.html` es un archivo público (el formulario se ve, pero sin token no sirve de nada). Si prefieres que ni siquiera se vea, no lo subas al repositorio: ábrelo desde tu computadora con doble clic y funciona igual.

---

## 3. El día a día

Todo se hace desde el panel:

- **Torneo** — nombre, sede, temporada, puntos por victoria y empate, cuántos equipos califican, y cómo se llaman las tres categorías.
- **Equipos** — alta, edición y logo. Si un equipo se retira, márcalo **inactivo**: sale de la tabla pero sus partidos jugados se conservan.
- **Jugadores** — alta por equipo, número y foto. La foto se recorta cuadrada y se comprime sola antes de subirse. Si no hay foto, la página muestra las iniciales.
- **Calendario** — decides cuántas jornadas tendrá cada categoría y desde qué fecha, y el panel genera el rol de todos contra todos completo. También arma las semifinales y la final.
- **Partidos** — fase, jornada, fecha, hora, estado, marcador y quién anotó. Un partido cuenta para la tabla cuando lo marcas como **jugado**.

### Los cuatro estados de un partido

| Estado | Qué hace |
|---|---|
| Por jugar | Aparece en el calendario y, si es el más próximo, en la portada con su cuenta regresiva. |
| Jugado | Suma a la tabla de posiciones y al goleo. |
| Pospuesto | Guardas la fecha original y la nueva; la página muestra ambas con un aviso amarillo. |
| Suspendido | Se marca en rojo con el motivo y no suma nada a la tabla. |

Cuando termines, presiona **Guardar y publicar**. El panel sube las imágenes pendientes, escribe `data.json` y la página queda actualizada en menos de un minuto.

**Lo que no tienes que capturar:** la tabla de posiciones, la de goleo y la de mejor portería se calculan solas a partir de los partidos.

**Los dos premios.** El goleador del torneo sale de la tabla de goleo y el mejor portero del equipo menos goleado; ambos se cuentan únicamente con los partidos de la fase regular.

### Cómo se arma el rol

El generador usa el método del círculo, igual al que describiste: con equipos a, b, c, d salen `a-b, c-d` / `a-c, b-d` / `a-d, b-c`. Con número impar de equipos uno descansa cada jornada y la página escribe **Descansa \<equipo\>** debajo de los partidos. Si pides más jornadas que una vuelta completa, el ciclo se repite invirtiendo local y visitante.

La liguilla se arma con la tabla al momento: semifinales 1° contra 2° y 3° contra 4°. La final se crea cuando las dos semifinales están marcadas como jugadas, y la página muestra al campeón en cuanto capturas su marcador.

### Si prefieres no usar token

En la pestaña *Conexión* hay dos botones: **Abrir un data.json** y **Descargar data.json**. Editas en el panel, descargas el archivo y lo subes tú mismo al repositorio arrastrándolo en la página de GitHub. Las fotos las copiarías a mano a `assets/jugadores/` y `assets/equipos/`.

---

## 4. Cómo están guardados los datos

`data.json` en resumen:

```jsonc
{
  "torneo":    { "nombre": "", "sede": "", "temporada": "", "facebook": "" },
  "ajustes":   { "puntosVictoria": 3, "puntosEmpate": 1, "puntosDerrota": 0, "equiposCalifican": 4 },
  "categorias":[ { "id": "pb", "nombre": "Primaria Baja", "descripcion": "", "orden": 1 } ],
  "equipos":   [ { "id": "", "nombre": "", "categoria": "pb", "logo": "", "ajustePuntos": 0, "activo": true } ],
  "jugadores": [ { "id": "", "nombre": "", "equipo": "", "categoria": "pb", "dorsal": 9, "foto": "", "activo": true } ],
  "jornadas":  [ { "categoria": "pb", "numero": 1, "fecha": "2026-08-16" } ],
  "partidos":  [ {
    "id": "", "categoria": "pb", "fase": "regular", "jornada": 1,
    "fecha": "2026-08-16", "hora": "10:00",
    "local": "", "visitante": "", "golesLocal": 3, "golesVisitante": 1,
    "estado": "jugado", "fechaAnterior": "", "nota": "",
    "goles": [ { "jugador": "", "cantidad": 2 } ]
  } ]
}
```

- `fase` puede ser `regular`, `semifinal` o `final`. Solo la fase regular cuenta para la tabla de posiciones, para el goleo y para la mejor portería. Los goles de semifinales y final se muestran en el calendario y en la ficha del jugador, pero marcados como que no cuentan para los premios.
- `estado` puede ser `programado`, `jugado`, `pospuesto` o `suspendido`.
- `ajustePuntos` sirve para sanciones o bonos: se suma directo a los puntos del equipo.
- Los desempates de la tabla van en este orden: puntos, diferencia de goles, goles a favor.
- Todos los partidos se juegan en la misma sede, así que no hay campo de cancha.
- El `data.json` que viene incluido trae **datos de ejemplo** con equipos y jugadores inventados. Bórralos desde el panel o empieza con *Empezar de cero*.

---

## 5. Respaldos

Cada cambio queda como un commit en GitHub, así que el historial completo está guardado y puedes volver a cualquier versión anterior desde la pestaña *History* del repositorio. Aun así, de vez en cuando descarga un `data.json` y guárdalo aparte.
