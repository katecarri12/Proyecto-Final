# Proyecto-Final
#Juego de interrogación.

# ============================================================
#  INTERROGATORIO CRIMINAL
# ============================================================

import ipywidgets as widgets
from IPython.display import display, clear_output, HTML
import random
import json

# ─────────────────────────────────────────────
#  DATOS DEL JUEGO
# ─────────────────────────────────────────────

SUSPECTS = [
    {
        "id": 0,
        "name": "Viktor 'El Sombrío' Raines",
        "crime": "Robo al Banco Central — $4.2M desaparecidos",
        "avatar": "🧊",
        "avatar_color": "#1a3a5c",
        "personality": "frio",
        "weakness": "evidencia",
        "strong": "engano",
        "story": (
            "Viktor fue jefe de seguridad del Banco Central durante 11 años. "
            "Tres noches atrás, alguien usó sus credenciales para vaciar la bóveda secundaria. "
            "Él jura que se las robaron. Las cámaras dicen otra cosa."
        ),
        "flavor": [
            "No tengo nada que decirle, detective.",
            "Usted no tiene pruebas. Solo suposiciones.",
            "Mis abogados van a destruirlo en el tribunal.",
            "¿Eso es todo lo que tiene? Qué decepcionante.",
            "Sigo sin verle el punto a esta conversación.",
            "Haga lo que quiera. No voy a hablar.",
            "Conozco mis derechos mejor que usted.",
        ],
        "confession": (
            "...\n\n"
            "Está bien. Está bien, detective. Ya basta.\n\n"
            "Sí. Fui yo. Llevaba tres años planeándolo — cada acceso, cada turno de guardia, "
            "cada cámara ciega. No fue un robo, fue una extracción quirúrgica. "
            "Nadie en ese banco me valoraba lo que valía. Once años cubriendo sus errores, "
            "once años siendo el que apagaba los incendios sin que nadie lo supiera.\n\n"
            "El dinero está distribuido en cuatro cuentas. Les daré los números. "
            "Pero quiero que quede claro en el expediente: no me arrepiento. "
            "Me deben mucho más de lo que tomé."
        ),
        "evidences": [
            {"id": "e1", "name": "Grabación cámara 4B",        "icon": "📹", "desc": "Video del pasillo sur: alguien con el perfil de Viktor, 2:17 AM.",         "effect": 18, "strength": "strong"},
            {"id": "e2", "name": "Registro biométrico",         "icon": "🖐️", "desc": "Su huella abrió la bóveda. Hora exacta: 2:22 AM.",                          "effect": 22, "strength": "strong"},
            {"id": "e3", "name": "Transferencia offshore",      "icon": "💸", "desc": "$180K movidos a una cuenta en las Islas Caimán esa misma noche.",           "effect": 16, "strength": "med"},
            {"id": "e4", "name": "Testimonio del guardia",      "icon": "📋", "desc": "Dice que Viktor llegó 'a revisar algo' sin registrarse. Poco convincente.", "effect": 8,  "strength": "weak"},
        ],
    },
    {
        "id": 1,
        "name": "Carla 'La Araña' Mendez",
        "crime": "Triple homicidio en el Puerto Sur",
        "avatar": "🕷️",
        "avatar_color": "#5c1a3a",
        "personality": "manipuladora",
        "weakness": "presion",
        "strong": "silencio",
        "story": (
            "Tres estibadores aparecieron muertos en el muelle 7. "
            "Todos habían testificado contra la organización de Carla hace dos años. "
            "Ella tiene coartada perfecta — demasiado perfecta."
        ),
        "flavor": [
            "¿Detective? Usted parece cansado. ¿Duerme bien?",
            "Soy una mujer inocente. Esto es acoso.",
            "Tengo amigos en lugares que usted ni imagina.",
            "Siga jugando, detective. Veremos quién se rinde primero.",
            "Qué interesante teoría. Completamente equivocada, pero interesante.",
            "¿Sabe qué le pasó al último detective que me molestó?",
            "Me aburre esta conversación.",
        ],
        "confession": (
            "...\n\n"
            "Muy bien, detective. Muy bien. Me quito el sombrero.\n\n"
            "Sí, fui yo quien dio la orden. Los tres sabían demasiado y empezaban a hablar "
            "con gente que no debía escucharlos. En este negocio, los errores se corrigen "
            "rápido o te cuestan todo.\n\n"
            "No me ensucié las manos — para eso tengo gente. Pero la decisión fue mía. "
            "Siempre fue mía. Eso es lo único que le voy a confirmar esta noche.\n\n"
            "El resto... que lo pruebe usted."
        ),
        "evidences": [
            {"id": "e1", "name": "Análisis de fibra de tela",   "icon": "🧵", "desc": "Fibra de seda importada — la misma marca que Carla usa — hallada en el muelle.", "effect": 14, "strength": "med"},
            {"id": "e2", "name": "Llamada interceptada",         "icon": "📞", "desc": "'El problema del muelle ya está resuelto.' Voz compatible. 11:50 PM.",          "effect": 20, "strength": "strong"},
            {"id": "e3", "name": "Registro vehicular",           "icon": "🚗", "desc": "Su auto fue visto en la zona a las 11:30 PM. Ella dice que dormía.",            "effect": 16, "strength": "strong"},
            {"id": "e4", "name": "Historial de coartadas",       "icon": "📁", "desc": "Absuelta dos veces por 'falta de pruebas'. El patrón es llamativo.",            "effect": 7,  "strength": "weak"},
        ],
    },
    {
        "id": 2,
        "name": "Marco 'El Nerviosa' Deluca",
        "crime": "Red de extorsión — 23 víctimas",
        "avatar": "😰",
        "avatar_color": "#1a4a1a",
        "personality": "nervioso",
        "weakness": "engano",
        "strong": "presion",
        "story": (
            "Durante tres años, negocios del barrio portuario pagaron 'seguro' "
            "a cambio de no ser incendiados. Ocho sí ardieron. "
            "Marco siempre estaba en otro lado. O eso decía."
        ),
        "flavor": [
            "Yo... yo no sé de qué me habla.",
            "Eso no es verdad. O sea... no exactamente.",
            "¿Puedo irme ya? Tengo cosas que hacer.",
            "Mire, hay mucha gente peor que yo ahí afuera.",
            "No firmé nada. No hice nada. ¿Entiende?",
            "Espere, espere. Eso que dice no es como parece.",
            "Necesito agua. ¿Me puede dar agua?",
        ],
        "confession": (
            "Okay, okay, para. Por favor.\n\n"
            "Sí. Sí fui yo. Pero escúcheme — no empecé queriendo quemar nada. "
            "Solo necesitaba que pagaran. Al principio todos pagaban y no había problema. "
            "Pero el del almacén 4 se negó, habló con otros, y entonces... "
            "se me fue de las manos. Los incendios no eran el plan.\n\n"
            "Mire, yo le puedo dar nombres. Gente más grande que yo está detrás de esto. "
            "Yo era solo el cobrador. Si me ayuda, yo los entrego a todos. "
            "Tengo los libros. Tengo los registros. Todo.\n\n"
            "¿Hacemos un trato o no?"
        ),
        "evidences": [
            {"id": "e1", "name": "Libro de cuentas cifrado",    "icon": "📒", "desc": "Encontrado en su auto. Nombres, montos, fechas. 23 entradas activas.",         "effect": 24, "strength": "strong"},
            {"id": "e2", "name": "Mensajes borrados recuperados","icon": "💬", "desc": "'Turno 3 no pagó. Ya saben qué toca.' Recuperados por forense.",              "effect": 19, "strength": "strong"},
            {"id": "e3", "name": "Fotos del incendio #4",        "icon": "🔥", "desc": "Marco aparece en el fondo, observando. Sonríe levemente.",                     "effect": 15, "strength": "med"},
            {"id": "e4", "name": "Depósitos en efectivo",        "icon": "💰", "desc": "Cada semana, montos irregulares. Sin origen declarado.",                       "effect": 9,  "strength": "weak"},
        ],
    },
]

CPU_RESPONSES = {
    "frio": {
        "presion":  {"desc": "Se inclina hacia atrás. Tu presión no lo mueve.",        "mod": 8},
        "engano":   {"desc": "Sonríe levemente. Detectó la trampa.",                   "mod": 12},
        "silencio": {"desc": "El silencio lo incomoda menos de lo esperado.",          "mod": 3},
    },
    "manipuladora": {
        "presion":  {"desc": "Te devuelve la presión con una sonrisa fría.",           "mod": 10},
        "engano":   {"desc": "Juega al mismo juego. Difícil de leer.",                 "mod": 6},
        "silencio": {"desc": "El silencio la frustra. Empieza a hablar sola.",         "mod": 8},
    },
    "nervioso": {
        "presion":  {"desc": "Se cierra en banda. El miedo lo paraliza.",              "mod": 5},
        "engano":   {"desc": "Cae en la trampa. Empieza a contradecirse.",             "mod": 2},
        "silencio": {"desc": "El silencio lo desespera. Habla de más.",                "mod": 4},
    },
}

EVIDENCE_REACTIONS = {
    "frio": {
        "strong": "Mira la prueba en silencio. Su mandíbula se tensa.",
        "med":    "Examina el documento. Nada en su rostro cambia.",
        "weak":   "Le echa un vistazo breve. 'Eso no prueba nada.'",
        "mod": 4,
    },
    "manipuladora": {
        "strong": "La prueba la desestabiliza visiblemente. Cambia de postura.",
        "med":    "Estudia cada detalle. Por primera vez, parece incómoda.",
        "weak":   "Sonríe. 'Su evidencia es un chiste, detective.'",
        "mod": 2,
    },
    "nervioso": {
        "strong": "Se le van los ojos a la prueba. Empieza a sudar.",
        "med":    "Traga saliva. Sus manos tiemblan ligeramente.",
        "weak":   "'Eso... eso podría ser cualquiera.'",
        "mod": 3,
    },
}

TACTIC_BASE = {"presion": 12, "engano": 14, "silencio": 8}
TACTIC_RISK = {"presion": 0.25, "engano": 0.30, "silencio": 0.15}

# ─────────────────────────────────────────────
#  ESTADO GLOBAL
# ─────────────────────────────────────────────

class GameState:
    """Mantiene el estado completo de una partida y contiene toda la lógica de juego."""

    def __init__(self):
        self.suspect = None
        self.pressure = 0
        self.suspicion = 0
        self.turns = 8
        self.over = False
        self.won = False
        self.tactic_count = {}
        self.used_turns = 0
        self.evidence_used = set()

    def init(self, suspect_data):
        import copy
        self.suspect = copy.deepcopy(suspect_data)
        self.pressure = 0
        self.suspicion = 0
        self.turns = 8
        self.over = False
        self.won = False
        self.tactic_count = {"presion": 0, "engano": 0, "silencio": 0, "evidencia": 0}
        self.used_turns = 0
        self.evidence_used = set()

    def apply_tactic(self, key):
        """Aplica una táctica no-evidencia, actualiza el estado y devuelve el HTML del resultado."""
        s = self.suspect
        personality = s["personality"]
        base = TACTIC_BASE[key]
        effect = base

        self.tactic_count[key] += 1
        repeat = min((self.tactic_count[key] - 1) * 4, 16)

        resp = CPU_RESPONSES[personality][key]
        cpu_desc = resp["desc"]
        cpu_mod = resp["mod"]

        if key == s["weakness"]:
            effect = int(effect * 1.6)
            result_class, result_label = "col-good", "✅ PUNTO DÉBIL"
        elif key == s["strong"]:
            effect = int(effect * 0.3)
            result_class, result_label = "col-bad", "🛡️ RESISTE"
        else:
            result_class, result_label = "col-neu", "➡️ EFECTO"

        effect = max(0, effect - repeat)
        if random.random() < TACTIC_RISK[key]:
            effect = -int(base * 0.5)
            result_class, result_label = "col-bad", "💥 BACKFIRE"
            cpu_desc = "Tu táctica se vuelve en tu contra. El sospechoso gana terreno."

        self.suspicion = min(100, self.suspicion + cpu_mod)
        self.pressure = max(0, min(100, self.pressure + effect))
        self.turns -= 1
        self.used_turns += 1

        flavor = random.choice(s["flavor"])
        sign = "+" if effect >= 0 else ""
        rp_note = f' <span style="color:#333;font-size:10px;">(-{repeat} por repetición)</span>' if repeat > 0 else ""

        return f"""
        <div class="dialog-box">
          <div class="db-result {result_class}">{result_label}: {sign}{effect}% presión{rp_note}</div>
          <div class="db-cpu">{cpu_desc}</div>
          <div class="db-flavor">"{flavor}"</div>
        </div>"""

    def apply_evidence(self, ev_id):
        """Aplica una evidencia elegida, actualiza el estado y devuelve el HTML del resultado."""
        s = self.suspect
        personality = s["personality"]
        ev = next((e for e in s["evidences"] if e["id"] == ev_id), None)
        if not ev or ev_id in self.evidence_used:
            return ""

        self.evidence_used.add(ev_id)
        self.tactic_count["evidencia"] += 1
        repeat = min((self.tactic_count["evidencia"] - 1) * 3, 12)
        effect = max(0, ev["effect"] - repeat)

        strength_map = {
            "strong": ("col-good", "🔑 PRUEBA CLAVE"),
            "med":    ("col-neu",  "📄 EVIDENCIA SÓLIDA"),
            "weak":   ("col-bad",  "📎 EVIDENCIA DÉBIL"),
        }
        result_class, result_label = strength_map[ev["strength"]]

        reactions = EVIDENCE_REACTIONS[personality]
        cpu_desc = reactions[ev["strength"]]

        self.pressure = max(0, min(100, self.pressure + effect))
        self.suspicion = min(100, self.suspicion + reactions["mod"])
        self.turns -= 1
        self.used_turns += 1

        flavor = random.choice(s["flavor"])
        sign = "+" if effect >= 0 else ""
        rp_note = f' <span style="color:#333;font-size:10px;">(-{repeat} acumulado)</span>' if repeat > 0 else ""

        return f"""
        <div class="dialog-box">
          <div class="db-result {result_class}">{result_label}: {sign}{effect}% presión{rp_note}</div>
          <div class="db-cpu">{cpu_desc}</div>
          <div class="db-flavor">"{flavor}"</div>
        </div>"""

    def check_end(self):
        """Evalúa si la partida terminó y actualiza los flags correspondientes."""
        if self.pressure >= 100:
            self.over = True
            self.won = True
        elif self.suspicion >= 100 or self.turns <= 0:
            self.over = True
            self.won = False
        return self.over

G = GameState()

# ─────────────────────────────────────────────
#  HTML TEMPLATES
# ─────────────────────────────────────────────

CSS = """
<style>
@import url('https://fonts.googleapis.com/css2?family=Courier+Prime:wght@400;700&family=Special+Elite&display=swap');

.ic { font-family:'Courier Prime',monospace; color:#e0d5c1; background:#0d0d0d;
      border:1px solid #2a2a2a; border-radius:6px; padding:20px; max-width:680px; }

/* --- INTRO --- */
.ic-stamp { font-size:10px; letter-spacing:3px; color:#555; text-transform:uppercase; margin-bottom:6px; }
.ic-title { font-family:'Special Elite',cursive; color:#cc0000; font-size:26px;
            letter-spacing:4px; text-shadow:0 0 8px rgba(200,0,0,0.4); margin-bottom:2px; }
.ic-subtitle { font-size:12px; color:#555; letter-spacing:2px; margin-bottom:16px; }
.ic-divider { border:none; border-top:1px solid #1e1e1e; margin:14px 0; }
.ic-story { background:#111; border-left:3px solid #8b0000; padding:12px 16px;
            font-size:13px; line-height:1.8; color:#999; margin-bottom:16px; border-radius:0 4px 4px 0; }
.ic-story b { color:#c9b99a; }
.ic-section-label { font-size:10px; letter-spacing:2px; color:#555; text-transform:uppercase; margin-bottom:10px; }
.suspect-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:10px; margin-bottom:16px; }
.suspect-card { border:1px solid #2a2a2a; border-radius:4px; padding:12px; cursor:pointer;
                background:#111; transition:border-color 0.15s; }
.suspect-card:hover { border-color:#5a0000; }
.suspect-card.sel { border:2px solid #cc0000; }
.sc-avatar { font-size:24px; margin-bottom:6px; }
.sc-name { font-size:12px; font-weight:700; margin-bottom:3px; color:#c9b99a; }
.sc-crime { font-size:10px; color:#555; line-height:1.4; }

/* --- GAME --- */
.ic-header { display:flex; justify-content:space-between; align-items:flex-start;
             margin-bottom:14px; }
.ih-name { font-size:15px; font-weight:700; color:#c9b99a; }
.ih-charge { font-size:11px; color:#555; margin-top:2px; }
.turns-badge { font-size:11px; color:#666; background:#111; padding:4px 10px;
               border:1px solid #2a2a2a; border-radius:3px; white-space:nowrap; }
.meter-row { display:flex; align-items:center; gap:8px; font-size:11px;
             color:#666; margin-bottom:6px; }
.m-label { width:70px; text-align:right; flex-shrink:0; }
.m-track { flex:1; height:6px; background:#1a1a1a; border-radius:3px; overflow:hidden;
           border:1px solid #222; }
.m-fill { height:100%; border-radius:3px; }
.m-val { width:30px; font-size:11px; font-weight:700; }

.dialog-box { background:#111; border-left:3px solid #3a0000; padding:12px 16px;
              margin:12px 0; border-radius:0 4px 4px 0; min-height:80px; }
.db-result { font-size:12px; font-weight:700; margin-bottom:6px; }
.db-cpu { font-size:11px; color:#555; margin-bottom:8px; }
.db-flavor { font-size:13px; color:#c9b99a; font-style:italic; line-height:1.6; }
.col-good { color:#4caf50; }
.col-bad  { color:#f44336; }
.col-neu  { color:#ff9800; }

.tactics-grid { display:grid; grid-template-columns:1fr 1fr; gap:8px; margin-top:12px; }
.tac-btn { border:1px solid #2a2a2a; border-radius:4px; padding:10px 12px;
           background:#111; cursor:pointer; text-align:left; display:block; width:100%; }
.tac-btn:hover { border-color:#5a0000; background:#161616; }
.tac-btn:disabled { opacity:0.35; cursor:default; }
.tac-btn.ev-btn { border-color:#1a3a5c; }
.tac-btn.ev-btn:hover { border-color:#2a5a8c; background:#0d1a2a; }
.tac-label { font-size:13px; font-weight:700; color:#c9b99a; margin-bottom:3px; }
.tac-desc  { font-size:10px; color:#555; line-height:1.4; }
.tip { font-size:10px; color:#333; text-align:center; margin-top:12px; }

/* --- EVIDENCE PANEL --- */
.ev-panel { }
.ev-panel-title { font-size:13px; font-weight:700; color:#c9b99a; margin-bottom:4px; }
.ev-panel-sub   { font-size:10px; color:#555; margin-bottom:12px; }
.ev-item { border:1px solid #2a2a2a; border-radius:4px; padding:10px 12px;
           cursor:pointer; margin-bottom:6px; display:flex; gap:10px; align-items:flex-start;
           background:#111; }
.ev-item:hover { border-color:#5a0000; background:#161616; }
.ev-item.used  { opacity:0.35; cursor:default; }
.ev-icon  { font-size:20px; flex-shrink:0; }
.ev-name  { font-size:12px; font-weight:700; color:#c9b99a; margin-bottom:3px; }
.ev-desc  { font-size:11px; color:#666; line-height:1.4; }
.ev-badge { font-size:10px; padding:1px 8px; border-radius:99px; display:inline-block; margin-top:4px; }
.badge-s { background:#1a3a1a; color:#4caf50; }
.badge-m { background:#3a2a00; color:#ff9800; }
.badge-w { background:#3a0000; color:#f44336; }
.badge-u { background:#1a1a1a; color:#555; }

/* --- GAME OVER --- */
.go-title { font-family:'Special Elite',cursive; font-size:24px; text-align:center; }
.go-sub   { font-size:13px; color:#888; text-align:center; line-height:1.6;
            max-width:400px; margin:0 auto 16px; }
.go-stats { display:grid; grid-template-columns:repeat(3,1fr); gap:8px; margin:12px 0 16px; }
.go-stat  { background:#111; border:1px solid #2a2a2a; border-radius:4px;
            padding:10px; text-align:center; }
.go-stat-label { font-size:10px; color:#555; margin-bottom:4px; }
.go-stat-val   { font-size:18px; font-weight:700; color:#c9b99a; }
</style>
"""

def bar_html(pct, color):
    return f'<div class="m-track"><div class="m-fill" style="width:{pct}%;background:{color};"></div></div>'

def pressure_color(p):
    return "#cc0000" if p < 50 else ("#ff6600" if p < 85 else "#4caf50")

def suspicion_color(s):
    return "#4caf50" if s < 50 else ("#ff9800" if s < 75 else "#f44336")

def turns_color(t):
    return "#4caf50" if t > 4 else ("#ff9800" if t > 2 else "#f44336")

# ─────────────────────────────────────────────
#  PANTALLAS
# ─────────────────────────────────────────────

def render_intro(out, selected_id, on_select, on_start):
    cards_html = ""
    for s in SUSPECTS:
        sel_class = "sel" if s["id"] == selected_id else ""
        cards_html += f"""
        <div class="suspect-card {sel_class}" style="cursor:pointer;" data-id="{s['id']}">
          <div class="sc-avatar">{s['avatar']}</div>
          <div class="sc-name">{s['name']}</div>
          <div class="sc-crime">{s['crime']}</div>
        </div>"""

    html = CSS + f"""
    <div class="ic">
      <div class="ic-stamp">Unidad de crímenes graves — Expediente activo</div>
      <div class="ic-title">🔫 INTERROGATORIO</div>
      <div class="ic-subtitle">Ciudad de Puerto Oscuro · 02:40 AM</div>
      <hr class="ic-divider">
      <div class="ic-story">
        <b>Tres noches atrás</b>, algo salió muy mal en esta ciudad.
        Dependiendo del sospechoso que elijas, enfrentarás una historia diferente:
        un robo millonario, tres muertes sin testigos, o una red de extorsión que
        opera en silencio desde hace años.<br><br>
        Tienes <b>8 turnos</b> antes de que llegue el abogado.
        Cuatro tácticas disponibles. Solo una forma de ganar:
        quebrar al sospechoso antes de que se cierre.
      </div>
      <hr class="ic-divider">
      <div class="ic-section-label">Selecciona un sospechoso</div>
      <div class="suspect-grid">{cards_html}</div>
      <div style="font-size:11px;color:#555;text-align:center;margin-top:4px;">
        {'↑ Elige un sospechoso para comenzar' if selected_id is None else ''}
      </div>
    </div>
    """

    with out:
        clear_output(wait=True)
        display(HTML(html))

def render_game(out, dialog_html=""):
    s = G.suspect
    p = G.pressure
    su = G.suspicion
    t = G.turns

    pc = pressure_color(p)
    sc = suspicion_color(su)
    tc = turns_color(t)

    if not dialog_html:
        dialog_html = f"""
        <div class="dialog-box">
          <div class="db-flavor">
            El detective entra a la sala. {s['story']}<br><br>
            El sospechoso te mira en silencio, esperando tu primer movimiento.
          </div>
        </div>"""

    ev_used_badges = ""
    for ev in s["evidences"]:
        if ev["id"] in G.evidence_used:
            ev_used_badges += f'<span style="font-size:10px;color:#333;margin-right:6px;">{ev["icon"]} usada</span>'

    html = CSS + f"""
    <div class="ic">
      <div class="ic-header">
        <div>
          <div class="ih-name">{s['name']}</div>
          <div class="ih-charge">{s['crime']}</div>
        </div>
        <div style="text-align:right;">
          <div class="turns-badge" style="color:{tc};">⏱ Turnos: <b>{t}</b></div>
        </div>
      </div>

      <div class="meter-row">
        <div class="m-label">🔥 Presión</div>
        {bar_html(p, pc)}
        <div class="m-val" style="color:{pc};">{p}%</div>
      </div>
      <div class="meter-row">
        <div class="m-label">🛡️ Resistencia</div>
        {bar_html(su, sc)}
        <div class="m-val" style="color:{sc};">{su}%</div>
      </div>

      {dialog_html}

      <div class="tactics-grid">
        <button class="tac-btn" id="b-presion">
          <div class="tac-label">⚡ Presión</div>
          <div class="tac-desc">Intimidación directa. Golpea la mesa. Eleva la voz.</div>
        </button>
        <button class="tac-btn" id="b-engano">
          <div class="tac-label">🎭 Engaño</div>
          <div class="tac-desc">Finges tener más pruebas. Alto riesgo, alto impacto.</div>
        </button>
        <button class="tac-btn ev-btn" id="b-evidencia">
          <div class="tac-label">🔍 Evidencia</div>
          <div class="tac-desc">Elige qué prueba mostrarle al sospechoso.</div>
        </button>
        <button class="tac-btn" id="b-silencio">
          <div class="tac-label">😶 Silencio</div>
          <div class="tac-desc">Te callas. Los nervios trabajan solos.</div>
        </button>
      </div>
      <div class="tip">⚠ La CPU adapta su defensa si repites la misma táctica</div>
    </div>
    """
    with out:
        clear_output(wait=True)
        display(HTML(html))

def render_evidence_panel(out_ev):
    s = G.suspect
    strength_map = {
        "strong": ("badge-s", "Alta eficacia"),
        "med":    ("badge-m", "Eficacia media"),
        "weak":   ("badge-w", "Eficacia baja"),
    }

    # List comprehension: genera el bloque HTML de cada evidencia en una sola expresión
    items_html = "".join([
        f"""<div class="ev-item {'used' if ev['id'] in G.evidence_used else ''}" data-ev="{ev['id']}">
          <div class="ev-icon">{ev['icon']}</div>
          <div>
            <div class="ev-name">{ev['name']}</div>
            <div class="ev-desc">{ev['desc']}</div>
            <span class="ev-badge {strength_map[ev['strength']][0]}">{strength_map[ev['strength']][1]}</span>
            {'<span class="ev-badge badge-u">Ya usada</span>' if ev['id'] in G.evidence_used else ''}
          </div>
        </div>"""
        for ev in s["evidences"]
    ])

    html = CSS + f"""
    <div class="ic" style="margin-top:8px;">
      <div class="ev-panel-title">📂 Expediente de evidencias</div>
      <div class="ev-panel-sub">Selecciona una prueba para mostrarle al sospechoso</div>
      {items_html}
    </div>"""

    with out_ev:
        clear_output(wait=True)
        display(HTML(html))

def render_gameover(out):
    s = G.suspect
    p = G.pressure
    su = G.suspicion

    if G.won:
        title = "🔒 CONFESIÓN OBTENIDA"
        title_color = "#4caf50"
        sub = f"Caso cerrado. {s['name']} será procesado por la justicia."

        # List comprehension: convierte cada párrafo de la confesión en un <p> HTML
        confession_paragraphs = "".join([
            f'<p style="margin:0 0 10px 0;">{p_text}</p>'
            for p_text in s.get("confession", "").strip().split("\n\n")
            if p_text.strip()
        ])

        confession_block = f"""
        <div style="margin:16px 0 0 0;">
          <div style="font-size:10px;letter-spacing:2px;color:#555;text-transform:uppercase;margin-bottom:10px;text-align:left;">
            📝 Transcripción — Declaración espontánea
          </div>
          <div style="background:#0a1a0a;border:1px solid #1a3a1a;border-left:3px solid #4caf50;
                      border-radius:0 4px 4px 0;padding:14px 16px;font-size:13px;
                      line-height:1.8;color:#a8d5a2;font-style:italic;text-align:left;">
            {confession_paragraphs}
          </div>
          <div style="font-size:10px;color:#333;text-align:right;margin-top:6px;">
            — {s['name']} · Sala de interrogatorio B · 0{3 + G.used_turns}:1{G.used_turns % 10} AM
          </div>
        </div>"""

    else:
        title = "⚖️ CASO PERDIDO"
        title_color = "#f44336"
        if su >= 100:
            sub = f"{s['name']} se cerró completamente. El interrogatorio fracasó."
        else:
            sub = f"El abogado defensor ha llegado. {s['name']} queda libre por ahora."
        confession_block = ""

    html = CSS + f"""
    <div class="ic">
      <div class="go-title" style="color:{title_color};margin-bottom:8px;text-align:center;">{title}</div>
      <div class="go-sub" style="text-align:center;">{sub}</div>
      <div class="go-stats">
        <div class="go-stat"><div class="go-stat-label">Presión final</div><div class="go-stat-val">{p}%</div></div>
        <div class="go-stat"><div class="go-stat-label">Turnos usados</div><div class="go-stat-val">{G.used_turns}/8</div></div>
        <div class="go-stat"><div class="go-stat-label">Resistencia</div><div class="go-stat-val">{su}%</div></div>
      </div>
      {confession_block}
    </div>"""

    with out:
        clear_output(wait=True)
        display(HTML(html))

# ─────────────────────────────────────────────
#  LANZADOR DEL JUEGO
# ─────────────────────────────────────────────

def launch_game():
    # ── Outputs ──
    out_intro  = widgets.Output()
    out_game   = widgets.Output()
    out_ev     = widgets.Output()
    out_go     = widgets.Output()

    state = {"selected": None, "phase": "intro"}  # fases: intro | game | evidence | gameover

    # ── Botones de sospechoso (intro) ──
    suspect_btns = []
    for s in SUSPECTS:
        btn = widgets.Button(
            description=f"{s['avatar']} {s['name'].split(' ')[0]}",
            layout=widgets.Layout(width="200px", height="70px"),
            style={"button_color": "#1a1a1a", "font_weight": "bold"},
        )
        btn.suspect_id = s["id"]
        suspect_btns.append(btn)

    start_btn = widgets.Button(
        description="Entrar a sala de interrogatorio →",
        layout=widgets.Layout(width="360px", height="42px"),
        style={"button_color": "#3a0000", "font_weight": "bold"},
        disabled=True,
    )

    # ── Botones de táctica (juego) ──
    tac_btns = {
        "presion":   widgets.Button(description="⚡ PRESIÓN",   layout=widgets.Layout(width="160px", height="54px"), style={"button_color": "#2a0000", "font_weight": "bold"}),
        "engano":    widgets.Button(description="🎭 ENGAÑO",    layout=widgets.Layout(width="160px", height="54px"), style={"button_color": "#1a1a3a", "font_weight": "bold"}),
        "evidencia": widgets.Button(description="🔍 EVIDENCIA", layout=widgets.Layout(width="160px", height="54px"), style={"button_color": "#001a2a", "font_weight": "bold"}),
        "silencio":  widgets.Button(description="😶 SILENCIO",  layout=widgets.Layout(width="160px", height="54px"), style={"button_color": "#1a1a00", "font_weight": "bold"}),
    }
    tac_row = widgets.HBox(list(tac_btns.values()), layout=widgets.Layout(gap="8px", margin="8px 0"))

    # ── Botones de evidencia (panel) ──
    ev_btns = []
    for s in SUSPECTS:
        row = []
        for ev in s["evidences"]:
            b = widgets.Button(
                description=f"{ev['icon']} {ev['name']}",
                layout=widgets.Layout(width="300px", height="42px"),
                style={"button_color": "#111"},
            )
            b.ev_id = ev["id"]
            row.append(b)
        ev_btns.append(row)

    close_ev_btn = widgets.Button(
        description="✖ Cerrar expediente",
        layout=widgets.Layout(width="200px", height="36px"),
        style={"button_color": "#1a1a1a"},
    )

    # ── Botones de game over ──
    replay_btn = widgets.Button(
        description="🔄 Nuevo caso",
        layout=widgets.Layout(width="200px", height="42px"),
        style={"button_color": "#1a1a1a", "font_weight": "bold"},
    )

    # ── Contenedor principal ──
    main_box = widgets.VBox([], layout=widgets.Layout(max_width="700px"))

    def set_phase_intro():
        state["phase"] = "intro"
        render_intro(out_intro, state["selected"], None, None)
        main_box.children = [
            out_intro,
            widgets.HBox(suspect_btns, layout=widgets.Layout(gap="8px", flex_wrap="wrap", margin="8px 0")),
            start_btn,
        ]

    def set_phase_game(dialog_html=""):
        state["phase"] = "game"
        render_game(out_game, dialog_html)
        out_ev.clear_output()
        out_go.clear_output()
        main_box.children = [out_game, tac_row]

    def set_phase_evidence():
        state["phase"] = "evidence"
        render_game(out_game)  # mantener el estado visible arriba
        render_evidence_panel(out_ev)
        current_ev_btns = ev_btns[G.suspect["id"]]
        # deshabilitar ya usados
        for b in current_ev_btns:
            b.disabled = b.ev_id in G.evidence_used
        ev_col = widgets.VBox(
            current_ev_btns + [close_ev_btn],
            layout=widgets.Layout(gap="6px", margin="8px 0"),
        )
        main_box.children = [out_game, out_ev, ev_col]

    def set_phase_gameover():
        state["phase"] = "gameover"
        render_gameover(out_go)
        main_box.children = [out_go, replay_btn]

    # ── Handlers intro ──
    def on_suspect_select(b):
        state["selected"] = b.suspect_id
        for sb in suspect_btns:
            sb.style.button_color = "#3a0000" if sb.suspect_id == b.suspect_id else "#1a1a1a"
        start_btn.disabled = False

    for sb in suspect_btns:
        sb.on_click(on_suspect_select)

    def on_start(b):
        sid = state["selected"]
        G.init(SUSPECTS[sid])
        for k in tac_btns:
            tac_btns[k].disabled = False
        set_phase_game()

    start_btn.on_click(on_start)

    # ── Handlers táctica ──
    def make_tactic_handler(key):
        def handler(b):
            if G.over or state["phase"] != "game":
                return
            if key == "evidencia":
                set_phase_evidence()
                return
            dialog = G.apply_tactic(key)
            if G.check_end():
                render_game(out_game, dialog)
                for k in tac_btns:
                    tac_btns[k].disabled = True
                set_phase_gameover()
            else:
                set_phase_game(dialog)
        return handler

    for key, btn in tac_btns.items():
        btn.on_click(make_tactic_handler(key))

    # ── Handlers evidencia ──
    def make_ev_handler(ev_id):
        def handler(b):
            if G.over or ev_id in G.evidence_used:
                return
            dialog = G.apply_evidence(ev_id)
            if G.check_end():
                render_game(out_game, dialog)
                for k in tac_btns:
                    tac_btns[k].disabled = True
                out_ev.clear_output()
                set_phase_gameover()
            else:
                set_phase_game(dialog)
        return handler

    for suspect_ev_btns in ev_btns:
        for b in suspect_ev_btns:
            b.on_click(make_ev_handler(b.ev_id))

    close_ev_btn.on_click(lambda b: set_phase_game())

    # ── Handler replay ──
    def on_replay(b):
        state["selected"] = None
        for sb in suspect_btns:
            sb.style.button_color = "#1a1a1a"
        start_btn.disabled = True
        set_phase_intro()

    replay_btn.on_click(on_replay)

    # ── Arrancar ──
    set_phase_intro()
    display(main_box)


# ─────────────────────────────────────────────
#  INICIAR
# ─────────────────────────────────────────────
launch_game()
