<!DOCTYPE html>
<html lang="si">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grocery Shop POS & Inventory System - Sri Lanka</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Noto+Sans+Sinhala:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- JsBarcode -->
    <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>

    <style>
        body { font-family: 'Inter', 'Noto Sans Sinhala', sans-serif; }
        
        @media print {
            body * { visibility: hidden; }
            #printable-area, #printable-area * { visibility: visible; }
            #printable-area {
                position: absolute;
                left: 0;
                top: 0;
                width: 80mm;
                padding: 5px;
                font-family: monospace;
            }
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen flex flex-col font-sans">

    <!-- Top Bar -->
    <header class="bg-slate-900 text-white sticky top-0 z-30 shadow-md">
        <div class="max-w-7xl mx-auto px-4 py-2.5 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <div class="w-9 h-9 rounded-lg bg-emerald-500 flex items-center justify-center font-bold text-lg text-slate-900 shadow-md">
                    <i class="fa-solid fa-cart-shopping"></i>
                </div>
                <div>
                    <h1 class="text-base font-bold leading-tight">Applantics POS Pro <span class="text-xs bg-emerald-500/20 text-emerald-400 px-2 py-0.5 rounded border border-emerald-500/30">v3.0 Sri Lanka</span></h1>
                    <p class="text-[11px] text-slate-400">Grocery Shop Billing, Inventory & ERP System</p>
                </div>
            </div>

            <div class="flex items-center space-x-4 text-xs">
                <!-- Cash Drawer Status -->
                <div id="drawer-badge" class="bg-slate-800 px-3 py-1.5 rounded-lg border border-slate-700 flex items-center space-x-2">
                    <span class="w-2 h-2 rounded-full bg-rose-500 animate-pulse" id="drawer-status-dot"></span>
                    <span class="text-slate-300" id="drawer-status-text">Drawer Closed</span>
                </div>

                <!-- User Profile -->
                <div class="flex items-center space-x-2 bg-slate-800 px-3 py-1.5 rounded-lg border border-slate-700">
                    <i class="fa-solid fa-user-shield text-emerald-400"></i>
                    <span id="current-user-name" class="font-medium text-slate-200">Admin User</span>
                </div>
            </div>
        </div>
    </header>

    <!-- Navigation Tabs -->
    <nav class="bg-white border-b border-slate-200 shadow-sm sticky top-[53px] z-20">
        <div class="max-w-7xl mx-auto px-4 flex space-x-1 overflow-x-auto text-xs font-semibold py-1.5 scrollbar-thin">
            <button onclick="switchTab('dashboard')" id="tab-btn-dashboard" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 bg-emerald-600 text-white transition">
                <i class="fa-solid fa-chart-pie"></i><span>Dashboard</span>
            </button>
            <button onclick="switchTab('drawer')" id="tab-btn-drawer" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-cash-register"></i><span>Cash Drawer</span>
            </button>
            <button onclick="switchTab('products')" id="tab-btn-products" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-boxes-stacked"></i><span>Products</span>
            </button>
            <button onclick="switchTab('pos')" id="tab-btn-pos" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-cash-register"></i><span>POS Terminal</span>
            </button>
            <button onclick="switchTab('customers')" id="tab-btn-customers" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-users"></i><span>Customers / Credit</span>
            </button>
            <button onclick="switchTab('cheques')" id="tab-btn-cheques" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-money-check"></i><span>Cheques</span>
            </button>
            <button onclick="switchTab('banking')" id="tab-btn-banking" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-building-columns"></i><span>Bank Deposits</span>
            </button>
            <button onclick="switchTab('po')" id="tab-btn-po" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-file-invoice"></i><span>Purchase Orders</span>
            </button>
            <button onclick="switchTab('promotions')" id="tab-btn-promotions" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-tags"></i><span>Promotions</span>
            </button>
            <button onclick="switchTab('damaged')" id="tab-btn-damaged" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-trash-can"></i><span>Damaged Goods</span>
            </button>
            <button onclick="switchTab('reports')" id="tab-btn-reports" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-chart-column"></i><span>Reports & Profit</span>
            </button>
            <button onclick="switchTab('settings')" id="tab-btn-settings" class="px-3.5 py-2 rounded-lg flex items-center space-x-2 text-slate-600 hover:bg-slate-100 transition">
                <i class="fa-solid fa-gear"></i><span>Settings</span>
            </button>
        </div>
    </nav>

    <!-- Main Workspace -->
    <main class="max-w-7xl mx-auto px-4 py-5 flex-1 w-full">

        <!-- 1. DASHBOARD -->
        <section id="tab-dashboard" class="space-y-5">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex justify-between items-center">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Today Sales (අද අලෙවිය)</p>
                        <h3 class="text-xl font-bold text-slate-900 mt-1" id="dash-today-sales">LKR 0.00</h3>
                    </div>
                    <div class="p-3 bg-emerald-50 text-emerald-600 rounded-lg text-xl"><i class="fa-solid fa-sack-dollar"></i></div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex justify-between items-center">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Today Orders (බිල් ගණන)</p>
                        <h3 class="text-xl font-bold text-slate-900 mt-1" id="dash-today-orders">0</h3>
                    </div>
                    <div class="p-3 bg-blue-50 text-blue-600 rounded-lg text-xl"><i class="fa-solid fa-receipt"></i></div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex justify-between items-center">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Active Products (භාණ්ඩ)</p>
                        <h3 class="text-xl font-bold text-slate-900 mt-1" id="dash-product-count">0</h3>
                    </div>
                    <div class="p-3 bg-indigo-50 text-indigo-600 rounded-lg text-xl"><i class="fa-solid fa-box"></i></div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex justify-between items-center">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">Low Stock Alert (අඩු ස්ටොක්)</p>
                        <h3 class="text-xl font-bold text-rose-600 mt-1" id="dash-low-stock-count">0</h3>
                    </div>
                    <div class="p-3 bg-rose-50 text-rose-600 rounded-lg text-xl"><i class="fa-solid fa-triangle-exclamation"></i></div>
                </div>
            </div>

            <!-- Charts & Low Stock -->
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-5">
                <div class="lg:col-span-2 bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-sm font-semibold text-slate-900 mb-3 flex items-center space-x-2">
                        <i class="fa-solid fa-chart-area text-emerald-600"></i><span>Sales Performance Trend</span>
                    </h3>
                    <div class="h-64"><canvas id="dashboardSalesChart"></canvas></div>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-sm font-semibold text-slate-900 mb-3 text-rose-600 flex items-center space-x-2">
                        <i class="fa-solid fa-bell"></i><span>Low Stock Alerts</span>
                    </h3>
                    <div class="space-y-2 overflow-y-auto max-h-60" id="dash-low-stock-list"></div>
                </div>
            </div>
        </section>

        <!-- 2. CASH DRAWER -->
        <section id="tab-drawer" class="hidden space-y-5">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-5">
                <div class="bg-white p-5 rounded-xl border border-slate-200 shadow-sm space-y-4">
                    <h3 class="text-sm font-bold text-slate-900 border-b pb-2">Start / Open Drawer Shift</h3>
                    <div>
                        <label class="block text-xs text-slate-600 mb-1">Opening Float Balance (LKR)</label>
                        <input type="number" id="drawer-float-input" value="5000" class="w-full border rounded px-3 py-2 text-sm">
                    </div>
                    <button onclick="openDrawerSession()" class="w-full bg-emerald-600 text-white font-medium py-2 rounded text-xs hover:bg-emerald-700 shadow">
                        <i class="fa-solid fa-lock-open mr-1"></i> Open Cash Drawer Session
                    </button>
                    <button onclick="closeDrawerSession()" class="w-full bg-slate-800 text-white font-medium py-2 rounded text-xs hover:bg-slate-900 shadow mt-2">
                        <i class="fa-solid fa-lock mr-1"></i> Close Drawer & Reconciliation
                    </button>
                </div>

                <div class="md:col-span-2 bg-white p-5 rounded-xl border border-slate-200 shadow-sm space-y-3">
                    <h3 class="text-sm font-bold text-slate-900 border-b pb-2">Drawer Session Summary</h3>
                    <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 text-xs">
                        <div class="bg-slate-50 p-3 rounded border"><p class="text-slate-500">Opening Float</p><p class="font-bold text-sm" id="dr-float">LKR 0.00</p></div>
                        <div class="bg-slate-50 p-3 rounded border"><p class="text-slate-500">Cash Sales</p><p class="font-bold text-sm text-emerald-600" id="dr-cash-sales">LKR 0.00</p></div>
                        <div class="bg-slate-50 p-3 rounded border"><p class="text-slate-500">Card Sales</p><p class="font-bold text-sm text-blue-600" id="dr-card-sales">LKR 0.00</p></div>
                        <div class="bg-slate-50 p-3 rounded border"><p class="text-slate-500">Total Drawer Cash</p><p class="font-bold text-sm text-purple-600" id="dr-total-cash">LKR 0.00</p></div>
                    </div>
                </div>
            </div>
        </section>

        <!-- 3. PRODUCTS & INVENTORY -->
        <section id="tab-products" class="hidden space-y-5">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-5">
                <!-- Add Product Form -->
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                    <h3 class="text-xs font-bold text-slate-900 uppercase tracking-wider border-b pb-2">Add / Edit Product</h3>
                    <form id="product-form" onsubmit="saveProduct(event)" class="space-y-2 text-xs">
                        <input type="hidden" id="prod-id">
                        <div>
                            <label class="block text-slate-600 mb-0.5">Product Name</label>
                            <input type="text" id="prod-name" required placeholder="e.g. White Sugar" class="w-full border rounded px-2.5 py-1.5 text-xs">
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <label class="block text-slate-600 mb-0.5">Item / Barcode</label>
                                <input type="text" id="prod-code" required placeholder="BAR101" class="w-full border rounded px-2.5 py-1.5 text-xs">
                            </div>
                            <div>
                                <label class="block text-slate-600 mb-0.5">Unit Type</label>
                                <select id="prod-unit" class="w-full border rounded px-2.5 py-1.5 text-xs">
                                    <option value="PCS">Pieces (PCS)</option>
                                    <option value="KG">Kilograms (KG)</option>
                                    <option value="GRAMS">Grams (G)</option>
                                </select>
                            </div>
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <label class="block text-slate-600 mb-0.5">Cost Price (LKR)</label>
                                <input type="number" id="prod-cost" oninput="calcProfitMargin()" required class="w-full border rounded px-2.5 py-1.5 text-xs">
                            </div>
                            <div>
                                <label class="block text-slate-600 mb-0.5">Selling Price (LKR)</label>
                                <input type="number" id="prod-price" oninput="calcProfitMargin()" required class="w-full border rounded px-2.5 py-1.5 text-xs">
                            </div>
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <label class="block text-slate-600 mb-0.5">Profit Margin</label>
                                <input type="text" id="prod-margin" readonly class="w-full bg-slate-100 border rounded px-2.5 py-1.5 text-xs font-bold text-emerald-600">
                            </div>
                            <div>
                                <label class="block text-slate-600 mb-0.5">Stock Quantity</label>
                                <input type="number" id="prod-stock" required class="w-full border rounded px-2.5 py-1.5 text-xs">
                            </div>
                        </div>
                        <div>
                            <label class="block text-slate-600 mb-0.5">Expiry Date</label>
                            <input type="date" id="prod-expiry" class="w-full border rounded px-2.5 py-1.5 text-xs">
                        </div>
                        <button type="submit" class="w-full bg-emerald-600 text-white font-medium py-2 rounded text-xs hover:bg-emerald-700 shadow transition">Save Product</button>
                    </form>
                </div>

                <!-- Product Catalog Table -->
                <div class="md:col-span-2 bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                    <div class="flex justify-between items-center mb-3">
                        <h3 class="text-xs font-bold text-slate-900 uppercase tracking-wider">Product Inventory Catalog</h3>
                        <input type="text" id="prod-search" oninput="renderProducts()" placeholder="Search products..." class="border rounded px-2.5 py-1 text-xs w-48">
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs text-slate-700">
                            <thead class="bg-slate-50 uppercase text-[10px] text-slate-500 border-b">
                                <tr>
                                    <th class="p-2">Code</th>
                                    <th class="p-2">Name</th>
                                    <th class="p-2">Cost</th>
                                    <th class="p-2">Selling</th>
                                    <th class="p-2">Stock</th>
                                    <th class="p-2">Actions</th>
                                </tr>
                            </thead>
                            <tbody id="products-table-body"></tbody>
                        </table>
                    </div>
                </div>
            </div>
        </section>

        <!-- 4. POS TERMINAL -->
        <section id="tab-pos" class="hidden space-y-4">
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-5">
                <!-- Search & Product Quick Grid -->
                <div class="lg:col-span-2 space-y-3">
                    <div class="bg-white p-3 rounded-xl border border-slate-200 shadow-sm flex space-x-2">
                        <input type="text" id="pos-barcode-input" onkeypress="if(event.key==='Enter') handlePOSBarcodeScan()" placeholder="Scan Barcode or Type Code/Name & Press Enter..." class="w-full border rounded px-3 py-2 text-xs focus:outline-none focus:border-emerald-500">
                        <button onclick="handlePOSBarcodeScan()" class="bg-emerald-600 text-white px-4 py-2 rounded text-xs font-medium"><i class="fa-solid fa-magnifying-glass"></i></button>
                    </div>

                    <div class="grid grid-cols-2 sm:grid-cols-3 gap-3" id="pos-product-grid"></div>
                </div>

                <!-- Billing Cart Panel -->
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3 flex flex-col justify-between">
                    <div>
                        <div class="border-b pb-2 flex justify-between items-center">
                            <h3 class="text-xs font-bold text-slate-900 uppercase tracking-wider">Current Bill / Cart</h3>
                            <button onclick="clearCart()" class="text-[11px] text-rose-600 hover:underline">Clear</button>
                        </div>

                        <!-- Customer Selection -->
                        <div class="mt-2 space-y-1">
                            <label class="block text-[11px] text-slate-500">Customer (Loyalty / Credit)</label>
                            <select id="pos-customer-select" class="w-full border rounded px-2 py-1 text-xs">
                                <option value="">Walk-in Customer</option>
                            </select>
                        </div>

                        <!-- Cart Items List -->
                        <div class="mt-3 overflow-y-auto max-h-60 border-b pb-2 space-y-2 text-xs" id="cart-items-container">
                            <p class="text-slate-400 text-center py-6">Cart is empty</p>
                        </div>
                    </div>

                    <!-- Payment Summary & Split -->
                    <div class="space-y-2 pt-2 text-xs">
                        <div class="flex justify-between"><span>Subtotal:</span><span id="pos-subtotal" class="font-bold">LKR 0.00</span></div>
                        <div class="flex justify-between items-center">
                            <span>Discount (LKR):</span>
                            <input type="number" id="pos-discount" oninput="calculatePOSBill()" value="0" class="w-20 border rounded px-1.5 py-0.5 text-right text-xs">
                        </div>
                        <div class="flex justify-between text-sm font-bold text-slate-900 border-t pt-1">
                            <span>Total Payable:</span><span id="pos-total" class="text-emerald-600">LKR 0.00</span>
                        </div>

                        <!-- Payment Method Split -->
                        <div class="bg-slate-50 p-2 rounded border space-y-1 text-[11px]">
                            <p class="font-semibold text-slate-700">Split Payment Mode</p>
                            <div class="grid grid-cols-2 gap-2">
                                <div><label>Cash Amount</label><input type="number" id="pos-pay-cash" oninput="calculatePOSBill()" value="0" class="w-full border rounded px-1.5 py-0.5"></div>
                                <div><label>Card Amount</label><input type="number" id="pos-pay-card" oninput="calculatePOSBill()" value="0" class="w-full border rounded px-1.5 py-0.5"></div>
                            </div>
                            <div class="flex justify-between font-bold text-slate-700 pt-1">
                                <span>Change Due:</span><span id="pos-change">LKR 0.00</span>
                            </div>
                        </div>

                        <button onclick="processPOSCheckout()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2.5 rounded shadow text-xs transition">
                            <i class="fa-solid fa-print mr-1"></i> Complete & Print Thermal Receipt
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 5. CUSTOMERS & CREDIT -->
        <section id="tab-customers" class="hidden space-y-5">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-5">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                    <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Add Customer</h3>
                    <form onsubmit="saveCustomer(event)" class="space-y-2 text-xs">
                        <div><label>Customer Name</label><input type="text" id="cust-name" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>NIC Number</label><input type="text" id="cust-nic" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>Phone</label><input type="tel" id="cust-phone" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>Credit Limit (LKR)</label><input type="number" id="cust-limit" value="100000" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <button type="submit" class="w-full bg-emerald-600 text-white font-medium py-2 rounded text-xs">Save Customer</button>
                    </form>
                </div>
                <div class="md:col-span-2 bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-xs font-bold text-slate-900 uppercase mb-3">Customer Accounts & Credit Balances</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs text-slate-700">
                            <thead class="bg-slate-50 uppercase text-[10px] border-b">
                                <tr>
                                    <th class="p-2">Name</th>
                                    <th class="p-2">NIC / Phone</th>
                                    <th class="p-2">Credit Limit</th>
                                    <th class="p-2">Used Balance</th>
                                    <th class="p-2">Loyalty Points</th>
                                </tr>
                            </thead>
                            <tbody id="customer-table-body"></tbody>
                        </table>
                    </div>
                </div>
            </div>
        </section>

        <!-- 6. CHEQUES -->
        <section id="tab-cheques" class="hidden space-y-5">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-5">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                    <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Record Cheque</h3>
                    <form onsubmit="saveCheque(event)" class="space-y-2 text-xs">
                        <div><label>Cheque Number</label><input type="text" id="chq-number" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>Bank Name</label><input type="text" id="chq-bank" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>Amount (LKR)</label><input type="number" id="chq-amount" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <div><label>Realization Date</label><input type="date" id="chq-date" required class="w-full border rounded px-2.5 py-1.5"></div>
                        <button type="submit" class="w-full bg-emerald-600 text-white font-medium py-2 rounded text-xs">Save Cheque</button>
                    </form>
                </div>
                <div class="md:col-span-2 bg-white p-4 rounded-xl border border-slate-200 shadow-sm">
                    <h3 class="text-xs font-bold text-slate-900 uppercase mb-3">Cheque Ledger</h3>
                    <table class="w-full text-left text-xs border-collapse">
                        <thead class="bg-slate-50 border-b text-[10px] uppercase">
                            <tr><th class="p-2">Chq No</th><th class="p-2">Bank</th><th class="p-2">Amount</th><th class="p-2">Date</th><th class="p-2">Status</th></tr>
                        </thead>
                        <tbody id="cheque-table-body"></tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 7. BANK DEPOSITS -->
        <section id="tab-banking" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                <div class="flex justify-between items-center border-b pb-2">
                    <h3 class="text-xs font-bold text-slate-900 uppercase">Bank Deposits Ledger</h3>
                    <button onclick="exportCSV('banking')" class="bg-slate-800 text-white text-xs px-3 py-1 rounded">Export CSV</button>
                </div>
                <form onsubmit="saveBankDeposit(event)" class="grid grid-cols-1 sm:grid-cols-4 gap-2 text-xs">
                    <input type="text" id="bank-acc" placeholder="Account Name / No" required class="border rounded px-2 py-1">
                    <input type="number" id="bank-amt" placeholder="Deposit Amount (LKR)" required class="border rounded px-2 py-1">
                    <input type="text" id="bank-ref" placeholder="Reference No" required class="border rounded px-2 py-1">
                    <button type="submit" class="bg-emerald-600 text-white rounded font-medium">Record Deposit</button>
                </form>
                <table class="w-full text-left text-xs border-collapse mt-3">
                    <thead class="bg-slate-50 border-b text-[10px] uppercase">
                        <tr><th class="p-2">Date</th><th class="p-2">Account</th><th class="p-2">Ref No</th><th class="p-2">Amount</th></tr>
                    </thead>
                    <tbody id="bank-table-body"></tbody>
                </table>
            </div>
        </section>

        <!-- 8. PURCHASE ORDERS -->
        <section id="tab-po" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Create Purchase Order (PO)</h3>
                <form onsubmit="savePO(event)" class="grid grid-cols-1 sm:grid-cols-4 gap-2 text-xs">
                    <input type="text" id="po-supplier" placeholder="Supplier Name" required class="border rounded px-2 py-1">
                    <input type="text" id="po-item" placeholder="Item Name / Code" required class="border rounded px-2 py-1">
                    <input type="number" id="po-qty" placeholder="Quantity" required class="border rounded px-2 py-1">
                    <button type="submit" class="bg-indigo-600 text-white rounded font-medium">Create PO</button>
                </form>
                <table class="w-full text-left text-xs border-collapse mt-3">
                    <thead class="bg-slate-50 border-b text-[10px] uppercase">
                        <tr><th class="p-2">PO ID</th><th class="p-2">Supplier</th><th class="p-2">Item</th><th class="p-2">Qty</th><th class="p-2">Status</th></tr>
                    </thead>
                    <tbody id="po-table-body"></tbody>
                </table>
            </div>
        </section>

        <!-- 9. PROMOTIONS -->
        <section id="tab-promotions" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Promotions & Discounts</h3>
                <form onsubmit="savePromotion(event)" class="grid grid-cols-1 sm:grid-cols-4 gap-2 text-xs">
                    <input type="text" id="promo-name" placeholder="Promo Title" required class="border rounded px-2 py-1">
                    <input type="number" id="promo-min" placeholder="Min Purchase (LKR)" required class="border rounded px-2 py-1">
                    <input type="number" id="promo-disc" placeholder="Discount (LKR)" required class="border rounded px-2 py-1">
                    <button type="submit" class="bg-purple-600 text-white rounded font-medium">Add Promo</button>
                </form>
                <table class="w-full text-left text-xs border-collapse mt-3">
                    <thead class="bg-slate-50 border-b text-[10px] uppercase">
                        <tr><th class="p-2">Title</th><th class="p-2">Min Purchase</th><th class="p-2">Discount</th></tr>
                    </thead>
                    <tbody id="promo-table-body"></tbody>
                </table>
            </div>
        </section>

        <!-- 10. DAMAGED GOODS -->
        <section id="tab-damaged" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Record Damaged / Expired Inventory</h3>
                <form onsubmit="saveDamaged(event)" class="grid grid-cols-1 sm:grid-cols-4 gap-2 text-xs">
                    <input type="text" id="dmg-item" placeholder="Product Code / Name" required class="border rounded px-2 py-1">
                    <input type="number" id="dmg-qty" placeholder="Qty Damaged" required class="border rounded px-2 py-1">
                    <input type="text" id="dmg-reason" placeholder="Reason (Expired/Broken)" required class="border rounded px-2 py-1">
                    <button type="submit" class="bg-rose-600 text-white rounded font-medium">Record Loss</button>
                </form>
                <table class="w-full text-left text-xs border-collapse mt-3">
                    <thead class="bg-slate-50 border-b text-[10px] uppercase">
                        <tr><th class="p-2">Item</th><th class="p-2">Qty</th><th class="p-2">Reason</th><th class="p-2">Date</th></tr>
                    </thead>
                    <tbody id="damaged-table-body"></tbody>
                </table>
            </div>
        </section>

        <!-- 11. REPORTS & PROFIT -->
        <section id="tab-reports" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3">
                <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">Profit & Loss Summary</h3>
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 text-xs">
                    <div class="bg-emerald-50 p-3 rounded border border-emerald-200"><p class="text-slate-600">Total Gross Revenue</p><p class="text-base font-bold text-emerald-600" id="rep-revenue">LKR 0.00</p></div>
                    <div class="bg-rose-50 p-3 rounded border border-rose-200"><p class="text-slate-600">Estimated Cost of Goods</p><p class="text-base font-bold text-rose-600" id="rep-cogs">LKR 0.00</p></div>
                    <div class="bg-blue-50 p-3 rounded border border-blue-200"><p class="text-slate-600">Estimated Net Profit</p><p class="text-base font-bold text-blue-600" id="rep-profit">LKR 0.00</p></div>
                </div>
            </div>
        </section>

        <!-- 12. SETTINGS -->
        <section id="tab-settings" class="hidden space-y-5">
            <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm space-y-3 text-xs">
                <h3 class="text-xs font-bold text-slate-900 uppercase border-b pb-2">System Preferences & Roles</h3>
                <div>
                    <label class="block font-medium mb-1">Store Name</label>
                    <input type="text" id="set-store-name" value="Applantics Super Grocery" class="w-64 border rounded px-2 py-1">
                </div>
                <div>
                    <label class="block font-medium mb-1">Receipt Footer Note</label>
                    <input type="text" id="set-footer" value="Thank you for shopping with us!" class="w-64 border rounded px-2 py-1">
                </div>
            </div>
        </section>

    </main>

    <!-- PRINT MODAL: THERMAL RECEIPT -->
    <div id="receipt-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white text-slate-900 w-full max-w-xs rounded-xl p-5 shadow-2xl space-y-3">
            <div id="printable-area" class="text-xs font-mono space-y-2 border-b pb-3">
                <div class="text-center">
                    <h2 class="font-bold text-sm uppercase text-black" id="rec-store-title">Applantics Grocery</h2>
                    <p class="text-[10px] text-slate-600">Main Street, Panadura, Sri Lanka</p>
                    <p class="text-[10px] text-slate-600">Tel: 038-2234567</p>
                    <p class="mt-1 border-b border-dashed border-black"></p>
                    <p class="font-bold text-black mt-1">CASH RECEIPT</p>
                    <p class="text-[10px]" id="rec-bill-id">#ORD-000</p>
                    <p class="text-[10px]" id="rec-date">2026-10-06</p>
                    <p class="border-b border-dashed border-black"></p>
                </div>
                <div id="rec-items-list" class="space-y-1 text-[11px]"></div>
                <p class="border-b border-dashed border-black"></p>
                <div class="space-y-0.5 text-[11px]">
                    <div class="flex justify-between font-bold"><span>Total:</span><span id="rec-total-val">LKR 0.00</span></div>
                    <div class="flex justify-between text-slate-600"><span>Paid Cash:</span><span id="rec-cash-val">LKR 0.00</span></div>
                    <div class="flex justify-between text-slate-600"><span>Paid Card:</span><span id="rec-card-val">LKR 0.00</span></div>
                    <div class="flex justify-between text-slate-600"><span>Change:</span><span id="rec-change-val">LKR 0.00</span></div>
                </div>
                <div class="text-center text-[10px] pt-2 text-slate-500">
                    <p id="rec-footer-text">Thank you! Come again!</p>
                </div>
            </div>
            <div class="flex space-x-2">
                <button onclick="window.print()" class="flex-1 bg-emerald-600 text-white py-1.5 rounded text-xs font-semibold">Print</button>
                <button onclick="closeReceiptModal()" class="bg-slate-200 text-slate-700 px-3 py-1.5 rounded text-xs">Close</button>
            </div>
        </div>
    </div>

    <!-- PRINT MODAL: BARCODE -->
    <div id="barcode-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xs z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-xs rounded-xl p-5 shadow-2xl space-y-3 text-center">
            <h3 class="text-xs font-bold uppercase">Barcode Label Preview</h3>
            <svg id="barcode-svg" class="mx-auto"></svg>
            <div class="flex space-x-2">
                <button onclick="window.print()" class="flex-1 bg-blue-600 text-white py-1.5 rounded text-xs font-semibold">Print Label</button>
                <button onclick="closeBarcodeModal()" class="bg-slate-200 text-slate-700 px-3 py-1.5 rounded text-xs">Close</button>
            </div>
        </div>
    </div>

    <!-- CORE JAVASCRIPT SYSTEM -->
    <script>
        // APP INITIAL STATE
        let state = {
            products: JSON.parse(localStorage.getItem('pos_products')) || [
                { id: '1', code: 'BAR101', name: 'White Sugar', unit: 'KG', cost: 240, price: 280, stock: 50, expiry: '' },
                { id: '2', code: 'BAR102', name: 'Samba Rice', unit: 'KG', cost: 210, price: 240, stock: 100, expiry: '' },
                { id: '3', code: 'BAR103', name: 'Anchor Milk Powder 400g', unit: 'PCS', cost: 1020, price: 1150, stock: 25, expiry: '2027-05-10' }
            ],
            customers: JSON.parse(localStorage.getItem('pos_customers')) || [
                { id: '1', name: 'Sunil Perera', nic: '198512345678', phone: '0771234567', limit: 50000, balance: 0, points: 120 }
            ],
            orders: JSON.parse(localStorage.getItem('pos_orders')) || [],
            drawer: JSON.parse(localStorage.getItem('pos_drawer')) || { isOpen: false, floatAmount: 0, cashSales: 0, cardSales: 0 },
            cheques: JSON.parse(localStorage.getItem('pos_cheques')) || [],
            banking: JSON.parse(localStorage.getItem('pos_banking')) || [],
            po: JSON.parse(localStorage.getItem('pos_po')) || [],
            promotions: JSON.parse(localStorage.getItem('pos_promotions')) || [],
            damaged: JSON.parse(localStorage.getItem('pos_damaged')) || []
        };

        let cart = [];
        let dashboardSalesChart = null;

        function saveState() {
            localStorage.setItem('pos_products', JSON.stringify(state.products));
            localStorage.setItem('pos_customers', JSON.stringify(state.customers));
            localStorage.setItem('pos_orders', JSON.stringify(state.orders));
            localStorage.setItem('pos_drawer', JSON.stringify(state.drawer));
            localStorage.setItem('pos_cheques', JSON.stringify(state.cheques));
            localStorage.setItem('pos_banking', JSON.stringify(state.banking));
            localStorage.setItem('pos_po', JSON.stringify(state.po));
            localStorage.setItem('pos_promotions', JSON.stringify(state.promotions));
            localStorage.setItem('pos_damaged', JSON.stringify(state.damaged));
            renderAll();
        }

        function switchTab(tab) {
            ['dashboard','drawer','products','pos','customers','cheques','banking','po','promotions','damaged','reports','settings'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                document.getElementById(`tab-btn-${t}`).classList.remove('bg-emerald-600','text-white');
                document.getElementById(`tab-btn-${t}`).classList.add('text-slate-600');
            });
            document.getElementById(`tab-${tab}`).classList.remove('hidden');
            document.getElementById(`tab-btn-${tab}`).classList.add('bg-emerald-600','text-white');
        }

        /* 1. DASHBOARD */
        function renderDashboard() {
            const today = new Date().toLocaleDateString();
            const todayOrders = state.orders.filter(o => o.date.startsWith(today));
            const todaySales = todayOrders.reduce((sum, o) => sum + o.total, 0);

            document.getElementById('dash-today-sales').innerText = `LKR ${todaySales.toFixed(2)}`;
            document.getElementById('dash-today-orders').innerText = todayOrders.length;
            document.getElementById('dash-product-count').innerText = state.products.length;

            const lowStock = state.products.filter(p => p.stock < 10);
            document.getElementById('dash-low-stock-count').innerText = lowStock.length;

            const list = document.getElementById('dash-low-stock-list');
            list.innerHTML = lowStock.map(p => `
                <div class="p-2 bg-rose-50 border border-rose-200 rounded text-xs flex justify-between">
                    <span>${p.name}</span><span class="font-bold text-rose-600">${p.stock} ${p.unit} left</span>
                </div>
            `).join('') || '<p class="text-xs text-slate-400">All products adequately stocked.</p>';

            updateSalesChart();
        }

        function updateSalesChart() {
            const ctx = document.getElementById('dashboardSalesChart').getContext('2d');
            if (dashboardSalesChart) dashboardSalesChart.destroy();
            dashboardSalesChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
                    datasets: [{
                        label: 'Sales (LKR)',
                        data: [12000, 19000, 15000, 25000, 22000, 30000, state.orders.reduce((s,o)=>s+o.total,0)],
                        borderColor: '#059669',
                        tension: 0.3
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });
        }

        /* 2. CASH DRAWER */
        function openDrawerSession() {
            const val = parseFloat(document.getElementById('drawer-float-input').value) || 0;
            state.drawer = { isOpen: true, floatAmount: val, cashSales: 0, cardSales: 0 };
            saveState();
        }

        function closeDrawerSession() {
            alert(`Shift Closed!\nExpected Cash: LKR ${(state.drawer.floatAmount + state.drawer.cashSales).toFixed(2)}`);
            state.drawer.isOpen = false;
            saveState();
        }

        function renderDrawer() {
            const statusDot = document.getElementById('drawer-status-dot');
            const statusText = document.getElementById('drawer-status-text');
            if (state.drawer.isOpen) {
                statusDot.className = "w-2 h-2 rounded-full bg-emerald-500 animate-pulse";
                statusText.innerText = "Drawer Open";
            } else {
                statusDot.className = "w-2 h-2 rounded-full bg-rose-500 animate-pulse";
                statusText.innerText = "Drawer Closed";
            }

            document.getElementById('dr-float').innerText = `LKR ${state.drawer.floatAmount.toFixed(2)}`;
            document.getElementById('dr-cash-sales').innerText = `LKR ${state.drawer.cashSales.toFixed(2)}`;
            document.getElementById('dr-card-sales').innerText = `LKR ${state.drawer.cardSales.toFixed(2)}`;
            document.getElementById('dr-total-cash').innerText = `LKR ${(state.drawer.floatAmount + state.drawer.cashSales).toFixed(2)}`;
        }

        /* 3. PRODUCTS */
        function calcProfitMargin() {
            const cost = parseFloat(document.getElementById('prod-cost').value) || 0;
            const price = parseFloat(document.getElementById('prod-price').value) || 0;
            if (cost > 0 && price > 0) {
                const margin = ((price - cost) / price) * 100;
                document.getElementById('prod-margin').value = `${margin.toFixed(1)}%`;
            }
        }

        function saveProduct(e) {
            e.preventDefault();
            const id = document.getElementById('prod-id').value || Date.now().toString();
            const prod = {
                id: id,
                name: document.getElementById('prod-name').value,
                code: document.getElementById('prod-code').value,
                unit: document.getElementById('prod-unit').value,
                cost: parseFloat(document.getElementById('prod-cost').value),
                price: parseFloat(document.getElementById('prod-price').value),
                stock: parseFloat(document.getElementById('prod-stock').value),
                expiry: document.getElementById('prod-expiry').value
            };

            const idx = state.products.findIndex(p => p.id === id);
            if (idx > -1) state.products[idx] = prod;
            else state.products.push(prod);

            document.getElementById('product-form').reset();
            saveState();
        }

        function renderProducts() {
            const tbody = document.getElementById('products-table-body');
            const search = document.getElementById('prod-search').value.toLowerCase();
            tbody.innerHTML = state.products.filter(p => p.name.toLowerCase().includes(search) || p.code.toLowerCase().includes(search)).map(p => `
                <tr class="border-b hover:bg-slate-50">
                    <td class="p-2 font-mono text-emerald-600">${p.code}</td>
                    <td class="p-2 font-semibold">${p.name}</td>
                    <td class="p-2">LKR ${p.cost.toFixed(2)}</td>
                    <td class="p-2 font-bold text-slate-900">LKR ${p.price.toFixed(2)}</td>
                    <td class="p-2"><span class="px-2 py-0.5 rounded text-[10px] font-bold ${p.stock < 10 ? 'bg-rose-100 text-rose-700' : 'bg-emerald-100 text-emerald-700'}">${p.stock} ${p.unit}</span></td>
                    <td class="p-2 space-x-1">
                        <button onclick="showBarcode('${p.code}')" class="text-blue-600 hover:underline"><i class="fa-solid fa-barcode"></i></button>
                    </td>
                </tr>
            `).join('');
        }

        function showBarcode(code) {
            JsBarcode("#barcode-svg", code, { format: "CODE128", width: 1.5, height: 40 });
            document.getElementById('barcode-modal').classList.remove('hidden');
        }
        function closeBarcodeModal() { document.getElementById('barcode-modal').classList.add('hidden'); }

        /* 4. POS TERMINAL */
        function renderPOS() {
            const grid = document.getElementById('pos-product-grid');
            grid.innerHTML = state.products.map(p => `
                <div onclick="addToCart('${p.id}')" class="bg-white p-3 rounded-xl border border-slate-200 hover:border-emerald-500 cursor-pointer transition shadow-xs">
                    <p class="font-bold text-slate-900 text-xs">${p.name}</p>
                    <p class="text-[10px] text-slate-500">${p.code} | Stock: ${p.stock} ${p.unit}</p>
                    <p class="text-xs font-bold text-emerald-600 mt-1">LKR ${p.price.toFixed(2)}</p>
                </div>
            `).join('');

            const select = document.getElementById('pos-customer-select');
            select.innerHTML = '<option value="">Walk-in Customer</option>' + state.customers.map(c => `<option value="${c.id}">${c.name} (${c.phone})</option>`).join('');
        }

        function handlePOSBarcodeScan() {
            const val = document.getElementById('pos-barcode-input').value.trim();
            const prod = state.products.find(p => p.code.toLowerCase() === val.toLowerCase() || p.name.toLowerCase().includes(val.toLowerCase()));
            if (prod) {
                addToCart(prod.id);
                document.getElementById('pos-barcode-input').value = '';
            } else alert('Product not found!');
        }

        function addToCart(prodId) {
            const prod = state.products.find(p => p.id === prodId);
            const item = cart.find(c => c.id === prodId);
            if (item) {
                item.qty += 1;
            } else {
                cart.push({ id: prod.id, name: prod.name, price: prod.price, qty: 1, unit: prod.unit });
            }
            renderCart();
        }

        function updateCartQty(id, qty) {
            const item = cart.find(c => c.id === id);
            if (item) {
                item.qty = parseFloat(qty) || 1;
                renderCart();
            }
        }

        function removeFromCart(id) {
            cart = cart.filter(c => c.id !== id);
            renderCart();
        }

        function clearCart() { cart = []; renderCart(); }

        function renderCart() {
            const container = document.getElementById('cart-items-container');
            if (cart.length === 0) {
                container.innerHTML = '<p class="text-slate-400 text-center py-6">Cart is empty</p>';
            } else {
                container.innerHTML = cart.map(c => `
                    <div class="flex justify-between items-center bg-slate-50 p-2 rounded">
                        <div>
                            <p class="font-semibold text-slate-900 text-xs">${c.name}</p>
                            <p class="text-[10px] text-slate-500">LKR ${c.price.toFixed(2)} / ${c.unit}</p>
                        </div>
                        <div class="flex items-center space-x-1">
                            <input type="number" step="0.1" value="${c.qty}" onchange="updateCartQty('${c.id}', this.value)" class="w-12 border rounded px-1 py-0.5 text-center text-xs">
                            <button onclick="removeFromCart('${c.id}')" class="text-rose-600"><i class="fa-solid fa-xmark"></i></button>
                        </div>
                    </div>
                `).join('');
            }
            calculatePOSBill();
        }

        function calculatePOSBill() {
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const discount = parseFloat(document.getElementById('pos-discount').value) || 0;
            const total = Math.max(0, subtotal - discount);

            const cashPay = parseFloat(document.getElementById('pos-pay-cash').value) || 0;
            const cardPay = parseFloat(document.getElementById('pos-pay-card').value) || 0;
            const change = Math.max(0, (cashPay + cardPay) - total);

            document.getElementById('pos-subtotal').innerText = `LKR ${subtotal.toFixed(2)}`;
            document.getElementById('pos-total').innerText = `LKR ${total.toFixed(2)}`;
            document.getElementById('pos-change').innerText = `LKR ${change.toFixed(2)}`;
        }

        function processPOSCheckout() {
            if (cart.length === 0) return alert('Cart is empty!');
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.qty), 0);
            const discount = parseFloat(document.getElementById('pos-discount').value) || 0;
            const total = Math.max(0, subtotal - discount);
            const cashPay = parseFloat(document.getElementById('pos-pay-cash').value) || 0;
            const cardPay = parseFloat(document.getElementById('pos-pay-card').value) || 0;

            // Deduct stock
            cart.forEach(c => {
                const prod = state.products.find(p => p.id === c.id);
                if (prod) prod.stock = Math.max(0, prod.stock - c.qty);
            });

            // Update Drawer
            state.drawer.cashSales += cashPay;
            state.drawer.cardSales += cardPay;

            const order = {
                id: 'ORD-' + Math.floor(1000 + Math.random() * 9000),
                date: new Date().toLocaleString(),
                items: [...cart],
                subtotal: subtotal,
                discount: discount,
                total: total,
                cashPay: cashPay,
                cardPay: cardPay
            };

            state.orders.unshift(order);
            saveState();

            showThermalReceipt(order);
            clearCart();
        }

        function showThermalReceipt(order) {
            document.getElementById('rec-bill-id').innerText = '#' + order.id;
            document.getElementById('rec-date').innerText = order.date;
            document.getElementById('rec-total-val').innerText = `LKR ${order.total.toFixed(2)}`;
            document.getElementById('rec-cash-val').innerText = `LKR ${order.cashPay.toFixed(2)}`;
            document.getElementById('rec-card-val').innerText = `LKR ${order.cardPay.toFixed(2)}`;
            document.getElementById('rec-change-val').innerText = `LKR ${Math.max(0, (order.cashPay + order.cardPay) - order.total).toFixed(2)}`;

            document.getElementById('rec-items-list').innerHTML = order.items.map(i => `
                <div class="flex justify-between">
                    <span>${i.name} x${i.qty}</span>
                    <span>LKR ${(i.price * i.qty).toFixed(2)}</span>
                </div>
            `).join('');

            document.getElementById('receipt-modal').classList.remove('hidden');
        }
        function closeReceiptModal() { document.getElementById('receipt-modal').classList.add('hidden'); }

        /* 5. CUSTOMERS */
        function saveCustomer(e) {
            e.preventDefault();
            state.customers.push({
                id: Date.now().toString(),
                name: document.getElementById('cust-name').value,
                nic: document.getElementById('cust-nic').value,
                phone: document.getElementById('cust-phone').value,
                limit: parseFloat(document.getElementById('cust-limit').value),
                balance: 0,
                points: 0
            });
            saveState();
        }
        function renderCustomers() {
            document.getElementById('customer-table-body').innerHTML = state.customers.map(c => `
                <tr class="border-b hover:bg-slate-50">
                    <td class="p-2 font-bold">${c.name}</td>
                    <td class="p-2">${c.nic} / ${c.phone}</td>
                    <td class="p-2">LKR ${c.limit.toFixed(2)}</td>
                    <td class="p-2 text-rose-600 font-bold">LKR ${c.balance.toFixed(2)}</td>
                    <td class="p-2 text-purple-600 font-bold">${c.points} pts</td>
                </tr>
            `).join('');
        }

        /* 6. CHEQUES */
        function saveCheque(e) {
            e.preventDefault();
            state.cheques.push({
                number: document.getElementById('chq-number').value,
                bank: document.getElementById('chq-bank').value,
                amount: parseFloat(document.getElementById('chq-amount').value),
                date: document.getElementById('chq-date').value,
                status: 'PENDING'
            });
            saveState();
        }
        function renderCheques() {
            document.getElementById('cheque-table-body').innerHTML = state.cheques.map(c => `
                <tr class="border-b">
                    <td class="p-2 font-mono">${c.number}</td>
                    <td class="p-2">${c.bank}</td>
                    <td class="p-2 font-bold">LKR ${c.amount.toFixed(2)}</td>
                    <td class="p-2">${c.date}</td>
                    <td class="p-2"><span class="bg-amber-100 text-amber-700 px-2 py-0.5 rounded text-[10px] font-bold">${c.status}</span></td>
                </tr>
            `).join('');
        }

        /* 7. BANKING */
        function saveBankDeposit(e) {
            e.preventDefault();
            state.banking.push({
                date: new Date().toLocaleDateString(),
                account: document.getElementById('bank-acc').value,
                ref: document.getElementById('bank-ref').value,
                amount: parseFloat(document.getElementById('bank-amt').value)
            });
            saveState();
        }
        function renderBanking() {
            document.getElementById('bank-table-body').innerHTML = state.banking.map(b => `
                <tr class="border-b">
                    <td class="p-2">${b.date}</td>
                    <td class="p-2 font-bold">${b.account}</td>
                    <td class="p-2 font-mono">${b.ref}</td>
                    <td class="p-2 text-emerald-600 font-bold">LKR ${b.amount.toFixed(2)}</td>
                </tr>
            `).join('');
        }

        /* 8. PURCHASE ORDERS */
        function savePO(e) {
            e.preventDefault();
            state.po.push({
                id: 'PO-' + Math.floor(100 + Math.random() * 900),
                supplier: document.getElementById('po-supplier').value,
                item: document.getElementById('po-item').value,
                qty: document.getElementById('po-qty').value,
                status: 'PLACED'
            });
            saveState();
        }
        function renderPO() {
            document.getElementById('po-table-body').innerHTML = state.po.map(p => `
                <tr class="border-b">
                    <td class="p-2 font-mono text-indigo-600">${p.id}</td>
                    <td class="p-2 font-bold">${p.supplier}</td>
                    <td class="p-2">${p.item}</td>
                    <td class="p-2">${p.qty}</td>
                    <td class="p-2"><span class="bg-blue-100 text-blue-700 px-2 py-0.5 rounded text-[10px] font-bold">${p.status}</span></td>
                </tr>
            `).join('');
        }

        /* 9. PROMOTIONS */
        function savePromotion(e) {
            e.preventDefault();
            state.promotions.push({
                title: document.getElementById('promo-name').value,
                min: parseFloat(document.getElementById('promo-min').value),
                discount: parseFloat(document.getElementById('promo-disc').value)
            });
            saveState();
        }
        function renderPromotions() {
            document.getElementById('promo-table-body').innerHTML = state.promotions.map(p => `
                <tr class="border-b">
                    <td class="p-2 font-bold">${p.title}</td>
                    <td class="p-2">LKR ${p.min.toFixed(2)}</td>
                    <td class="p-2 text-emerald-600 font-bold">LKR ${p.discount.toFixed(2)}</td>
                </tr>
            `).join('');
        }

        /* 10. DAMAGED GOODS */
        function saveDamaged(e) {
            e.preventDefault();
            state.damaged.push({
                item: document.getElementById('dmg-item').value,
                qty: document.getElementById('dmg-qty').value,
                reason: document.getElementById('dmg-reason').value,
                date: new Date().toLocaleDateString()
            });
            saveState();
        }
        function renderDamaged() {
            document.getElementById('damaged-table-body').innerHTML = state.damaged.map(d => `
                <tr class="border-b">
                    <td class="p-2 font-bold">${d.item}</td>
                    <td class="p-2">${d.qty}</td>
                    <td class="p-2 text-rose-600">${d.reason}</td>
                    <td class="p-2">${d.date}</td>
                </tr>
            `).join('');
        }

        /* 11. REPORTS */
        function renderReports() {
            const revenue = state.orders.reduce((sum, o) => sum + o.total, 0);
            const cogs = revenue * 0.8; // Estimated 80% COGS
            const profit = revenue - cogs;

            document.getElementById('rep-revenue').innerText = `LKR ${revenue.toFixed(2)}`;
            document.getElementById('rep-cogs').innerText = `LKR ${cogs.toFixed(2)}`;
            document.getElementById('rep-profit').innerText = `LKR ${profit.toFixed(2)}`;
        }

        function exportCSV(type) {
            alert(`Exporting ${type} report to CSV file...`);
        }

        /* RENDER ALL COMPONENTS */
        function renderAll() {
            renderDashboard();
            renderDrawer();
            renderProducts();
            renderPOS();
            renderCustomers();
            renderCheques();
            renderBanking();
            renderPO();
            renderPromotions();
            renderDamaged();
            renderReports();
        }

        window.onload = function() {
            renderAll();
        };
    </script>
</body>
</html>
