<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Painel Frota CAT — Visão de Gestão</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
<style>
:root,[data-theme="dark"]{
  --bg:#0b0f14;--surface:#121820;--surface-2:#1a222d;--surface-3:#24303f;--border:#26354a;
  --text:#f0f4f8;--muted:#8a9cae;--faint:#526477;--primary:#ffcd11;--primary-2:#e1b300;
  --red:#ff4d5a;--red-bg:rgba(255, 77, 90, 0.12);--amber:#ffad1f;--amber-bg:rgba(255, 173, 31, 0.12);--green:#28c76f;--green-bg:rgba(40, 199, 111, 0.12);
  --blue:#40a0ff;--blue-bg:rgba(64, 160, 255, 0.12);--purple:#a370ff;--purple-bg:rgba(163, 112, 255, 0.12);
  --shadow:0 8px 24px rgba(0,0,0,.3);--radius:12px;--radius-sm:8px;
}
[data-theme="light"]{
  --bg:#f4f6f9;--surface:#ffffff;--surface-2:#f8fafc;--surface-3:#e2e8f0;--border:#cbd5e1;
  --text:#0f172a;--muted:#475569;--faint:#94a3b8;--primary:#d97706;--primary-2:#b45309;
  --red:#dc2626;--red-bg:#fef2f2;--amber:#d97706;--amber-bg:#fffbeb;--green:#16a34a;--green-bg:#f0fdf4;
  --blue:#2563eb;--blue-bg:#eff6ff;--purple:#7c3aed;--purple-bg:#f5f3ff;
  --shadow:0 4px 12px rgba(15,23,42,.05);
}
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:Inter,Segoe UI,system-ui,sans-serif;background:var(--bg);color:var(--text);line-height:1.5;font-size:14px}
button,input,select{font:inherit}
.topbar{display:flex;align-items:center;justify-content:space-between;gap:16px;flex-wrap:wrap;padding:16px 24px;background:var(--surface);border-bottom:1px solid var(--border);position:sticky;top:0;z-index:20;backdrop-filter:blur(10px)}
.brand{display:flex;align-items:center;gap:14px}.brand-mark{width:46px;height:46px;border-radius:10px;background:var(--primary);color:#000;display:grid;place-items:center;font-weight:900;font-size:16px}.brand h1{font-size:18px;letter-spacing:-.02em;font-weight:700}.brand p{font-size:12px;color:var(--muted)}
.top-actions,.filters,.subfilters,.nav,.toolbar{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
.btn{border:1px solid var(--border);background:var(--surface-2);color:var(--text);padding:8px 14px;border-radius:8px;cursor:pointer;transition:.2s;font-weight:600;font-size:13px}
.btn:hover{transform:translateY(-1px);background:var(--surface-3);box-shadow:var(--shadow)}
.btn.primary{background:var(--primary);border-color:var(--primary);color:#000}.btn.primary:hover{background:var(--primary-2)}
.btn.soft{background:var(--surface)}
select,input[type=text]{background:var(--surface-2);border:1px solid var(--border);color:var(--text);padding:8px 12px;border-radius:8px;min-height:38px;transition:0.2s}
select:focus,input[type=text]:focus{border-color:var(--primary);outline:none}
label.meta{font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:.08em;font-weight:700}
.shell{max-width:1440px;margin:0 auto;padding:20px 24px 48px}
.filters-card,.card,.kpi,.timeline-item,.alert,.table-box{background:var(--surface);border:1px solid var(--border);border-radius:var(--radius);box-shadow:var(--shadow)}
.filters-card{padding:16px;margin-bottom:16px}
.filters-grid{display:grid;grid-template-columns:repeat(6,minmax(0,1fr));gap:12px}
.filters-grid .field{display:flex;flex-direction:column;gap:4px}
@media(max-width:1180px){.filters-grid{grid-template-columns:repeat(3,1fr)}}
@media(max-width:720px){.filters-grid{grid-template-columns:1fr 1fr}}
@media(max-width:520px){.filters-grid{grid-template-columns:1fr}}
.nav{margin:14px 0 18px;padding-bottom:4px;overflow:auto;border-bottom:1px solid var(--border)}.nav button{white-space:nowrap;border:none;background:transparent;color:var(--muted);padding:10px 16px;font-weight:700;position:relative;border-radius:0}.nav button.on{color:var(--primary)}.nav button.on::after{content:"";position:absolute;bottom:-1px;left:0;right:0;height:3px;background:var(--primary);border-radius:3px 3px 0 0}
.hero{display:grid;grid-template-columns:1.2fr .8fr;gap:16px;margin-bottom:16px}.hero-panel{padding:20px}.hero h2{font-size:20px;margin-bottom:6px;font-weight:700}.hero p{color:var(--muted);max-width:72ch;font-size:13px}.hero-stats{display:grid;grid-template-columns:1fr 1fr;gap:12px}.mini{padding:12px;border-radius:10px;background:var(--surface-2);border:1px solid var(--border)}
@media(max-width:960px){.hero{grid-template-columns:1fr}.hero-stats{grid-template-columns:1fr 1fr}}
.kpis{display:grid;grid-template-columns:repeat(5,1fr);gap:14px;margin-bottom:16px}.kpi{padding:16px;position:relative;overflow:hidden}.kpi::before{content:"";position:absolute;left:0;top:0;bottom:0;width:4px;background:var(--primary)}.kpi.red::before{background:var(--red)}.kpi.amber::before{background:var(--amber)}.kpi.green::before{background:var(--green)}.kpi.blue::before{background:var(--blue)}
.kpi .lab{font-size:11px;color:var(--muted);text-transform:uppercase;letter-spacing:.08em;font-weight:700}.kpi .val{font-size:28px;font-weight:800;margin-top:4px;letter-spacing:-.03em}.kpi .sub{font-size:11px;color:var(--faint);margin-top:2px}
@media(max-width:1180px){.kpis{grid-template-columns:repeat(3,1fr)}} @media(max-width:720px){.kpis{grid-template-columns:1fr 1fr}} @media(max-width:520px){.kpis{grid-template-columns:1fr}}
.grid{display:grid;gap:16px}.g2{grid-template-columns:repeat(2,1fr)}.g3{grid-template-columns:repeat(3,1fr)}
@media(max-width:1080px){.g3,.g2{grid-template-columns:1fr}} 
.card{padding:20px}.card h3{font-size:16px;margin-bottom:2px;font-weight:700}.hint{font-size:12px;color:var(--muted);margin-bottom:16px}
.chart-wrap{position:relative;min-height:300px}.chart-wrap.short{min-height:220px}
.page{display:none}
.page.on{display:block}
.table-scroll{max-height:520px;overflow:auto;border:1px solid var(--border);border-radius:10px}
table{width:100%;border-collapse:collapse}th,td{padding:12px 14px;border-bottom:1px solid var(--border);text-align:left;font-size:13px}th{position:sticky;top:0;background:var(--surface-2);font-size:11px;text-transform:uppercase;letter-spacing:.08em;color:var(--muted);z-index:5}tr:hover td{background:var(--surface-2)}td.num{text-align:right;font-variant-numeric:tabular-nums}
.badge{display:inline-flex;align-items:center;gap:6px;padding:3px 8px;border-radius:6px;font-size:11px;font-weight:700}.p1{background:var(--red-bg);color:var(--red)}.p2{background:var(--amber-bg);color:var(--amber)}.p3{background:var(--green-bg);color:var(--green)}.soft{background:var(--surface-2);color:var(--muted);border:1px solid var(--border)}
.progress-bar-group{display:flex;align-items:center;gap:4px;width:100px;background:var(--surface-3);height:14px;border-radius:4px;overflow:hidden;padding:0 2px}
.progress-bar-segment{height:10px;border-radius:2px}
.alerts{display:grid;gap:10px}
.alert{padding:14px 16px;border-radius:8px;transition:0.2s}
.alert strong{color:var(--text);font-weight:700}
.alert.red{border-left:4px solid var(--red);background:linear-gradient(90deg, var(--red-bg), transparent)}
.alert.amber{border-left:4px solid var(--amber);background:linear-gradient(90deg, var(--amber-bg), transparent)}
.alert.blue{border-left:4px solid var(--blue);background:linear-gradient(90deg, var(--blue-bg), transparent);animation:pulseGlow 2s infinite alternate}
@keyframes pulseGlow{0%{box-shadow:0 0 4px rgba(64,160,255,0.1)}100%{box-shadow:0 0 12px rgba(64,160,255,0.3)}}
.trend-indicator{font-weight:bold;margin-right:4px;font-size:14px}
.trend-up{color:var(--red)}
.trend-down{color:var(--green)}
.trend-stable{color:var(--muted)}
.pill-row{display:flex;flex-wrap:wrap;gap:6px}.pill{padding:4px 10px;border-radius:6px;background:var(--surface-2);border:1px solid var(--border);font-size:12px;color:var(--muted);font-weight:500}
.footer{padding:32px 0;color:var(--faint);font-size:12px;text-align:center}
.hidden{display:none !important}
</style>
</head>
<body data-theme="dark">
<header class="topbar">
  <div class="brand">
    <div class="brand-mark">CAT</div>
    <div>
      <h1>Painel de Confiabilidade — Frota CAT</h1>
      <p>Gestão Operacional de Ativos, Tendências e Precursores de Falha</p>
    </div>
  </div>
  <div class="top-actions">
    <button class="btn soft" id="themeToggle">Alternar Tema</button>
    <button class="btn soft" id="btnHelp">Formato CSV</button>
    <button class="btn soft" id="btnExportCsv">Exportar Recorte</button>
    <button class="btn primary" id="btnUpload">Carregar CSV</button>
    <input type="file" id="fileInput" accept=".csv,text/csv" class="hidden">
  </div>
</header>

<main class="shell">
  <section class="filters-card">
    <div class="filters-grid">
      <div class="field"><label class="meta">Frota</label><select id="selFrota"></select></div>
      <div class="field"><label class="meta">Máquina</label><select id="selMaq"></select></div>
      <div class="field"><label class="meta">Sistema</label><select id="selSistema"></select></div>
      <div class="field"><label class="meta">Nível</label><select id="selNivel"></select></div>
      <div class="field"><label class="meta">Operador</label><select id="selOperador"></select></div>
      <div class="field"><label class="meta">Período</label><select id="selPeriodo"></select></div>
    </div>
    <div class="toolbar" style="margin-top:12px;justify-content:space-between">
      <div class="pill-row" id="scopePills"></div>
      <div class="subfilters">
        <button class="btn soft" id="btnReset">Limpar Filtros</button>
        <button class="btn soft" id="btnSample">Gerar Dados Amostra</button>
      </div>
    </div>
  </section>

  <section class="hero">
    <div class="filters-card hero-panel">
      <h2>Foco em Engenharia de Confiabilidade e Tendências</h2>
      <p>Dashboard configurado para tomada de decisão imediata: identifica desvios críticos e sequências repetitivas de alarmes antes que ocorra a parada corretiva forçada.</p>
      <div class="pill-row" style="margin-top:14px">
        <span class="pill">▲ Indicador de Degradação</span>
        <span class="pill">⇄ Cruzamento de Eventos Precursores</span>
        <span class="pill">⚡ Score Ponderado (Recência + Reincidência)</span>
      </div>
    </div>
    <div class="filters-card hero-panel">
      <div class="hero-stats">
        <div class="mini"><div class="meta">Último Evento</div><div id="lastUpdate" style="font-size:18px;font-weight:800;margin-top:6px;color:var(--primary)">—</div></div>
        <div class="mini"><div class="meta">Status de Cobertura</div><div id="coverage" style="font-size:16px;font-weight:800;margin-top:6px">Sem dados</div></div>
        <div class="mini"><div class="meta">Ativos Críticos (Score ≥ 20)</div><div id="criticalMachines" style="font-size:22px;font-weight:800;margin-top:6px;color:var(--red)">0</div></div>
        <div class="mini"><div class="meta">Alarme Precursor Ativo</div><div id="precursorStat" style="font-size:13px;font-weight:700;margin-top:6px;color:var(--blue)">—</div></div>
      </div>
    </div>
  </section>

  <section class="nav" aria-label="Navegação do painel">
    <button class="on" data-page="executivo">Visão Executiva</button>
    <button data-page="ativos">Ativos Críticos</button>
    <button data-page="timeline">Timeline Técnica</button>
    <button data-page="analitico">Alarmes e Sistemas</button>
    <button data-page="operadores">Operadores</button>
    <button data-page="dados">Dados Analíticos</button>
  </section>

  <section id="executivo" class="page on">
    <div class="kpis">
      <div class="kpi"><div class="lab">Total Alarmes</div><div class="val" id="kpiTotal">0</div><div class="sub" id="kpiTotalSub">Amostra</div></div>
      <div class="kpi red"><div class="lab">Eventos P1</div><div class="val" id="kpiP1">0</div><div class="sub" id="kpiP1Sub">Ação Imediata</div></div>
      <div class="kpi amber"><div class="lab">Eventos P2</div><div class="val" id="kpiP2">0</div><div class="sub" id="kpiP2Sub">Atenção Operacional</div></div>
      <div class="kpi green"><div class="lab">Eventos P3</div><div class="val" id="kpiP3">0</div><div class="sub" id="kpiP3Sub">Monitoramento</div></div>
      <div class="kpi blue"><div class="lab">Score Médio Frota</div><div class="val" id="kpiScore">0</div><div class="sub" id="kpiScoreSub">Criticidade Global</div></div>
    </div>
    <div class="grid g2">
      <article class="card">
        <h3>Criticidade por Subsistema</h3>
        <div class="hint">Volume de alarmes ponderado por severidade (P1=9, P2=4, P3=1)</div>
        <div class="chart-wrap"><canvas id="chartSistema"></canvas></div>
      </article>
      <article class="card">
        <h3>Tendência Temporal de Ocorrências</h3>
        <div class="hint">Análise gráfica de aceleração ou controle de anomalias diárias</div>
        <div class="chart-wrap"><canvas id="chartTrend"></canvas></div>
      </article>
    </div>
    <div class="grid g2" style="margin-top:16px">
      <article class="card">
        <h3>Diagnósticos Importantes & Precursores</h3>
        <div class="hint">Identificação automática de progressão de falhas iminentes.</div>
        <div class="alerts" id="alertsBox"></div>
      </article>
      <article class="card">
        <h3>Rank de Ativos Críticos (Resumido)</h3>
        <div class="hint">Ativos ordenados pelo Score de Criticidade com indicadores de tendência.</div>
        <div id="machineSummary"></div>
      </article>
    </div>
  </section>

  <section id="ativos" class="page">
    <div class="grid g2">
      <article class="card">
        <h3>Top Ativos por Score de Confiabilidade</h3>
        <div class="hint">Score considera recorrência, peso de nível e tempo desde a última falha.</div>
        <div class="chart-wrap"><canvas id="chartMachines"></canvas></div>
      </article>
      <article class="card">
        <h3>Matriz de Criticidade e Severidade</h3>
        <div class="hint">Visualização direta da distribuição de impacto e tendência por máquina.</div>
        <div class="table-scroll"><table><thead><tr><th>Máquina</th><th>Frota</th><th>Tendência</th><th>Score</th><th>Mix Severidade</th><th>Último Evento</th></tr></thead><tbody id="tbMachines"></tbody></table></div>
      </article>
    </div>
  </section>

  <section id="timeline" class="page">
    <article class="card">
      <h3>Linha do Tempo Cronológica do Ativo</h3>
      <div class="hint">Selecione uma máquina nos filtros superiores para obter o encadeamento de alarmes e rastrear sintomas precursores.</div>
      <div class="timeline" id="timelineBox"></div>
    </article>
  </section>

  <section id="analitico" class="page">
    <div class="grid g2">
      <article class="card">
        <h3>Modos de Falha / Alarmes Recorrentes</h3>
        <div class="hint">Frequência bruta e percentual de participação na frota filtrada.</div>
        <div class="table-scroll"><table><thead><tr><th>Alarme</th><th>Sistema</th><th>Nível</th><th>Ocorrências</th><th>Share</th></tr></thead><tbody id="tbAlarmes"></tbody></table></div>
      </article>
      <article class="card">
        <h3>Resumo Gerencial por Sistema</h3>
        <div class="hint">Mapeamento direto de criticidade por área técnica do equipamento.</div>
        <div class="table-scroll"><table><thead><tr><th>Sistema</th><th>Total</th><th>P1</th><th>P2</th><th>P3</th><th>Carga Crítica</th></tr></thead><tbody id="tbSistemas"></tbody></table></div>
      </article>
    </div>
  </section>

  <section id="operadores" class="page">
    <div class="grid g2">
      <article class="card">
        <h3>Operadores com Maior Carga de Exposição</h3>
        <div class="hint">Indicador técnico quantitativo de eventos ocorridos durante a jornada.</div>
        <div class="chart-wrap short"><canvas id="chartOperador"></canvas></div>
      </article>
      <article class="card">
        <h3>Análise Consolidada por Operador</h3>
        <div class="hint">Volume total, diversidade de frotas e peso crítico gerado.</div>
        <div class="table-scroll"><table><thead><tr><th>Operador</th><th>Ocorrências</th><th>Máquinas Distintas</th><th>Carga de Severidade</th></tr></thead><tbody id="tbOperadores"></tbody></table></div>
      </article>
    </div>
  </section>

  <section id="dados" class="page">
    <article class="card">
      <h3>Base Operacional Completa (Registros Filtrados)</h3>
      <div class="hint">Mecanismo de busca textual integrado para auditoria técnica detalhada.</div>
      <div class="table-tools"><input type="text" id="searchTable" placeholder="Filtrar por TAG, descrição, ativo ou operador..."></div>
      <div class="table-scroll"><table><thead><tr><th>Data</th><th>Frota</th><th>Máquina</th><th>Sistema</th><th>Alarme</th><th>Nível</th><th>Operador</th><th>TAG</th></tr></thead><tbody id="tbRaw"></tbody></table></div>
    </article>
  </section>

  <footer class="footer">Processamento em tempo de execução local. Interface otimizada para Engenharia de Manutenção Preditiva e Confiabilidade da Frota.</footer>
</main>

<dialog id="helpDialog" style="padding:0;border:none;border-radius:12px;max-width:760px;width:min(92vw,760px)">
  <div style="padding:22px;background:var(--surface);color:var(--text);border:1px solid var(--border);border-radius:12px">
    <div style="display:flex;justify-content:space-between;align-items:center;gap:12px;margin-bottom:12px"><h3>Formato de Colunas para Importação (CSV)</h3><button class="btn soft" id="closeHelp">Fechar</button></div>
    <p style="color:var(--muted);margin-bottom:12px">Certifique-se de carregar arquivos delimitados por vírgula ou ponto-e-vírgula contendo as colunas padrão abaixo:</p>
    <div class="pill-row">
      <span class="pill">Frota</span>
      <span class="pill">Máquina</span>
      <span class="pill">Data da Ocorrência_</span>
      <span class="pill">Descrição do alarme</span>
      <span class="pill">Nível do evento</span>
      <span class="pill">Operador</span>
      <span class="pill">TAG</span>
    </div>
  </div>
</dialog>

<script>
const state = { raw: [], data: [], charts: {}, sampleLoaded: false };
const els = {
  fileInput: document.getElementById('fileInput'), btnUpload: document.getElementById('btnUpload'), btnHelp: document.getElementById('btnHelp'),
  closeHelp: document.getElementById('closeHelp'), helpDialog: document.getElementById('helpDialog'), themeToggle: document.getElementById('themeToggle'),
  selFrota: document.getElementById('selFrota'), selMaq: document.getElementById('selMaq'), selSistema: document.getElementById('selSistema'),
  selNivel: document.getElementById('selNivel'), selOperador: document.getElementById('selOperador'), selPeriodo: document.getElementById('selPeriodo'),
  btnReset: document.getElementById('btnReset'), btnSample: document.getElementById('btnSample'), btnExportCsv: document.getElementById('btnExportCsv'),
  searchTable: document.getElementById('searchTable'), scopePills: document.getElementById('scopePills')
};
const severityWeight = {1:9,2:4,3:1};
const precursorPairs = [
  ['Low Brake Accumulator Pressure','Brake Pressure Low'],
  ['High Transmission Oil Temperature','Transmission Slip Detected'],
  ['Engine Coolant Temperature High','Engine Derate Active'],
  ['Low Engine Oil Pressure','Engine Shutdown Warning'],
  ['Hydraulic Oil Temperature High','Hydraulic System Overload']
];

function systemFromAlarm(text=''){
  const t = text.toLowerCase();
  if(/brake|accumulator/.test(t)) return 'Freios';
  if(/transmission|gear|torque|slip/.test(t)) return 'Transmissão';
  if(/engine|coolant|oil pressure|derate/.test(t)) return 'Motor';
  if(/hydraulic|hoist|steering/.test(t)) return 'Hidráulico';
  if(/electrical|voltage|sensor|communication/.test(t)) return 'Elétrico';
  if(/suspension|ride/.test(t)) return 'Suspensão';
  return 'Outros';
}
function parseDateBR(s=''){
  const m = String(s).match(/(\d{2})\/(\d{2})\/(\d{4})(?:\s+(\d{2}):(\d{2}):(\d{2}))?/);
  if(!m) return null;
  return new Date(Number(m[3]), Number(m[2])-1, Number(m[1]), Number(m[4]||0), Number(m[5]||0), Number(m[6]||0));
}
function fmtDate(d){return d ? d.toLocaleString('pt-BR') : '—';}
function daysDiff(a,b){return Math.max(0,(a-b)/(1000*60*60*24));}
function uniq(v){return [...new Set(v.filter(Boolean))].sort((a,b)=>String(a).localeCompare(String(b),'pt-BR'));}
function optionize(select, arr, label='Todos'){
  select.innerHTML = '';
  [label, ...arr].forEach(v=>{
    const op = document.createElement('option'); op.value = v===label?'':v; op.textContent = v; select.appendChild(op);
  });
}
function normalizeRow(r){
  const alarm = (r['Descrição do alarme'] || r['Descricao do alarme'] || '').trim();
  const date = parseDateBR(r['Data da Ocorrência_'] || r['Data da Ocorrencia_'] || r['Data'] || '');
  const nivel = Number(r['Nível do evento'] || r['Nivel do evento'] || r['Nível'] || r['Nivel'] || 3) || 3;
  return {
    frota:(r['Frota']||'').trim(), maq:(r['Máquina']||r['Maquina']||'').trim(), date,
    alarm, nivel, operador:(r['Operador']||'Não informado').trim() || 'Não informado', tag:(r['TAG']||'').trim(), sistema: systemFromAlarm(alarm)
  };
}
function sampleData(){
  const alarms = [
    ['CAT_777G','777_01','01/06/2026 07:10:00','Low Brake Accumulator Pressure',2,'Carlos','TAG-777-01'],
    ['CAT_777G','777_01','01/06/2026 09:32:00','Brake Pressure Low',1,'Carlos','TAG-777-01'],
    ['CAT_777G','777_02','02/06/2026 11:20:00','High Transmission Oil Temperature',2,'Mateus','TAG-777-02'],
    ['CAT_777G','777_02','02/06/2026 12:10:00','Transmission Slip Detected',1,'Mateus','TAG-777-02'],
    ['CAT_785C','785_07','03/06/2026 14:20:00','Engine Coolant Temperature High',2,'Renato','TAG-785-07'],
    ['CAT_785C','785_07','03/06/2026 14:50:00','Engine Derate Active',1,'Renato','TAG-785-07'],
    ['CAT_785C','785_03','04/06/2026 08:15:00','Hydraulic Oil Temperature High',2,'Ana','TAG-785-03'],
    ['CAT_785C','785_03','04/06/2026 10:30:00','Hydraulic System Overload',2,'Ana','TAG-785-03'],
    ['CAT_777G','777_01','05/06/2026 05:50:00','Brake Pressure Low',1,'Carlos','TAG-777-01'],
    ['CAT_777G','777_03','05/06/2026 16:00:00','Electrical Voltage Low',3,'Daniel','TAG-777-03'],
    ['CAT_785C','785_05','06/06/2026 18:05:00','Low Engine Oil Pressure',2,'Marcos','TAG-785-05'],
    ['CAT_785C','785_05','06/06/2026 18:40:00','Engine Shutdown Warning',1,'Marcos','TAG-785-05'],
    ['CAT_785C','785_05','07/06/2026 07:25:00','Engine Shutdown Warning',1,'Marcos','TAG-785-05'],
    ['CAT_777G','777_02','07/06/2026 09:00:00','Transmission Slip Detected',1,'Mateus','TAG-777-02'],
    ['CAT_777G','777_04','07/06/2026 12:45:00','Steering Hydraulic Pressure Low',2,'Pedro','TAG-777-04'],
    ['CAT_777G','777_04','08/06/2026 06:30:00','Hydraulic Oil Temperature High',2,'Pedro','TAG-777-04'],
    ['CAT_785C','785_07','08/06/2026 07:05:00','Engine Derate Active',1,'Renato','TAG-785-07']
  ];
  return alarms.map(r=>normalizeRow({Frota:r[0],'Máquina':r[1],'Data da Ocorrência_':r[2],'Descrição do alarme':r[3],'Nível do evento':r[4],Operador:r[5],TAG:r[6]}));
}
function hydrateControls(){
  optionize(els.selFrota, uniq(state.raw.map(d=>d.frota)));
  optionize(els.selSistema, uniq(state.raw.map(d=>d.sistema)));
  optionize(els.selOperador, uniq(state.raw.map(d=>d.operador)));
  optionize(els.selNivel, ['1','2','3']);
  optionize(els.selPeriodo, ['24h','7d','30d']);
  syncMachineOptions();
}
function syncMachineOptions(){
  const subset = state.raw.filter(d=>!els.selFrota.value || d.frota===els.selFrota.value);
  optionize(els.selMaq, uniq(subset.map(d=>d.maq)));
}
function applyFilters(){
  const now = state.raw.reduce((m,d)=> d.date && d.date>m ? d.date : m, new Date(0));
  state.data = state.raw.filter(d=>{
    if(els.selFrota.value && d.frota !== els.selFrota.value) return false;
    if(els.selMaq.value && d.maq !== els.selMaq.value) return false;
    if(els.selSistema.value && d.sistema !== els.selSistema.value) return false;
    if(els.selNivel.value && String(d.nivel) !== els.selNivel.value) return false;
    if(els.selOperador.value && d.operador !== els.selOperador.value) return false;
    if(els.selPeriodo.value){
      const lim = { '24h':1, '7d':7, '30d':30 }[els.selPeriodo.value] || 9999;
      if(!d.date || daysDiff(now,d.date) > lim) return false;
    }
    return true;
  }).sort((a,b)=>(b.date||0)-(a.date||0));
  renderScope();
  renderAll();
}
function renderScope(){
  const pills = [];
  if(els.selFrota.value) pills.push('Frota: '+els.selFrota.value);
  if(els.selMaq.value) pills.push('Máquina: '+els.selMaq.value);
  if(els.selSistema.value) pills.push('Sistema: '+els.selSistema.value);
  if(els.selNivel.value) pills.push('Nível: P'+els.selNivel.value);
  if(els.selOperador.value) pills.push('Operador: '+els.selOperador.value);
  if(els.selPeriodo.value) pills.push('Período: '+els.selPeriodo.value);
  els.scopePills.innerHTML = pills.length ? pills.map(p=>`<span class="pill">${p}</span>`).join('') : '<span class="pill">Sem filtros ativos</span>';
}
function groupBy(arr, keyFn){return arr.reduce((m,x)=>{const k=keyFn(x); (m[k] ||= []).push(x); return m;},{});}

// ALGORITMO COMPLEMENTAR: Identifica a tendência (Se o score da máquina está acelerando na metade recente do período)
function getTrendIndicator(rows, now) {
  if (rows.length < 2) return {html: '<span class="trend-indicator trend-stable">●</span>', label: 'Estável'};
  const sorted = [...rows].sort((a,b)=>a.date - b.date);
  const midPoint = sorted[0].date.getTime() + (sorted[sorted.length-1].date.getTime() - sorted[0].date.getTime()) / 2;
  
  let scoreFirstHalf = 0;
  let scoreSecondHalf = 0;

  sorted.forEach(r => {
    const weight = severityWeight[r.nivel] || 1;
    if(r.date.getTime() <= midPoint) scoreFirstHalf += weight;
    else scoreSecondHalf += weight;
  });

  if (scoreSecondHalf > scoreFirstHalf * 1.15) {
    return {html: '<span class="trend-indicator trend-up" title="Degradação Acelerando (Aumento de Falhas)">▲</span>', label: 'Piorando'};
  } else if (scoreSecondHalf < scoreFirstHalf * 0.85) {
    return {html: '<span class="trend-indicator trend-down" title="Melhora de Comportamento Técnico">▼</span>', label: 'Melhorando'};
  }
  return {html: '<span class="trend-indicator trend-stable" title="Taxa de Falhas Linear Contínua">●</span>', label: 'Estável'};
}

function computeMachineStats(data){
  const now = data.reduce((m,d)=> d.date && d.date>m ? d.date : m, new Date(0));
  return Object.entries(groupBy(data,d=>d.maq)).map(([maq,rows])=>{
    const p1 = rows.filter(r=>r.nivel===1).length, p2 = rows.filter(r=>r.nivel===2).length, p3 = rows.filter(r=>r.nivel===3).length;
    const distinctAlarms = new Set(rows.map(r=>r.alarm)).size;
    const latest = rows.reduce((m,r)=> r.date && r.date>m ? r.date : m, new Date(0));
    const recency = Math.max(0, 15 - Math.min(15, daysDiff(now, latest)));
    const score = p1*9 + p2*4 + p3 + distinctAlarms*1.5 + recency;
    const trend = getTrendIndicator(rows, now);
    return {maq,frota:rows[0]?.frota||'',total:rows.length,p1,p2,p3,distinctAlarms,latest,score:+score.toFixed(1),trend};
  }).sort((a,b)=>b.score-a.score);
}
function computeSystemStats(data){
  return Object.entries(groupBy(data,d=>d.sistema)).map(([s,rows])=>({
    sistema:s,total:rows.length,p1:rows.filter(r=>r.nivel===1).length,p2:rows.filter(r=>r.nivel===2).length,p3:rows.filter(r=>r.nivel===3).length,
    peso: rows.reduce((a,r)=>a+(severityWeight[r.nivel]||1),0)
  })).sort((a,b)=>b.peso-a.peso);
}
function computeAlarmStats(data){
  const total = data.length || 1;
  return Object.entries(groupBy(data,d=>`${d.alarm}|||${d.sistema}|||${d.nivel}`)).map(([k,rows])=>{
    const [alarm,sistema,nivel] = k.split('|||');
    return {alarm,sistema,nivel:Number(nivel),count:rows.length,share:(rows.length/total*100)};
  }).sort((a,b)=>b.count-a.count);
}
function computeOperatorStats(data){
  return Object.entries(groupBy(data,d=>d.operador)).map(([op,rows])=>({
    operador:op,count:rows.length,machines:new Set(rows.map(r=>r.maq)).size,peso:rows.reduce((a,r)=>a+(severityWeight[r.nivel]||1),0)
  })).sort((a,b)=>b.peso-a.peso);
}
function computeTrend(data){
  const g = groupBy(data,d=> d.date ? d.date.toLocaleDateString('pt-BR') : 'Sem data');
  return Object.entries(g).map(([d,rows])=>({date:d,count:rows.length})).sort((a,b)=>{
    const pa=a.date.split('/').reverse().join('-'), pb=b.date.split('/').reverse().join('-');
    return pa.localeCompare(pb);
  });
}
function detectAlerts(data){
  const machineStats = computeMachineStats(data).slice(0,5);
  const alerts = [];
  if(machineStats[0]) alerts.push({type:'red', title:'Ativo Crítico Prioritário', text:`O equipamento <strong>${machineStats[0].maq}</strong> possui o maior score de instabilidade (${machineStats[0].score}). Recomenda-se parada programada imediata.`});
  const systems = computeSystemStats(data);
  if(systems[0]) alerts.push({type:'amber', title:'Desvio Técnico Sistêmico', text:`O subsistema <strong>${systems[0].sistema}</strong> concentra o maior peso crítico de falhas (${systems[0].peso}) no intervalo selecionado.`});
  precursorPairs.forEach(([a,b])=>{
    const hasA = data.some(d=>d.alarm===a), hasB = data.some(d=>d.alarm===b);
    if(hasA && hasB) alerts.push({type:'blue', title:'Alerta de Precursor Ativo', text:`Relação de causa e efeito detectada: <strong>${a}</strong> evoluiu para <strong>${b}</strong> nas últimas horas.`});
  });
  return alerts.slice(0,6);
}
function badgeNivel(n){return `<span class="badge p${n}">P${n}</span>`;}
function setHtml(id, html){document.getElementById(id).innerHTML = html;}
function setText(id, txt){document.getElementById(id).textContent = txt;}
function destroyCharts(){Object.values(state.charts).forEach(ch=>ch?.destroy && ch.destroy()); state.charts={};}
function makeChart(id, config){ const ctx = document.getElementById(id); if(state.charts[id]) state.charts[id].destroy(); state.charts[id]=new Chart(ctx,config); }
function renderKPIs(data){
  const p1 = data.filter(d=>d.nivel===1).length, p2 = data.filter(d=>d.nivel===2).length, p3 = data.filter(d=>d.nivel===3).length;
  const machineStats = computeMachineStats(data); const avgScore = machineStats.length ? (machineStats.reduce((a,b)=>a+b.score,0)/machineStats.length).toFixed(1) : '0';
  setText('kpiTotal', data.length); setText('kpiP1', p1); setText('kpiP1Sub', `${data.length ? ((p1/data.length)*100).toFixed(1) : '0'}% da frota`);
  setText('kpiP2', p2); setText('kpiP2Sub', `${data.length ? ((p2/data.length)*100).toFixed(1) : '0'}% da frota`);
  setText('kpiP3', p3); setText('kpiP3Sub', `${data.length ? ((p3/data.length)*100).toFixed(1) : '0'}% da frota`);
  setText('kpiScore', avgScore); setText('kpiTotalSub', `${new Set(data.map(d=>d.maq)).size} ativos analisados`);
  setText('kpiScoreSub', `Média ponderada da frota ativa`);
  const latest = data.reduce((m,d)=> d.date && d.date>m ? d.date : m, new Date(0));
  setText('lastUpdate', latest.getTime() ? fmtDate(latest) : '—');
  setText('coverage', `${new Set(data.map(d=>d.frota)).size} Frotas Ativas · ${new Set(data.map(d=>d.maq)).size} Equipamentos`);
  setText('criticalMachines', machineStats.filter(m=>m.score>=20).length);
  const firstPair = precursorPairs.find(([a,b])=>data.some(d=>d.alarm===a)&&data.some(d=>d.alarm===b));
  setText('precursorStat', firstPair ? `${firstPair[0]} → ${firstPair[1]}` : 'Nenhuma sequência detectada');
}
function renderExecutive(data){
  const systems = computeSystemStats(data).slice(0,8);
  makeChart('chartSistema',{type:'bar',data:{labels:systems.map(x=>x.sistema),datasets:[{label:'Peso de Severidade',data:systems.map(x=>x.peso),backgroundColor:'#ffcd11',borderRadius:4}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{ticks:{color:getCss('--muted')},grid:{display:false}},y:{ticks:{color:getCss('--muted')},grid:{color:getCss('--border')}}}}});
  const trend = computeTrend(data);
  makeChart('chartTrend',{type:'line',data:{labels:trend.map(x=>x.date),datasets:[{label:'Volume Ocorrências',data:trend.map(x=>x.count),borderColor:'#40a0ff',backgroundColor:'rgba(64,160,255,.08)',fill:true,tension:.25,borderWidth:2.5}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{ticks:{color:getCss('--muted')},grid:{display:false}},y:{ticks:{color:getCss('--muted')},grid:{color:getCss('--border')}}}}});
  const alerts = detectAlerts(data);
  setHtml('alertsBox', alerts.length ? alerts.map(a=>`<div class="alert ${a.type}"><strong>${a.title}:</strong> ${a.text}</div>`).join('') : '<div class="alert blue"><strong>Monitoramento Normal:</strong> Nenhuma assinatura de falha preditiva encontrada.</div>');
  const top = computeMachineStats(data).slice(0,6);
  // Inserido mini barra visual empilhada dividindo a gravidade dos eventos nos cards pequenos
  setHtml('machineSummary', top.length ? top.map(m=>{
    const totalP = m.p1 + m.p2 + m.p3 || 1;
    const wP1 = (m.p1 / totalP) * 100;
    const wP2 = (m.p2 / totalP) * 100;
    const wP3 = (m.p3 / totalP) * 100;
    return `<div style="margin-bottom:14px; padding-bottom:10px; border-bottom:1px dashed var(--border)">
              <div style="display:flex;justify-content:space-between;gap:10px; align-items:center">
                <strong>${m.trend.html} <span style="font-size:14px">${m.maq}</span></strong>
                <span class="badge soft" style="font-variant-numeric: tabular-nums">${m.score} Pts</span>
              </div>
              <div style="font-size:11px;color:var(--muted);margin:4px 0 6px">${m.frota} · Eventos: P1: ${m.p1} | P2: ${m.p2} | P3: ${m.p3}</div>
              <div class="progress-bar-group">
                <div class="progress-bar-segment" style="width:${wP1}%; background-color:var(--red)"></div>
                <div class="progress-bar-segment" style="width:${wP2}%; background-color:var(--amber)"></div>
                <div class="progress-bar-segment" style="width:${wP3}%; background-color:var(--green)"></div>
              </div>
            </div>`}).join('') : '<div class="hint">Sem dados para exibição do ranking.</div>');
}
function renderAssets(data){
  const top = computeMachineStats(data).slice(0,10);
  makeChart('chartMachines',{type:'bar',data:{labels:top.map(x=>x.maq),datasets:[{label:'Criticidade (Score)',data:top.map(x=>x.score),backgroundColor:'#a370ff',borderRadius:4}]},options:{indexAxis:'y',responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{ticks:{color:getCss('--muted')},grid:{color:getCss('--border')}},y:{ticks:{color:getCss('--muted')},grid:{display:false}}}}});
  
  // Adicionado a coluna de Tendência Visual Dinâmica (Seta Vermelha, Seta Verde ou Ponto Neutro)
  setHtml('tbMachines', top.map(m=>{
    const totalP = m.p1 + m.p2 + m.p3 || 1;
    return `<tr>
              <td><strong>${m.maq}</strong></td>
              <td>${m.frota}</td>
              <td style="text-align:center">${m.trend.html} <span style="font-size:11px; color:var(--muted)">(${m.trend.label})</span></td>
              <td class="num" style="font-weight:700">${m.score}</td>
              <td>
                <div class="progress-bar-group">
                  <div class="progress-bar-segment" style="width:${(m.p1/totalP)*100}%; background-color:var(--red)"></div>
                  <div class="progress-bar-segment" style="width:${(m.p2/totalP)*100}%; background-color:var(--amber)"></div>
                  <div class="progress-bar-segment" style="width:${(m.p3/totalP)*100}%; background-color:var(--green)"></div>
                </div>
              </td>
              <td>${fmtDate(m.latest)}</td>
            </tr>`}).join('') || '<tr><td colspan="6">Nenhum ativo crítico registrado.</td></tr>');
}
function renderTimeline(data){
  const timelineData = els.selMaq.value ? data.filter(d=>d.maq===els.selMaq.value) : data.slice(0,20);
  setHtml('timelineBox', timelineData.length ? timelineData.map(d=>`<div class="timeline-item" style="border-left: 3px solid var(--border)"><div class="timeline-top"><div><div>${badgeNivel(d.nivel)} <strong style="margin-left:8px; font-size:14px">${d.maq}</strong></div><div class="timeline-meta">${fmtDate(d.date)} · ${d.frota} · Subsistema: <strong>${d.sistema}</strong> · Operador: ${d.operador}</div></div><div class="badge soft">${d.tag || 'Sem TAG'}</div></div><div class="timeline-desc" style="font-family:monospace; font-size:13px; color:var(--primary)">${d.alarm}</div></div>`).join('') : '<div class="timeline-item">Sem sequências cronológicas de eventos para este filtro.</div>');
}
function renderAnalytical(data){
  const alarms = computeAlarmStats(data).slice(0,25);
  setHtml('tbAlarmes', alarms.map(a=>`<tr><td><code style="color:var(--blue)">${a.alarm}</code></td><td>${a.sistema}</td><td>${badgeNivel(a.nivel)}</td><td class="num">${a.count}</td><td class="num">${a.share.toFixed(1)}%</td></tr>`).join('') || '<tr><td colspan="5">Sem dados.</td></tr>');
  const systems = computeSystemStats(data);
  setHtml('tbSistemas', systems.map(s=>`<tr><td><strong>${s.sistema}</strong></td><td class="num">${s.total}</td><td class="num" style="color:var(--red)">${s.p1}</td><td class="num" style="color:var(--amber)">${s.p2}</td><td class="num" style="color:var(--green)">${s.p3}</td><td class="num" style="font-weight:700">${s.peso}</td></tr>`).join('') || '<tr><td colspan="6">Sem dados.</td></tr>');
}
function renderOperators(data){
  const ops = computeOperatorStats(data).slice(0,10);
  makeChart('chartOperador',{type:'bar',data:{labels:ops.map(o=>o.operador),datasets:[{label:'Peso de Incidentes',data:ops.map(o=>o.peso),backgroundColor:'#28c76f',borderRadius:4}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},scales:{x:{ticks:{color:getCss('--muted')}},y:{ticks:{color:getCss('--muted')}}}}});
  setHtml('tbOperadores', ops.map(o=>`<tr><td><strong>${o.operador}</strong></td><td class="num">${o.count}</td><td class="num">${o.machines}</td><td class="num" style="font-weight:700">${o.peso}</td></tr>`).join('') || '<tr><td colspan="4">Sem dados.</td></tr>');
}
function renderRaw(data){
  const q = els.searchTable.value.trim().toLowerCase();
  const rows = data.filter(d=> !q || [d.alarm,d.maq,d.operador,d.sistema,d.frota,d.tag].join(' ').toLowerCase().includes(q)).slice(0,500);
  setHtml('tbRaw', rows.map(d=>`<tr><td>${fmtDate(d.date)}</td><td>${d.frota}</td><td><strong>${d.maq}</strong></td><td>${d.sistema}</td><td>${d.alarm}</td><td>${badgeNivel(d.nivel)}</td><td>${d.operador}</td><td><small>${d.tag}</small></td></tr>`).join('') || '<tr><td colspan="8">Sem dados.</td></tr>');
}
function renderAll(){
  const data = state.data;
  renderKPIs(data); renderExecutive(data); renderAssets(data); renderTimeline(data); renderAnalytical(data); renderOperators(data); renderRaw(data);
}
function getCss(v){return getComputedStyle(document.body).getPropertyValue(v).trim();}
function loadRows(rows){ state.raw = rows.filter(r=>r.alarm && r.maq); hydrateControls(); applyFilters(); }
function exportCurrentCsv(){
  const rows = state.data.map(d=>({Frota:d.frota,Maquina:d.maq,Data:fmtDate(d.date),Sistema:d.sistema,Alarme:d.alarm,Nivel:d.nivel,Operador:d.operador,TAG:d.tag}));
  const csv = Papa.unparse(rows); const blob = new Blob([csv],{type:'text/csv;charset=utf-8;'}); const a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'recorte_painel_frota_cat.csv'; a.click(); URL.revokeObjectURL(a.href);
}

document.querySelectorAll('.nav button').forEach(b=>b.addEventListener('click',()=>{
  document.querySelectorAll('.nav button').forEach(x=>x.classList.remove('on')); b.classList.add('on');
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('on')); document.getElementById(b.dataset.page).classList.add('on');
}));
els.btnUpload.addEventListener('click',()=>els.fileInput.click());
els.fileInput.addEventListener('change',e=>{
  const file = e.target.files[0]; if(!file) return;
  Papa.parse(file,{header:true,skipEmptyLines:true,complete:(res)=>loadRows(res.data.map(normalizeRow))});
});
els.btnSample.addEventListener('click',()=>{state.sampleLoaded=true; loadRows(sampleData());});
els.btnReset.addEventListener('click',()=>{document.querySelectorAll('select').forEach(s=>s.value=''); syncMachineOptions(); applyFilters();});
els.selFrota.addEventListener('change',()=>{syncMachineOptions(); applyFilters();});
[els.selMaq,els.selSistema,els.selNivel,els.selOperador,els.selPeriodo].forEach(el=>el.addEventListener('change',applyFilters));
els.searchTable.addEventListener('input',()=>renderRaw(state.data));
els.btnHelp.addEventListener('click',()=>els.helpDialog.showModal());
els.closeHelp.addEventListener('click',()=>els.helpDialog.close());
els.themeToggle.addEventListener('click',()=>{document.body.dataset.theme = document.body.dataset.theme === 'light' ? 'dark' : 'light'; renderAll();});
els.btnExportCsv.addEventListener('click',exportCurrentCsv);
loadRows(sampleData());
</script>
</body>
</html>
