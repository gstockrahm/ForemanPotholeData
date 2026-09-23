# ForemanPotholeData
# Helps with Keeping Data for our potholing
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pothole & Utility Data Tracker</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        @media print {
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            body {
                background-color: white !important;
                color: black !important;
            }
            .print-container {
                box-shadow: none !important;
                border: none !important;
                padding: 0 !important;
                margin: 0 !important;
                width: 100% !important;
            }
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col antialiased">

     <!-- Header -->
    <header class="bg-slate-800 border-b border-slate-700 sticky top-0 z-30 no-print">
        <div class="max-w-7xl mx-auto px-4 py-4 sm:px-6 flex flex-col sm:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <div class="bg-amber-500 p-2.5 rounded-xl text-slate-950 font-bold shadow-lg shadow-amber-500/20">
                    <i data-lucide="hard-hat" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-tight text-white flex items-center gap-2">
                        PotholeTrack <span class="text-xs bg-amber-500/20 text-amber-400 font-medium px-2 py-0.5 rounded-full border border-amber-500/30">Foreman Field Tool</span>
                    </h1>
                    <p class="text-xs text-slate-400">Utility Elevation & Depth Recorder</p>
                </div>
            </div>
            <!-- Quick Action Buttons -->
            <div class="flex items-center gap-2.5 w-full sm:w-auto justify-end">
                <button onclick="openExportModal()" class="flex-1 sm:flex-none inline-flex items-center justify-center gap-2 bg-slate-700 hover:bg-slate-600 text-slate-200 px-4 py-2.5 rounded-xl font-medium text-sm transition border border-slate-600 shadow-sm">
                    <i data-lucide="download" class="w-4 h-4 text-amber-400"></i> Export CSV / Print
                </button>
                <button onclick="clearAllData()" class="inline-flex items-center justify-center p-2.5 bg-rose-500/10 hover:bg-rose-500/20 text-rose-400 rounded-xl transition border border-rose-500/30" title="Reset All Data">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 py-6 flex-grow w-full space-y-6">

        <!-- Notification Banner / Message Box -->
        <div id="toast" class="hidden fixed bottom-5 right-5 z-50 transition-all transform duration-300 translate-y-2 opacity-0">
            <div id="toast-content" class="bg-slate-800 border border-slate-700 text-white px-5 py-3 rounded-xl shadow-2xl flex items-center gap-3">
                <i data-lucide="check-circle" class="w-5 h-5 text-emerald-400" id="toast-icon"></i>
                <span id="toast-message" class="text-sm font-medium">Action completed successfully.</span>
            </div>
        </div>

        <!-- Setup & Baseline Card -->
        <section class="bg-slate-800/90 backdrop-blur border border-slate-700 rounded-2xl p-5 shadow-xl no-print">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 mb-4 pb-4 border-b border-slate-700/60">
                <div class="flex items-center gap-3">
                    <div class="p-2 bg-blue-500/10 text-blue-400 rounded-lg border border-blue-500/20">
                        <i data-lucide="crosshair" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <h2 class="text-base font-semibold text-white">1. Instrument Setup & Benchmark</h2>
                        <p class="text-xs text-slate-400">Establish your baseline benchmark and instrument height (HI)</p>
                    </div>
                </div>
                <div id="setup-status-badge" class="flex items-center gap-2 bg-emerald-500/10 border border-emerald-500/30 text-emerald-400 px-3 py-1.5 rounded-full text-xs font-semibold">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                    HI Active & Ready
                </div>
            </div>

            <form id="setup-form" onsubmit="saveSetup(event)" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1.5">Foreman / Crew Name</label>
                    <input type="text" id="foreman-name" required placeholder="e.g. Mike Johnson" 
                        class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1.5">Benchmark / Magspike ID</label>
                    <input type="text" id="bm-name" required placeholder="e.g. Magspike #104" 
                        class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1.5">BM Elevation (ft)</label>
                    <input type="number" step="0.001" id="bm-elev" required placeholder="100.00" 
                        class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="calculateHI()">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1.5">+ BS Rod Reading on BM</label>
                    <input type="number" step="0.001" id="bs-reading" required placeholder="4.52" 
                        class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="calculateHI()">
                </div>
                <div class="flex flex-col justify-end">
                    <div class="bg-slate-900/90 border border-amber-500/40 rounded-xl p-2.5 flex items-center justify-between">
                        <div>
                            <span class="text-[10px] uppercase tracking-wider text-amber-400 font-bold block">Current HI</span>
                            <span id="display-hi" class="text-lg font-bold text-white">0.000 ft</span>
                        </div>
                        <button type="submit" class="bg-amber-500 hover:bg-amber-400 text-slate-950 font-semibold px-3 py-2 rounded-lg text-xs transition shadow-md shadow-amber-500/20">
                            Update
                        </button>
                    </div>
                </div>
            </form>
        </section>

        <!-- Pothole Data Entry Form -->
        <section class="bg-slate-800/90 backdrop-blur border border-slate-700 rounded-2xl p-5 shadow-xl no-print">
            <div class="flex items-center gap-3 mb-4 pb-4 border-b border-slate-700/60">
                <div class="p-2 bg-amber-500/10 text-amber-400 rounded-lg border border-amber-500/20">
                    <i data-lucide="plus-circle" class="w-5 h-5"></i>
                </div>
                <div>
                    <h2 class="text-base font-semibold text-white">2. Record Pothole / Utility Shot</h2>
                    <p class="text-xs text-slate-400">Capture ground elevation, pipe elevation, and measurement point type</p>
                </div>
            </div>

            <form id="pothole-form" onsubmit="savePothole(event)" class="space-y-4">
                <input type="hidden" id="edit-index" value="-1">
                
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1.5">Point ID / Name</label>
                        <input type="text" id="ph-name" required placeholder="e.g. PH-01 or MH-3" 
                            class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition">
                    </div>

                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1.5">Ground Rod Reading OR Direct Elev</label>
                        <div class="flex gap-2">
                            <input type="number" step="0.001" id="ground-rod" placeholder="Rod (e.g. 5.21)" 
                                class="w-1/2 bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="computeGroundElev()">
                            <input type="number" step="0.001" id="ground-elev" required placeholder="Elev Ground" 
                                class="w-1/2 bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="updateCalculations()">
                        </div>
                    </div>

                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1.5">Pipe Rod Reading OR Direct Elev</label>
                        <div class="flex gap-2">
                            <input type="number" step="0.001" id="pipe-rod" placeholder="Rod (e.g. 8.45)" 
                                class="w-1/2 bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="computePipeElev()">
                            <input type="number" step="0.001" id="pipe-elev" required placeholder="Elev Pipe" 
                                class="w-1/2 bg-slate-900 border border-slate-700 rounded-xl px-3 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition" oninput="updateCalculations()">
                        </div>
                    </div>

                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1.5">Measurement Label</label>
                        <select id="ph-label" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition">
                            <option value="Top of Pipe">Top of Pipe (T.O.P.)</option>
                            <option value="Bottom of Pipe">Bottom of Pipe / Flowline</option>
                            <option value="Centerline (CL)">Centerline (C/L)</option>
                            <option value="Pit Entry Point">Pit Entry Point</option>
                            <option value="Invert">Invert</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 items-center pt-2">
                    <div>
                        <label class="block text-xs font-medium text-slate-300 mb-1.5">Notes / Utility Type</label>
                        <input type="text" id="ph-notes" placeholder="e.g. 8 inch PVC Watermain, crossing gas" 
                            class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-amber-500 transition">
                    </div>

                    <!-- Live Calculated preview pill -->
                    <div class="bg-slate-900/80 border border-slate-700 rounded-xl p-3 flex items-center justify-around text-center">
                        <div>
                            <span class="text-[10px] text-slate-400 uppercase font-semibold">Calc Depth to Pipe</span>
                            <span id="preview-depth" class="text-base font-bold text-amber-400">0.00 ft</span>
                        </div>
                        <div class="h-8 w-px bg-slate-700"></div>
                        <div>
                            <span class="text-[10px] text-slate-400 uppercase font-semibold">Status</span>
                            <span id="preview-status" class="text-xs font-medium text-emerald-400">Ready</span>
                        </div>
                    </div>

                    <div class="flex items-center gap-2 justify-end">
                        <button type="button" id="cancel-edit-btn" onclick="resetPotholeForm()" class="hidden px-4 py-2.5 bg-slate-700 hover:bg-slate-600 text-slate-200 rounded-xl text-sm font-medium transition">
                            Cancel
                        </button>
                        <button type="submit" id="submit-btn" class="w-full md:w-auto inline-flex items-center justify-center gap-2 bg-gradient-to-r from-amber-500 to-amber-600 hover:from-amber-400 hover:to-amber-500 text-slate-950 px-6 py-2.5 rounded-xl font-bold text-sm transition shadow-lg shadow-amber-500/20">
                            <i data-lucide="plus" class="w-4 h-4"></i> Add Pothole Entry
                        </button>
                    </div>
                </div>
            </form>
        </section>

        <!-- Summary Cards & Data Table -->
        <section class="space-y-4">
            <!-- Stats Row -->
            <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 no-print">
                <div class="bg-slate-800 border border-slate-700/80 rounded-xl p-3.5 flex items-center gap-3">
                    <div class="p-2.5 bg-blue-500/10 text-blue-400 rounded-lg">
                        <i data-lucide="layers" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <span class="text-xs text-slate-400 block font-medium">Total Entries</span>
                        <span id="stat-total" class="text-lg font-bold text-white">0</span>
                    </div>
                </div>
                <div class="bg-slate-800 border border-slate-700/80 rounded-xl p-3.5 flex items-center gap-3">
                    <div class="p-2.5 bg-emerald-500/10 text-emerald-400 rounded-lg">
                        <i data-lucide="arrow-down-narrow-wide" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <span class="text-xs text-slate-400 block font-medium">Max Depth</span>
                        <span id="stat-max-depth" class="text-lg font-bold text-white">0.00 ft</span>
                    </div>
                </div>
                <div class="bg-slate-800 border border-slate-700/80 rounded-xl p-3.5 flex items-center gap-3">
                    <div class="p-2.5 bg-amber-500/10 text-amber-400 rounded-lg">
                        <i data-lucide="arrow-up-narrow-wide" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <span class="text-xs text-slate-400 block font-medium">Min Depth</span>
                        <span id="stat-min-depth" class="text-lg font-bold text-white">0.00 ft</span>
                    </div>
                </div>
                <div class="bg-slate-800 border border-slate-700/80 rounded-xl p-3.5 flex items-center gap-3">
                    <div class="p-2.5 bg-purple-500/10 text-purple-400 rounded-lg">
                        <i data-lucide="activity" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <span class="text-xs text-slate-400 block font-medium">Avg Depth</span>
                        <span id="stat-avg-depth" class="text-lg font-bold text-white">0.00 ft</span>
                    </div>
                </div>
            </div>

            <!-- Table Container -->
            <div class="bg-slate-800/90 backdrop-blur border border-slate-700 rounded-2xl shadow-xl overflow-hidden print-container">
                <!-- Printable Report Header (Visible on Print) -->
                <div class="hidden print-only p-6 border-b border-slate-300 mb-4">
                    <div class="flex justify-between items-start">
                        <div>
                            <h2 class="text-2xl font-bold text-slate-900">POTHOLE & UTILITY SURVEY REPORT</h2>
                            <p class="text-sm text-slate-600 mt-1">Project Field Data Sheet</p>
                        </div>
                        <div class="text-right text-sm text-slate-700">
                            <p><strong class="text-slate-900">Date:</strong> <span id="print-date"></span></p>
                            <p><strong class="text-slate-900">Foreman:</strong> <span id="print-foreman"></span></p>
                        </div>
                    </div>
                    <div class="grid grid-cols-3 gap-4 mt-4 p-3 bg-slate-100 rounded-lg text-sm text-slate-800">
                        <div><strong>Benchmark ID:</strong> <span id="print-bm">--</span></div>
                        <div><strong>BM Elevation:</strong> <span id="print-bmelev">0.00</span> ft</div>
                        <div><strong>Instrument HI:</strong> <span id="print-hi">0.00</span> ft</div>
                    </div>
                </div>

                <div class="p-4 sm:p-5 border-b border-slate-700/60 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 no-print">
                    <div class="flex items-center gap-3">
                        <div class="p-2 bg-purple-500/10 text-purple-400 rounded-lg border border-purple-500/20">
                            <i data-lucide="table" class="w-5 h-5"></i>
                        </div>
                        <div>
                            <h2 class="text-base font-semibold text-white">Recorded Pothole Data Log</h2>
                            <p class="text-xs text-slate-400">All utility crossings and measurements</p>
                        </div>
                    </div>
                    <div class="relative w-full sm:w-64">
                        <span class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-slate-400">
                            <i data-lucide="search" class="w-4 h-4"></i>
                        </span>
                        <input type="text" id="search-input" oninput="renderTable()" placeholder="Search point or notes..." 
                            class="w-full bg-slate-900 border border-slate-700 rounded-xl pl-9 pr-3 py-2 text-xs text-white focus:outline-none focus:border-amber-500 transition">
                    </div>
                </div>

                <!-- Table -->
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse text-sm">
                        <thead>
                            <tr class="bg-slate-900/60 text-slate-400 uppercase text-[11px] tracking-wider border-b border-slate-700">
                                <th class="py-3 px-4 font-semibold">Point ID</th>
                                <th class="py-3 px-4 font-semibold">Label / Type</th>
                                <th class="py-3 px-4 font-semibold text-right">Ground Elev (ft)</th>
                                <th class="py-3 px-4 font-semibold text-right">Pipe Elev (ft)</th>
                                <th class="py-3 px-4 font-semibold text-right">Depth to Pipe</th>
                                <th class="py-3 px-4 font-semibold">Notes / Utility</th>
                                <th class="py-3 px-4 font-semibold text-center no-print">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="pothole-table-body" class="divide-y divide-slate-700/50 text-slate-200">
                            <!-- Populated dynamically via JS -->
                        </tbody>
                    </table>
                </div>

                <!-- Empty State -->
                <div id="empty-state" class="hidden p-12 text-center">
                    <div class="w-12 h-12 bg-slate-700/50 rounded-full flex items-center justify-center mx-auto mb-3 text-slate-400">
                        <i data-lucide="folder-open" class="w-6 h-6"></i>
                    </div>
                    <p class="text-sm font-medium text-slate-300">No pothole shots recorded yet.</p>
                    <p class="text-xs text-slate-500 mt-1">Enter your benchmark and add your first point above.</p>
                </div>
            </div>
        </section>
    </main>

    <!-- Export / Print Modal -->
    <div id="export-modal" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="bg-slate-800 border border-slate-700 rounded-2xl max-w-md w-full p-6 shadow-2xl space-y-5 animate-in fade-in zoom-in-95 duration-200">
            <div class="flex justify-between items-center pb-3 border-b border-slate-700">
                <div class="flex items-center gap-2.5">
                    <div class="p-2 bg-amber-500/10 text-amber-400 rounded-lg">
                        <i data-lucide="file-spreadsheet" class="w-5 h-5"></i>
                    </div>
                    <h3 class="font-bold text-white text-base">Export or Print Data</h3>
                </div>
                <button onclick="closeExportModal()" class="text-slate-400 hover:text-white p-1 rounded-lg">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <p class="text-xs text-slate-300">
                Choose your preferred format to export or share the tracked potholing data with project engineers, office staff, or foremen.
            </p>

            <div class="space-y-3">
                <button onclick="exportCSV()" class="w-full flex items-center justify-between p-3.5 bg-slate-900 hover:bg-slate-700/80 border border-slate-700 rounded-xl transition group">
                    <div class="flex items-center gap-3">
                        <div class="p-2 bg-emerald-500/10 text-emerald-400 rounded-lg group-hover:scale-105 transition">
                            <i data-lucide="file-text" class="w-5 h-5"></i>
                        </div>
                        <div class="text-left">
                            <span class="block text-sm font-semibold text-white">Download CSV (Excel)</span>
                            <span class="block text-xs text-slate-400">Compatible with Excel, Google Sheets, Procore</span>
                        </div>
                    </div>
                    <i data-lucide="chevron-right" class="w-4 h-4 text-slate-500"></i>
                </button>

                <button onclick="triggerPrint()" class="w-full flex items-center justify-between p-3.5 bg-slate-900 hover:bg-slate-700/80 border border-slate-700 rounded-xl transition group">
                    <div class="flex items-center gap-3">
                        <div class="p-2 bg-blue-500/10 text-blue-400 rounded-lg group-hover:scale-105 transition">
                            <i data-lucide="printer" class="w-5 h-5"></i>
                        </div>
                        <div class="text-left">
                            <span class="block text-sm font-semibold text-white">Print / Save as PDF</span>
                            <span class="block text-xs text-slate-400">Clean formatted report sheet for binder</span>
                        </div>
                    </div>
                    <i data-lucide="chevron-right" class="w-4 h-4 text-slate-500"></i>
                </button>
            </div>

            <div class="pt-2 flex justify-end">
                <button onclick="closeExportModal()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-slate-200 text-xs font-medium rounded-xl transition">
                    Close
                </button>
            </div>
        </div>
    </div>

    <!-- Application Logic -->
    <script>
        // State management
        let state = {
            foremanName: "John Foreman",
            bmName: "Magspike #1",
            bmElev: 100.000,
            bsReading: 5.250,
            hi: 105.250,
            potholes: [
                { id: "PH-01", groundElev: 98.500, pipeElev: 91.200, depth: 7.300, label: "Top of Pipe", notes: "8\" DIP Watermain" },
                { id: "PH-02", groundElev: 98.200, pipeElev: 92.500, depth: 5.700, label: "Centerline (CL)", notes: "12\" Storm Sewer" },
                { id: "PH-03", groundElev: 97.900, pipeElev: 93.100, depth: 4.800, label: "Pit Entry Point", notes: "Gas service crossing" }
            ]
        };

        // Load saved state on startup
        window.addEventListener('DOMContentLoaded', () => {
            const saved = localStorage.getItem('pothole_tracker_state');
            if (saved) {
                try {
                    state = JSON.parse(saved);
                } catch (e) {
                    console.error("Failed to load local storage", e);
                }
            }
            
            // Populate Setup inputs
            document.getElementById('foreman-name').value = state.foremanName || '';
            document.getElementById('bm-name').value = state.bmName || '';
            document.getElementById('bm-elev').value = state.bmElev !== undefined ? state.bmElev : '';
            document.getElementById('bs-reading').value = state.bsReading !== undefined ? state.bsReading : '';

            calculateHI();
            renderTable();
            lucide.createIcons();
        });

        function saveState() {
            localStorage.setItem('pothole_tracker_state', JSON.stringify(state));
        }

        function showToast(message, isError = false) {
            const toast = document.getElementById('toast');
            const msg = document.getElementById('toast-message');
            const icon = document.getElementById('toast-icon');
            
            msg.textContent = message;
            if (isError) {
                icon.setAttribute('data-lucide', 'alert-circle');
                icon.className = 'w-5 h-5 text-rose-400';
            } else {
                icon.setAttribute('data-lucide', 'check-circle');
                icon.className = 'w-5 h-5 text-emerald-400';
            }
            lucide.createIcons();

            toast.classList.remove('hidden', 'translate-y-2', 'opacity-0');
            setTimeout(() => {
                toast.classList.add('translate-y-2', 'opacity-0');
                setTimeout(() => toast.classList.add('hidden'), 300);
            }, 3000);
        }

        // Calculate Instrument HI = BM Elevation + Backsight Rod Reading
        function calculateHI() {
            const bmElev = parseFloat(document.getElementById('bm-elev').value) || 0;
            const bsReading = parseFloat(document.getElementById('bs-reading').value) || 0;
            const hi = bmElev + bsReading;
            
            state.hi = hi;
            state.bmElev = bmElev;
            state.bsReading = bsReading;
            state.bmName = document.getElementById('bm-name').value;
            state.foremanName = document.getElementById('foreman-name').value;

            document.getElementById('display-hi').textContent = hi.toFixed(3) + ' ft';
            saveState();
        }

        function saveSetup(e) {
            e.preventDefault();
            calculateHI();
            showToast("Instrument setup & HI updated successfully!");
        }

        // Compute Ground Elevation from Rod Reading (HI - Rod)
        function computeGroundElev() {
            const rod = parseFloat(document.getElementById('ground-rod').value);
            if (!isNaN(rod)) {
                const elev = state.hi - rod;
                document.getElementById('ground-elev').value = elev.toFixed(3);
                updateCalculations();
            }
        }

        // Compute Pipe Elevation from Rod Reading (HI - Rod)
        function computePipeElev() {
            const rod = parseFloat(document.getElementById('pipe-rod').value);
            if (!isNaN(rod)) {
                const elev = state.hi - rod;
                document.getElementById('pipe-elev').value = elev.toFixed(3);
                updateCalculations();
            }
        }

        // Live update depth calculation preview
        function updateCalculations() {
            const ground = parseFloat(document.getElementById('ground-elev').value) || 0;
            const pipe = parseFloat(document.getElementById('pipe-elev').value) || 0;
            const depth = ground - pipe;

            const previewDepth = document.getElementById('preview-depth');
            const previewStatus = document.getElementById('preview-status');

            previewDepth.textContent = depth.toFixed(3) + ' ft';
            if (depth < 0) {
                previewStatus.textContent = "Pipe Above Ground?! Check values";
                previewStatus.className = "text-xs font-medium text-rose-400";
            } else {
                previewStatus.textContent = "Normal Depth";
                previewStatus.className = "text-xs font-medium text-emerald-400";
            }
        }

        // Save or update pothole entry
        function savePothole(e) {
            e.preventDefault();
            const editIndex = parseInt(document.getElementById('edit-index').value);
            const name = document.getElementById('ph-name').value.trim();
            const groundElev = parseFloat(document.getElementById('ground-elev').value);
            const pipeElev = parseFloat(document.getElementById('pipe-elev').value);
            const label = document.getElementById('ph-label').value;
            const notes = document.getElementById('ph-notes').value.trim();

            const depth = groundElev - pipeElev;

            const entry = {
                id: name,
                groundElev: groundElev,
                pipeElev: pipeElev,
                depth: depth,
                label: label,
                notes: notes
            };

            if (editIndex >= 0) {
                state.potholes[editIndex] = entry;
                showToast(`Updated entry ${name}`);
            } else {
                state.potholes.push(entry);
                showToast(`Added pothole shot ${name}`);
            }

            resetPotholeForm();
            renderTable();
        }

        function resetPotholeForm() {
            document.getElementById('pothole-form').reset();
            document.getElementById('edit-index').value = "-1";
            document.getElementById('submit-btn').innerHTML = '<i data-lucide="plus" class="w-4 h-4"></i> Add Pothole Entry';
            document.getElementById('cancel-edit-btn').classList.add('hidden');
            document.getElementById('preview-depth').textContent = "0.00 ft";
            lucide.createIcons();
        }

        function editPothole(index) {
            const p = state.potholes[index];
            document.getElementById('edit-index').value = index;
            document.getElementById('ph-name').value = p.id;
            document.getElementById('ground-elev').value = p.groundElev;
            document.getElementById('pipe-elev').value = p.pipeElev;
            document.getElementById('ph-label').value = p.label;
            document.getElementById('ph-notes').value = p.notes || '';
            
            document.getElementById('submit-btn').innerHTML = '<i data-lucide="check" class="w-4 h-4"></i> Update Entry';
            document.getElementById('cancel-edit-btn').classList.remove('hidden');
            updateCalculations();

            window.scrollTo({ top: 400, behavior: 'smooth' });
            lucide.createIcons();
        }

        function deletePothole(index) {
            if (confirm("Are you sure you want to delete this pothole record?")) {
                state.potholes.splice(index, 1);
                renderTable();
                showToast("Entry removed", true);
            }
        }

        function clearAllData() {
            if (confirm("Are you sure you want to clear all pothole records? This cannot be undone.")) {
                state.potholes = [];
                renderTable();
                showToast("All records cleared", true);
            }
        }

        // Render Data Table and Statistics
        function renderTable() {
            saveState();
            const tbody = document.getElementById('pothole-table-body');
            const emptyState = document.getElementById('empty-state');
            const search = document.getElementById('search-input').value.toLowerCase();

            tbody.innerHTML = '';

            const filtered = state.potholes.filter(p => 
                p.id.toLowerCase().includes(search) || 
                p.label.toLowerCase().includes(search) || 
                (p.notes && p.notes.toLowerCase().includes(search))
            );

            if (filtered.length === 0) {
                emptyState.classList.remove('hidden');
            } else {
                emptyState.classList.add('hidden');
            }

            // Calculate statistics across all records
            let total = state.potholes.length;
            let maxDepth = total > 0 ? Math.max(...state.potholes.map(p => p.depth)) : 0;
            let minDepth = total > 0 ? Math.min(...state.potholes.map(p => p.depth)) : 0;
            let avgDepth = total > 0 ? state.potholes.reduce((acc, p) => acc + p.depth, 0) / total : 0;

            document.getElementById('stat-total').textContent = total;
            document.getElementById('stat-max-depth').textContent = maxDepth.toFixed(2) + ' ft';
            document.getElementById('stat-min-depth').textContent = minDepth.toFixed(2) + ' ft';
            document.getElementById('stat-avg-depth').textContent = avgDepth.toFixed(2) + ' ft';

            // Render rows
            state.potholes.forEach((p, originalIndex) => {
                // If filtered out by search, skip rendering in table
                if (!filtered.includes(p)) return;

                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-700/30 transition border-b border-slate-700/40";
                
                tr.innerHTML = `
                    <td class="py-3 px-4 font-semibold text-white">${escapeHtml(p.id)}</td>
                    <td class="py-3 px-4">
                        <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium bg-slate-700 text-slate-300 border border-slate-600">
                            ${escapeHtml(p.label)}
                        </span>
                    </td>
                    <td class="py-3 px-4 text-right font-mono text-slate-300">${p.groundElev.toFixed(3)}</td>
                    <td class="py-3 px-4 text-right font-mono text-slate-300">${p.pipeElev.toFixed(3)}</td>
                    <td class="py-3 px-4 text-right font-mono font-bold text-amber-400">${p.depth.toFixed(3)} ft</td>
                    <td class="py-3 px-4 text-slate-400 text-xs italic">${escapeHtml(p.notes || '--')}</td>
                    <td class="py-3 px-4 text-center no-print">
                        <div class="inline-flex items-center gap-1">
                            <button onclick="editPothole(${originalIndex})" class="p-1.5 hover:bg-slate-700 text-slate-400 hover:text-amber-400 rounded-lg transition" title="Edit">
                                <i data-lucide="edit-3" class="w-4 h-4"></i>
                            </button>
                            <button onclick="deletePothole(${originalIndex})" class="p-1.5 hover:bg-slate-700 text-slate-400 hover:text-rose-400 rounded-lg transition" title="Delete">
                                <i data-lucide="trash" class="w-4 h-4"></i>
                            </button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });

            lucide.createIcons();
        }

        function escapeHtml(str) {
            return String(str)
                .replace(/&/g, '&amp;')
                .replace(/</g, '&lt;')
                .replace(/>/g, '&gt;')
                .replace(/"/g, '&quot;');
        }

        // Export Modal Control
        function openExportModal() {
            document.getElementById('export-modal').classList.remove('hidden');
            document.getElementById('export-modal').classList.add('flex');
        }

        function closeExportModal() {
            document.getElementById('export-modal').classList.remove('flex');
            document.getElementById('export-modal').classList.add('hidden');
        }

        // Export to CSV format
        function exportCSV() {
            if (state.potholes.length === 0) {
                showToast("No data to export!", true);
                return;
            }

            let csvContent = "data:text/csv;charset=utf-8,";
            csvContent += `Foreman,${state.foremanName}\n`;
            csvContent += `Benchmark ID,${state.bmName}\n`;
            csvContent += `Benchmark Elev,${state.bmElev}\n`;
            csvContent += `Instrument HI,${state.hi}\n\n`;
            
            csvContent += "Point ID,Measurement Label,Ground Elevation (ft),Pipe Elevation (ft),Depth to Pipe (ft),Notes\n";

            state.potholes.forEach(p => {
                const row = [
                    `"${p.id}"`,
                    `"${p.label}"`,
                    p.groundElev.toFixed(3),
                    p.pipeElev.toFixed(3),
                    p.depth.toFixed(3),
                    `"${(p.notes || '').replace(/"/g, '""')}"`
                ];
                csvContent += row.join(",") + "\n";
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Pothole_Survey_${new Date().toISOString().slice(0,10)}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            closeExportModal();
            showToast("CSV exported successfully!");
        }

        // Trigger Print / PDF Report
        function triggerPrint() {
            document.getElementById('print-date').textContent = new Date().toLocaleDateString();
            document.getElementById('print-foreman').textContent = state.foremanName || 'Foreman';
            document.getElementById('print-bm').textContent = state.bmName || '--';
            document.getElementById('print-bmelev').textContent = Number(state.bmElev || 0).toFixed(3);
            document.getElementById('print-hi').textContent = Number(state.hi || 0).toFixed(3);

            closeExportModal();
            window.print();
        }
    </script>
</body>
</html>