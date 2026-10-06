[index.html.html](https://github.com/user-attachments/files/33110932/index.html.html)
<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Dashboard de Gastos Mensais - Pro</title>

  <!-- Google Fonts & Supabase SDK -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

  <style>
    :root {
      --bg: #090d16;
      --panel: #111827;
      --panel-hover: #1f293d;
      --muted: #94a3b8;
      --text: #f8fafc;
      --accent: #38bdf8;
      --accent-glow: rgba(56, 189, 248, 0.15);
      --ok: #22c55e;
      --ok-glow: rgba(34, 197, 94, 0.15);
      --warn: #f59e0b;
      --bad: #ef4444;
      --bad-glow: rgba(239, 68, 68, 0.15);
      --border: rgba(255, 255, 255, 0.08);
      --border-focus: rgba(56, 189, 248, 0.5);
      --shadow: 0 12px 30px -10px rgba(0, 0, 0, 0.5);
      --radius: 16px;
      --transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: 'Plus Jakarta Sans', system-ui, -apple-system, sans-serif;
      background: radial-gradient(1200px 600px at 15% -10%, #1e293b 0, transparent 60%),
                  radial-gradient(1000px 500px at 110% -10%, #0369a1 0, transparent 50%),
                  var(--bg);
      color: var(--text);
      min-height: 100vh;
      line-height: 1.5;
    }

    .cloud-status-banner {
      background: rgba(245, 158, 11, 0.15);
      border-bottom: 1px solid rgba(245, 158, 11, 0.3);
      color: #fbbf24;
      padding: 8px 16px;
      font-size: 13px;
      text-align: center;
      font-weight: 600;
      display: none;
    }

    header {
      padding: 24px 20px;
      max-width: 1200px;
      margin: 0 auto;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid var(--border);
    }
    .brand-title {
      font-size: clamp(20px, 3vw, 26px);
      font-weight: 800;
      letter-spacing: -0.02em;
      background: linear-gradient(135deg, #ffffff 30%, var(--muted));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
    .brand-subtitle { font-size: 13px; color: var(--muted); margin-top: 2px; }

    .user-profile {
      display: flex;
      align-items: center;
      gap: 12px;
      background: rgba(255, 255, 255, 0.03);
      padding: 6px 14px;
      border-radius: 999px;
      border: 1px solid var(--border);
    }
    .user-email { font-size: 13px; font-weight: 600; color: var(--text); }

    .grid {
      display: grid;
      gap: 16px;
      padding: 24px 20px;
      max-width: 1200px;
      margin: 0 auto;
      grid-template-columns: repeat(12, 1fr);
    }

    .card {
      background: linear-gradient(180deg, rgba(255, 255, 255, 0.04) 0%, rgba(255, 255, 255, 0.01) 100%);
      backdrop-filter: blur(12px);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      padding: 20px;
      box-shadow: var(--shadow);
      transition: var(--transition);
    }
    .card.compact { padding: 16px 18px; }
    .card:hover { border-color: rgba(255, 255, 255, 0.15); }

    .span-3 { grid-column: span 3; }
    .span-4 { grid-column: span 4; }
    .span-12 { grid-column: span 12; }

    @media (max-width: 1024px) {
      .span-3 { grid-column: span 6; }
      .span-4 { grid-column: span 6; }
    }
    @media (max-width: 640px) {
      .grid { grid-template-columns: repeat(6, 1fr); }
      .span-3, .span-4 { grid-column: span 6; }
      header { flex-direction: column; align-items: flex-start; gap: 12px; }
    }

    .label {
      font-size: 11px;
      color: var(--muted);
      text-transform: uppercase;
      letter-spacing: 0.1em;
      font-weight: 700;
      display: flex;
      align-items: center;
      gap: 6px;
    }
    .card-badge {
      width: 8px; height: 8px; border-radius: 50%; display: inline-block;
    }
    .badge-bbva { background: #004481; box-shadow: 0 0 10px #004481; }
    .badge-wise { background: #2e008b; box-shadow: 0 0 10px #2e008b; }
    .badge-mp { background: #00a9e0; box-shadow: 0 0 10px #00a9e0; }
    .badge-spay { background: #1259e2; box-shadow: 0 0 10px #1259e2; }

    .value {
      font-variant-numeric: tabular-nums;
      font-size: clamp(20px, 2.8vw, 28px);
      font-weight: 800;
      margin-top: 8px;
      letter-spacing: -0.02em;
    }
    .hint { font-size: 12px; color: var(--muted); margin-top: 6px; }

    .income-input { display: flex; align-items: center; gap: 10px; margin-top: 10px; }
    input, select {
      width: 100%;
      padding: 11px 14px;
      background: rgba(0, 0, 0, 0.3);
      border: 1px solid var(--border);
      border-radius: 12px;
      color: var(--text);
      font-size: 14px;
      font-family: inherit;
      outline: none;
      transition: var(--transition);
    }
    input:focus, select:focus {
      border-color: var(--accent);
      box-shadow: 0 0 0 3px var(--accent-glow);
    }

    .btn {
      cursor: pointer;
      border: 1px solid var(--border);
      background: rgba(255, 255, 255, 0.05);
      color: var(--text);
      padding: 11px 16px;
      border-radius: 12px;
      font-weight: 600;
      font-size: 13px;
      font-family: inherit;
      transition: var(--transition);
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
      white-space: nowrap;
    }
    .btn:hover { background: rgba(255, 255, 255, 0.1); border-color: rgba(255, 255, 255, 0.2); }
    .btn.primary { background: var(--accent); color: #090d16; border: none; font-weight: 700; }
    .btn.primary:hover { background: #7dd3fc; }
    .btn.bad { border-color: rgba(239, 68, 68, 0.3); background: var(--bad-glow); color: #fca5a5; }
    .btn.bad:hover { background: rgba(239, 68, 68, 0.3); }
    .btn.sm { padding: 6px 12px; font-size: 12px; border-radius: 8px; }

    .form-grid {
      display: grid;
      gap: 12px;
      grid-template-columns: 2fr 1.2fr 1fr 1fr auto;
      align-items: end;
      margin-top: 12px;
      padding-bottom: 20px;
      border-bottom: 1px solid var(--border);
    }
    .form-group label { display: block; font-size: 12px; color: var(--muted); margin-bottom: 6px; font-weight: 500; }
    .form-actions { display: flex; gap: 8px; }

    @media (max-width: 900px) {
      .form-grid { grid-template-columns: 1fr 1fr; }
      .form-actions { grid-column: span 2; justify-content: flex-end; }
    }

    .table-container { overflow-x: auto; margin-top: 16px; }
    table { width: 100%; border-collapse: collapse; font-size: 14px; }
    th {
      color: var(--muted);
      font-weight: 600;
      font-size: 11px;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      padding: 12px 10px;
      text-align: left;
      border-bottom: 1px solid var(--border);
    }
    td { padding: 14px 10px; border-bottom: 1px solid var(--border); }
    tbody tr { transition: var(--transition); }
    tbody tr:hover { background: rgba(255, 255, 255, 0.02); }
    .right { text-align: right; }
    .tag {
      font-size: 11px;
      padding: 3px 10px;
      border-radius: 999px;
      border: 1px solid var(--border);
      background: rgba(255, 255, 255, 0.03);
      color: var(--muted);
      font-weight: 500;
    }

    .kpi-ok { color: var(--ok); text-shadow: 0 0 12px var(--ok-glow); }
    .kpi-warn { color: var(--warn); }
    .kpi-bad { color: var(--bad); text-shadow: 0 0 12px var(--bad-glow); }

    .modal-overlay {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(9, 13, 22, 0.88);
      backdrop-filter: blur(12px);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 100;
      opacity: 0;
      pointer-events: none;
      transition: var(--transition);
      padding: 20px;
    }
    .modal-overlay.active { opacity: 1; pointer-events: auto; }
    .modal-card {
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 24px;
      width: 100%;
      max-width: 420px;
      padding: 32px;
      box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7);
    }
    .modal-header { margin-bottom: 20px; text-align: center; }
    .modal-header h2 { font-size: 22px; font-weight: 800; margin-bottom: 6px; }
    .modal-header p { font-size: 13px; color: var(--muted); }

    .auth-tabs {
      display: flex;
      background: rgba(0, 0, 0, 0.3);
      padding: 4px;
      border-radius: 12px;
      border: 1px solid var(--border);
      margin-bottom: 20px;
    }
    .auth-tab {
      flex: 1;
      padding: 8px;
      font-size: 13px;
      font-weight: 600;
      text-align: center;
      background: transparent;
      border: none;
      color: var(--muted);
      cursor: pointer;
      border-radius: 8px;
      transition: var(--transition);
    }
    .auth-tab.active {
      background: rgba(255, 255, 255, 0.1);
      color: var(--text);
    }

    .modal-form { display: flex; flex-direction: column; gap: 14px; }

    .toast-container {
      position: fixed; bottom: 20px; right: 20px; z-index: 200;
      display: flex; flex-direction: column; gap: 8px;
    }
    .toast {
      background: var(--panel);
      border: 1px solid var(--border);
      padding: 12px 18px;
      border-radius: 12px;
      font-size: 13px;
      box-shadow: var(--shadow);
      display: flex;
      align-items: center;
      gap: 10px;
      animation: slideIn 0.3s ease;
    }
    @keyframes slideIn { from { transform: translateY(100%); opacity: 0; } to { transform: translateY(0); opacity: 1; } }

    .empty-state {
      text-align: center;
      padding: 40px 20px;
      color: var(--muted);
      font-size: 14px;
    }

    footer {
      max-width: 1200px;
      margin: 20px auto 40px;
      padding: 0 20px;
      color: var(--muted);
      font-size: 12px;
      text-align: center;
    }
  </style>
</head>
<body>

  <div id="demo-banner" class="cloud-status-banner">
    ⚠️ <strong>Atenção:</strong> Chaves do Supabase não configuradas no código. O site está rodando em <em>Modo Local Temporário</em> e os dados não serão sincronizados em outros aparelhos.
  </div>

  <header>
    <div>
      <div class="brand-title">Dashboard de Gastos</div>
      <div class="brand-subtitle">Gestão financeira multi-emissor em nuvem</div>
    </div>
    <div id="auth-status" class="user-profile" style="display: none;">
      <span class="user-email" id="user-email-display">-</span>
      <button class="btn sm bad" id="btn-logout">Sair</button>
    </div>
  </header>

  <main class="grid">
    <div class="card span-3 compact">
      <div class="label"><span class="card-badge badge-bbva"></span> BBVA</div>
      <div class="value" id="tot-bbva">€ 0,00</div>
    </div>
    <div class="card span-3 compact">
      <div class="label"><span class="card-badge badge-wise"></span> Wise</div>
      <div class="value" id="tot-wise">€ 0,00</div>
    </div>
    <div class="card span-3 compact">
      <div class="label"><span class="card-badge badge-mp"></span> Mercado Pago</div>
      <div class="value" id="tot-mp">€ 0,00</div>
    </div>
    <div class="card span-3 compact">
      <div class="label"><span class="card-badge badge-spay"></span> Samsung Pay</div>
      <div class="value" id="tot-spay">€ 0,00</div>
    </div>

    <div class="card span-4 compact">
      <div class="label">Total de Gastos</div>
      <div class="value" id="tot-geral">€ 0,00</div>
      <div class="hint" id="qtd-itens">0 lançamentos registrados</div>
    </div>

    <div class="card span-4 compact">
      <div class="label">Renda Mensal (Prevista)</div>
      <div class="income-input">
        <input type="number" id="renda" placeholder="Ex: 2500.00" min="0" step="0.01" inputmode="decimal">
        <button class="btn primary" id="salvar-renda">Salvar</button>
      </div>
      <div class="hint">Salva automaticamente no seu perfil do Supabase.</div>
    </div>

    <div class="card span-4 compact">
      <div class="label">Saldo do Mês (Renda - Gastos)</div>
      <div class="value" id="saldo">€ 0,00</div>
      <div class="hint" id="saldo-hint">Defina sua renda para ver o saldo final.</div>
    </div>

    <div class="card span-12">
      <div class="label" style="margin-bottom: 4px;">Novo Lançamento</div>
      
      <div class="form-grid">
        <div class="form-group">
          <label for="desc">Descrição</label>
          <input id="desc" placeholder="Ex.: Supermercado / Assinatura">
        </div>
        <div class="form-group">
          <label for="cat">Categoria</label>
          <input id="cat" placeholder="Ex.: Alimentação">
        </div>
        <div class="form-group">
          <label for="valor">Valor (€)</label>
          <input id="valor" type="number" step="0.01" min="0" inputmode="decimal" placeholder="0.00">
        </div>
        <div class="form-group">
          <label for="cartao">Cartão</label>
          <select id="cartao">
            <option value="BBVA">BBVA</option>
            <option value="Wise">Wise</option>
            <option value="Mercado Pago">Mercado Pago</option>
            <option value="Samsung Pay">Samsung Pay</option>
          </select>
        </div>
        <div class="form-actions">
          <button class="btn primary" id="add">+ Adicionar</button>
          <button class="btn" id="export">Exportar CSV</button>
        </div>
      </div>

      <div class="table-container">
        <table>
          <thead>
            <tr>
              <th>Descrição</th>
              <th>Categoria</th>
              <th class="right">Valor</th>
              <th>Cartão</th>
              <th class="right">Ação</th>
            </tr>
          </thead>
          <tbody id="tbody"></tbody>
        </table>
        <div id="empty-state" class="empty-state" style="display: none;">
          Nenhuma despesa cadastrada até o momento.
        </div>
      </div>
    </div>
  </main>

  <footer>
    Desenvolvido com Supabase PostgreSQL & Architecture MVP • Custo Zero de Operação
  </footer>

  <div class="modal-overlay active" id="auth-modal">
    <div class="modal-card">
      <div class="modal-header">
        <h2 id="modal-title">Acessar Conta</h2>
        <p id="modal-desc">Insira seus dados para acessar suas finanças em nuvem.</p>
      </div>

      <div class="auth-tabs">
        <button type="button" class="auth-tab active" id="tab-login">Entrar</button>
        <button type="button" class="auth-tab" id="tab-signup">Criar Conta</button>
      </div>
      
      <form class="modal-form" id="auth-form">
        <div>
          <label class="form-group" style="font-size: 12px; color: var(--muted);">E-mail</label>
          <input type="email" id="auth-email" placeholder="seu@email.com" required style="margin-top: 4px;">
        </div>
        <div>
          <label class="form-group" style="font-size: 12px; color: var(--muted);">Senha</label>
          <input type="password" id="auth-password" placeholder="Sua senha secreta" required minlength="6" style="margin-top: 4px;">
        </div>
        <button type="submit" class="btn primary" id="btn-auth-submit" style="width: 100%; margin-top: 8px;">Entrar no Dashboard</button>
      </form>
    </div>
  </div>

  <div class="toast-container" id="toast-container"></div>

  <script>
    // ==========================================
    // 1. CONFIGURAÇÃO DO SUPABASE
    // (Substitua 'https://hpcrykdbxhonsrzgmnbr.supabase.co/rest/v1/' e 'sb_publishable_ylG0Cnm5eCP7LuG63_RAJQ_xdmzli-6' pelas chaves do seu projeto)
    // ==========================================
    const SUPABASE_URL = 'https://hpcrykdbxhonsrzgmnbr.supabase.co/rest/v1/';
    const SUPABASE_KEY = 'sb_publishable_ylG0Cnm5eCP7LuG63_RAJQ_xdmzli-6';

    let supabaseClient = null;

    function isSupabaseConfigured() {
      return (
        SUPABASE_URL && 
        SUPABASE_URL !== 'https://hpcrykdbxhonsrzgmnbr.supabase.co/rest/v1/' && 
        SUPABASE_KEY && 
        SUPABASE_KEY !== 'sb_publishable_ylG0Cnm5eCP7LuG63_RAJQ_xdmzli-6'
      );
    }

    function getSupabaseClient() {
      if (supabaseClient) return supabaseClient;
      if (!window.supabase || typeof window.supabase.createClient !== 'function') return null;
      if (!isSupabaseConfigured()) return null;

      try {
        supabaseClient = window.supabase.createClient(SUPABASE_URL, SUPABASE_KEY);
        return supabaseClient;
      } catch (err) {
        console.error('Erro ao conectar com Supabase:', err);
        return null;
      }
    }

    const fmt = new Intl.NumberFormat('es-ES', { style: 'currency', currency: 'EUR' });
    const byId = id => document.getElementById(id);

    // Estado inicial 100% LIMPO
    let state = { renda: 0, itens: [] };
    let currentUser = null;
    let dbCards = [];
    let authMode = 'login';

    function showToast(message, type = 'info') {
      const container = byId('toast-container');
      const toast = document.createElement('div');
      toast.className = 'toast';
      toast.style.borderColor = type === 'error' ? 'var(--bad)' : 'var(--border)';
      toast.innerHTML = `<span>${type === 'error' ? '⚠️' : '✅'}</span> ${message}`;
      container.appendChild(toast);
      setTimeout(() => toast.remove(), 4000);
    }

    // ==========================================
    // 2. AUTENTICAÇÃO E SESSÃO
    // ==========================================
    async function initAuth() {
      const client = getSupabaseClient();
      
      if (!isSupabaseConfigured()) {
        byId('demo-banner').style.display = 'block';
      }

      if (!client) {
        showAuthModal(true);
        return;
      }

      try {
        const { data: { session } } = await client.auth.getSession();
        if (session) {
          handleUserAuthenticated(session.user);
        } else {
          showAuthModal(true);
        }

        client.auth.onAuthStateChange((event, session) => {
          if (event === 'SIGNED_IN' && session) {
            handleUserAuthenticated(session.user);
          } else if (event === 'SIGNED_OUT') {
            resetState();
            showAuthModal(true);
          }
        });
      } catch (e) {
        console.error(e);
        showAuthModal(true);
      }
    }

    function resetState() {
      currentUser = null;
      dbCards = [];
      state = { renda: 0, itens: [] };
      byId('auth-status').style.display = 'none';
      render();
    }

    function showAuthModal(show) {
      byId('auth-modal').classList.toggle('active', show);
    }

    function handleUserAuthenticated(user) {
      resetState();
      currentUser = user;
      byId('user-email-display').textContent = user.email || 'Usuário Sem E-mail';
      byId('auth-status').style.display = 'flex';
      showAuthModal(false);
      loadDashboardData();
    }

    byId('tab-login').addEventListener('click', () => setAuthMode('login'));
    byId('tab-signup').addEventListener('click', () => setAuthMode('signup'));

    function setAuthMode(mode) {
      authMode = mode;
      byId('tab-login').classList.toggle('active', mode === 'login');
      byId('tab-signup').classList.toggle('active', mode === 'signup');
      byId('modal-title').textContent = mode === 'login' ? 'Acessar Conta' : 'Criar Nova Conta';
      byId('btn-auth-submit').textContent = mode === 'login' ? 'Entrar no Dashboard' : 'Cadastrar e Entrar';
    }

    byId('auth-form').addEventListener('submit', async (e) => {
      e.preventDefault();
      const email = byId('auth-email').value.trim();
      const password = byId('auth-password').value;
      const btn = byId('btn-auth-submit');

      const client = getSupabaseClient();

      if (!client) {
        // Fallback local se não colocou as chaves do Supabase ainda
        showToast('Acesso efetuado em Modo Local Temporário.');
        handleUserAuthenticated({ id: 'local-demo-user', email: email });
        return;
      }

      btn.disabled = true;
      btn.textContent = 'Aguarde...';

      if (authMode === 'login') {
        const { data, error } = await client.auth.signInWithPassword({ email, password });
        if (error) {
          showToast('Erro ao entrar: ' + error.message, 'error');
        } else {
          showToast('Sessão iniciada!');
          if (data.session) handleUserAuthenticated(data.user);
        }
      } else {
        const { data, error } = await client.auth.signUp({ email, password });
        if (error) {
          showToast('Erro ao cadastrar: ' + error.message, 'error');
        } else {
          showToast('Conta criada com sucesso!');
          if (data.session) handleUserAuthenticated(data.user);
        }
      }

      btn.disabled = false;
      btn.textContent = authMode === 'login' ? 'Entrar no Dashboard' : 'Cadastrar e Entrar';
    });

    byId('btn-logout').addEventListener('click', async () => {
      const client = getSupabaseClient();
      if (client) await client.auth.signOut();
      resetState();
      showAuthModal(true);
      showToast('Sessão encerrada.');
    });

    // ==========================================
    // 3. CARREGAMENTO DOS DADOS DO BANCO
    // ==========================================
    async function loadDashboardData() {
      const client = getSupabaseClient();
      
      // Se estiver em modo local/demo sem chave, renderiza a tela zerada
      if (!client || !currentUser) {
        render();
        return;
      }

      try {
        // Carregar Renda do Perfil
        const { data: profile } = await client
          .from('profiles')
          .select('monthly_income')
          .eq('id', currentUser.id)
          .maybeSingle();
        
        state.renda = profile && profile.monthly_income ? parseFloat(profile.monthly_income) : 0;

        // Carregar Cartões do Usuário
        let { data: cards } = await client
          .from('cards')
          .select('*')
          .eq('user_id', currentUser.id);

        if (!cards || cards.length === 0) {
          const defaultCards = [
            { user_id: currentUser.id, name: 'BBVA', color: '#004481' },
            { user_id: currentUser.id, name: 'Wise', color: '#2E008B' },
            { user_id: currentUser.id, name: 'Mercado Pago', color: '#00A9E0' },
            { user_id: currentUser.id, name: 'Samsung Pay', color: '#1259E2' }
          ];
          const { data: createdCards } = await client.from('cards').insert(defaultCards).select();
          cards = createdCards || [];
        }

        dbCards = cards;

        // Carregar Despesas do Usuário
        const { data: expenses } = await client
          .from('expenses')
          .select('*, cards(name)')
          .eq('user_id', currentUser.id)
          .order('created_at', { ascending: false });

        state.itens = (expenses || []).map(e => ({
          id: e.id,
          desc: e.description,
          cat: e.category,
          valor: parseFloat(e.amount),
          cartao: e.cards ? e.cards.name : 'Outro'
        }));

        render();
      } catch (err) {
        console.error('Erro ao carregar dados:', err);
      }
    }

    // ==========================================
    // 4. INTERFACE E RENDERIZAÇÃO
    // ==========================================
    function render() {
      const tbody = byId('tbody');
      const emptyState = byId('empty-state');
      tbody.innerHTML = '';

      let totBbva = 0, totWise = 0, totMp = 0, totSp = 0, total = 0;

      emptyState.style.display = state.itens.length === 0 ? 'block' : 'none';

      state.itens.forEach((row, idx) => {
        const tr = document.createElement('tr');
        
        const tdDesc = document.createElement('td'); 
        tdDesc.textContent = row.desc || '-';
        
        const tdCat = document.createElement('td'); 
        tdCat.innerHTML = row.cat ? `<span class="tag">${row.cat}</span>` : '-';
        
        const tdVal = document.createElement('td'); 
        tdVal.className = 'right'; 
        tdVal.style.fontWeight = '700';
        tdVal.textContent = fmt.format(row.valor || 0);
        
        const tdCard = document.createElement('td'); 
        tdCard.textContent = row.cartao;
        
        const tdAct = document.createElement('td'); 
        tdAct.className = 'right';
        
        const delBtn = document.createElement('button'); 
        delBtn.className = 'btn bad sm'; 
        delBtn.textContent = 'Excluir'; 
        delBtn.onclick = () => removeItem(row.id, idx);
        
        tdAct.appendChild(delBtn);
        tr.append(tdDesc, tdCat, tdVal, tdCard, tdAct);
        tbody.appendChild(tr);

        total += row.valor;
        switch(row.cartao) {
          case 'BBVA': totBbva += row.valor; break;
          case 'Wise': totWise += row.valor; break;
          case 'Mercado Pago': totMp += row.valor; break;
          case 'Samsung Pay': totSp += row.valor; break;
        }
      });

      byId('tot-bbva').textContent = fmt.format(totBbva);
      byId('tot-wise').textContent = fmt.format(totWise);
      byId('tot-mp').textContent = fmt.format(totMp);
      byId('tot-spay').textContent = fmt.format(totSp);
      
      byId('tot-geral').textContent = fmt.format(total);
      byId('qtd-itens').textContent = `${state.itens.length} lançamento${state.itens.length === 1 ? '' : 's'} registrado${state.itens.length === 1 ? '' : 's'}`;

      byId('renda').value = state.renda > 0 ? state.renda : '';
      const renda = Number(state.renda) || 0;
      const saldo = renda - total;
      
      const saldoEl = byId('saldo');
      saldoEl.textContent = fmt.format(saldo);
      
      byId('saldo-hint').textContent = renda > 0 
        ? `Renda: ${fmt.format(renda)} − Gastos: ${fmt.format(total)}` 
        : `Defina sua renda para ver o saldo final.`;

      saldoEl.className = 'value ' + (saldo > 0 ? 'kpi-ok' : saldo === 0 ? 'kpi-warn' : 'kpi-bad');
    }

    // ==========================================
    // 5. AÇÕES CRUD (SALVAR E EXCLUIR)
    // ==========================================
    async function addItem() {
      const desc = byId('desc').value.trim();
      const cat = byId('cat').value.trim();
      const valorRaw = byId('valor').value;
      const valor = parseFloat(valorRaw);
      const cartaoNome = byId('cartao').value;

      if (!valor || valor <= 0) {
        showToast('Informe um valor válido maior que zero.', 'error');
        return;
      }

      const client = getSupabaseClient();

      if (!client || !currentUser) {
        // Fallback local se o Supabase ainda não foi configurado
        state.itens.unshift({
          id: String(Date.now()),
          desc: desc || 'Sem descrição',
          cat: cat || 'Geral',
          valor,
          cartao: cartaoNome
        });
        byId('desc').value = '';
        byId('cat').value = '';
        byId('valor').value = '';
        render();
        showToast('Despesa salva localmente (Cole as chaves do Supabase para salvar na nuvem).');
        return;
      }

      const cardObj = dbCards.find(c => c.name === cartaoNome);
      const addBtn = byId('add');
      addBtn.disabled = true;

      const { data, error } = await client
        .from('expenses')
        .insert([{
          user_id: currentUser.id,
          card_id: cardObj ? cardObj.id : null,
          description: desc || 'Sem descrição',
          category: cat || 'Geral',
          amount: valor
        }])
        .select();

      addBtn.disabled = false;

      if (error) {
        showToast('Erro ao salvar no Supabase: ' + error.message, 'error');
      } else if (data && data.length > 0) {
        state.itens.unshift({
          id: data[0].id,
          desc: desc || 'Sem descrição',
          cat: cat || 'Geral',
          valor,
          cartao: cartaoNome
        });
        
        byId('desc').value = '';
        byId('cat').value = '';
        byId('valor').value = '';
        
        render();
        showToast('Despesa salva na nuvem com sucesso!');
      }
    }

    async function removeItem(dbId, idx) {
      const client = getSupabaseClient();
      if (client && currentUser) {
        await client.from('expenses').delete().eq('id', dbId);
      }
      state.itens.splice(idx, 1);
      render();
      showToast('Despesa removida.');
    }

    async function salvarRenda() {
      const rendaVal = parseFloat(byId('renda').value);
      if (isNaN(rendaVal) || rendaVal < 0) {
        showToast('Informe um valor de renda válido.', 'error');
        return;
      }

      state.renda = rendaVal;

      const client = getSupabaseClient();
      if (client && currentUser) {
        await client
          .from('profiles')
          .upsert({ 
            id: currentUser.id, 
            email: currentUser.email, 
            monthly_income: rendaVal 
          });
      }

      render();
      showToast('Renda salva com sucesso!');
    }

    function exportCSV() {
      if (state.itens.length === 0) {
        showToast('Sem dados para exportar.', 'error');
        return;
      }

      const headers = ['Descricao', 'Categoria', 'Valor', 'Cartao'];
      const lines = state.itens.map(i => [
        `"${(i.desc || '').replace(/"/g, '""')}"`,
        `"${(i.cat || '').replace(/"/g, '""')}"`,
        String(i.valor).replace('.', ','),
        `"${(i.cartao || '').replace(/"/g, '""')}"`
      ].join(';'));

      const csv = [headers.join(';'), ...lines].join('\n');
      const blob = new Blob(["\ufeff" + csv], { type: 'text/csv;charset=utf-8;' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `gastos-mensais-${new Date().toISOString().slice(0, 10)}.csv`;
      a.click();
      URL.revokeObjectURL(url);
      showToast('Relatório CSV baixado!');
    }

    byId('add').addEventListener('click', addItem);
    byId('salvar-renda').addEventListener('click', salvarRenda);
    byId('export').addEventListener('click', exportCSV);

    initAuth();
  </script>
</body>
</html>
