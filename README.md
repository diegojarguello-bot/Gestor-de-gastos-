<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Gestor de Gastos</title>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore-compat.js"></script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

:root {
  --bg: #0f1117;
  --surface: #1a1d27;
  --surface2: #222536;
  --surface3: #2a2e42;
  --border: #2e3248;
  --text: #e8eaf0;
  --text-muted: #7b82a0;
  --text-dim: #4a5068;
  --green: #00c896;
  --green-bg: #00c89618;
  --green-border: #00c89640;
  --red: #ff4d6d;
  --red-bg: #ff4d6d18;
  --red-border: #ff4d6d40;
  --yellow: #f5a623;
  --yellow-bg: #f5a62318;
  --yellow-border: #f5a62340;
  --blue: #4c9aff;
  --blue-bg: #4c9aff15;
  --radius: 10px;
  --radius-sm: 6px;
}

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  font-family: 'Inter', sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  font-size: 14px;
}

/* ── LOGIN ── */
#login-screen {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: radial-gradient(ellipse at 50% 0%, #1a2040 0%, var(--bg) 70%);
}

.login-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 16px;
  padding: 40px;
  width: 360px;
  box-shadow: 0 24px 64px #00000060;
}

.login-logo {
  text-align: center;
  margin-bottom: 32px;
}

.login-logo .icon {
  width: 52px; height: 52px;
  background: linear-gradient(135deg, var(--green), #00a07a);
  border-radius: 14px;
  display: flex; align-items: center; justify-content: center;
  margin: 0 auto 12px;
  font-size: 24px;
}

.login-logo h1 { font-size: 20px; font-weight: 700; }
.login-logo p  { color: var(--text-muted); font-size: 13px; margin-top: 4px; }

.login-tabs {
  display: flex;
  background: var(--surface2);
  border-radius: var(--radius-sm);
  padding: 3px;
  margin-bottom: 24px;
}

.login-tab {
  flex: 1; padding: 8px; text-align: center;
  border-radius: 4px; cursor: pointer;
  font-size: 13px; font-weight: 500;
  color: var(--text-muted); transition: all .2s;
  border: none; background: transparent;
}
.login-tab.active { background: var(--surface3); color: var(--text); }

.form-group { margin-bottom: 16px; }
.form-group label { display: block; font-size: 12px; color: var(--text-muted); margin-bottom: 6px; font-weight: 500; letter-spacing: .03em; }
.form-group input {
  width: 100%; padding: 10px 14px;
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--radius-sm); color: var(--text);
  font-family: inherit; font-size: 14px; outline: none; transition: border .2s;
}
.form-group input:focus { border-color: var(--green); }
.form-group input[type=number]::-webkit-inner-spin-button { -webkit-appearance: none; }

.btn-primary {
  width: 100%; padding: 11px;
  background: var(--green); color: #0a1a14;
  border: none; border-radius: var(--radius-sm);
  font-family: inherit; font-size: 14px; font-weight: 700;
  cursor: pointer; transition: opacity .2s; margin-top: 4px;
}
.btn-primary:hover { opacity: .88; }

.login-error {
  background: var(--red-bg); border: 1px solid var(--red-border);
  color: var(--red); border-radius: var(--radius-sm);
  padding: 10px 14px; font-size: 13px; margin-top: 12px;
  display: none;
}

/* ── APP SHELL ── */
#app { display: none; flex-direction: column; min-height: 100vh; }

header {
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  padding: 0 24px;
  display: flex; align-items: center; justify-content: space-between;
  height: 56px; position: sticky; top: 0; z-index: 100;
}

.header-left { display: flex; align-items: center; gap: 12px; }
.header-logo { 
  width: 32px; height: 32px;
  background: linear-gradient(135deg, var(--green), #00a07a);
  border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
  font-size: 16px;
}
.header-title { font-weight: 700; font-size: 15px; }

nav { display: flex; gap: 2px; }
.nav-tab {
  padding: 6px 16px; border-radius: var(--radius-sm);
  border: none; background: transparent;
  color: var(--text-muted); font-family: inherit;
  font-size: 13px; font-weight: 500; cursor: pointer; transition: all .2s;
}
.nav-tab.active { background: var(--surface2); color: var(--text); }

.header-right { display: flex; align-items: center; gap: 12px; }
.user-chip {
  display: flex; align-items: center; gap: 8px;
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: 20px; padding: 5px 12px 5px 8px;
  font-size: 13px; color: var(--text-muted);
}
.user-avatar {
  width: 22px; height: 22px; border-radius: 50%;
  background: var(--green); color: #0a1a14;
  display: flex; align-items: center; justify-content: center;
  font-size: 11px; font-weight: 700;
}
.btn-logout {
  background: transparent; border: 1px solid var(--border);
  color: var(--text-muted); padding: 5px 12px;
  border-radius: var(--radius-sm); font-family: inherit;
  font-size: 12px; cursor: pointer; transition: all .2s;
}
.btn-logout:hover { border-color: var(--red); color: var(--red); }

/* ── TABS ── */
.tab-content { display: none; padding: 24px; flex: 1; }
.tab-content.active { display: block; }

/* ── MES CORRIENTE ── */
.page-header {
  display: flex; align-items: center; justify-content: space-between;
  margin-bottom: 20px;
}
.page-header h2 { font-size: 18px; font-weight: 700; }
.month-badge {
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: 20px; padding: 5px 14px;
  font-size: 13px; font-weight: 600; color: var(--text-muted);
  text-transform: capitalize;
}

/* Ingresos */
.section-card {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 18px 20px;
  margin-bottom: 16px;
}
.section-label {
  font-size: 11px; font-weight: 600; color: var(--text-muted);
  letter-spacing: .08em; text-transform: uppercase; margin-bottom: 14px;
}
.ingresos-row {
  display: flex; align-items: center; gap: 10px; flex-wrap: wrap;
}
.ingreso-item {
  display: flex; align-items: center; gap: 6px;
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--radius-sm); padding: 6px 10px;
}
.ingreso-item label { font-size: 12px; color: var(--text-muted); }
.ingreso-name-input, .ingreso-amount-input {
  background: transparent; border: none; outline: none;
  color: var(--text); font-family: inherit; font-size: 13px;
}
.ingreso-name-input { width: 90px; font-weight: 500; }
.ingreso-amount-input { width: 110px; font-weight: 700; color: var(--green); text-align: right; }
.btn-remove-ingreso {
  background: none; border: none; color: var(--text-dim);
  cursor: pointer; font-size: 16px; padding: 0 2px; line-height: 1;
  transition: color .15s;
}
.btn-remove-ingreso:hover { color: var(--red); }
.btn-add-small {
  background: transparent; border: 1px dashed var(--border);
  color: var(--text-muted); border-radius: var(--radius-sm);
  padding: 6px 12px; font-family: inherit; font-size: 12px;
  cursor: pointer; transition: all .2s; display: flex; align-items: center; gap: 5px;
}
.btn-add-small:hover { border-color: var(--green); color: var(--green); }

/* Tabla */
.table-wrap {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--radius); overflow: hidden; margin-bottom: 16px;
}

table { width: 100%; border-collapse: collapse; }

thead th {
  padding: 11px 14px; font-size: 11px; font-weight: 600;
  color: var(--text-muted); letter-spacing: .06em;
  text-transform: uppercase; text-align: left;
  background: var(--surface2); border-bottom: 1px solid var(--border);
  white-space: nowrap;
}
thead th:not(:first-child) { text-align: right; }
thead th:last-child { text-align: left; }

tbody tr { border-bottom: 1px solid var(--border); transition: background .15s; }
tbody tr:last-child { border-bottom: none; }
tbody tr:hover { background: #ffffff04; }

td { padding: 7px 14px; vertical-align: middle; }

.td-entidad { min-width: 160px; }
.entidad-cell { display: flex; align-items: center; gap: 8px; }
.pin-btn {
  background: none; border: none; cursor: pointer;
  color: var(--text-dim); font-size: 13px;
  transition: color .15s; padding: 2px;
  line-height: 1;
}
.pin-btn.pinned { color: var(--yellow); }
.entidad-input {
  background: transparent; border: none; outline: none;
  color: var(--text); font-family: inherit; font-size: 13px;
  width: 100%; font-weight: 500;
}

/* Celdas de montos */
td.amount-cell { text-align: right; min-width: 130px; }
.amount-pill {
  display: inline-block; padding: 5px 12px;
  border-radius: 20px; font-size: 13px; font-weight: 700;
  font-variant-numeric: tabular-nums; cursor: pointer;
  transition: transform .15s, opacity .15s;
  animation: slideIn .3s ease;
}
.amount-pill:hover { transform: scale(1.04); }

@keyframes slideIn {
  from { opacity: 0; transform: translateX(6px) scale(.96); }
  to   { opacity: 1; transform: translateX(0) scale(1); }
}

.pill-pending {
  background: var(--red-bg); border: 1px solid var(--red-border);
  color: var(--red);
}
.pill-paid {
  background: var(--green-bg); border: 1px solid var(--green-border);
  color: var(--green);
}
.pill-deferred {
  background: var(--yellow-bg); border: 1px solid var(--yellow-border);
  color: var(--yellow);
}
.pill-empty { color: var(--text-dim); font-size: 12px; cursor: default; }

.amount-inline-input {
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--radius-sm); color: var(--text);
  font-family: inherit; font-size: 13px; font-weight: 700;
  padding: 5px 10px; outline: none; width: 120px; text-align: right;
  transition: border .2s;
}
.amount-inline-input:focus { border-color: var(--red); }
.amount-inline-input::placeholder { color: var(--text-dim); font-weight: 400; }

td.td-detalle { min-width: 150px; }
.detalle-input {
  background: transparent; border: none; outline: none;
  color: var(--text-muted); font-family: inherit; font-size: 12px;
  width: 100%; font-style: italic;
}
.detalle-input::placeholder { color: var(--text-dim); }

.btn-remove-row {
  background: none; border: none; color: var(--text-dim);
  cursor: pointer; font-size: 15px; padding: 0; transition: color .15s;
}
.btn-remove-row:hover { color: var(--red); }

.tfoot-add td {
  padding: 10px 14px;
  border-top: 1px solid var(--border);
  background: var(--surface);
}
.btn-add-row {
  background: transparent; border: 1px dashed var(--border);
  color: var(--text-muted); border-radius: var(--radius-sm);
  padding: 6px 14px; font-family: inherit; font-size: 12px;
  cursor: pointer; transition: all .2s; display: inline-flex;
  align-items: center; gap: 5px;
}
.btn-add-row:hover { border-color: var(--green); color: var(--green); }

/* Métricas */
.metrics-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 14px; margin-bottom: 20px; }

.metric-card {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 18px 20px;
}
.metric-title { font-size: 11px; font-weight: 600; color: var(--text-muted); letter-spacing: .07em; text-transform: uppercase; margin-bottom: 14px; }
.metric-big { font-size: 22px; font-weight: 700; font-variant-numeric: tabular-nums; }
.metric-big.positive { color: var(--green); }
.metric-big.negative { color: var(--red); }
.metric-big.neutral  { color: var(--text); }

.resumen-row { display: flex; gap: 12px; }
.resumen-item { flex: 1; }
.resumen-item label { display: block; font-size: 11px; color: var(--text-muted); margin-bottom: 6px; }
.resumen-item .val {
  font-size: 15px; font-weight: 700; font-variant-numeric: tabular-nums;
  padding: 8px 10px; border-radius: var(--radius-sm); text-align: right;
}
.val.pending { background: var(--red-bg); color: var(--red); }
.val.paid    { background: var(--green-bg); color: var(--green); }
.val.deferred{ background: var(--yellow-bg); color: var(--yellow); }

/* Botón cerrar mes */
.close-month-bar {
  display: flex; justify-content: flex-end; padding-top: 4px;
}
.btn-close-month {
  background: var(--surface2); border: 1px solid var(--border);
  color: var(--text-muted); padding: 9px 20px;
  border-radius: var(--radius-sm); font-family: inherit;
  font-size: 13px; font-weight: 500; cursor: pointer; transition: all .2s;
  display: flex; align-items: center; gap: 7px;
}
.btn-close-month:hover { border-color: var(--yellow); color: var(--yellow); }

/* ── HISTÓRICO ── */
.historico-empty {
  text-align: center; padding: 80px 20px;
  color: var(--text-muted); font-size: 14px;
}
.historico-empty .icon { font-size: 40px; margin-bottom: 12px; }

.hist-entry {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--radius); margin-bottom: 16px; overflow: hidden;
}
.hist-header {
  display: flex; align-items: center; justify-content: space-between;
  padding: 14px 20px; background: var(--surface2);
  border-bottom: 1px solid var(--border);
  cursor: pointer; user-select: none;
}
.hist-header h3 { font-size: 15px; font-weight: 600; text-transform: capitalize; }
.hist-meta { display: flex; gap: 16px; align-items: center; }
.hist-badge {
  font-size: 12px; font-weight: 600; padding: 3px 10px;
  border-radius: 20px;
}
.hist-body { padding: 16px 20px; display: none; }
.hist-body.open { display: block; }

/* Menú contextual */
.ctx-menu {
  position: fixed; z-index: 1000;
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--radius-sm); padding: 6px;
  box-shadow: 0 8px 24px #00000060;
  min-width: 180px;
}
.ctx-menu button {
  display: block; width: 100%; text-align: left;
  padding: 9px 12px; background: none; border: none;
  color: var(--text); font-family: inherit; font-size: 13px;
  cursor: pointer; border-radius: 4px; transition: background .15s;
}
.ctx-menu button:hover { background: var(--surface3); }
.ctx-partial-input {
  padding: 6px 8px;
  border-top: 1px solid var(--border); margin-top: 4px;
}
.ctx-partial-input input {
  width: 100%; padding: 8px 10px;
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--radius-sm); color: var(--text);
  font-family: inherit; font-size: 13px; outline: none;
  transition: border .2s; margin-bottom: 6px;
}
.ctx-partial-input input:focus { border-color: var(--yellow); }
.ctx-partial-input button {
  width: 100%; padding: 8px;
  background: var(--yellow); color: #1a1200;
  border: none; border-radius: var(--radius-sm);
  font-family: inherit; font-size: 13px; font-weight: 700;
  cursor: pointer; text-align: center;
}

/* Modal */
.modal-bg {
  position: fixed; inset: 0; background: #000000a0;
  z-index: 500; display: flex; align-items: center; justify-content: center;
  backdrop-filter: blur(3px);
}
.modal {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: 14px; padding: 28px 32px;
  width: 360px; box-shadow: 0 24px 60px #00000080;
}
.modal h3 { font-size: 17px; font-weight: 700; margin-bottom: 8px; }
.modal p { color: var(--text-muted); font-size: 13px; margin-bottom: 22px; line-height: 1.5; }
.modal-actions { display: flex; gap: 10px; justify-content: flex-end; }
.btn-ghost {
  padding: 9px 18px; background: transparent;
  border: 1px solid var(--border); border-radius: var(--radius-sm);
  color: var(--text-muted); font-family: inherit; font-size: 13px;
  cursor: pointer; transition: all .2s;
}
.btn-ghost:hover { border-color: var(--text-muted); color: var(--text); }
.btn-confirm {
  padding: 9px 18px;
  background: var(--yellow); border: none; border-radius: var(--radius-sm);
  color: #1a1200; font-family: inherit; font-size: 13px; font-weight: 700;
  cursor: pointer; transition: opacity .2s;
}
.btn-confirm:hover { opacity: .88; }

/* Toast */
.toast {
  position: fixed; bottom: 28px; right: 28px; z-index: 2000;
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--radius-sm); padding: 12px 18px;
  font-size: 13px; box-shadow: 0 8px 24px #00000060;
  animation: toastIn .3s ease;
  display: none;
}
.toast.show { display: block; }
@keyframes toastIn {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Loading */
#loading-overlay {
  position: fixed; inset: 0; background: var(--bg);
  display: flex; align-items: center; justify-content: center;
  z-index: 9999; flex-direction: column; gap: 16px;
}
.spinner {
  width: 36px; height: 36px; border: 3px solid var(--border);
  border-top-color: var(--green); border-radius: 50%;
  animation: spin .7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
#loading-overlay p { color: var(--text-muted); font-size: 13px; }

@media (max-width: 768px) {
  .metrics-grid { grid-template-columns: 1fr; }
  header { padding: 0 14px; }
  .tab-content { padding: 14px; }
  .header-title { display: none; }
}
</style>
</head>
<body>

<!-- Loading -->
<div id="loading-overlay">
  <div class="spinner"></div>
  <p>Cargando...</p>
</div>

<!-- Login -->
<div id="login-screen" style="display:none">
  <div class="login-card">
    <div class="login-logo">
      <div class="icon">💸</div>
      <h1>Gestor de Gastos</h1>
      <p>Control de cuentas mensuales</p>
    </div>
    <div class="login-tabs">
      <button class="login-tab active" onclick="switchLoginTab('user')">Usuario</button>
      <button class="login-tab" onclick="switchLoginTab('admin')">Admin</button>
    </div>
    <!-- User login -->
    <div id="tab-user">
      <div class="form-group">
        <label>NOMBRE DE USUARIO</label>
        <input type="text" id="login-username" placeholder="Tu nombre de usuario" autocomplete="off"
               onkeydown="if(event.key==='Enter') document.getElementById('login-pin').focus()"/>
      </div>
      <div class="form-group">
        <label>PIN</label>
        <input type="password" id="login-pin" placeholder="PIN numérico (mín. 4 dígitos)" inputmode="numeric" maxlength="8"
               onkeydown="if(event.key==='Enter') loginUser()"/>
      </div>
      <button class="btn-primary" onclick="loginUser()">Ingresar</button>
    </div>
    <!-- Admin login -->
    <div id="tab-admin" style="display:none">
      <div class="form-group">
        <label>EMAIL</label>
        <input type="email" id="login-email" placeholder="admin@email.com"
               onkeydown="if(event.key==='Enter') document.getElementById('login-password').focus()"/>
      </div>
      <div class="form-group">
        <label>CONTRASEÑA</label>
        <input type="password" id="login-password" placeholder="Contraseña"
               onkeydown="if(event.key==='Enter') loginAdmin()"/>
      </div>
      <button class="btn-primary" onclick="loginAdmin()">Ingresar como Admin</button>
    </div>
    <div class="login-error" id="login-error"></div>
  </div>
</div>

<!-- App -->
<div id="app">
  <header>
    <div class="header-left">
      <div class="header-logo">💸</div>
      <span class="header-title">Gestor de Gastos</span>
    </div>
    <nav>
      <button class="nav-tab active" onclick="showTab('mes')">Mes Corriente</button>
      <button class="nav-tab" onclick="showTab('historico')">Histórico</button>
    </nav>
    <div class="header-right">
      <div class="user-chip">
        <div class="user-avatar" id="user-avatar">?</div>
        <span id="user-name-display">Usuario</span>
      </div>
      <span id="save-indicator" style="font-size:11px;color:var(--text-dim);display:none">💾 guardando...</span>
      <button class="btn-logout" onclick="logout()">Salir</button>
    </div>
  </header>

  <!-- TAB 1: Mes corriente -->
  <div class="tab-content active" id="tab-mes">
    <div class="page-header">
      <h2>Mes Corriente</h2>
      <div class="month-badge" id="current-month-badge">—</div>
    </div>

    <!-- Ingresos -->
    <div class="section-card">
      <div class="section-label">💰 Ingresos del mes</div>
      <div class="ingresos-row" id="ingresos-row"></div>
    </div>

    <!-- Tabla -->
    <div class="table-wrap">
      <table id="gastos-table">
        <thead>
          <tr>
            <th style="width:32px"></th>
            <th class="td-entidad">Entidad</th>
            <th style="min-width:130px; text-align:right">Monto a Pagar</th>
            <th style="min-width:130px; text-align:right">Ya Pagado</th>
            <th style="min-width:130px; text-align:right">Pago Aplazado</th>
            <th style="min-width:160px; text-align:left">Detalle</th>
            <th style="width:32px"></th>
          </tr>
        </thead>
        <tbody id="gastos-body"></tbody>
        <tfoot>
          <tr class="tfoot-add">
            <td colspan="7">
              <button class="btn-add-row" onclick="addRow()">＋ Agregar fila</button>
            </td>
          </tr>
        </tfoot>
      </table>
    </div>

    <!-- Métricas -->
    <div class="metrics-grid">
      <div class="metric-card">
        <div class="metric-title">📊 Total Deudas</div>
        <div class="metric-big neutral" id="metric-total">$ 0</div>
        <div style="font-size:11px; color:var(--text-dim); margin-top:6px;">Suma de todas las columnas B+C+D</div>
      </div>
      <div class="metric-card">
        <div class="metric-title">📋 Resumen de Deudas Actual</div>
        <div class="resumen-row">
          <div class="resumen-item">
            <label>Falta Pagar</label>
            <div class="val pending" id="metric-pending">$ 0</div>
          </div>
          <div class="resumen-item">
            <label>Pagadas</label>
            <div class="val paid" id="metric-paid">$ 0</div>
          </div>
          <div class="resumen-item">
            <label>Aplazado</label>
            <div class="val deferred" id="metric-deferred">$ 0</div>
          </div>
        </div>
      </div>
      <div class="metric-card">
        <div class="metric-title">💳 Resto sobre Sueldo</div>
        <div class="metric-big" id="metric-resto">$ 0</div>
        <div style="font-size:11px; color:var(--text-dim); margin-top:6px;">Ingresos totales − Total Deudas</div>
      </div>
    </div>

    <div class="close-month-bar">
      <button class="btn-close-month" onclick="confirmCloseMonth()">📁 Cerrar mes y archivar</button>
    </div>
  </div>

  <!-- TAB 2: Histórico -->
  <div class="tab-content" id="tab-historico">
    <div class="page-header">
      <h2>Histórico de Meses</h2>
    </div>
    <div id="historico-container">
      <div class="historico-empty">
        <div class="icon">🗂️</div>
        <div>Aún no hay meses archivados.<br>Cerrá el mes corriente para guardar el primer registro.</div>
      </div>
    </div>
  </div>
</div>

<!-- Menú contextual -->
<div class="ctx-menu" id="ctx-menu" style="display:none">
  <button onclick="ctxAction('paid')">✅ Marcar como Pagado</button>
  <button onclick="ctxAction('partial-show')">🔸 Pago Parcial</button>
  <button onclick="ctxAction('revert')">↩️ Revertir a Pendiente</button>
  <div class="ctx-partial-input" id="ctx-partial-wrap" style="display:none">
    <input type="text" id="ctx-partial-val" placeholder="Monto pagado..." inputmode="numeric"
           oninput="fmtInlineInput(this)"
           onkeydown="if(event.key==='Enter'){event.preventDefault();ctxAction('partial-confirm');}"/>
    <button onclick="ctxAction('partial-confirm')">Confirmar parcial</button>
  </div>
</div>

<!-- Modal cierre de mes -->
<div class="modal-bg" id="close-modal" style="display:none">
  <div class="modal">
    <h3>📁 Cerrar mes corriente</h3>
    <p>Esto archivará el estado actual en el Histórico y reiniciará la tabla para el nuevo mes. Las filas <strong style="color:var(--yellow)">fijadas</strong> se conservarán. ¿Continuar?</p>
    <div class="modal-actions">
      <button class="btn-ghost" onclick="document.getElementById('close-modal').style.display='none'">Cancelar</button>
      <button class="btn-confirm" onclick="closeMonth()">Archivar y Cerrar</button>
    </div>
  </div>
</div>

<!-- Toast -->
<div class="toast" id="toast"></div>

<script>
// ══════════════════════════════════════════════
//  CONFIGURACIÓN FIREBASE
//  ⚠️  Reemplazá estos valores con los de tu
//  proyecto Firebase nuevo
// ══════════════════════════════════════════════
const firebaseConfig = {
  apiKey: "AIzaSyAeI5uRRpW1KjB5LLP6MwtzhWi_cmmvbSo",
  authDomain: "gastos-e4029.firebaseapp.com",
  projectId: "gastos-e4029",
  storageBucket: "gastos-e4029.firebasestorage.app",
  messagingSenderId: "345839024109",
  appId: "1:345839024109:web:69b10f80e5881d27d259e4"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db   = firebase.firestore();

// ══════════════════════════════════════════════
//  ESTADO GLOBAL
// ══════════════════════════════════════════════
let currentUser   = null;
let currentUserId = null;
let saveTimer     = null;
let ctxTargetRow  = null;   // { rowId, col } col: 'b' | 'd'

let state = {
  ingresos: [ { id: uid(), name: 'Sueldo', amount: '' } ],
  rows: []
};

function uid() {
  return Math.random().toString(36).slice(2, 9);
}

// ══════════════════════════════════════════════
//  LOGIN
// ══════════════════════════════════════════════
function switchLoginTab(tab) {
  document.querySelectorAll('.login-tab').forEach((t,i) => t.classList.toggle('active', (i===0 && tab==='user') || (i===1 && tab==='admin')));
  document.getElementById('tab-user').style.display  = tab === 'user'  ? 'block' : 'none';
  document.getElementById('tab-admin').style.display = tab === 'admin' ? 'block' : 'none';
  clearLoginError();
}

function showLoginError(msg) {
  const el = document.getElementById('login-error');
  el.textContent = msg; el.style.display = 'block';
}
function clearLoginError() {
  document.getElementById('login-error').style.display = 'none';
}

async function loginUser() {
  const username = document.getElementById('login-username').value.trim();
  const pin      = document.getElementById('login-pin').value.trim();
  if (!username) return showLoginError('Ingresá tu nombre de usuario.');
  if (!pin || pin.length < 4) return showLoginError('El PIN debe tener al menos 4 dígitos.');
  if (!/^\d+$/.test(pin)) return showLoginError('El PIN debe ser solo números.');
  const email = `${username.toLowerCase().replace(/\s+/g,'_')}@gastos.internal`;
  const pass  = `pin_${pin}`;
  try {
    await auth.signInWithEmailAndPassword(email, pass);
  } catch(e) {
    if (e.code === 'auth/user-not-found' || e.code === 'auth/invalid-credential' || e.code === 'auth/wrong-password' || e.code === 'auth/invalid-email') {
      try {
        await auth.createUserWithEmailAndPassword(email, pass);
      } catch(e2) {
        showLoginError('No se pudo crear la cuenta. Verificá el usuario y PIN.');
      }
    } else {
      showLoginError('Error de acceso. Verificá tus datos.');
    }
  }
}

async function loginAdmin() {
  const email = document.getElementById('login-email').value.trim();
  const pass  = document.getElementById('login-password').value;
  if (!email || !pass) return showLoginError('Completá email y contraseña.');
  try {
    await auth.signInWithEmailAndPassword(email, pass);
  } catch(e) {
    showLoginError('Credenciales incorrectas.');
  }
}

async function logout() {
  await auth.signOut();
}

// ══════════════════════════════════════════════
//  AUTH STATE
// ══════════════════════════════════════════════
auth.onAuthStateChanged(async user => {
  document.getElementById('loading-overlay').style.display = 'none';
  if (user) {
    currentUser   = user;
    currentUserId = user.uid;
    const displayName = user.email.replace('@gastos.internal','').replace(/_/g,' ');
    document.getElementById('user-name-display').textContent = displayName;
    document.getElementById('user-avatar').textContent = displayName[0].toUpperCase();
    document.getElementById('login-screen').style.display = 'none';
    document.getElementById('app').style.display = 'flex';
    await loadData();
    renderAll();
  } else {
    currentUser = null;
    document.getElementById('app').style.display = 'none';
    document.getElementById('login-screen').style.display = 'flex';
  }
});

// ══════════════════════════════════════════════
//  FIRESTORE — CARGA Y GUARDADO
// ══════════════════════════════════════════════
async function loadData() {
  try {
    const doc = await db.collection('usuarios').doc(currentUserId).collection('data').doc('mes_actual').get();
    if (doc.exists) {
      state = doc.data();
    } else {
      state = { ingresos: [{ id: uid(), name: 'Sueldo', amount: '' }], rows: [] };
    }
  } catch(e) {
    console.error('Error cargando datos:', e);
  }
}

function scheduleSave() {
  clearTimeout(saveTimer);
  const ind = document.getElementById('save-indicator');
  if (ind) ind.style.display = 'inline';
  saveTimer = setTimeout(saveData, 800);
}

async function saveData() {
  if (!currentUserId) return;
  try {
    await db.collection('usuarios').doc(currentUserId).collection('data').doc('mes_actual').set(state);
    const ind = document.getElementById('save-indicator');
    if (ind) ind.style.display = 'none';
  } catch(e) {
    console.error('Error guardando:', e);
  }
}

async function loadHistorico() {
  try {
    const snap = await db.collection('usuarios').doc(currentUserId).collection('historico').orderBy('timestamp','desc').get();
    return snap.docs.map(d => ({ id: d.id, ...d.data() }));
  } catch(e) { return []; }
}

// ══════════════════════════════════════════════
//  FORMATO DE NÚMEROS
// ══════════════════════════════════════════════
function fmtARS(val) {
  if (!val && val !== 0) return '';
  const n = parseFloat(String(val).replace(/\./g,'').replace(',','.'));
  if (isNaN(n)) return '';
  return '$ ' + n.toLocaleString('es-AR', { minimumFractionDigits: 0, maximumFractionDigits: 0 });
}

function parseARS(str) {
  if (str === null || str === undefined || str === '') return 0;
  // Si ya es número
  if (typeof str === 'number') return str;
  // Eliminar $, espacios
  let clean = String(str).replace(/\$/g,'').replace(/\s/g,'');
  // Formato es-AR: puntos=miles, coma=decimal
  // Eliminar puntos de miles y convertir coma decimal
  clean = clean.replace(/\./g,'').replace(',','.');
  return parseFloat(clean) || 0;
}

function formatInput(input) {
  const raw = input.value.replace(/\D/g,'');
  if (!raw) { input.value = ''; return; }
  const n = parseInt(raw, 10);
  input.value = '$ ' + n.toLocaleString('es-AR');
}

// ══════════════════════════════════════════════
//  INGRESOS
// ══════════════════════════════════════════════
function renderIngresos() {
  const container = document.getElementById('ingresos-row');
  container.innerHTML = '';

  state.ingresos.forEach(ing => {
    const div = document.createElement('div');
    div.className = 'ingreso-item';
    div.innerHTML = `
      <input class="ingreso-name-input" value="${escHtml(ing.name)}" placeholder="Concepto"
             onchange="updateIngreso('${ing.id}','name',this.value)"/>
      <input class="ingreso-amount-input" value="${ing.amount ? '$ '+parseARS(ing.amount).toLocaleString('es-AR') : ''}"
             placeholder="$ 0"
             oninput="formatInput(this)"
             onchange="updateIngreso('${ing.id}','amount',this.value)"/>
      <button class="btn-remove-ingreso" onclick="removeIngreso('${ing.id}')" title="Quitar">×</button>
    `;
    container.appendChild(div);
  });

  const addBtn = document.createElement('button');
  addBtn.className = 'btn-add-small';
  addBtn.innerHTML = '＋ Ingreso extra';
  addBtn.onclick = addIngreso;
  container.appendChild(addBtn);
}

function addIngreso() {
  state.ingresos.push({ id: uid(), name: 'Extra', amount: '' });
  renderIngresos(); scheduleSave();
}

function removeIngreso(id) {
  state.ingresos = state.ingresos.filter(i => i.id !== id);
  renderIngresos(); recalc(); scheduleSave();
}

function updateIngreso(id, field, val) {
  const ing = state.ingresos.find(i => i.id === id);
  if (!ing) return;
  if (field === 'amount') {
    ing.amount = val.replace(/\$/g,'').replace(/\s/g,'').replace(/\./g,'');
  } else {
    ing[field] = val;
  }
  recalc(); scheduleSave();
}

// ══════════════════════════════════════════════
//  FILAS DE GASTOS
// ══════════════════════════════════════════════
function addRow() {
  state.rows.push({ id: uid(), entity: '', b: '', c: '', d: '', detalle: '', pinned: false });
  renderRows(); scheduleSave();
}

function removeRow(id) {
  state.rows = state.rows.filter(r => r.id !== id);
  renderRows(); recalc(); scheduleSave();
}

function togglePin(id) {
  const row = state.rows.find(r => r.id === id);
  if (row) { row.pinned = !row.pinned; renderRows(); scheduleSave(); }
}

function updateRow(id, field, val) {
  const row = state.rows.find(r => r.id === id);
  if (!row) return;
  if (['b','c','d'].includes(field)) {
    row[field] = val.replace(/\$/g,'').replace(/\s/g,'').replace(/\./g,'');
  } else {
    row[field] = val;
  }
  recalc(); scheduleSave();
}

function renderRows() {
  const tbody = document.getElementById('gastos-body');
  tbody.innerHTML = '';

  state.rows.forEach(row => {
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>
        <button class="pin-btn ${row.pinned?'pinned':''}" title="${row.pinned?'Fijada (click para soltar)':'Fijar fila'}"
                onclick="togglePin('${row.id}')">
          ${row.pinned ? '📌' : '📍'}
        </button>
      </td>
      <td class="td-entidad">
        <div class="entidad-cell">
          <input class="entidad-input" value="${escHtml(row.entity)}" placeholder="Entidad a pagar"
                 onchange="updateRow('${row.id}','entity',this.value)"/>
        </div>
      </td>
      <td class="amount-cell">${renderAmountCell(row, 'b')}</td>
      <td class="amount-cell">${renderAmountCell(row, 'c')}</td>
      <td class="amount-cell">${renderAmountCell(row, 'd')}</td>
      <td class="td-detalle">
        <input class="detalle-input" value="${escHtml(row.detalle)}" placeholder="aclaración..."
               onchange="updateRow('${row.id}','detalle',this.value)"/>
      </td>
      <td>
        <button class="btn-remove-row" onclick="removeRow('${row.id}')" title="Eliminar fila">✕</button>
      </td>
    `;
    tbody.appendChild(tr);
  });

  recalc();
}

function renderAmountCell(row, col) {
  const val = row[col];
  const n = parseARS(val);

  // Columna B vacía → input editable directo
  if (col === 'b' && !n) {
    return `<input class="amount-inline-input"
              placeholder="$ 0"
              value=""
              oninput="fmtInlineInput(this)"
              onchange="commitInlineB('${row.id}', this.value)"
              onclick="event.stopPropagation()"
            />`;
  }

  // Columna C o D vacías → solo guión estático
  if (!n) return `<span class="amount-pill pill-empty">—</span>`;

  const cls = col==='b' ? 'pill-pending' : col==='c' ? 'pill-paid' : 'pill-deferred';
  return `<span class="amount-pill ${cls}" onclick="openCtxMenu(event,'${row.id}','${col}')">${fmtARS(n)}</span>`;
}

function fmtInlineInput(input) {
  const raw = input.value.replace(/\D/g,'');
  if (!raw) { input.value = ''; return; }
  // guardamos posición para no saltar el cursor
  input.value = '$ ' + parseInt(raw,10).toLocaleString('es-AR');
}

function commitInlineB(rowId, val) {
  const row = state.rows.find(r => r.id === rowId);
  if (!row) return;
  row.b = val.replace(/\$/g,'').replace(/\s/g,'').replace(/\./g,'');
  renderRows(); recalc(); scheduleSave();
}

// ══════════════════════════════════════════════
//  MENÚ CONTEXTUAL (B y D)
// ══════════════════════════════════════════════
function openCtxMenu(e, rowId, col) {
  e.stopPropagation();
  ctxTargetRow = { rowId, col };

  // Solo columna B y D permiten acciones
  // En C mostramos solo revertir
  const menu = document.getElementById('ctx-menu');
  const partialWrap = document.getElementById('ctx-partial-wrap');
  partialWrap.style.display = 'none';
  document.getElementById('ctx-partial-val').value = '';

  // Posicionar
  menu.style.display = 'block';
  let x = e.pageX, y = e.pageY;
  if (x + 200 > window.innerWidth) x = window.innerWidth - 210;
  menu.style.left = x + 'px';
  menu.style.top  = y + 'px';
}

document.addEventListener('click', () => {
  document.getElementById('ctx-menu').style.display = 'none';
});

function ctxAction(action) {
  if (!ctxTargetRow) return;
  const row = state.rows.find(r => r.id === ctxTargetRow.rowId);
  if (!row) return;

  if (action === 'paid') {
    // Mover todo a C, limpiar B y D
    const total = parseARS(row.b) + parseARS(row.c) + parseARS(row.d);
    row.c = String(total); row.b = ''; row.d = '';
    document.getElementById('ctx-menu').style.display = 'none';
    finalize();
  }
  else if (action === 'partial-show') {
    document.getElementById('ctx-partial-wrap').style.display = 'block';
    document.getElementById('ctx-partial-val').focus();
  }
  else if (action === 'partial-confirm') {
    const inputEl = document.getElementById('ctx-partial-val');
    let partialStr = inputEl.value.replace(/\$/g,'').replace(/\s/g,'').replace(/\./g,'');
    const partial = parseFloat(partialStr.replace(',','.')) || 0;
    if (!partial || partial <= 0) { showToast('Ingresá un monto válido.'); return; }
    const original = parseARS(row.b) + parseARS(row.d);
    const restante = original - partial;
    row.c = String(parseARS(row.c) + partial);
    row.d = restante > 0 ? String(restante) : '';
    row.b = '';
    document.getElementById('ctx-menu').style.display = 'none';
    finalize();
  }
  else if (action === 'revert') {
    // Devuelve c+d a b
    const total = parseARS(row.b) + parseARS(row.c) + parseARS(row.d);
    row.b = String(total); row.c = ''; row.d = '';
    document.getElementById('ctx-menu').style.display = 'none';
    finalize();
  }
}

function finalize() {
  renderRows(); recalc(); scheduleSave();
}

// ══════════════════════════════════════════════
//  MÉTRICAS
// ══════════════════════════════════════════════
function recalc() {
  let totalB = 0, totalC = 0, totalD = 0;
  state.rows.forEach(r => {
    totalB += parseARS(r.b);
    totalC += parseARS(r.c);
    totalD += parseARS(r.d);
  });
  const totalDeudas = totalB + totalC + totalD;
  const totalIngresos = state.ingresos.reduce((s,i) => s + parseARS(i.amount), 0);
  const resto = totalIngresos - totalDeudas;

  document.getElementById('metric-total').textContent   = fmtARS(totalDeudas);
  document.getElementById('metric-pending').textContent = fmtARS(totalB);
  document.getElementById('metric-paid').textContent    = fmtARS(totalC);
  document.getElementById('metric-deferred').textContent= fmtARS(totalD);

  const restoEl = document.getElementById('metric-resto');
  restoEl.textContent = (resto < 0 ? '-' : '') + fmtARS(Math.abs(resto));
  restoEl.className = 'metric-big ' + (resto < 0 ? 'negative' : resto > 0 ? 'positive' : 'neutral');
}

// ══════════════════════════════════════════════
//  MES ACTUAL — BADGE
// ══════════════════════════════════════════════
function updateMonthBadge() {
  const now = new Date();
  const label = now.toLocaleDateString('es-AR', { month: 'long', year: 'numeric' });
  document.getElementById('current-month-badge').textContent = label;
}

// ══════════════════════════════════════════════
//  CERRAR MES
// ══════════════════════════════════════════════
function confirmCloseMonth() {
  document.getElementById('close-modal').style.display = 'flex';
}

async function closeMonth() {
  document.getElementById('close-modal').style.display = 'none';

  // Calcular totales snapshot
  let totalB = 0, totalC = 0, totalD = 0;
  state.rows.forEach(r => {
    totalB += parseARS(r.b);
    totalC += parseARS(r.c);
    totalD += parseARS(r.d);
  });
  const totalIngresos = state.ingresos.reduce((s,i) => s + parseARS(i.amount), 0);

  const now = new Date();
  const snapshot = {
    label: now.toLocaleDateString('es-AR', { month: 'long', year: 'numeric' }),
    timestamp: firebase.firestore.Timestamp.now(),
    ingresos: JSON.parse(JSON.stringify(state.ingresos)),
    rows: JSON.parse(JSON.stringify(state.rows)),
    totalB, totalC, totalD,
    totalDeudas: totalB + totalC + totalD,
    totalIngresos
  };

  try {
    await db.collection('usuarios').doc(currentUserId).collection('historico').add(snapshot);
  } catch(e) { showToast('Error guardando histórico.'); return; }

  // Resetear: conservar filas fijadas, limpiar montos
  state.ingresos = state.ingresos.map(i => ({ ...i, amount: '' }));
  state.rows = state.rows
    .filter(r => r.pinned)
    .map(r => ({ ...r, b: '', c: '', d: '', detalle: '' }));

  await saveData();
  renderAll();
  showTab('historico');
  await renderHistorico();
  showToast('✅ Mes archivado correctamente.');
}

// ══════════════════════════════════════════════
//  HISTÓRICO
// ══════════════════════════════════════════════
async function renderHistorico() {
  const container = document.getElementById('historico-container');
  const entries = await loadHistorico();

  if (!entries.length) {
    container.innerHTML = `
      <div class="historico-empty">
        <div class="icon">🗂️</div>
        <div>Aún no hay meses archivados.<br>Cerrá el mes corriente para guardar el primer registro.</div>
      </div>`;
    return;
  }

  container.innerHTML = '';
  entries.forEach(entry => {
    const div = document.createElement('div');
    div.className = 'hist-entry';
    const resto = entry.totalIngresos - entry.totalDeudas;
    const restoColor = resto < 0 ? 'var(--red)' : 'var(--green)';

    div.innerHTML = `
      <div class="hist-header" onclick="toggleHistEntry(this)">
        <h3>📅 ${entry.label}</h3>
        <div class="hist-meta">
          <span class="hist-badge" style="background:var(--red-bg);color:var(--red);border:1px solid var(--red-border)">
            Pendiente: ${fmtARS(entry.totalB)}
          </span>
          <span class="hist-badge" style="background:var(--green-bg);color:var(--green);border:1px solid var(--green-border)">
            Pagado: ${fmtARS(entry.totalC)}
          </span>
          <span style="font-size:12px;color:${restoColor};font-weight:700">
            Resto: ${resto<0?'-':''}${fmtARS(Math.abs(resto))}
          </span>
          <span style="color:var(--text-dim);font-size:16px">▼</span>
        </div>
      </div>
      <div class="hist-body">
        ${buildHistTable(entry)}
      </div>
    `;
    container.appendChild(div);
  });
}

function toggleHistEntry(header) {
  const body = header.nextElementSibling;
  body.classList.toggle('open');
}

function buildHistTable(entry) {
  if (!entry.rows || !entry.rows.length) return '<p style="color:var(--text-muted);font-size:13px">Sin filas registradas.</p>';
  let html = `
    <table style="width:100%;border-collapse:collapse;font-size:13px">
      <thead>
        <tr>
          <th style="text-align:left;padding:8px 10px;color:var(--text-muted);font-size:11px;border-bottom:1px solid var(--border)">Entidad</th>
          <th style="text-align:right;padding:8px 10px;color:var(--text-muted);font-size:11px;border-bottom:1px solid var(--border)">A Pagar</th>
          <th style="text-align:right;padding:8px 10px;color:var(--text-muted);font-size:11px;border-bottom:1px solid var(--border)">Pagado</th>
          <th style="text-align:right;padding:8px 10px;color:var(--text-muted);font-size:11px;border-bottom:1px solid var(--border)">Aplazado</th>
          <th style="text-align:left;padding:8px 10px;color:var(--text-muted);font-size:11px;border-bottom:1px solid var(--border)">Detalle</th>
        </tr>
      </thead><tbody>`;
  entry.rows.forEach(r => {
    html += `<tr>
      <td style="padding:7px 10px;border-bottom:1px solid var(--border)">${escHtml(r.entity)||'—'}</td>
      <td style="padding:7px 10px;text-align:right;border-bottom:1px solid var(--border);color:var(--red)">${r.b?fmtARS(parseARS(r.b)):'—'}</td>
      <td style="padding:7px 10px;text-align:right;border-bottom:1px solid var(--border);color:var(--green)">${r.c?fmtARS(parseARS(r.c)):'—'}</td>
      <td style="padding:7px 10px;text-align:right;border-bottom:1px solid var(--border);color:var(--yellow)">${r.d?fmtARS(parseARS(r.d)):'—'}</td>
      <td style="padding:7px 10px;border-bottom:1px solid var(--border);color:var(--text-muted);font-style:italic">${escHtml(r.detalle)||''}</td>
    </tr>`;
  });
  html += '</tbody></table>';
  return html;
}

// ══════════════════════════════════════════════
//  UI HELPERS
// ══════════════════════════════════════════════
function showTab(tab) {
  document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
  document.getElementById('tab-' + tab).classList.add('active');
  document.querySelectorAll('.nav-tab')[tab === 'mes' ? 0 : 1].classList.add('active');
  if (tab === 'historico') renderHistorico();
}

function renderAll() {
  updateMonthBadge();
  renderIngresos();
  renderRows();
}

function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 3000);
}

function escHtml(str) {
  if (!str) return '';
  return String(str)
    .replace(/&/g,'&amp;')
    .replace(/</g,'&lt;')
    .replace(/>/g,'&gt;')
    .replace(/"/g,'&quot;');
}

// Formateo en inputs de ingreso al tipear
document.addEventListener('input', e => {
  if (e.target.classList.contains('ingreso-amount-input')) {
    const raw = e.target.value.replace(/\D/g,'');
    if (!raw) { e.target.value = ''; return; }
    e.target.value = '$ ' + parseInt(raw,10).toLocaleString('es-AR');
  }
});
</script>
</body>
</html>
