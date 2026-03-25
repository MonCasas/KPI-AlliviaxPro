<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>ALLIVIAX · Agente de Inteligencia Comercial</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
  :root {
    --bg: #0d0f14;
    --surface: #151821;
    --surface2: #1c2130;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.13);
    --teal: #2dd4bf;
    --teal-dim: rgba(45,212,191,0.12);
    --teal-mid: rgba(45,212,191,0.25);
    --amber: #fbbf24;
    --amber-dim: rgba(251,191,36,0.12);
    --red: #f87171;
    --red-dim: rgba(248,113,113,0.12);
    --green: #4ade80;
    --green-dim: rgba(74,222,128,0.12);
    --text: #e2e8f0;
    --muted: #64748b;
    --muted2: #94a3b8;
    --font: 'DM Sans', sans-serif;
    --mono: 'Space Mono', monospace;
  }
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  html { font-size: 14px; }
  body { background: var(--bg); color: var(--text); font-family: var(--font); min-height: 100vh; display: flex; flex-direction: column; }

  /* TOPBAR */
  .topbar {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 0 2rem;
    height: 56px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 100;
  }
  .brand { display: flex; align-items: center; gap: 12px; }
  .brand-icon {
    width: 32px; height: 32px;
    background: var(--teal);
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-family: var(--mono);
    font-size: 11px;
    font-weight: 700;
    color: #0d0f14;
    letter-spacing: -0.5px;
  }
  .brand-name { font-size: 15px; font-weight: 600; letter-spacing: 0.5px; }
  .brand-sub { font-size: 11px; color: var(--muted); font-family: var(--mono); margin-top: 1px; }
  .topbar-right { display: flex; align-items: center; gap: 12px; }
  .badge-live {
    background: var(--green-dim);
    color: var(--green);
    border: 1px solid rgba(74,222,128,0.3);
    border-radius: 20px;
    padding: 3px 10px;
    font-size: 10px;
    font-family: var(--mono);
    letter-spacing: 1px;
    display: flex; align-items: center; gap: 5px;
  }
  .badge-live::before {
    content: '';
    width: 6px; height: 6px;
    background: var(--green);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }
  .period-tag { font-size: 11px; color: var(--muted2); font-family: var(--mono); }

  /* LAYOUT */
  .main { display: flex; flex: 1; overflow: hidden; }
  .content { flex: 1; overflow-y: auto; padding: 1.5rem 2rem; display: flex; flex-direction: column; gap: 1.5rem; }
  .sidebar { width: 360px; min-width: 360px; border-left: 1px solid var(--border); display: flex; flex-direction: column; background: var(--surface); }

  /* SECTION HEADER */
  .section-header {
    display: flex; align-items: center; gap: 8px;
    margin-bottom: 1rem;
  }
  .section-num {
    width: 22px; height: 22px;
    background: var(--teal-dim);
    border: 1px solid rgba(45,212,191,0.3);
    border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 10px;
    font-family: var(--mono);
    color: var(--teal);
    font-weight: 700;
  }
  .section-title { font-size: 13px; font-weight: 600; color: var(--text); letter-spacing: 0.3px; }
  .section-desc { font-size: 11px; color: var(--muted); margin-left: auto; font-family: var(--mono); }

  /* CARDS */
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem;
  }
  .card-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
  .card-wide { grid-column: span 2; }

  /* METRIC ROW */
  .metrics-row { display: flex; gap: 1rem; margin-bottom: 1.25rem; flex-wrap: wrap; }
  .metric {
    flex: 1; min-width: 110px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 0.875rem 1rem;
  }
  .metric-label { font-size: 10px; color: var(--muted); font-family: var(--mono); letter-spacing: 0.5px; margin-bottom: 6px; text-transform: uppercase; }
  .metric-value { font-size: 22px; font-weight: 600; line-height: 1; }
  .metric-sub { font-size: 10px; margin-top: 4px; }
  .up { color: var(--green); }
  .down { color: var(--red); }
  .neutral { color: var(--amber); }

  /* CHART CONTAINER */
  .chart-wrap { position: relative; width: 100%; }

  /* PROGRESS BARS */
  .prog-row { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; }
  .prog-label { font-size: 11px; color: var(--muted2); width: 80px; flex-shrink: 0; }
  .prog-bar { flex: 1; height: 6px; background: var(--surface2); border-radius: 3px; overflow: hidden; }
  .prog-fill { height: 100%; border-radius: 3px; transition: width 1s ease; }
  .prog-val { font-size: 11px; font-family: var(--mono); width: 38px; text-align: right; flex-shrink: 0; }

  /* PRICE TABLE */
  .price-table { width: 100%; border-collapse: collapse; font-size: 12px; }
  .price-table th {
    text-align: left;
    padding: 6px 10px;
    color: var(--muted);
    font-weight: 500;
    font-family: var(--mono);
    font-size: 10px;
    text-transform: uppercase;
    border-bottom: 1px solid var(--border);
  }
  .price-table td { padding: 8px 10px; border-bottom: 1px solid var(--border); }
  .price-table tr:last-child td { border-bottom: none; }
  .price-table tr.alliviax td { color: var(--teal); font-weight: 600; }
  .tag-up { background: var(--red-dim); color: var(--red); border-radius: 4px; padding: 1px 6px; font-size: 10px; }
  .tag-down { background: var(--green-dim); color: var(--green); border-radius: 4px; padding: 1px 6px; font-size: 10px; }
  .tag-neutral { background: var(--amber-dim); color: var(--amber); border-radius: 4px; padding: 1px 6px; font-size: 10px; }

  /* CHANNEL VISIBILITY */
  .channel-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; }
  .channel-card {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px;
    text-align: center;
  }
  .channel-name { font-size: 10px; color: var(--muted); font-family: var(--mono); margin-bottom: 6px; }
  .channel-pct { font-size: 18px; font-weight: 600; }
  .channel-sku { font-size: 10px; color: var(--muted); margin-top: 2px; }

  /* AI SIDEBAR */
  .ai-header {
    padding: 1rem 1.25rem;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; gap: 10px;
  }
  .ai-avatar {
    width: 30px; height: 30px;
    background: linear-gradient(135deg, var(--teal), #0891b2);
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 13px;
  }
  .ai-title { font-size: 13px; font-weight: 600; }
  .ai-model { font-size: 10px; color: var(--muted); font-family: var(--mono); }

  .ai-messages { flex: 1; overflow-y: auto; padding: 1rem; display: flex; flex-direction: column; gap: 0.875rem; }

  .msg { display: flex; gap: 8px; animation: fadein 0.3s ease; }
  @keyframes fadein { from{opacity:0;transform:translateY(6px)} to{opacity:1;transform:none} }
  .msg-avatar {
    width: 26px; height: 26px; border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 11px; flex-shrink: 0; margin-top: 2px;
  }
  .msg-ai .msg-avatar { background: var(--teal-dim); color: var(--teal); border: 1px solid rgba(45,212,191,0.25); }
  .msg-user .msg-avatar { background: var(--surface2); color: var(--muted2); border: 1px solid var(--border); }
  .msg-user { flex-direction: row-reverse; }
  .msg-bubble {
    max-width: 85%;
    padding: 8px 12px;
    border-radius: 10px;
    font-size: 12px;
    line-height: 1.55;
  }
  .msg-ai .msg-bubble { background: var(--surface2); border: 1px solid var(--border); color: var(--text); border-radius: 2px 10px 10px 10px; }
  .msg-user .msg-bubble { background: var(--teal-mid); border: 1px solid rgba(45,212,191,0.25); color: var(--text); border-radius: 10px 2px 10px 10px; }
  .msg-bubble strong { color: var(--teal); font-weight: 600; }
  .msg-bubble .kpi-tag { display: inline-block; background: var(--surface); border: 1px solid var(--border); border-radius: 4px; padding: 1px 6px; font-size: 10px; font-family: var(--mono); color: var(--muted2); margin: 2px 2px; }

  .typing { display: flex; gap: 4px; align-items: center; padding: 4px 0; }
  .typing span { width: 5px; height: 5px; background: var(--teal); border-radius: 50%; animation: bounce 1.2s infinite; }
  .typing span:nth-child(2) { animation-delay: 0.2s; }
  .typing span:nth-child(3) { animation-delay: 0.4s; }
  @keyframes bounce { 0%,80%,100%{transform:translateY(0)} 40%{transform:translateY(-6px)} }

  /* AI INPUT */
  .ai-input-area {
    padding: 0.875rem 1rem;
    border-top: 1px solid var(--border);
    background: var(--surface);
  }
  .quick-prompts { display: flex; flex-wrap: wrap; gap: 5px; margin-bottom: 8px; }
  .qp-btn {
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 20px;
    padding: 3px 10px;
    font-size: 10px;
    color: var(--muted2);
    cursor: pointer;
    font-family: var(--font);
    transition: all 0.15s;
  }
  .qp-btn:hover { border-color: var(--teal); color: var(--teal); }
  .input-row { display: flex; gap: 8px; }
  .ai-input {
    flex: 1;
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 8px;
    padding: 8px 12px;
    color: var(--text);
    font-family: var(--font);
    font-size: 12px;
    outline: none;
    transition: border 0.15s;
  }
  .ai-input:focus { border-color: var(--teal); }
  .ai-input::placeholder { color: var(--muted); }
  .send-btn {
    background: var(--teal);
    border: none;
    border-radius: 8px;
    width: 34px; height: 34px;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer;
    transition: all 0.15s;
    color: #0d0f14;
    font-size: 14px;
  }
  .send-btn:hover { background: #5eead4; }
  .send-btn:disabled { opacity: 0.4; cursor: not-allowed; }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 5px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--surface2); border-radius: 10px; }

  /* ALERTS */
  .alert-row { display: flex; gap: 8px; flex-wrap: wrap; margin-top: 0.75rem; }
  .alert {
    display: flex; align-items: center; gap: 6px;
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: 6px; padding: 5px 10px; font-size: 10px; color: var(--muted2);
    font-family: var(--mono);
  }
  .alert-dot { width: 6px; height: 6px; border-radius: 50%; }
</style>
</head>
<body>

<div class="topbar">
  <div class="brand">
    <div class="brand-icon">ALX</div>
    <div>
      <div class="brand-name">ALLIVIAX</div>
      <div class="brand-sub">INTELIGENCIA COMERCIAL · MÉXICO</div>
    </div>
  </div>
  <div class="topbar-right">
    <span class="period-tag">ENE–MAR 2025 · Q1</span>
    <div class="badge-live">LIVE</div>
  </div>
</div>

<div class="main">
  <!-- DASHBOARD CONTENT -->
  <div class="content">

    <!-- KPI 1: VENTA MENSUAL VS OBJETIVO -->
    <div class="card">
      <div class="section-header">
        <div class="section-num">01</div>
        <span class="section-title">Venta Mensual vs Objetivo</span>
        <span class="section-desc">Unidades & MXN · Rolling 6M</span>
      </div>
      <div class="metrics-row">
        <div class="metric">
          <div class="metric-label">Venta Mar'25</div>
          <div class="metric-value" style="color:var(--teal)">$4.8M</div>
          <div class="metric-sub up">▲ +12.3% vs Feb</div>
        </div>
        <div class="metric">
          <div class="metric-label">Objetivo Mar'25</div>
          <div class="metric-value" style="color:var(--amber)">$5.2M</div>
          <div class="metric-sub down">Gap: -$400K</div>
        </div>
        <div class="metric">
          <div class="metric-label">Cumplimiento</div>
          <div class="metric-value" style="color:var(--red)">92.3%</div>
          <div class="metric-sub neutral">▼ -3.1pp vs Q1'24</div>
        </div>
        <div class="metric">
          <div class="metric-label">YTD Acumulado</div>
          <div class="metric-value" style="color:var(--green)">$13.6M</div>
          <div class="metric-sub up">▲ +8.7% vs YTD'24</div>
        </div>
      </div>
      <div class="chart-wrap" style="height:220px">
        <canvas id="salesChart"></canvas>
      </div>
      <div class="alert-row">
        <div class="alert"><span class="alert-dot" style="background:var(--red)"></span>Mar'25 bajo objetivo por 2do mes consecutivo</div>
        <div class="alert"><span class="alert-dot" style="background:var(--amber)"></span>CDMX representa 41% de la venta total</div>
        <div class="alert"><span class="alert-dot" style="background:var(--green)"></span>GDL creciendo +22% YoY</div>
      </div>
    </div>

    <!-- KPI 2 & 3 ROW -->
    <div class="card-grid">

      <!-- KPI 2: DISTRIBUCIÓN NUMÉRICA TRADICIONAL -->
      <div class="card">
        <div class="section-header">
          <div class="section-num">02</div>
          <span class="section-title">Distribución Numérica · Tradicional</span>
        </div>
        <div class="metrics-row">
          <div class="metric">
            <div class="metric-label">Cobertura Nac.</div>
            <div class="metric-value" style="color:var(--amber)">63.4%</div>
            <div class="metric-sub neutral">Obj: 75%</div>
          </div>
          <div class="metric">
            <div class="metric-label">Puntos activos</div>
            <div class="metric-value" style="color:var(--teal)">18,420</div>
            <div class="metric-sub up">▲ +2,100 vs Q4</div>
          </div>
        </div>
        <div style="margin-top: 0.5rem;">
          <div class="prog-row"><span class="prog-label">CDMX</span><div class="prog-bar"><div class="prog-fill" style="width:78%;background:var(--teal)"></div></div><span class="prog-val up">78%</span></div>
          <div class="prog-row"><span class="prog-label">GDL</span><div class="prog-bar"><div class="prog-fill" style="width:71%;background:var(--teal)"></div></div><span class="prog-val up">71%</span></div>
          <div class="prog-row"><span class="prog-label">MTY</span><div class="prog-bar"><div class="prog-fill" style="width:68%;background:var(--teal)"></div></div><span class="prog-val neutral">68%</span></div>
          <div class="prog-row"><span class="prog-label">Puebla</span><div class="prog-bar"><div class="prog-fill" style="width:55%;background:var(--amber)"></div></div><span class="prog-val neutral">55%</span></div>
          <div class="prog-row"><span class="prog-label">Tijuana</span><div class="prog-bar"><div class="prog-fill" style="width:48%;background:var(--amber)"></div></div><span class="prog-val down">48%</span></div>
          <div class="prog-row"><span class="prog-label">Mérida</span><div class="prog-bar"><div class="prog-fill" style="width:41%;background:var(--red)"></div></div><span class="prog-val down">41%</span></div>
          <div class="prog-row"><span class="prog-label">Otros</span><div class="prog-bar"><div class="prog-fill" style="width:38%;background:var(--red)"></div></div><span class="prog-val down">38%</span></div>
        </div>
        <div style="margin-top: 1rem;">
          <div class="chart-wrap" style="height:140px"><canvas id="distChart"></canvas></div>
        </div>
      </div>

      <!-- KPI 3: VISIBILIDAD CANAL MODERNO -->
      <div class="card">
        <div class="section-header">
          <div class="section-num">03</div>
          <span class="section-title">Visibilidad · Canal Moderno</span>
        </div>
        <div class="metrics-row">
          <div class="metric">
            <div class="metric-label">Presencia Nac.</div>
            <div class="metric-value" style="color:var(--green)">81.2%</div>
            <div class="metric-sub up">▲ +4pp vs Q4</div>
          </div>
          <div class="metric">
            <div class="metric-label">SKUs en exhib.</div>
            <div class="metric-value" style="color:var(--teal)">3.2 / 5</div>
            <div class="metric-sub neutral">avg por tienda</div>
          </div>
        </div>
        <div class="channel-grid">
          <div class="channel-card">
            <div class="channel-name">WALMART</div>
            <div class="channel-pct" style="color:var(--green)">94%</div>
            <div class="channel-sku">4.1 SKUs</div>
          </div>
          <div class="channel-card">
            <div class="channel-name">OXXO</div>
            <div class="channel-pct" style="color:var(--green)">88%</div>
            <div class="channel-sku">1.8 SKUs</div>
          </div>
          <div class="channel-card">
            <div class="channel-name">CHEDRAUI</div>
            <div class="channel-pct" style="color:var(--teal)">82%</div>
            <div class="channel-sku">3.7 SKUs</div>
          </div>
          <div class="channel-card">
            <div class="channel-name">SORIANA</div>
            <div class="channel-pct" style="color:var(--amber)">74%</div>
            <div class="channel-sku">3.2 SKUs</div>
          </div>
          <div class="channel-card">
            <div class="channel-name">FARMACIAS</div>
            <div class="channel-pct" style="color:var(--amber)">69%</div>
            <div class="channel-sku">2.9 SKUs</div>
          </div>
          <div class="channel-card">
            <div class="channel-name">HEB</div>
            <div class="channel-pct" style="color:var(--red)">51%</div>
            <div class="channel-sku">2.1 SKUs</div>
          </div>
        </div>
        <div style="margin-top: 1rem;">
          <div class="chart-wrap" style="height:140px"><canvas id="visChart"></canvas></div>
        </div>
      </div>
    </div>

    <!-- KPI 4: PRECIOS VS COMPETENCIA -->
    <div class="card">
      <div class="section-header">
        <div class="section-num">04</div>
        <span class="section-title">Precios vs Competencia</span>
        <span class="section-desc">PVP Promedio · MXN · Mar 2025</span>
      </div>
      <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1.25rem; align-items: start;">
        <div>
          <div class="metrics-row" style="margin-bottom:0.875rem">
            <div class="metric">
              <div class="metric-label">Índice de precio</div>
              <div class="metric-value" style="color:var(--amber)">+8.4%</div>
              <div class="metric-sub neutral">vs promedio categ.</div>
            </div>
            <div class="metric">
              <div class="metric-label">Posición</div>
              <div class="metric-value" style="color:var(--text)">#2</div>
              <div class="metric-sub neutral">de 5 marcas</div>
            </div>
          </div>
          <table class="price-table">
            <thead>
              <tr><th>Marca</th><th>PVP (MXN)</th><th>vs ALLIVIAX</th><th>Posición</th></tr>
            </thead>
            <tbody>
              <tr><td style="color:var(--muted)">BrandRex</td><td>$198</td><td><span class="tag-down">-12%</span></td><td>Líder precio</td></tr>
              <tr class="alliviax"><td>▶ ALLIVIAX</td><td>$225</td><td>—</td><td>#2</td></tr>
              <tr><td style="color:var(--muted)">MediCure Pro</td><td>$231</td><td><span class="tag-up">+2.7%</span></td><td>#3</td></tr>
              <tr><td style="color:var(--muted)">PharmaMax</td><td>$248</td><td><span class="tag-up">+10.2%</span></td><td>#4</td></tr>
              <tr><td style="color:var(--muted)">GenéricoX</td><td>$160</td><td><span class="tag-down">-28.9%</span></td><td>Bajo precio</td></tr>
            </tbody>
          </table>
        </div>
        <div>
          <div class="chart-wrap" style="height:220px"><canvas id="priceChart"></canvas></div>
        </div>
      </div>
      <div class="alert-row">
        <div class="alert"><span class="alert-dot" style="background:var(--amber)"></span>ALLIVIAX 8.4% sobre el promedio de categoría</div>
        <div class="alert"><span class="alert-dot" style="background:var(--red)"></span>BrandRex gana participación por precio 12% menor</div>
        <div class="alert"><span class="alert-dot" style="background:var(--green)"></span>Elasticidad estimada: -1.4 (sensible a precio)</div>
      </div>
    </div>

  </div><!-- /content -->

  <!-- AI SIDEBAR -->
  <div class="sidebar">
    <div class="ai-header">
      <div class="ai-avatar">🤖</div>
      <div>
        <div class="ai-title">Agente ALLIVIAX</div>
        <div class="ai-model">claude-sonnet-4 · comercial MX</div>
      </div>
    </div>

    <div class="ai-messages" id="messages">
      <!-- Initial message -->
      <div class="msg msg-ai">
        <div class="msg-avatar">A</div>
        <div class="msg-bubble">
          ¡Hola! Soy tu agente de inteligencia comercial para <strong>ALLIVIAX México</strong>. Analizo los 4 KPIs en tiempo real:<br><br>
          <span class="kpi-tag">01 Ventas</span><span class="kpi-tag">02 Distribución</span><span class="kpi-tag">03 Visibilidad</span><span class="kpi-tag">04 Precios</span><br><br>
          ¿Qué quieres analizar hoy?
        </div>
      </div>
    </div>

    <div class="ai-input-area">
      <div class="quick-prompts">
        <button class="qp-btn" onclick="sendQuick('¿Por qué ventas están bajo objetivo en marzo?')">📉 ¿Por qué bajo objetivo?</button>
        <button class="qp-btn" onclick="sendQuick('¿Qué regiones tienen baja distribución y qué hacer?')">📍 Brechas distribución</button>
        <button class="qp-btn" onclick="sendQuick('¿Cómo está ALLIVIAX vs la competencia en precio?')">💰 Análisis de precio</button>
        <button class="qp-btn" onclick="sendQuick('Dame un resumen ejecutivo de todos los KPIs')">📊 Resumen ejecutivo</button>
        <button class="qp-btn" onclick="sendQuick('¿Cuáles son las 3 acciones prioritarias para cerrar el gap de ventas?')">🎯 Acciones prioritarias</button>
      </div>
      <div class="input-row">
        <input class="ai-input" id="userInput" type="text" placeholder="Pregunta sobre ALLIVIAX MX..." maxlength="300"/>
        <button class="send-btn" id="sendBtn" onclick="sendMessage()">➤</button>
      </div>
    </div>
  </div>
</div><!-- /main -->

<script>
// ===== CHART.JS SETUP =====
Chart.defaults.color = '#64748b';
Chart.defaults.borderColor = 'rgba(255,255,255,0.07)';

const TEAL = '#2dd4bf';
const AMBER = '#fbbf24';
const RED = '#f87171';
const GREEN = '#4ade80';
const SURFACE2 = '#1c2130';

// 1. SALES CHART
const salesCtx = document.getElementById('salesChart').getContext('2d');
new Chart(salesCtx, {
  type: 'bar',
  data: {
    labels: ['Oct', 'Nov', 'Dic', 'Ene', 'Feb', 'Mar'],
    datasets: [
      {
        label: 'Venta Real',
        data: [4200, 4650, 5100, 4380, 4280, 4800],
        backgroundColor: 'rgba(45,212,191,0.25)',
        borderColor: TEAL,
        borderWidth: 1.5,
        borderRadius: 4,
      },
      {
        label: 'Objetivo',
        data: [4500, 4800, 5300, 4600, 4700, 5200],
        type: 'line',
        borderColor: AMBER,
        borderWidth: 2,
        borderDash: [5,4],
        pointBackgroundColor: AMBER,
        pointRadius: 3,
        tension: 0.3,
        fill: false,
      }
    ]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false } },
    scales: {
      x: { ticks: { font: { family: 'Space Mono', size: 10 } } },
      y: {
        ticks: {
          font: { family: 'Space Mono', size: 10 },
          callback: v => '$' + (v/1000).toFixed(0) + 'K'
        }
      }
    }
  }
});

// 2. DISTRIBUTION CHART (donut)
const distCtx = document.getElementById('distChart').getContext('2d');
new Chart(distCtx, {
  type: 'doughnut',
  data: {
    labels: ['Activos', 'Sin cobertura', 'Potencial'],
    datasets: [{
      data: [63.4, 21.6, 15],
      backgroundColor: [TEAL, SURFACE2, AMBER],
      borderColor: '#151821',
      borderWidth: 3,
    }]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    cutout: '68%',
    plugins: {
      legend: {
        display: true,
        position: 'right',
        labels: { font: { family: 'Space Mono', size: 9 }, boxWidth: 10, padding: 10, color: '#94a3b8' }
      }
    }
  }
});

// 3. VISIBILITY CHART (radar or bar)
const visCtx = document.getElementById('visChart').getContext('2d');
new Chart(visCtx, {
  type: 'bar',
  data: {
    labels: ['Walmart','OXXO','Chedraui','Soriana','Farmacias','HEB'],
    datasets: [{
      label: 'Presencia %',
      data: [94, 88, 82, 74, 69, 51],
      backgroundColor: [GREEN, GREEN, TEAL, AMBER, AMBER, RED].map(c => c + '44'),
      borderColor: [GREEN, GREEN, TEAL, AMBER, AMBER, RED],
      borderWidth: 1.5,
      borderRadius: 4,
    }]
  },
  options: {
    indexAxis: 'y',
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false } },
    scales: {
      x: {
        min: 0, max: 100,
        ticks: { font: { family: 'Space Mono', size: 9 }, callback: v => v + '%' }
      },
      y: { ticks: { font: { family: 'Space Mono', size: 9 } } }
    }
  }
});

// 4. PRICE CHART
const priceCtx = document.getElementById('priceChart').getContext('2d');
new Chart(priceCtx, {
  type: 'bar',
  data: {
    labels: ['GenéricoX','BrandRex','ALLIVIAX','MediCure','PharmaMax'],
    datasets: [{
      data: [160, 198, 225, 231, 248],
      backgroundColor: ['rgba(100,116,139,0.3)','rgba(100,116,139,0.3)','rgba(45,212,191,0.35)','rgba(100,116,139,0.3)','rgba(100,116,139,0.3)'],
      borderColor: ['#475569','#475569', TEAL,'#475569','#475569'],
      borderWidth: 1.5,
      borderRadius: 4,
    }]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false } },
    scales: {
      x: { ticks: { font: { family: 'Space Mono', size: 9 }, maxRotation: 30 } },
      y: {
        min: 130,
        ticks: { font: { family: 'Space Mono', size: 9 }, callback: v => '$' + v }
      }
    }
  }
});

// ===== AI AGENT =====
const KPI_CONTEXT = `
Eres un agente de inteligencia comercial especializado en la marca ALLIVIAX en México.

DATOS ACTUALES (Q1 2025 - Marzo):

KPI 1 - VENTA MENSUAL VS OBJETIVO:
- Venta Mar'25: $4.8M MXN | Objetivo: $5.2M | Cumplimiento: 92.3%
- Gap: -$400K | YTD acumulado: $13.6M (+8.7% vs 2024)
- Tendencia: Feb también bajo objetivo, 2 meses consecutivos
- CDMX concentra 41% de ventas totales
- GDL creciendo +22% YoY, mejor región
- Historial: Oct $4.2M, Nov $4.65M, Dic $5.1M, Ene $4.38M, Feb $4.28M, Mar $4.8M
- Objetivos: Oct $4.5M, Nov $4.8M, Dic $5.3M, Ene $4.6M, Feb $4.7M, Mar $5.2M

KPI 2 - DISTRIBUCIÓN NUMÉRICA CANAL TRADICIONAL:
- Cobertura nacional: 63.4% (objetivo: 75%, brecha: -11.6pp)
- Puntos activos: 18,420 (crecimiento de +2,100 vs Q4 2024)
- Por región: CDMX 78%, GDL 71%, MTY 68%, Puebla 55%, Tijuana 48%, Mérida 41%, Otros 38%
- Oportunidad crítica: Sur/Sureste y frontera norte con menos del 50%

KPI 3 - VISIBILIDAD CANAL MODERNO:
- Presencia nacional: 81.2% (+4pp vs Q4)
- SKUs promedio por tienda: 3.2 de 5 disponibles
- Por cadena: Walmart 94% (4.1 SKUs), OXXO 88% (1.8 SKUs), Chedraui 82% (3.7 SKUs), Soriana 74% (3.2 SKUs), Farmacias 69% (2.9 SKUs), HEB 51% (2.1 SKUs)
- HEB es el canal más débil; oportunidad en la región noreste

KPI 4 - PRECIOS VS COMPETENCIA:
- Precio ALLIVIAX: $225 MXN PVP promedio
- BrandRex: $198 (-12% vs ALLIVIAX) — líder en precio bajo, ganando market share
- MediCure Pro: $231 (+2.7%)
- PharmaMax: $248 (+10.2%)
- GenéricoX: $160 (-28.9%)
- Índice precio ALLIVIAX: +8.4% sobre el promedio de la categoría
- Elasticidad estimada: -1.4 (consumidores sensibles al precio)
- ALLIVIAX ocupa posición #2 en precio (2da más cara tras PharmaMax si excluimos el genérico)

CONTEXTO DE MERCADO:
- Categoría en crecimiento del +6% en México
- Temporada alta: Q4 (Dic) por epidemia de resfriados/gripa
- Canal tradicional (tienditas, farmacias de barrio) representa 58% del volumen total
- Canal moderno (autoservicios, conveniencia) 42% del volumen
- Principal competidor BrandRex lanzó promoción 3x2 en febrero, explicando parte del gap

Responde siempre en español. Sé conciso, analítico y orientado a acción. Usa datos específicos del contexto. 
Cuando identifiques un problema, propón 1-3 acciones concretas y medibles.
Usa emojis con moderación para estructurar mejor tus respuestas.
`;

const messages = [];
const msgsEl = document.getElementById('messages');
const inputEl = document.getElementById('userInput');
const sendBtn = document.getElementById('sendBtn');

function addMsg(role, text) {
  const isAI = role === 'ai';
  const div = document.createElement('div');
  div.className = 'msg ' + (isAI ? 'msg-ai' : 'msg-user');
  div.innerHTML = `
    <div class="msg-avatar">${isAI ? 'A' : 'U'}</div>
    <div class="msg-bubble">${text}</div>
  `;
  msgsEl.appendChild(div);
  msgsEl.scrollTop = msgsEl.scrollHeight;
  return div;
}

function addTyping() {
  const div = document.createElement('div');
  div.className = 'msg msg-ai';
  div.id = 'typing';
  div.innerHTML = `
    <div class="msg-avatar">A</div>
    <div class="msg-bubble"><div class="typing"><span></span><span></span><span></span></div></div>
  `;
  msgsEl.appendChild(div);
  msgsEl.scrollTop = msgsEl.scrollHeight;
}

function removeTyping() {
  const t = document.getElementById('typing');
  if (t) t.remove();
}

function formatResponse(text) {
  return text
    .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
    .replace(/\n/g, '<br>');
}

async function sendMessage() {
  const text = inputEl.value.trim();
  if (!text || sendBtn.disabled) return;

  inputEl.value = '';
  sendBtn.disabled = true;

  addMsg('user', text);
  messages.push({ role: 'user', content: text });

  addTyping();

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: KPI_CONTEXT,
        messages: messages
      })
    });

    const data = await response.json();
    removeTyping();

    const reply = data.content?.map(b => b.text || '').join('') || 'Lo siento, no pude procesar la solicitud.';
    addMsg('ai', formatResponse(reply));
    messages.push({ role: 'assistant', content: reply });

  } catch (err) {
    removeTyping();
    addMsg('ai', '⚠️ Error de conexión. Verifica tu API key de Anthropic e intenta de nuevo.');
  }

  sendBtn.disabled = false;
  inputEl.focus();
}

function sendQuick(text) {
  inputEl.value = text;
  sendMessage();
}

inputEl.addEventListener('keydown', e => {
  if (e.key === 'Enter') sendMessage();
});
</script>
</body>
</html>
