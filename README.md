<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Calculadora — Tarjeta de Crédito Garantizada con BTC</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    :root{
      --azul:#1e3a8a;
      --azul-acento:#2563eb;
      --verde:#059669;
    }
    .card{background:#fff;border-radius:1rem;box-shadow:0 10px 30px rgba(0,0,0,.08)}
    .label{color:#111827;font-weight:600}
    .hint{color:#6b7280;font-size:.85rem}
    .title{color:var(--azul)}
    .input{border:1px solid #e5e7eb;border-radius:.75rem;padding:.85rem 1rem;width:100%;outline:none;transition:.15s}
    .input:focus{border-color:var(--azul-acento);box-shadow:0 0 0 3px rgba(37,99,235,.15)}
    .btn{display:inline-flex;align-items:center;justify-content:center;gap:.5rem;border-radius:.75rem;padding:.9rem 1rem;font-weight:600;min-height:44px}
    .btn-primary{background:var(--verde);color:#fff}
    .btn-primary[disabled]{opacity:.6}
    .btn-primary:hover{filter:brightness(.97)}
    .btn-secondary{background:var(--azul-acento);color:#fff}
    .btn-secondary:hover{filter:brightness(.97)}
    .btn-ghost{background:#f3f4f6;color:#111827}
    .btn-ghost:hover{background:#e5e7eb}
    .grid-col{background:#f9fafb;border:1px solid #eef2f7;border-radius:.75rem;padding:1rem}
    .value{
      font-feature-settings:"tnum" on,"lnum" on;
      white-space:nowrap;
    }
    @media (max-width: 420px){
      h1{font-size:1.5rem}
      .input{padding:.8rem .9rem}
    }
  </style>
</head>
<body class="bg-gradient-to-br from-blue-50 via-white to-emerald-50 min-h-screen">
  <header class="max-w-4xl mx-auto px-4 pt-8 pb-2">
    <h1 class="text-2xl md:text-4xl font-extrabold title">Calculadora — Tarjeta de Crédito Garantizada con BTC</h1>
    <p class="mt-2 text-gray-600">
      Tasa anual fija <span class="font-semibold">14%</span> · Formato en-US (1,234.56)
    </p>
  </header>

  <main class="max-w-4xl mx-auto px-4 pb-16">
    <section class="card p-4 sm:p-6 md:p-8">
      <!-- Controles -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6">
        <div>
          <label for="limitInput" class="label">
            Límite de la Tarjeta (USD)
            <span class="ml-2 text-xs text-gray-500">máx. 10,000</span>
          </label>
          <div class="mt-2 grid grid-cols-[1fr_auto] gap-2 items-start">
            <input
              id="limitInput"
              class="input value"
              inputmode="decimal"
              autocomplete="off"
              placeholder="10,000.00"
              aria-describedby="limitHelp limitWarn"
            />
            <button id="clearBtn" class="btn btn-ghost" title="Borra límite y resultados">
              Limpiar
            </button>
          </div>
          <p id="limitHelp" class="hint mt-2">
            Ingresa el límite (tope 10,000). “Limpiar” vacía este campo y los resultados.
          </p>
          <p id="limitWarn" class="text-sm mt-2 hidden text-amber-700">
            ⚠️ Se ajustó al tope permitido (10,000.00).
          </p>
        </div>

        <div class="grid-col">
          <p class="label">Tasa anual</p>
          <p class="mt-1 font-semibold">
            14%
            <span class="hint">(fija, no editable)</span>
          </p>
          <div class="mt-4">
            <label for="priceInput" class="label">Precio BTC (USD)</label>
            <div class="mt-2 grid grid-cols-[1fr_auto] gap-2">
              <input
                id="priceInput"
                class="input value bg-gray-100"
                placeholder="—"
                disabled
              />
              <button id="refreshBtn" class="btn btn-secondary">
                Actualizar precio BTC
              </button>
            </div>
            <p id="priceStatus" class="hint mt-2" aria-live="polite">
              Pulsa “Actualizar precio BTC” para obtener el valor actual.
            </p>
          </div>
        </div>
      </div>

      <div class="mt-6 grid grid-cols-1 sm:grid-cols-2 gap-3">
        <button id="calcBtn" class="btn btn-primary" disabled>
          Calcular
        </button>
        <span id="calcHelp" class="hint self-center">
          Requiere límite válido y precio BTC vigente.
        </span>
      </div>

      <!-- Resultados -->
      <div class="mt-8 grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6">
        <!-- (1) LTV 70% -->
        <div class="grid-col">
          <h3 class="font-bold text-lg text-blue-900">
            1) BTC requerido para LTV inicial 70%
          </h3>
          <div class="mt-3 space-y-2">
            <div class="flex justify-between">
              <span class="hint">LTV Inicial</span>
              <span class="font-semibold">70%</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">Unidades de BTC requeridas</span>
              <span id="btcUnits" class="font-semibold value">—</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">Valor de BTC equivalente en USD</span>
              <span id="btcValueUsd" class="font-semibold value">—</span>
            </div>
          </div>
        </div>

        <!-- (2) Margin Call 80% -->
        <div class="grid-col">
          <h3 class="font-bold text-lg text-blue-900">
            2) Margin Call (LTV 80%)
          </h3>
          <div class="mt-3 space-y-2">
            <div class="flex justify-between">
              <span class="hint">Precio de BTC (USD)</span>
              <span id="priceMC" class="font-semibold value">—</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">Valor de la garantía (USD)</span>
              <span id="collMC" class="font-semibold value">—</span>
            </div>
          </div>
        </div>

        <!-- (3) Bloqueo 90% -->
        <div class="grid-col">
          <h3 class="font-bold text-lg text-blue-900">
            3) Bloqueo (LTV 90%)
          </h3>
          <div class="mt-3 space-y-2">
            <div class="flex justify-between">
              <span class="hint">Precio de BTC (USD)</span>
              <span id="priceBlock" class="font-semibold value">—</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">Valor de la garantía (USD)</span>
              <span id="collBlock" class="font-semibold value">—</span>
            </div>
          </div>
        </div>

        <!-- (4) Escenario de Liquidación (LTV 80%) -->
        <div class="grid-col">
          <h3 class="font-bold text-lg text-blue-900">
            4) Escenario de Liquidación (LTV 80%)
          </h3>
          <div class="mt-3 space-y-2">
            <div class="flex justify-between">
              <span class="hint">Precio de BTC en este escenario (USD)</span>
              <span id="priceLiq" class="font-semibold value">—</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">Valor de la garantía en este escenario (USD)</span>
              <span id="collAtBlock" class="font-semibold value">—</span>
            </div>
            <div class="flex justify-between">
              <span class="hint">
                Si al momento del bloqueo el saldo es igual o mayor a este valor, se ejecuta la liquidación
              </span>
              <span id="dueAt80" class="font-semibold value">—</span>
            </div>
          </div>
        </div>
      </div>

      <div id="msg" class="mt-6 text-sm text-red-600 hidden" aria-live="assertive"></div>
    </section>
  </main>

  <script>
    // Formato en-US
    const formatUSD = (n, max=2) =>
      isFinite(n)
        ? n.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: max })
        : '—';

    const formatBTC = (n) =>
      isFinite(n)
        ? n.toLocaleString('en-US', { minimumFractionDigits: 6, maximumFractionDigits: 8 })
        : '—';

    const parseNumber = (str) => {
      if (typeof str !== 'string') return Number(str) || 0;
      const clean = str.replace(/,/g,'').trim();
      const val = Number(clean);
      return isFinite(val) ? val : 0;
    };

    // DOM
    const limitInput    = document.getElementById('limitInput');
    const clearBtn      = document.getElementById('clearBtn');
    const limitWarn     = document.getElementById('limitWarn');
    const refreshBtn    = document.getElementById('refreshBtn');
    const priceInput    = document.getElementById('priceInput');
    const priceStatus   = document.getElementById('priceStatus');
    const calcBtn       = document.getElementById('calcBtn');
    const msg           = document.getElementById('msg');

    const btcUnitsEl    = document.getElementById('btcUnits');
    const btcValueUsdEl = document.getElementById('btcValueUsd');
    const priceMCEl     = document.getElementById('priceMC');
    const priceBlockEl  = document.getElementById('priceBlock');
    const priceLiqEl    = document.getElementById('priceLiq');

    const collMCEl      = document.getElementById('collMC');
    const collBlockEl   = document.getElementById('collBlock');
    const collAtBlockEl = document.getElementById('collAtBlock');

    const dueAt80El     = document.getElementById('dueAt80');

    // Estado
    let priceBTC = NaN;
    let u        = NaN;
    const CAP    = 10000;

    // Precarga opcional
    limitInput.value = formatUSD(10000);

    function normalizeLimit(){
      let L = parseNumber(limitInput.value);
      let capped = false;
      if (L > CAP){ L = CAP; capped = true; }
      if (L < 0) L = 0;
      limitInput.value = L ? formatUSD(L) : '';
      limitWarn.classList.toggle('hidden', !capped);
      updateCalcBtnState();
      return L;
    }

    function updateCalcBtnState(){
      const L = parseNumber(limitInput.value);
      const ok = L > 0 && isFinite(priceBTC) && priceBTC > 0;
      calcBtn.disabled = !ok;
    }

    // Inputs
    limitInput.addEventListener('blur', normalizeLimit);
    limitInput.addEventListener('input', (e)=>{
      const v = e.target.value;
      if (/[^0-9.,\s]/.test(v)) e.target.value = v.replace(/[^0-9.,\s]/g,'');
      updateCalcBtnState();
    });

    // Limpiar datos
    clearBtn.addEventListener('click', ()=>{
      limitInput.value = '';
      limitWarn.classList.add('hidden');
      setResults(null);
      msg.classList.add('hidden');
      updateCalcBtnState();
      limitInput.focus();
    });

    function setResults(obj){
      if (!obj){
        btcUnitsEl.textContent    = '—';
        btcValueUsdEl.textContent = '—';
        priceMCEl.textContent     = '—';
        priceBlockEl.textContent  = '—';
        priceLiqEl.textContent    = '—';
        collMCEl.textContent      = '—';
        collBlockEl.textContent   = '—';
        collAtBlockEl.textContent = '—';
        dueAt80El.textContent     = '—';
        return;
      }
      btcUnitsEl.textContent    = formatBTC(obj.u);
      btcValueUsdEl.textContent = `$ ${formatUSD(obj.collateralUSD)}`;
      priceMCEl.textContent     = `$ ${formatUSD(obj.priceMC)}`;
      priceBlockEl.textContent  = `$ ${formatUSD(obj.priceBlock)}`;
      priceLiqEl.textContent    = `$ ${formatUSD(obj.priceLiq)}`;
      collMCEl.textContent      = `$ ${formatUSD(obj.collateralMC)}`;
      collBlockEl.textContent   = `$ ${formatUSD(obj.collateralBlock)}`;
      collAtBlockEl.textContent = `$ ${formatUSD(obj.collAtBlock)}`;
      dueAt80El.textContent     = `$ ${formatUSD(obj.dueAt80)}`;
    }

    async function fetchCoinGecko(){
      priceStatus.textContent = 'Consultando CoinGecko…';
      priceStatus.classList.remove('text-red-600');
      msg.classList.add('hidden');
      try{
        const url = 'https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd';
        const res = await fetch(url,{headers:{accept:'application/json'}});
        if(!res.ok) throw new Error(`HTTP ${res.status}`);
        const data = await res.json();
        const p = Number(data?.bitcoin?.usd);
        if(!isFinite(p) || p<=0) throw new Error('Precio inválido');
        priceBTC = p;
        priceInput.value = `$ ${formatUSD(priceBTC)}`;
        priceStatus.textContent = 'Precio actualizado correctamente.';
        updateCalcBtnState();
      }catch(err){
        priceStatus.textContent = 'Error al actualizar. Verifica tu conexión y reintenta.';
        priceStatus.classList.add('text-red-600');
        console.error(err);
      }
    }

    refreshBtn.addEventListener('click', fetchCoinGecko);

    calcBtn.addEventListener('click', ()=>{
      const L = normalizeLimit();
      if(!(L>0)) return showMsg('Ingresa un límite válido.');
      if(!isFinite(priceBTC) || priceBTC<=0) return showMsg('Primero actualiza el precio de BTC.');

      // (1) BTC requerido para LTV 70%
      u = L / (0.70 * priceBTC);
      const collateralUSD = L / 0.70;

      // (2) Precio BTC para LTV 80% con u fija
      const priceMC = L / (0.80 * u);
      const collateralMC = u * priceMC;

      // (3) Precio BTC para LTV 90% con u fija (bloqueo)
      const priceBlock = L / (0.90 * u);
      const collateralBlock = u * priceBlock;

      // (4) Escenario de Liquidación: LTV 80% respecto a la garantía al precio de bloqueo
      const priceLiq    = priceBlock;
      const collAtBlock = collateralBlock;
      const dueAt80     = Math.min(L, 0.80 * collAtBlock);

      setResults({
        u,
        collateralUSD,
        priceMC,
        priceBlock,
        priceLiq,
        collateralMC,
        collateralBlock,
        collAtBlock,
        dueAt80
      });
      msg.classList.add('hidden');
    });

    function showMsg(t){
      msg.textContent = t;
      msg.classList.remove('hidden');
    }

    // Inicial
    normalizeLimit();
    updateCalcBtnState();
  </script>
</body>
</html>
