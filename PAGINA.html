# app.py
import streamlit as st
from datetime import datetime, date, time, timedelta
import calendar
import re
import uuid
import urllib.parse

st.set_page_config(page_title="Agenda Extensionista", page_icon="🌱", layout="wide")

# -----------------------------
# Helpers (CPF / datas / etc.)
# -----------------------------
def is_valid_cpf(str_cpf: str) -> bool:
    if not str_cpf:
        return False
    cpf = re.sub(r"\D", "", str_cpf)
    if len(cpf) != 11:
        return False
    if re.fullmatch(r"(\d)\1{10}", cpf):
        return False

    def calc_digit(cpf_base: str, weights):
        s = sum(int(d) * w for d, w in zip(cpf_base, weights))
        r = 11 - (s % 11)
        return 0 if r in (10, 11) else r

    d1 = calc_digit(cpf[:9], list(range(10, 1, -1)))
    if d1 != int(cpf[9]):
        return False
    d2 = calc_digit(cpf[:10], list(range(11, 1, -1)))
    if d2 != int(cpf[10]):
        return False
    return True

def format_cpf(cpf: str) -> str:
    d = re.sub(r"\D", "", cpf or "")
    if len(d) != 11:
        return cpf
    return f"{d[0:3]}.{d[3:6]}.{d[6:9]}-{d[9:11]}"

def display_cpf(cpf: str) -> str:
    if not cpf:
        return ""
    d = re.sub(r"\D", "", cpf)
    return format_cpf(d) if len(d) == 11 else cpf

def parse_iso_datetime(iso: str) -> datetime:
    # iso no seu app é "YYYY-MM-DDTHH:MM"
    return datetime.strptime(iso, "%Y-%m-%dT%H:%M")

def fmt_date_br(d: date) -> str:
    return d.strftime("%d/%m/%Y")

def fmt_time_short(dt: datetime) -> str:
    # 8h ou 8h30
    if dt.minute == 0:
        return f"{dt.hour}h"
    return f"{dt.hour}h{dt.minute:02d}"

def month_year_label(d: date) -> str:
    meses = ["janeiro","fevereiro","março","abril","maio","junho","julho","agosto","setembro","outubro","novembro","dezembro"]
    return f"{meses[d.month-1]} de {d.year}"

def today_date() -> date:
    return datetime.now().date()

def is_same_month_year(d1: date, d2: date) -> bool:
    return d1.year == d2.year and d1.month == d2.month

def diff_days_from_today(target: date) -> int:
    return (target - today_date()).days

def wa_link(phone_digits: str, message: str) -> str:
    digits = re.sub(r"\D", "", phone_digits or "")
    if not digits:
        return ""
    base = f"https://wa.me/{digits}"
    if message:
        return base + "?text=" + urllib.parse.quote(message)
    return base

def short_name_first_last_initial(fullname: str) -> str:
    full = re.sub(r"\s+", " ", (fullname or "").strip())
    if not full:
        return "(sem nome)"
    parts = full.split(" ")
    first = parts[0]
    last = parts[-1] if len(parts) > 1 else ""
    initial = (last[0].upper() + ".") if last else ""
    return f"{first} {initial}".strip()

# -----------------------------
# Session State (dados)
# -----------------------------
if "farmers" not in st.session_state:
    st.session_state.farmers = []   # [{id,name,cpf,phone,place,cafExpiry}]
if "visits" not in st.session_state:
    st.session_state.visits = []    # [{id, farmerId, scheduled, status, planNotes, checkIn, doneNotes}]
if "config" not in st.session_state:
    st.session_state.config = {
        "technicianName": "",
        "whatsappTemplate": "",
        "monthlyCommitment": 60
    }
if "cal_anchor" not in st.session_state:
    # primeiro dia do mês corrente
    t = today_date()
    st.session_state.cal_anchor = date(t.year, t.month, 1)

# UI states
if "selected_farmer_id" not in st.session_state:
    st.session_state.selected_farmer_id = None

# -----------------------------
# Data access
# -----------------------------
def farmer_by_id(fid: str):
    return next((f for f in st.session_state.farmers if f["id"] == fid), None)

def visits_for_day(d: date):
    out = []
    for v in st.session_state.visits:
        try:
            dt = parse_iso_datetime(v["scheduled"])
        except Exception:
            continue
        if dt.date() == d:
            out.append(v)
    out.sort(key=lambda x: parse_iso_datetime(x["scheduled"]))
    return out

def month_done_count(month_anchor: date) -> int:
    c = 0
    for v in st.session_state.visits:
        if v.get("status") != "done":
            continue
        dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
        if dt.year == month_anchor.year and dt.month == month_anchor.month:
            c += 1
    return c

def month_scheduled_count(month_anchor: date) -> int:
    c = 0
    for v in st.session_state.visits:
        if v.get("status") != "scheduled":
            continue
        dt = parse_iso_datetime(v["scheduled"])
        if dt.year == month_anchor.year and dt.month == month_anchor.month:
            c += 1
    return c

def has_time_conflict(date_str: str, time_str: str, ignore_visit_id: str | None = None) -> bool:
    target = f"{date_str}T{time_str}"
    for v in st.session_state.visits:
        if v.get("status") != "scheduled":
            continue
        if ignore_visit_id and v["id"] == ignore_visit_id:
            continue
        if v["scheduled"] == target:
            return True
    return False

def has_farmer_scheduled_same_day(fid: str, date_str: str, ignore_visit_id: str | None = None) -> bool:
    for v in st.session_state.visits:
        if v.get("status") != "scheduled":
            continue
        if v.get("farmerId") != fid:
            continue
        if ignore_visit_id and v["id"] == ignore_visit_id:
            continue
        if (v.get("scheduled") or "").startswith(date_str + "T"):
            return True
    return False

def last_done_note_before(fid: str, scheduled_iso: str) -> str:
    target = parse_iso_datetime(scheduled_iso)
    prev = []
    for v in st.session_state.visits:
        if v.get("farmerId") != fid or v.get("status") != "done":
            continue
        dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
        if dt < target:
            prev.append((dt, v.get("doneNotes") or ""))
    if not prev:
        return ""
    prev.sort(key=lambda x: x[0], reverse=True)
    return prev[0][1] or ""

# -----------------------------
# Dialogs (Streamlit)
# -----------------------------
@st.dialog("Agendar / Editar visita", width="large")
def dialog_schedule(date_str: str, mode: str = "schedule", edit_visit_id: str | None = None):
    # mode: "schedule" ou "register"
    d = datetime.strptime(date_str, "%Y-%m-%d").date()
    label = d.strftime("%A, %d/%m/%Y").capitalize()

    st.markdown(f"### {label}")

    # Farmer picker
    farmers = sorted(st.session_state.farmers, key=lambda x: x.get("name","").lower())
    if not farmers:
        st.warning("Nenhum agricultor cadastrado. Cadastre na aba **Cadastrar agricultores**.")
        return

    # default values if editing/registering
    default_fid = None
    default_time = None
    default_plan = ""
    default_done = ""

    if edit_visit_id:
        v = next((x for x in st.session_state.visits if x["id"] == edit_visit_id), None)
        if v:
            default_fid = v.get("farmerId")
            dt = parse_iso_datetime(v["scheduled"])
            date_str = dt.strftime("%Y-%m-%d")
            d = dt.date()
            default_time = dt.strftime("%H:%M")
            default_plan = v.get("planNotes") or ""
            default_done = v.get("doneNotes") or ""

    # Select farmer
    farmer_options = {f'{f["name"]} • {display_cpf(f.get("cpf","")) or "(CPF não informado)"} • {f.get("place","") or "(sem localidade)"}': f["id"] for f in farmers}
    labels = list(farmer_options.keys())

    default_index = 0
    if default_fid:
        for i, lab in enumerate(labels):
            if farmer_options[lab] == default_fid:
                default_index = i
                break

    chosen_label = st.selectbox("Agricultor", labels, index=default_index)
    fid = farmer_options[chosen_label]

    # Date
    chosen_date = st.date_input("Data", value=d)
    date_str2 = chosen_date.strftime("%Y-%m-%d")

    # Time slots (mesmo padrão do HTML)
    def build_slots(start_h, start_m, end_h, end_m):
        slots = []
        cur = datetime.combine(date.today(), time(start_h, start_m))
        end = datetime.combine(date.today(), time(end_h, end_m))
        while cur <= end:
            slots.append(cur.strftime("%H:%M"))
            cur += timedelta(minutes=30)
        return slots

    morning = build_slots(8, 0, 11, 30)
    afternoon = build_slots(13, 0, 18, 0)
    all_slots = [("Manhã (08:00–11:30)", morning), ("Tarde (13:00–18:00)", afternoon)]

    # Busy = horários ocupados (visita = 2h => bloqueia 4 slots de 30 min)
    busy = set()
    if mode == "schedule":
        for v in st.session_state.visits:
            if v.get("status") != "scheduled":
                continue
            if edit_visit_id and v["id"] == edit_visit_id:
                continue
            if not (v.get("scheduled") or "").startswith(date_str2 + "T"):
                continue
            base_dt = parse_iso_datetime(v["scheduled"])
            for i in range(4):
                busy.add((base_dt + timedelta(minutes=30*i)).strftime("%H:%M"))

    # Default time
    if not default_time:
        default_time = ""

    # Build selectable times: show busy as disabled info
    flat_slots = []
    for group_name, slots in all_slots:
        for s in slots:
            tag = " (ocupado)" if (s in busy and s != default_time) else ""
            flat_slots.append((f"{s}{tag} — {group_name}", s, (s in busy and s != default_time)))

    # choose time
    visible_labels = [x[0] for x in flat_slots if not x[2]]
    visible_values = [x[1] for x in flat_slots if not x[2]]

    if default_time and default_time in visible_values:
        idx_time = visible_values.index(default_time)
    else:
        idx_time = 0

    chosen_time = st.selectbox("Horário (ocupados não aparecem)", visible_labels, index=idx_time)
    time_value = visible_values[visible_labels.index(chosen_time)]
    iso = f"{date_str2}T{time_value}"

    # Month warning like your modalFarmerAlert
    month_anchor = date(chosen_date.year, chosen_date.month, 1)
    already_done = any(
        v.get("farmerId") == fid and v.get("status") == "done"
        and parse_iso_datetime(v["checkIn"] or v["scheduled"]).year == month_anchor.year
        and parse_iso_datetime(v["checkIn"] or v["scheduled"]).month == month_anchor.month
        for v in st.session_state.visits
    )
    already_scheduled = any(
        v.get("farmerId") == fid and v.get("status") == "scheduled"
        and (not edit_visit_id or v["id"] != edit_visit_id)
        and parse_iso_datetime(v["scheduled"]).year == month_anchor.year
        and parse_iso_datetime(v["scheduled"]).month == month_anchor.month
        for v in st.session_state.visits
    )

    if mode == "schedule":
        if already_scheduled and already_done:
            st.info("Já agendado e já assessorado neste mês.")
        elif already_scheduled:
            st.info("Já agendado neste mês.")
        elif already_done:
            st.info("Já assessorado neste mês.")
    else:
        if already_done:
            st.info("Observação: este agricultor já tem assessoria registrada neste mês.")

    # Notes
    if mode == "register":
        st.markdown("**Registro de assessoria (pode ser retroativo).**")
        done_notes = st.text_area("Observação da assessoria (máx. 150)", value=default_done, max_chars=150)
        plan_notes = ""
    else:
        plan_notes = st.text_area("Observação (planejamento da assessoria)", value=default_plan)
        done_notes = ""

        # WhatsApp button + preview
        farmer = farmer_by_id(fid)
        if farmer and farmer.get("phone"):
            cfg = st.session_state.config
            nome_ag = farmer.get("name") or "companheiro(a)"
            nome_tec = cfg.get("technicianName") or "o(a) técnico(a)"
            weekday = chosen_date.strftime("%A").capitalize()
            date_only = fmt_date_br(chosen_date)
            time_label = time_value

            default_template = (
                "Olá {NOME_AGRICULTOR}, tudo bem? Aqui é {NOME_TECNICO}, técnico(a) do Convênio Semeando Gestão.\n\n"
                "Podemos agendar uma assessoria no dia *{DATA} ({DIA_SEMANA})* às *{HORA}*?\n\n"
                "Se estiver tudo bem para você, por favor confirme por aqui."
            )
            template = (cfg.get("whatsappTemplate") or "").strip() or default_template
            msg = (template
                   .replace("{NOME_AGRICULTOR}", nome_ag)
                   .replace("{NOME_TECNICO}", nome_tec)
                   .replace("{DATA}", date_only)
                   .replace("{DIA_SEMANA}", weekday)
                   .replace("{HORA}", time_label))
            link = wa_link(farmer["phone"], msg)
            st.link_button("WhatsApp agricultor", link, use_container_width=True)
        else:
            st.caption("WhatsApp: agricultor sem telefone cadastrado.")

    col1, col2, col3 = st.columns([1,1,1])
    with col1:
        if st.button("Cancelar", use_container_width=True):
            st.rerun()

    with col2:
        # Excluir só no modo schedule e editando
        if mode == "schedule" and edit_visit_id:
            if st.button("Excluir", type="secondary", use_container_width=True):
                st.session_state.visits = [v for v in st.session_state.visits if v["id"] != edit_visit_id]
                st.success("Visita excluída.")
                st.rerun()

    with col3:
        label_btn = "Registrar assessoria" if mode == "register" else "Salvar"
        if st.button(label_btn, type="primary", use_container_width=True):
            if mode == "register":
                # se veio de um agendamento existente (edit_visit_id) ou não
                if edit_visit_id:
                    idx = next((i for i, v in enumerate(st.session_state.visits) if v["id"] == edit_visit_id), None)
                    if idx is None:
                        st.error("Visita não encontrada.")
                        return
                    st.session_state.visits[idx] = {
                        **st.session_state.visits[idx],
                        "farmerId": fid,
                        "scheduled": iso,
                        "status": "done",
                        "checkIn": parse_iso_datetime(iso).isoformat(),
                        "doneNotes": (done_notes or "").strip()[:150],
                    }
                else:
                    st.session_state.visits.append({
                        "id": uuid.uuid4().hex,
                        "farmerId": fid,
                        "scheduled": iso,
                        "status": "done",
                        "planNotes": "",
                        "checkIn": parse_iso_datetime(iso).isoformat(),
                        "doneNotes": (done_notes or "").strip()[:150],
                    })
                st.success("Assessoria registrada! 🌱")
                st.rerun()

            else:
                # schedule validations
                if has_farmer_scheduled_same_day(fid, date_str2, ignore_visit_id=edit_visit_id):
                    st.error("Este agricultor já possui visita AGENDADA nesta data.")
                    return
                if has_time_conflict(date_str2, time_value, ignore_visit_id=edit_visit_id):
                    st.error("Este horário já está ocupado neste dia (visita = 2h).")
                    return

                if edit_visit_id:
                    idx = next((i for i, v in enumerate(st.session_state.visits) if v["id"] == edit_visit_id), None)
                    if idx is None:
                        st.error("Visita não encontrada.")
                        return
                    st.session_state.visits[idx] = {
                        **st.session_state.visits[idx],
                        "farmerId": fid,
                        "scheduled": iso,
                        "planNotes": (plan_notes or "").strip(),
                    }
                    st.success("Agendamento atualizado.")
                else:
                    st.session_state.visits.append({
                        "id": uuid.uuid4().hex,
                        "farmerId": fid,
                        "scheduled": iso,
                        "status": "scheduled",
                        "planNotes": (plan_notes or "").strip(),
                        "checkIn": None,
                        "doneNotes": "",
                    })
                    st.success("Visita agendada.")
                st.rerun()

@st.dialog("Agendamentos do dia", width="large")
def dialog_day_details(d: date):
    st.markdown(f"### {d.strftime('%A, %d/%m/%Y').capitalize()}")
    day_visits = visits_for_day(d)

    future = d > today_date()
    allow_register = not future

    if not day_visits:
        st.info("Nenhuma visita cadastrada neste dia.")
    else:
        for v in day_visits:
            farmer = farmer_by_id(v.get("farmerId"))
            name = farmer["name"] if farmer else "(agricultor removido)"
            dt = parse_iso_datetime(v["scheduled"])
            place = (farmer.get("place") if farmer else "") or ""
            status = v.get("status") or "scheduled"

            with st.container(border=True):
                top = st.columns([3,1])
                top[0].markdown(f"**{name}**")
                top[1].markdown(f"**{fmt_time_short(dt)}**")

                meta = []
                if place:
                    meta.append(place)
                meta.append("Registrada" if status == "done" else "Agendada")
                st.caption(" • ".join(meta))

                if v.get("planNotes"):
                    st.write(f"**Planejamento:** {v['planNotes']}")

                btns = st.columns([1,1,1])
                with btns[0]:
                    if st.button("Editar", key=f"edit_{v['id']}"):
                        if status == "done":
                            dialog_schedule(d.strftime("%Y-%m-%d"), mode="register", edit_visit_id=v["id"])
                        else:
                            dialog_schedule(d.strftime("%Y-%m-%d"), mode="schedule", edit_visit_id=v["id"])
                with btns[1]:
                    if status == "scheduled" and allow_register:
                        if st.button("Registrar assessoria", type="primary", key=f"reg_{v['id']}"):
                            dialog_schedule(d.strftime("%Y-%m-%d"), mode="register", edit_visit_id=v["id"])

    st.divider()
    if st.button("Agendar visita", type="primary", use_container_width=True):
        dialog_schedule(d.strftime("%Y-%m-%d"), mode="schedule", edit_visit_id=None)

# -----------------------------
# Header
# -----------------------------
st.markdown("## Agenda Extensionista 🌱")

tabs = st.tabs([
    "Agenda 🌾",
    "Assessorias realizadas 🚜",
    "Caderno de campo 📒",
    "Alertas de CAF ⚠️",
    "Cadastrar agricultores 👨‍🌾",
    "Configurações ⚙️",
])

# -----------------------------
# TAB: Agenda
# -----------------------------
with tabs[0]:
    anchor = st.session_state.cal_anchor
    col_prev, col_mid, col_next = st.columns([1,2,1])
    with col_prev:
        if st.button("◀ Mês anterior"):
            y, m = anchor.year, anchor.month
            m -= 1
            if m == 0:
                m = 12
                y -= 1
            st.session_state.cal_anchor = date(y, m, 1)
            st.rerun()
    with col_mid:
        st.markdown(f"### {month_year_label(anchor)}")
    with col_next:
        if st.button("Próximo mês ▶"):
            y, m = anchor.year, anchor.month
            m += 1
            if m == 13:
                m = 1
                y += 1
            st.session_state.cal_anchor = date(y, m, 1)
            st.rerun()

    # Counters + commitment
    scheduled_count = month_scheduled_count(anchor)
    done_count = month_done_count(anchor)
    goal = max(1, int(st.session_state.config.get("monthlyCommitment") or 60))

    a, b, c = st.columns(3)
    a.metric("Assessorias registradas", done_count)
    b.metric("Assessorias agendadas", scheduled_count)
    c.metric("Compromisso do mês", goal)

    pct_done = min(1.0, done_count / goal)
    st.progress(pct_done)
    if done_count >= goal:
        st.success("Parabéns, compromisso do mês atingido! 🎉🌱")

    st.divider()

    # Calendar grid
    year, month = anchor.year, anchor.month
    cal = calendar.Calendar(firstweekday=6)  # domingo
    weeks = cal.monthdatescalendar(year, month)

    # Header row
    days = ["Dom","Seg","Ter","Qua","Qui","Sex","Sáb"]
    head = st.columns(7)
    for i, dname in enumerate(days):
        head[i].markdown(f"**{dname}**")

    # Month visits map
    by_date = {}
    for v in st.session_state.visits:
        try:
            dt = parse_iso_datetime(v["scheduled"])
        except Exception:
            continue
        by_date.setdefault(dt.date(), []).append(v)

    today = today_date()

    for w in weeks:
        cols = st.columns(7)
        for i, d in enumerate(w):
            day_visits = sorted(by_date.get(d, []), key=lambda x: parse_iso_datetime(x["scheduled"]))
            is_this_month = (d.month == month)
            is_today = (d == today)

            with cols[i]:
                box_title = f"{d.day}"
                if is_today:
                    st.markdown(f"🟩 **{box_title}**")
                else:
                    st.markdown(f"**{box_title}**" if is_this_month else f"<span style='color:#9ca3af'><b>{box_title}</b></span>", unsafe_allow_html=True)

                # show up to 4
                shown = day_visits[:4]
                for v in shown:
                    farmer = farmer_by_id(v.get("farmerId"))
                    name = short_name_first_last_initial(farmer["name"]) if farmer else "(removido)"
                    tag = "✅" if v.get("status") == "done" else "🗓️"
                    st.caption(f"{tag} {name}")

                if len(day_visits) > 4:
                    st.caption(f"+{len(day_visits)-4} mais")

                # Click behavior:
                # - "Detalhes do dia" (equivale a clicar no nome/+X)
                # - "Agendar" (equivale a clicar no vazio)
                btn1, btn2 = st.columns(2)
                if btn1.button("Detalhes", key=f"det_{d.isoformat()}"):
                    dialog_day_details(d)
                if btn2.button("Agendar", key=f"sch_{d.isoformat()}"):
                    dialog_schedule(d.strftime("%Y-%m-%d"), mode="schedule", edit_visit_id=None)

    st.divider()

    st.markdown("### Visitas do dia")
    st.caption(f"Mostra todas as visitas agendadas para **hoje** ({fmt_date_br(today)}).")

    todays = [v for v in visits_for_day(today) if v.get("status") == "scheduled"]
    if not todays:
        st.info("Nenhuma visita marcada para hoje.")
    else:
        for v in todays:
            farmer = farmer_by_id(v.get("farmerId"))
            name = farmer["name"] if farmer else "(agricultor removido)"
            cpf = display_cpf(farmer.get("cpf","")) if farmer else ""
            place = farmer.get("place","") if farmer else ""
            dt = parse_iso_datetime(v["scheduled"])
            plan = v.get("planNotes") or ""
            last_obs = last_done_note_before(v.get("farmerId"), v["scheduled"])

            with st.container(border=True):
                t1, t2 = st.columns([3,1])
                t1.markdown(f"**{name}**")
                t2.markdown(f"**{dt.strftime('%d/%m/%Y %H:%M')}**")

                if place:
                    st.caption(f"Localidade: {place}")
                if cpf:
                    st.caption(f"CPF: {cpf}")
                if plan:
                    st.write(f"**Planejamento:** {plan}")
                if last_obs:
                    st.write(f"**Última observação:** {last_obs}")

                if st.button("Registrar assessoria", type="primary", key=f"regtoday_{v['id']}"):
                    # register tied to this visit
                    visit_dt = parse_iso_datetime(v["scheduled"])
                    dialog_schedule(visit_dt.strftime("%Y-%m-%d"), mode="register", edit_visit_id=v["id"])

# -----------------------------
# TAB: Assessorias realizadas
# -----------------------------
with tabs[1]:
    now = today_date()
    st.markdown(f"### Agricultores já assessorados em {month_year_label(date(now.year, now.month, 1))}")

    done_this_month = []
    for v in st.session_state.visits:
        if v.get("status") != "done":
            continue
        dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
        if dt.year == now.year and dt.month == now.month:
            done_this_month.append(v)

    if not done_this_month:
        st.info("Nenhuma assessoria registrada neste mês.")
    else:
        # last per farmer
        last_by_farmer = {}
        for v in done_this_month:
            dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
            fid = v.get("farmerId")
            if not fid:
                continue
            if fid not in last_by_farmer or dt > last_by_farmer[fid]:
                last_by_farmer[fid] = dt

        rows = []
        for fid, dt in last_by_farmer.items():
            f = farmer_by_id(fid)
            if not f:
                continue
            rows.append((f["name"], dt.date(), f.get("place","")))
        rows.sort(key=lambda x: x[0].lower())

        for name, d, place in rows:
            with st.container(border=True):
                st.markdown(f"**{name}**")
                st.caption(f"{fmt_date_br(d)}" + (f" • {place}" if place else ""))

    st.divider()
    st.markdown("### Sugestão de agricultores a serem visitados 🌱")

    # blocked = já tem done ou scheduled no mês atual
    blocked = set()
    for v in st.session_state.visits:
        dt = parse_iso_datetime((v["checkIn"] or v["scheduled"]) if v.get("status")=="done" else v["scheduled"])
        if dt.year == now.year and dt.month == now.month:
            blocked.add(v.get("farmerId"))

    # last done overall
    last_done = {}
    for v in st.session_state.visits:
        if v.get("status") != "done":
            continue
        dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
        fid = v.get("farmerId")
        if fid and (fid not in last_done or dt > last_done[fid]):
            last_done[fid] = dt

    suggestions = []
    for f in st.session_state.farmers:
        if f["id"] in blocked:
            continue
        suggestions.append((f, last_done.get(f["id"])))

    # sort: no last first, then oldest last visit
    def sort_key(item):
        f, dt = item
        if dt is None:
            return (0, f["name"].lower())
        return (1, dt, f["name"].lower())

    suggestions.sort(key=sort_key)

    if not st.session_state.farmers:
        st.info("Nenhum agricultor cadastrado.")
    elif not suggestions:
        st.info("Sem sugestões para este mês.")
    else:
        for f, dt in suggestions:
            place = f.get("place","")
            with st.container(border=True):
                st.markdown(f"**{f['name']}**")
                if dt is None:
                    st.caption("Sem assessoria registrada" + (f" • {place}" if place else ""))
                else:
                    days = (today_date() - dt.date()).days
                    msg = f"(Última visita) {fmt_date_br(dt.date())}"
                    if days > 90:
                        st.error(msg + (f" • {place}" if place else ""))
                    else:
                        st.caption(msg + (f" • {place}" if place else ""))

                if st.button("Agendar para este agricultor", key=f"sug_{f['id']}"):
                    st.session_state.selected_farmer_id = f["id"]
                    dialog_schedule(today_date().strftime("%Y-%m-%d"), mode="schedule", edit_visit_id=None)

# -----------------------------
# TAB: Caderno de campo
# -----------------------------
with tabs[2]:
    st.markdown("### Caderno de campo 📒")
    q = st.text_input("Buscar agricultor, CPF, localidade ou texto da observação", placeholder="Ex: nome, CPF, localidade, termo da observação")

    entries = []
    for v in st.session_state.visits:
        if v.get("status") != "done":
            continue
        f = farmer_by_id(v.get("farmerId"))
        if not f:
            continue
        note = (v.get("doneNotes") or "").strip()
        dt = parse_iso_datetime(v["checkIn"] or v["scheduled"])
        entries.append({
            "name": f.get("name",""),
            "cpf": display_cpf(f.get("cpf","")),
            "place": f.get("place",""),
            "date": dt.date(),
            "note": note
        })

    # filter
    if q:
        ql = q.lower()
        entries = [
            e for e in entries
            if ql in (e["name"] or "").lower()
            or ql in (e["cpf"] or "").lower()
            or ql in (e["place"] or "").lower()
            or ql in (e["note"] or "").lower()
        ]

    if not entries:
        st.info("Nenhum registro encontrado.")
    else:
        entries.sort(key=lambda e: (e["name"].lower(), e["date"]), reverse=False)
        for e in entries:
            with st.container(border=True):
                st.markdown(f"**{e['name']}**")
                meta = []
                if e["cpf"]:
                    meta.append(f"CPF: {e['cpf']}")
                if e["place"]:
                    meta.append(e["place"])
                meta.append(fmt_date_br(e["date"]))
                st.caption(" • ".join(meta))
                if e["note"]:
                    st.write(e["note"])
                else:
                    st.caption("(sem observação)")

# -----------------------------
# TAB: Alertas de CAF
# -----------------------------
with tabs[3]:
    st.markdown("### CAF vencidas e a vencer ⚠️")

    farmers_with_caf = [f for f in st.session_state.farmers if f.get("cafExpiry")]
    if not farmers_with_caf:
        st.info("Nenhuma CAF cadastrada com data de validade.")
    else:
        items = []
        for f in farmers_with_caf:
            try:
                caf_d = datetime.strptime(f["cafExpiry"], "%Y-%m-%d").date()
            except Exception:
                continue
            days = diff_days_from_today(caf_d)  # positivo = futuro
            items.append((f, caf_d, days))
        items.sort(key=lambda x: x[2])  # mais urgente primeiro

        for f, caf_d, days in items:
            place = f.get("place","")
            status = ""
            if days < 0:
                status = f"CAF vencida há {abs(days)} dia(s)."
                st.error(f"{f['name']} — Validade: {fmt_date_br(caf_d)} • {status}")
            elif days <= 90:
                status = f"CAF vence em {days} dia(s)."
                st.error(f"{f['name']} — Validade: {fmt_date_br(caf_d)} • {status}")
            elif days <= 120:
                status = f"CAF vence em {days} dia(s)."
                st.warning(f"{f['name']} — Validade: {fmt_date_br(caf_d)} • {status}")
            else:
                status = f"CAF vence em {days} dia(s)."
                st.success(f"{f['name']} — Validade: {fmt_date_br(caf_d)} • {status}")

            with st.expander("Editar validade da CAF"):
                new_date = st.date_input(
                    "Nova validade",
                    value=caf_d,
                    key=f"caf_{f['id']}"
                )
                if st.button("Salvar", key=f"cafsave_{f['id']}"):
                    idx = next((i for i, x in enumerate(st.session_state.farmers) if x["id"] == f["id"]), None)
                    if idx is not None:
                        st.session_state.farmers[idx]["cafExpiry"] = new_date.strftime("%Y-%m-%d")
                        st.success("CAF atualizada.")
                        st.rerun()

# -----------------------------
# TAB: Cadastrar agricultores
# -----------------------------
with tabs[4]:
    st.markdown("### Cadastrar agricultores 👨‍🌾")

    with st.container(border=True):
        name = st.text_input("Nome do agricultor", placeholder="Ex: José da Silva")
        cpf  = st.text_input("CPF", placeholder="000.000.000-00")
        phone= st.text_input("Telefone (WhatsApp)", placeholder="Ex: 45999999999")
        place= st.text_input("Comunidade / Assentamento / Localidade", placeholder="Ex: Assent. 16 de Maio / Linha X")
        caf  = st.date_input("Validade da CAF", value=None)

        c1, c2 = st.columns([1,1])
        with c1:
            if st.button("Salvar agricultor", type="primary", use_container_width=True):
                if not name.strip():
                    st.error("INFORME PELO MENOS O NOME.")
                else:
                    cpf_digits = re.sub(r"\D", "", cpf or "")
                    if cpf_digits:
                        if len(cpf_digits) != 11:
                            st.error("CPF deve ter 11 dígitos.")
                            st.stop()
                        if not is_valid_cpf(cpf_digits):
                            st.error("CPF inválido. Verifique o número.")
                            st.stop()
                        cpf_fmt = format_cpf(cpf_digits)
                    else:
                        cpf_fmt = ""

                    caf_str = caf.strftime("%Y-%m-%d") if caf else None

                    st.session_state.farmers.append({
                        "id": uuid.uuid4().hex,
                        "name": name.strip(),
                        "cpf": cpf_fmt,
                        "phone": phone.strip(),
                        "place": place.strip(),
                        "cafExpiry": caf_str
                    })
                    st.success("Agricultor cadastrado.")
                    st.rerun()
        with c2:
            if st.button("Limpar campos", use_container_width=True):
                st.rerun()

    st.divider()
    st.markdown("### Lista de agricultores")
    search = st.text_input("Buscar por nome, CPF ou localidade", placeholder="Digite para filtrar")

    farmers = sorted(st.session_state.farmers, key=lambda x: x.get("name","").lower())
    if search:
        s = search.lower().strip()
        farmers = [
            f for f in farmers
            if s in (f.get("name","").lower())
            or s in (display_cpf(f.get("cpf","")).lower())
            or s in (f.get("place","").lower())
        ]

    if not farmers:
        st.info("Nenhum agricultor cadastrado.")
    else:
        for f in farmers:
            with st.container(border=True):
                st.markdown(f"**{f['name']}**")
                meta = []
                if f.get("cpf"):
                    meta.append("CPF: " + display_cpf(f["cpf"]))
                if f.get("place"):
                    meta.append("Localidade: " + f["place"])
                if meta:
                    st.caption(" • ".join(meta))

                cols = st.columns([1,1,1])
                if f.get("phone"):
                    link = wa_link(f["phone"], "")
                    cols[0].link_button("WhatsApp", link, use_container_width=True)
                else:
                    cols[0].caption("WhatsApp: não informado")

                with cols[1]:
                    if st.button("Agendar", key=f"ag_{f['id']}", use_container_width=True):
                        st.session_state.selected_farmer_id = f["id"]
                        dialog_schedule(today_date().strftime("%Y-%m-%d"), mode="schedule", edit_visit_id=None)

                with cols[2]:
                    if st.button("Excluir", key=f"del_{f['id']}", use_container_width=True):
                        # remove farmer + visits linked
                        st.session_state.farmers = [x for x in st.session_state.farmers if x["id"] != f["id"]]
                        st.session_state.visits = [v for v in st.session_state.visits if v.get("farmerId") != f["id"]]
                        st.success("Agricultor excluído.")
                        st.rerun()

# -----------------------------
# TAB: Configurações
# -----------------------------
with tabs[5]:
    st.markdown("### Configurações do app ⚙️")
    cfg = st.session_state.config

    with st.container(border=True):
        tech = st.text_input("Nome do técnico(a)", value=cfg.get("technicianName",""))
        goal = st.number_input("Compromisso de famílias a serem visitadas no mês", min_value=1, step=1, value=int(cfg.get("monthlyCommitment") or 60))
        tpl = st.text_area(
            "Mensagem padrão do WhatsApp",
            value=(cfg.get("whatsappTemplate") or "").strip() or
            "Olá {NOME_AGRICULTOR}, tudo bem? Aqui é {NOME_TECNICO}, técnico(a) do Convênio Semeando Gestão.\n\n"
            "Podemos agendar uma assessoria no dia *{DATA} ({DIA_SEMANA})* às *{HORA}*?\n\n"
            "Se estiver tudo bem para você, por favor confirme por aqui.",
            height=160
        )
        st.caption("Marcadores: {NOME_AGRICULTOR}, {NOME_TECNICO}, {DATA}, {DIA_SEMANA}, {HORA}.")

        if st.button("Salvar configurações", type="primary"):
            st.session_state.config = {
                "technicianName": tech.strip(),
                "monthlyCommitment": int(goal),
                "whatsappTemplate": tpl
            }
            st.success("Configurações salvas.")
            st.rerun()
