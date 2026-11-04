# calarie
Cal tracker 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FlexiCal Pro - Multi-Week Calorie & Step Balance Planner</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Google Fonts: Inter -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  <!-- Chart.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: { sans: ['Inter', 'sans-serif'] },
        }
      }
    }
  </script>
  <style>
    body { font-family: 'Inter', sans-serif; }
    .no-scrollbar::-webkit-scrollbar { display: none; }
    .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 dark:bg-slate-900 dark:text-slate-100 min-h-screen pb-16 transition-colors duration-200">

  <!-- Custom Modal Container -->
  <div id="custom-modal" class="fixed inset-0 bg-black/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
    <div class="bg-white dark:bg-slate-800 rounded-3xl max-w-md w-full p-6 shadow-2xl border border-slate-200 dark:border-slate-700">
      <div id="modal-icon" class="w-12 h-12 rounded-2xl bg-green-100 dark:bg-green-950/50 text-green-600 dark:text-green-400 flex items-center justify-center text-xl mb-4 font-bold">
        <i class="fa-solid fa-circle-check"></i>
      </div>
      <h3 id="modal-title" class="text-lg font-bold text-slate-900 dark:text-slate-100 mb-2">Notice</h3>
      <p id="modal-message" class="text-xs text-slate-600 dark:text-slate-300 mb-6 leading-relaxed">Message here...</p>
      <div id="modal-actions" class="flex justify-end gap-3">
        <button id="modal-btn-cancel" class="px-4 py-2 text-xs font-semibold rounded-xl bg-slate-100 dark:bg-slate-700 text-slate-700 dark:text-slate-300 hover:bg-slate-200 transition hidden">Cancel</button>
        <button id="modal-btn-confirm" class="px-4 py-2 text-xs font-semibold rounded-xl bg-green-600 text-white hover:bg-green-700 transition">OK</button>
      </div>
    </div>
  </div>

  <!-- Main Container -->
  <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8 pt-6">

    <!-- Header Navigation -->
    <header class="flex flex-col md:flex-row justify-between items-center mb-8 gap-4 border-b border-slate-200 dark:border-slate-800 pb-5">
      <div class="flex items-center gap-3">
        <div class="w-12 h-12 bg-gradient-to-tr from-green-500 to-emerald-400 rounded-2xl flex items-center justify-center text-white shadow-lg shadow-green-500/20">
          <i class="fa-solid font-bold text-2xl fa-scale-balanced"></i>
        </div>
        <div>
          <h1 class="text-2xl font-bold bg-gradient-to-r from-green-600 to-teal-500 bg-clip-text text-transparent">FlexiCal Pro</h1>
          <p class="text-xs text-slate-500 dark:text-slate-400">Multi-week history & step-cal balance tracking</p>
        </div>
      </div>

      <!-- Week Switcher & Actions -->
      <div class="flex flex-wrap items-center gap-3 w-full md:w-auto justify-end">
        
        <!-- Week Selector Dropdown -->
        <div class="flex items-center gap-2 bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 px-3 py-2 rounded-2xl shadow-sm">
          <i class="fa-solid fa-calendar-week text-green-500"></i>
          <select id="week-selector" class="bg-transparent text-xs font-semibold text-slate-700 dark:text-slate-200 focus:outline-none cursor-pointer">
            <!-- Populated dynamically -->
          </select>
        </div>

        <!-- New Week Button -->
        <button id="btn-new-week" class="px-3.5 py-2 text-xs font-semibold bg-green-600 text-white rounded-2xl hover:bg-green-700 transition shadow-sm flex items-center gap-2">
          <i class="fa-solid fa-plus"></i> New Week
        </button>

        <!-- Settings / Maintenance Toggle -->
        <button id="btn-settings" class="p-2.5 rounded-2xl bg-slate-200 dark:bg-slate-800 text-slate-700 dark:text-slate-300 hover:bg-slate-300 dark:hover:bg-slate-700 transition" title="Settings & Publishing Guide">
          <i class="fa-solid fa-gears"></i>
        </button>

        <!-- Dark Mode Toggle -->
        <button id="btn-theme" class="p-2.5 rounded-2xl bg-slate-200 dark:bg-slate-800 text-slate-700 dark:text-slate-300 hover:bg-slate-300 dark:hover:bg-slate-700 transition">
          <i class="fa-solid fa-moon dark:hidden"></i>
          <i class="fa-solid fa-sun hidden dark:block text-amber-400"></i>
        </button>
      </div>
    </header>

    <!-- Maintenance Model & Data Settings Panel (Collapsible) -->
    <div id="settings-panel" class="hidden mb-8 bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 rounded-3xl p-6 shadow-sm">
      <div class="flex justify-between items-center mb-4">
        <h3 class="font-bold text-slate-800 dark:text-slate-100 flex items-center gap-2 text-sm">
          <i class="fa-solid fa-sliders text-green-500"></i> Baseline Goals, Backup Data & Publishing
        </h3>
        <button id="close-settings" class="text-slate-400 hover:text-slate-600 dark:hover:text-slate-200">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-4 gap-4 mb-6">
        <div class="bg-slate-50 dark:bg-slate-900/60 p-3 rounded-2xl border border-slate-200 dark:border-slate-700">
          <label class="text-[10px] uppercase font-semibold text-slate-400 block mb-1">Target Strategy Goal</label>
          <select id="input-target-strategy" class="w-full font-bold text-sm bg-transparent focus:outline-none text-slate-800 dark:text-slate-100 cursor-pointer">
            <option value="surplus">Surplus (+100 kcal target)</option>
            <option value="deficit">Deficit (-300 kcal target)</option>
            <option value="maintenance">Maintenance (Exact Balance)</option>
          </select>
        </div>
        <div class="bg-slate-50 dark:bg-slate-900/60 p-3 rounded-2xl border border-slate-200 dark:border-slate-700">
          <label class="text-[10px] uppercase font-semibold text-slate-400 block mb-1">Base Maintenance Intake Cals</label>
          <input type="number" id="input-base-cals" value="2475" step="25" class="w-full font-bold text-sm bg-transparent focus:outline-none">
        </div>
        <div class="bg-slate-50 dark:bg-slate-900/60 p-3 rounded-2xl border border-slate-200 dark:border-slate-700">
          <label class="text-[10px] uppercase font-semibold text-slate-400 block mb-1">Daily Step Goal (Average)</label>
          <input type="number" id="input-target-steps" value="14700" step="500" class="w-full font-bold text-sm bg-transparent focus:outline-none">
        </div>
        <div class="bg-slate-50 dark:bg-slate-900/60 p-3 rounded-2xl border border-slate-200 dark:border-slate-700">
          <label class="text-[10px] uppercase font-semibold text-slate-400 block mb-1">Target Calorie Intake Goal</label>
          <input type="number" id="input-target-cals" value="2575" step="25" class="w-full font-bold text-sm bg-transparent focus:outline-none">
        </div>
      </div>

      <!-- Backup & Import Controls -->
      <div class="pt-4 border-t border-slate-100 dark:border-slate-700/60 flex flex-wrap items-center justify-between gap-4">
        <div>
          <h4 class="text-xs font-bold text-slate-800 dark:text-slate-200 flex items-center gap-1.5">
            <i class="fa-solid fa-database text-blue-500"></i> Backup & Persistence Controls
          </h4>
          <p class="text-[11px] text-slate-500 dark:text-slate-400">Data automatically saves to your browser's local storage. Download backups anytime to prevent accidental browser cache clearing.</p>
        </div>
        <div class="flex items-center gap-2">
          <button id="btn-export-json" class="px-3 py-1.5 text-xs font-semibold bg-slate-100 dark:bg-slate-700 text-slate-700 dark:text-slate-200 rounded-xl hover:bg-slate-200 dark:hover:bg-slate-600 transition flex items-center gap-1.5">
            <i class="fa-solid fa-download"></i> Backup Data (JSON)
          </button>
          <label id="btn-import-label" class="px-3 py-1.5 text-xs font-semibold bg-slate-100 dark:bg-slate-700 text-slate-700 dark:text-slate-200 rounded-xl hover:bg-slate-200 dark:hover:bg-slate-600 transition flex items-center gap-1.5 cursor-pointer">
            <i class="fa-solid fa-upload"></i> Restore Data
            <input type="file" id="input-import-json" accept=".json" class="hidden">
          </label>
        </div>
      </div>

      <!-- Hosting Instructions -->
      <div class="mt-4 p-4 bg-emerald-50/50 dark:bg-emerald-950/20 rounded-2xl border border-emerald-100 dark:border-emerald-900/30 text-xs text-slate-700 dark:text-slate-300">
        <h5 class="font-bold text-emerald-700 dark:text-emerald-400 mb-1 flex items-center gap-1.5">
          <i class="fa-solid fa-globe"></i> Want to publish this online for free?
        </h5>
        <ol class="list-decimal list-inside space-y-1 text-[11px] text-slate-600 dark:text-slate-400">
          <li>Create a free account on <a href="https://github.com" target="_blank" class="underline text-emerald-600 font-semibold">GitHub.com</a>.</li>
          <li>Create a new repository named <code>flexical</code>, upload this <code>index.html</code> file.</li>
          <li>In Repository <strong>Settings &rarr; Pages</strong>, set branch to <code>main</code> and click <strong>Save</strong>.</li>
          <li>Your app will be live at <code>https://yourname.github.io/flexical/</code> to access from any phone or computer!</li>
        </ol>
      </div>

    </div>

    <!-- Weekly Budget Overview Dashboard (3 Cards) -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
      
      <!-- Calorie Dashboard Card -->
      <div class="bg-gradient-to-br from-white to-orange-50/30 dark:from-slate-800 dark:to-slate-800/80 p-6 rounded-3xl border border-orange-100 dark:border-slate-700 shadow-sm relative overflow-hidden">
        <div class="flex justify-between items-start mb-3">
          <div>
            <span class="px-2.5 py-1 text-[10px] font-semibold uppercase tracking-wider rounded-lg bg-orange-100 dark:bg-orange-950/60 text-orange-600 dark:text-orange-400">Weekly Calorie Pool</span>
            <h3 class="text-2xl font-extrabold mt-2 text-slate-800 dark:text-slate-100" id="cal-weekly-remaining">0 kcal</h3>
            <p class="text-xs text-slate-500 dark:text-slate-400">Remaining budget (<span id="cal-weekly-total">0</span> total)</p>
          </div>
          <div class="text-right">
            <span class="text-[10px] text-slate-400 block uppercase">7-Day Avg</span>
            <span class="text-lg font-bold text-slate-700 dark:text-slate-200" id="cal-current-avg">0</span>
            <span class="text-xs text-slate-400">/ <span id="cal-target-avg">2400</span></span>
          </div>
        </div>

        <div class="w-full bg-slate-200 dark:bg-slate-700 h-2.5 rounded-full overflow-hidden mb-2">
          <div id="cal-progress-bar" class="bg-orange-500 h-full rounded-full transition-all duration-500" style="width: 0%"></div>
        </div>
        <div class="flex justify-between text-[11px] text-slate-400 font-medium">
          <span>Consumed: <strong id="cal-total-used" class="text-slate-700 dark:text-slate-300">0</strong></span>
          <span>Target: <strong id="cal-budget-limit" class="text-slate-700 dark:text-slate-300">16,800</strong></span>
        </div>
      </div>

      <!-- Step Dashboard Card -->
      <div class="bg-gradient-to-br from-white to-blue-50/30 dark:from-slate-800 dark:to-slate-800/80 p-6 rounded-3xl border border-blue-100 dark:border-slate-700 shadow-sm relative overflow-hidden">
        <div class="flex justify-between items-start mb-3">
          <div>
            <span class="px-2.5 py-1 text-[10px] font-semibold uppercase tracking-wider rounded-lg bg-blue-100 dark:bg-blue-950/60 text-blue-600 dark:text-blue-400">Weekly Step Total</span>
            <h3 class="text-2xl font-extrabold mt-2 text-slate-800 dark:text-slate-100" id="step-total-display">0 steps</h3>
            <p class="text-xs text-slate-500 dark:text-slate-400">7-day average: <span id="step-current-avg" class="font-bold text-slate-700 dark:text-slate-200">0</span> steps/day</p>
          </div>
          <div class="text-right">
            <span class="text-[10px] text-slate-400 block uppercase">Step Goal</span>
            <span class="text-lg font-bold text-slate-700 dark:text-slate-200" id="step-target-avg">14,700</span>
          </div>
        </div>

        <div class="w-full bg-slate-200 dark:bg-slate-700 h-2.5 rounded-full overflow-hidden mb-2">
          <div id="step-progress-bar" class="bg-blue-500 h-full rounded-full transition-all duration-500" style="width: 0%"></div>
        </div>
        <div class="flex justify-between text-[11px] text-slate-400 font-medium">
          <span>Logged Total: <strong id="step-total-logged" class="text-slate-700 dark:text-slate-300">0</strong></span>
          <span>Weekly Goal: <strong id="step-budget-limit" class="text-slate-700 dark:text-slate-300">102,900</strong></span>
        </div>
      </div>

      <!-- Net Energy Balance Card -->
      <div class="bg-gradient-to-br from-white to-emerald-50/30 dark:from-slate-800 dark:to-slate-800/80 p-6 rounded-3xl border border-emerald-100 dark:border-slate-700 shadow-sm relative overflow-hidden">
        <div class="flex justify-between items-start mb-3">
          <div>
            <span class="px-2.5 py-1 text-[10px] font-semibold uppercase tracking-wider rounded-lg bg-emerald-100 dark:bg-emerald-950/60 text-emerald-600 dark:text-emerald-400">Net Energy Balance</span>
            <h3 class="text-2xl font-extrabold mt-2 text-slate-800 dark:text-slate-100" id="net-balance-display">0 kcal</h3>
            <p class="text-xs text-slate-500 dark:text-slate-400" id="net-balance-status">Estimated weekly surplus/deficit</p>
          </div>
          <div class="w-10 h-10 rounded-2xl bg-emerald-100 dark:bg-emerald-950/50 text-emerald-600 dark:text-emerald-400 flex items-center justify-center font-bold">
            <i class="fa-solid fa-fire-flame-curved"></i>
          </div>
        </div>

        <div class="p-2.5 bg-white/80 dark:bg-slate-900/80 rounded-2xl border border-emerald-200/50 dark:border-slate-700 text-[11px] text-slate-700 dark:text-slate-300">
          <i class="fa-solid fa-lightbulb text-emerald-500 mr-1"></i> <span id="balance-advice-text">Log your entries to see your step-adjusted energy balance.</span>
        </div>
      </div>

    </div>

    <!-- Quick Actions Bar -->
    <div class="flex flex-wrap items-center justify-between gap-3 mb-6 bg-slate-100 dark:bg-slate-800/50 p-4 rounded-3xl">
      <div class="text-xs font-semibold text-slate-600 dark:text-slate-300 flex items-center gap-2">
        <i class="fa-solid fa-wand-magic-sparkles text-purple-500"></i> Quick Actions:
      </div>
      <div class="flex flex-wrap gap-2">
        <button id="btn-load-example" class="px-3 py-1.5 text-xs bg-white dark:bg-slate-700 text-slate-700 dark:text-slate-200 rounded-xl hover:bg-slate-200 dark:hover:bg-slate-600 font-medium transition border border-slate-200 dark:border-slate-600">
          Load Your 7-Day Example (1000, 2490, 3500...)
        </button>
        <button id="btn-rename-week" class="px-3 py-1.5 text-xs bg-white dark:bg-slate-700 text-slate-700 dark:text-slate-200 rounded-xl hover:bg-slate-200 dark:hover:bg-slate-600 font-medium transition border border-slate-200 dark:border-slate-600">
          Rename Week
        </button>
        <button id="btn-delete-week" class="px-3 py-1.5 text-xs bg-rose-50 dark:bg-rose-950/40 text-rose-600 dark:text-rose-300 rounded-xl hover:bg-rose-100 dark:hover:bg-rose-900/60 font-medium transition border border-rose-200 dark:border-rose-900/50">
          Delete Current Week
        </button>
      </div>
    </div>

    <!-- Daily Log Grid & Chart Tabs -->
    <div class="bg-white dark:bg-slate-800 rounded-3xl p-6 shadow-sm border border-slate-200/80 dark:border-slate-700/60 mb-8">
      
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-6">
        <div>
          <h3 class="text-lg font-bold text-slate-800 dark:text-slate-100" id="current-week-title-display">Week Schedule</h3>
          <p class="text-xs text-slate-500 dark:text-slate-400">Dynamic re-balancing spreads unspent calories evenly over remaining days.</p>
        </div>
        
        <div class="flex bg-slate-100 dark:bg-slate-900 p-1 rounded-xl text-xs font-medium self-start sm:self-auto">
          <button id="view-mode-table" class="px-3 py-1.5 rounded-lg bg-white dark:bg-slate-800 shadow-sm text-slate-800 dark:text-slate-100 transition">
            <i class="fa-solid fa-calendar-days mr-1"></i> Daily Editor
          </button>
          <button id="view-mode-chart" class="px-3 py-1.5 rounded-lg text-slate-500 hover:text-slate-800 dark:hover:text-slate-200 transition">
            <i class="fa-solid fa-chart-column mr-1"></i> Visual Trends
          </button>
        </div>
      </div>

      <!-- TABLE VIEW -->
      <div id="container-table-view" class="overflow-x-auto">
        <table class="w-full text-left border-collapse">
          <thead>
            <tr class="text-xs text-slate-400 uppercase tracking-wider border-b border-slate-100 dark:border-slate-700/60">
              <th class="pb-3 font-semibold px-2">Day</th>
              <th class="pb-3 font-semibold px-2">Calories In</th>
              <th class="pb-3 font-semibold px-2">Rec. Target</th>
              <th class="pb-3 font-semibold px-2">Steps Walked</th>
              <th class="pb-3 font-semibold px-2">Est. Net Impact</th>
              <th class="pb-3 font-semibold px-2">Status</th>
            </tr>
          </thead>
          <tbody id="days-table-body" class="divide-y divide-slate-100 dark:divide-slate-700/40 text-sm">
            <!-- Dynamic Day Rows Rendered Here -->
          </tbody>
        </table>
      </div>

      <!-- CHART VIEW -->
      <div id="container-chart-view" class="hidden">
        <div class="h-72 w-full">
          <canvas id="weeklyChart"></canvas>
        </div>
      </div>

    </div>

  </div>

  <!-- JavaScript Application Logic -->
  <script>
    const STORAGE_KEY = 'flexical_pro_weeks_v3';
    
    let appData = {
      currentWeekId: null,
      weeks: []
    };

    let chartInstance = null;
    const defaultDayNames = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'];

    function createNewWeek(title = null) {
      const id = 'week_' + Date.now();
      const weekTitle = title || `Week of ${new Date().toLocaleDateString(undefined, { month: 'short', day: 'numeric' })}`;
      
      return {
        id: id,
        title: weekTitle,
        baseCals: 2475,
        targetSteps: 14700,
        targetCals: 2575,
        strategy: 'surplus',
        days: defaultDayNames.map(name => ({
          name: name,
          calories: 2575,
          steps: 14700,
          isLogged: false
        }))
      };
    }

    function showModal(title, message, isConfirm = false, onConfirm = null) {
      const modal = document.getElementById('custom-modal');
      document.getElementById('modal-title').textContent = title;
      document.getElementById('modal-message').textContent = message;
      
      const btnCancel = document.getElementById('modal-btn-cancel');
      const btnConfirm = document.getElementById('modal-btn-confirm');
      
      if (isConfirm) {
        btnCancel.classList.remove('hidden');
        btnConfirm.onclick = () => {
          modal.classList.add('hidden');
          if (onConfirm) onConfirm();
        };
        btnCancel.onclick = () => modal.classList.add('hidden');
      } else {
        btnCancel.classList.add('hidden');
        btnConfirm.onclick = () => modal.classList.add('hidden');
      }
      modal.classList.remove('hidden');
    }

    window.addEventListener('DOMContentLoaded', () => {
      loadData();
      setupEventListeners();
      renderApp();
    });

    function loadData() {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (raw) {
        try {
          appData = JSON.parse(raw);
        } catch(e) { console.error(e); }
      }

      if (!appData.weeks || appData.weeks.length === 0) {
        const initialWeek = createNewWeek('Current Week');
        appData.weeks = [initialWeek];
        appData.currentWeekId = initialWeek.id;
        saveData();
      } else if (!appData.currentWeekId || !appData.weeks.some(w => w.id === appData.currentWeekId)) {
        appData.currentWeekId = appData.weeks[0].id;
      }
    }

    function saveData() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(appData));
    }

    function getCurrentWeek() {
      return appData.weeks.find(w => w.id === appData.currentWeekId) || appData.weeks[0];
    }

    function setupEventListeners() {
      document.getElementById('week-selector').addEventListener('change', (e) => {
        appData.currentWeekId = e.target.value;
        saveData();
        renderApp();
      });

      document.getElementById('btn-new-week').addEventListener('click', () => {
        const newW = createNewWeek();
        appData.weeks.unshift(newW);
        appData.currentWeekId = newW.id;
        saveData();
        renderApp();
        showModal('Week Created', 'New week created successfully!');
      });

      const settingsPanel = document.getElementById('settings-panel');
      document.getElementById('btn-settings').addEventListener('click', () => {
        settingsPanel.classList.toggle('hidden');
      });
      document.getElementById('close-settings').addEventListener('click', () => {
        settingsPanel.classList.add('hidden');
      });

      ['input-base-cals', 'input-target-steps', 'input-target-cals', 'input-target-strategy'].forEach(id => {
        document.getElementById(id).addEventListener('change', (e) => {
          const week = getCurrentWeek();
          const val = e.target.value;
          if (id === 'input-base-cals') week.baseCals = parseFloat(val) || 0;
          if (id === 'input-target-steps') week.targetSteps = parseFloat(val) || 0;
          if (id === 'input-target-cals') week.targetCals = parseFloat(val) || 0;
          if (id === 'input-target-strategy') week.strategy = val;
          saveData();
          renderApp();
        });
      });

      document.getElementById('btn-theme').addEventListener('click', () => {
        document.documentElement.classList.toggle('dark');
        if (!document.getElementById('container-chart-view').classList.contains('hidden')) {
          renderChart();
        }
      });

      document.getElementById('btn-rename-week').addEventListener('click', () => {
        const week = getCurrentWeek();
        const newName = prompt('Enter new week title:', week.title);
        if (newName && newName.trim()) {
          week.title = newName.trim();
          saveData();
          renderApp();
        }
      });

      document.getElementById('btn-delete-week').addEventListener('click', () => {
        if (appData.weeks.length <= 1) {
          showModal('Cannot Delete', 'You must keep at least one week in your planner.');
          return;
        }
        showModal('Delete Week', 'Are you sure you want to delete this week?', true, () => {
          appData.weeks = appData.weeks.filter(w => w.id !== appData.currentWeekId);
          appData.currentWeekId = appData.weeks[0].id;
          saveData();
          renderApp();
        });
      });

      document.getElementById('btn-load-example').addEventListener('click', () => {
        const week = getCurrentWeek();
        const exampleCalories = [1000, 2490, 2400, 2383, 2747, 2300, 3500];
        const exampleSteps = [11000, 10500, 14700, 14700, 12500, 15000, 19700];
        
        week.days = week.days.map((d, index) => ({
          ...d,
          calories: exampleCalories[index],
          steps: exampleSteps[index],
          isLogged: true
        }));
        
        saveData();
        renderApp();
        showModal('Example Loaded', 'Loaded your 7-day example scenario (1000, 2490, 2400...) successfully!');
      });

      // JSON Backup Download
      document.getElementById('btn-export-json').addEventListener('click', () => {
        const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(appData, null, 2));
        const downloadAnchor = document.createElement('a');
        downloadAnchor.setAttribute("href", dataStr);
        downloadAnchor.setAttribute("download", `flexical_backup_${new Date().toISOString().slice(0,10)}.json`);
        document.body.appendChild(downloadAnchor);
        downloadAnchor.click();
        downloadAnchor.remove();
      });

      // JSON Data Import
      document.getElementById('input-import-json').addEventListener('change', (e) => {
        const fileReader = new FileReader();
        fileReader.onload = (event) => {
          try {
            const importedData = JSON.parse(event.target.result);
            if (importedData && importedData.weeks && Array.isArray(importedData.weeks)) {
              appData = importedData;
              saveData();
              renderApp();
              showModal('Success', 'Data imported successfully!');
            } else {
              showModal('Import Error', 'Invalid backup file format.');
            }
          } catch(err) {
            showModal('Import Error', 'Failed to parse backup JSON file.');
          }
        };
        if (e.target.files[0]) {
          fileReader.readAsText(e.target.files[0]);
        }
      });

      const btnTable = document.getElementById('view-mode-table');
      const btnChart = document.getElementById('view-mode-chart');
      const containerTable = document.getElementById('container-table-view');
      const containerChart = document.getElementById('container-chart-view');

      btnTable.addEventListener('click', () => {
        containerTable.classList.remove('hidden');
        containerChart.classList.add('hidden');
        btnTable.className = "px-3 py-1.5 rounded-lg bg-white dark:bg-slate-800 shadow-sm text-slate-800 dark:text-slate-100 transition";
        btnChart.className = "px-3 py-1.5 rounded-lg text-slate-500 hover:text-slate-800 dark:hover:text-slate-200 transition";
      });

      btnChart.addEventListener('click', () => {
        containerTable.classList.add('hidden');
        containerChart.classList.remove('hidden');
        btnChart.className = "px-3 py-1.5 rounded-lg bg-white dark:bg-slate-800 shadow-sm text-slate-800 dark:text-slate-100 transition";
        btnTable.className = "px-3 py-1.5 rounded-lg text-slate-500 hover:text-slate-800 dark:hover:text-slate-200 transition";
        renderChart();
      });
    }

    function calculateWeekStats(week) {
      const stepGoalDaily = week.targetSteps || 14700;
      const totalWeeklyCalorieBudget = week.targetCals * 7;
      const weeklyStepGoal = stepGoalDaily * 7;

      let consumedCalories = 0;
      let loggedSteps = 0;
      let loggedDaysCount = 0;
      let totalNetBalance = 0;
      let netStepAdjustmentCals = 0;

      week.days.forEach(day => {
        if (day.isLogged) {
          consumedCalories += Number(day.calories);
          loggedSteps += Number(day.steps);
          loggedDaysCount++;

          const stepDiff = Number(day.steps) - stepGoalDaily;
          const stepCalorieOffset = (stepDiff / 1000) * 24;
          netStepAdjustmentCals += stepCalorieOffset;

          const estMaintenance = week.baseCals + stepCalorieOffset;
          const net = Number(day.calories) - estMaintenance;
          totalNetBalance += net;
        }
      });

      const adjustedWeeklyBudget = totalWeeklyCalorieBudget + netStepAdjustmentCals;
      const remainingCalories = adjustedWeeklyBudget - consumedCalories;
      const unloggedDaysCount = 7 - loggedDaysCount;

      const recommendedDailyCalorie = unloggedDaysCount > 0 
        ? Math.round(remainingCalories / unloggedDaysCount) 
        : 0;

      const currentCalorieAvg = loggedDaysCount > 0 
        ? Math.round(consumedCalories / loggedDaysCount) 
        : 0;

      const currentStepAvg = loggedDaysCount > 0 
        ? Math.round(loggedSteps / loggedDaysCount) 
        : stepGoalDaily;

      return {
        totalWeeklyCalorieBudget,
        weeklyStepGoal,
        consumedCalories,
        loggedSteps,
        remainingCalories: Math.round(remainingCalories),
        loggedDaysCount,
        unloggedDaysCount,
        recommendedDailyCalorie,
        currentCalorieAvg,
        currentStepAvg,
        totalNetBalance: Math.round(totalNetBalance)
      };
    }

    function renderApp() {
      const week = getCurrentWeek();
      if (!week.strategy) week.strategy = 'surplus';
      const stats = calculateWeekStats(week);

      const selector = document.getElementById('week-selector');
      selector.innerHTML = '';
      appData.weeks.forEach(w => {
        const opt = document.createElement('option');
        opt.value = w.id;
        opt.textContent = w.title;
        if (w.id === appData.currentWeekId) opt.selected = true;
        selector.appendChild(opt);
      });

      document.getElementById('input-base-cals').value = week.baseCals;
      document.getElementById('input-target-steps').value = week.targetSteps || 14700;
      document.getElementById('input-target-cals').value = week.targetCals;
      document.getElementById('input-target-strategy').value = week.strategy;
      document.getElementById('current-week-title-display').textContent = week.title;

      document.getElementById('cal-weekly-remaining').textContent = `${stats.remainingCalories.toLocaleString()} kcal`;
      document.getElementById('cal-weekly-total').textContent = `${stats.totalWeeklyCalorieBudget.toLocaleString()} kcal`;
      document.getElementById('cal-current-avg').textContent = stats.currentCalorieAvg.toLocaleString();
      document.getElementById('cal-target-avg').textContent = week.targetCals.toLocaleString();
      document.getElementById('cal-total-used').textContent = stats.consumedCalories.toLocaleString();
      document.getElementById('cal-budget-limit').textContent = stats.totalWeeklyCalorieBudget.toLocaleString();

      const calPct = Math.min(100, Math.max(0, (stats.consumedCalories / stats.totalWeeklyCalorieBudget) * 100));
      const calProgressBar = document.getElementById('cal-progress-bar');
      calProgressBar.style.width = `${calPct}%`;
      calProgressBar.className = "bg-green-500 h-full rounded-full transition-all duration-500";

      document.getElementById('step-total-display').textContent = `${stats.loggedSteps.toLocaleString()} steps`;
      document.getElementById('step-current-avg').textContent = stats.currentStepAvg.toLocaleString();
      document.getElementById('step-target-avg').textContent = (week.targetSteps || 14700).toLocaleString();
      document.getElementById('step-total-logged').textContent = stats.loggedSteps.toLocaleString();
      document.getElementById('step-budget-limit').textContent = stats.weeklyStepGoal.toLocaleString();
      
      const stepPct = Math.min(100, Math.max(0, (stats.loggedSteps / stats.weeklyStepGoal) * 100));
      document.getElementById('step-progress-bar').style.width = `${stepPct}%`;

      const netDisplay = document.getElementById('net-balance-display');
      const netStatus = document.getElementById('net-balance-status');
      const balanceAdvice = document.getElementById('balance-advice-text');

      netDisplay.textContent = `${stats.totalNetBalance > 0 ? '+' : ''}${stats.totalNetBalance.toLocaleString()} kcal`;
      
      if (week.strategy === 'surplus') {
        if (stats.totalNetBalance > 50) {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-emerald-500";
          netStatus.textContent = "Goal Surplus Active 🎯";
          balanceAdvice.innerHTML = `Great job! You are hitting your target surplus to support your growth goals.`;
        } else if (stats.totalNetBalance < -50) {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-amber-500";
          netStatus.textContent = "Below Surplus Target";
          balanceAdvice.innerHTML = `You are currently in a deficit. Increase calories slightly to hit your surplus target.`;
        } else {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-blue-500";
          netStatus.textContent = "Balanced at Baseline";
          balanceAdvice.innerHTML = `Right at your baseline maintenance energy levels!`;
        }
      } else if (week.strategy === 'deficit') {
        if (stats.totalNetBalance < -50) {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-emerald-500";
          netStatus.textContent = "Goal Deficit Active 🎯";
          balanceAdvice.innerHTML = `Awesome! Your step activity and calorie intake are maintaining a healthy fat-loss deficit.`;
        } else if (stats.totalNetBalance > 50) {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-amber-500";
          netStatus.textContent = "Above Deficit Target";
          balanceAdvice.innerHTML = `Currently in a slight surplus relative to baseline maintenance.`;
        } else {
          netDisplay.className = "text-2xl font-extrabold mt-2 text-blue-500";
          netStatus.textContent = "At Maintenance Baseline";
          balanceAdvice.innerHTML = `Currently right at maintenance. Eat slightly less or walk more to build a deficit.`;
        }
      } else {
        netDisplay.className = "text-2xl font-extrabold mt-2 text-blue-500";
        netStatus.textContent = "Maintenance Goal";
        balanceAdvice.innerHTML = `Your energy intake is balancing smoothly with your daily step expenditure.`;
      }

      renderTable(week, stats);

      if (!document.getElementById('container-chart-view').classList.contains('hidden')) {
        renderChart();
      }
    }

    function renderTable(week, stats) {
      const tableBody = document.getElementById('days-table-body');
      tableBody.innerHTML = '';

      const stepGoalDaily = week.targetSteps || 14700;

      let runningAdjustedBudget = week.targetCals * 7;
      let loggedCount = 0;

      week.days.forEach(d => {
        if (d.isLogged) {
          const stepDiff = Number(d.steps) - stepGoalDaily;
          const stepCalorieOffset = (stepDiff / 1000) * 24;
          runningAdjustedBudget += stepCalorieOffset - Number(d.calories);
          loggedCount++;
        }
      });

      let unloggedDaysRemaining = 7 - loggedCount;

      week.days.forEach((day, index) => {
        const tr = document.createElement('tr');
        tr.className = "hover:bg-slate-50/80 dark:hover:bg-slate-700/30 transition-colors";

        let targetCalDisplay = '';
        if (day.isLogged) {
          targetCalDisplay = `<span class="text-xs text-slate-400 font-normal">Logged</span>`;
        } else {
          const rec = unloggedDaysRemaining > 0 ? Math.round(runningAdjustedBudget / unloggedDaysRemaining) : 0;
          targetCalDisplay = `<span class="font-bold text-orange-600 dark:text-orange-400">${rec.toLocaleString()}</span> <span class="text-xs text-slate-400">kcal</span>`;
        }

        let netImpactHTML = '-';
        let statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-medium text-slate-400 border border-dashed border-slate-200 dark:border-slate-700">Pending</span>`;

        if (day.isLogged) {
          const stepDiff = Number(day.steps) - stepGoalDaily;
          const estMaintenance = week.baseCals + (stepDiff / 1000) * 24;
          const net = Math.round(Number(day.calories) - estMaintenance);

          if (week.strategy === 'surplus') {
            if (net >= 15 && net <= 40) {
              netImpactHTML = `<span class="text-emerald-600 dark:text-emerald-400 font-semibold">+${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-emerald-100 dark:bg-emerald-950/50 text-emerald-600 dark:text-emerald-300">Maintenance / +25</span>`;
            } else if (net > 40) {
              netImpactHTML = `<span class="text-emerald-600 dark:text-emerald-400 font-semibold">+${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-emerald-100 dark:bg-emerald-950/50 text-emerald-600 dark:text-emerald-300">Goal Surplus</span>`;
            } else if (net < -15) {
              netImpactHTML = `<span class="text-amber-500 font-semibold">${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-amber-100 dark:bg-amber-950/50 text-amber-600 dark:text-amber-300">Below Surplus</span>`;
            } else {
              netImpactHTML = `<span class="text-blue-500 font-semibold">+${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-blue-100 dark:bg-blue-950/50 text-blue-600 dark:text-blue-300">Near Maintenance</span>`;
            }
          } else if (week.strategy === 'deficit') {
            if (net < -50) {
              netImpactHTML = `<span class="text-emerald-600 dark:text-emerald-400 font-semibold">${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-emerald-100 dark:bg-emerald-950/50 text-emerald-600 dark:text-emerald-300">Goal Deficit</span>`;
            } else if (net > 50) {
              netImpactHTML = `<span class="text-amber-500 font-semibold">+${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-amber-100 dark:bg-amber-950/50 text-amber-600 dark:text-amber-300">Above Target</span>`;
            } else {
              netImpactHTML = `<span class="text-blue-500 font-semibold">0 kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-blue-100 dark:bg-blue-950/50 text-blue-600 dark:text-blue-300">Maintenance</span>`;
            }
          } else {
            if (Math.abs(net) <= 35) {
              netImpactHTML = `<span class="text-emerald-600 dark:text-emerald-400 font-semibold">${net >= 0 ? '+' : ''}${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-emerald-100 dark:bg-emerald-950/50 text-emerald-600 dark:text-emerald-300">True Maintenance</span>`;
            } else {
              netImpactHTML = `<span class="text-blue-500 font-semibold">${net >= 0 ? '+' : ''}${net} kcal</span>`;
              statusPill = `<span class="px-2 py-1 rounded-lg text-[11px] font-semibold bg-blue-100 dark:bg-blue-950/50 text-blue-600 dark:text-blue-300">Balanced</span>`;
            }
          }
        }

        tr.innerHTML = `
          <td class="py-3 px-2 font-semibold text-slate-800 dark:text-slate-200">
            <div class="flex items-center gap-2">
              <button data-day-toggle="${index}" class="w-6 h-6 rounded-lg flex items-center justify-center transition ${day.isLogged ? 'bg-green-500 text-white' : 'bg-slate-200 dark:bg-slate-700 text-slate-400'}">
                <i class="fa-solid fa-check text-xs"></i>
              </button>
              ${day.name}
            </div>
          </td>
          <td class="py-3 px-2">
            <input type="number" data-day-cal="${index}" value="${day.calories}" step="25" class="w-24 bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 rounded-xl px-2.5 py-1 text-xs font-bold text-slate-800 dark:text-slate-100 focus:outline-none focus:ring-2 focus:ring-green-500">
          </td>
          <td class="py-3 px-2">${targetCalDisplay}</td>
          <td class="py-3 px-2">
            <input type="number" data-day-steps="${index}" value="${day.steps}" step="250" class="w-28 bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 rounded-xl px-2.5 py-1 text-xs font-bold text-slate-800 dark:text-slate-100 focus:outline-none focus:ring-2 focus:ring-blue-500">
          </td>
          <td class="py-3 px-2">${netImpactHTML}</td>
          <td class="py-3 px-2">${statusPill}</td>
        `;

        tableBody.appendChild(tr);
      });

      document.querySelectorAll('[data-day-toggle]').forEach(btn => {
        btn.addEventListener('click', (e) => {
          const idx = parseInt(e.currentTarget.getAttribute('data-day-toggle'));
          week.days[idx].isLogged = !week.days[idx].isLogged;
          saveData();
          renderApp();
        });
      });

      document.querySelectorAll('[data-day-cal]').forEach(input => {
        input.addEventListener('change', (e) => {
          const idx = parseInt(e.target.getAttribute('data-day-cal'));
          week.days[idx].calories = parseFloat(e.target.value) || 0;
          week.days[idx].isLogged = true;
          saveData();
          renderApp();
        });
      });

      document.querySelectorAll('[data-day-steps]').forEach(input => {
        input.addEventListener('change', (e) => {
          const idx = parseInt(e.target.getAttribute('data-day-steps'));
          week.days[idx].steps = parseFloat(e.target.value) || 0;
          week.days[idx].isLogged = true;
          saveData();
          renderApp();
        });
      });
    }

    function renderChart() {
      const week = getCurrentWeek();
      const ctx = document.getElementById('weeklyChart').getContext('2d');
      const isDark = document.documentElement.classList.contains('dark');

      const labels = week.days.map(d => d.name);
      const caloriesData = week.days.map(d => d.calories);
      const stepsData = week.days.map(d => d.steps);

      if (chartInstance) {
        chartInstance.destroy();
      }

      chartInstance = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: labels,
          datasets: [
            {
              label: 'Calories In',
              data: caloriesData,
              backgroundColor: 'rgba(249, 115, 22, 0.85)',
              borderRadius: 8,
              yAxisID: 'y'
            },
            {
              label: 'Steps Walked',
              data: stepsData,
              backgroundColor: 'rgba(59, 130, 246, 0.85)',
              borderRadius: 8,
              yAxisID: 'y1'
            }
          ]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              labels: { color: isDark ? '#cbd5e1' : '#334155' }
            }
          },
          scales: {
            x: {
              ticks: { color: isDark ? '#94a3b8' : '#64748b' },
              grid: { display: false }
            },
            y: {
              type: 'linear',
              position: 'left',
              title: { display: true, text: 'Calories', color: '#f97316' },
              ticks: { color: isDark ? '#94a3b8' : '#64748b' },
              grid: { color: isDark ? '#334155' : '#e2e8f0' }
            },
            y1: {
              type: 'linear',
              position: 'right',
              title: { display: true, text: 'Steps', color: '#3b82f6' },
              ticks: { color: isDark ? '#94a3b8' : '#64748b' },
              grid: { drawOnChartArea: false }
            }
          }
        }
      });
    }
  </script>
</body>
</html>