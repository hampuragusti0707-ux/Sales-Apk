<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DistriSales 58mm — Aplikasi Kanvas & Door-to-Door</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://unpkg.com/html5-qrcode@2.3.8/html5-qrcode.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace']
          }
        }
      }
    }
  </script>
  <style>
    body {
      font-family: 'Inter', sans-serif;
      background-color: #f8fafc;
    }
    .receipt-paper {
      font-family: 'JetBrains Mono', monospace;
      width: 290px;
      margin: 0 auto;
      background: #ffffff;
      color: #111827;
      font-size: 11px;
      line-height: 1.35;
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
      position: relative;
    }
    .receipt-paper::before, .receipt-paper::after {
      content: "";
      display: block;
      height: 6px;
      background: radial-gradient(circle, transparent 4px, #ffffff 4px);
      background-size: 12px 12px;
      background-position: 0 -6px;
    }
    @media print {
      @page {
        size: 58mm auto;
        margin: 0mm;
      }
      body * {
        visibility: hidden;
      }
      #printableArea, #printableArea * {
        visibility: visible;
      }
      #printableArea {
        position: absolute;
        left: 0;
        top: 0;
        width: 58mm !important;
        max-width: 58mm !important;
        box-shadow: none !important;
        padding: 2mm !important;
        margin: 0 !important;
        font-size: 10px !important;
      }
    }
  </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col">

  <!-- Top Header -->
  <header class="bg-indigo-900 text-white shadow-md sticky top-0 z-30">
    <div class="max-w-7xl mx-auto px-4 py-3 flex flex-wrap items-center justify-between gap-3">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-indigo-500 flex items-center justify-center font-bold text-lg shadow-inner">
          DS
        </div>
        <div>
          <div class="flex items-center gap-2">
            <h1 class="font-bold text-base sm:text-lg tracking-tight">DistriSales Kanvas</h1>
            <span class="bg-indigo-700 text-indigo-100 text-xs px-2 py-0.5 rounded-full font-medium">58mm POS</span>
          </div>
          <div class="flex flex-wrap items-center gap-2">
            <p class="text-xs text-indigo-200" id="headerSalesInfo">Sales: Andi Pratama (Mobil Kanvas #04 - Jawa Barat)</p>
            <button id="btnEditProfileHeader" type="button" class="text-[11px] bg-indigo-800 hover:bg-indigo-700 text-amber-300 font-semibold px-2 py-0.5 rounded transition">
              ✎ Edit Profil / Header Struk
            </button>
          </div>
        </div>
      </div>

      <!-- Printer Status & Action (Direct Iware 58mm Bluetooth & USB) -->
      <div class="flex flex-wrap items-center gap-2">
        <div id="printerBadge" class="flex items-center gap-1.5 bg-indigo-950/80 border border-indigo-700 px-3 py-1.5 rounded-lg text-xs">
          <span id="printerDot" class="w-2 h-2 rounded-full bg-amber-400 animate-pulse"></span>
          <span id="printerStatusText" class="text-indigo-100 font-medium">Iware 58mm: Klik Hubungkan / Langsung Print</span>
        </div>
        <button id="btnConnectBle" type="button" class="bg-emerald-600 hover:bg-emerald-500 active:bg-emerald-700 text-white text-xs font-semibold px-3 py-1.5 rounded-lg transition shadow-sm flex items-center gap-1.5">
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 3v2m6-2v2M9 19v2m6-2v2M5 9H3m2 6H3m18-6h-2m2 6h-2M7 19h10a2 2 0 002-2V7a2 2 0 00-2-2H7a2 2 0 00-2 2v10a2 2 0 002 2z"/></svg>
          <span>Pair Bluetooth 58"</span>
        </button>
        <button id="btnConnectUsb" type="button" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-2.5 py-1.5 rounded-lg transition shadow-sm">
          USB OTG
        </button>
        <button id="btnTestFeedIware" type="button" class="bg-amber-500 hover:bg-amber-400 text-slate-950 text-xs font-bold px-2.5 py-1.5 rounded-lg transition shadow-sm">
          Tes Cetak Iware
        </button>
      </div>
    </div>

    <!-- Navigation Tabs -->
    <div class="bg-indigo-950/60 border-t border-indigo-800/80">
      <div class="max-w-7xl mx-auto px-4 flex overflow-x-auto no-scrollbar">
        <button type="button" data-tab="pos" class="nav-tab border-b-2 border-amber-400 text-white font-semibold px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2">
          <span>1. Kasir Door-to-Door</span>
        </button>
        <button type="button" data-tab="rute" class="nav-tab border-b-2 border-transparent text-indigo-200 hover:text-white font-medium px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2">
          <span>2. Rute & Toko</span>
          <span id="badgeRuteCount" class="bg-indigo-800 text-indigo-100 text-xs px-1.5 py-0.2 rounded-full">5</span>
        </button>
        <button type="button" data-tab="stok" class="nav-tab border-b-2 border-transparent text-indigo-200 hover:text-white font-medium px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2">
          <span>3. Stok Mobil Kanvas</span>
        </button>
        <button type="button" data-tab="riwayat" class="nav-tab border-b-2 border-transparent text-indigo-200 hover:text-white font-medium px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2">
          <span>4. Riwayat & Rekap Setoran</span>
          <span id="badgeTxCount" class="bg-emerald-600 text-white text-xs px-1.5 py-0.2 rounded-full">2</span>
        </button>
      </div>
    </div>
  </header>

  <!-- Toast Notification -->
  <div id="toastBox" class="fixed bottom-5 right-5 z-50 hidden max-w-sm bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl border border-slate-700 flex items-center gap-3 transition-all">
    <span id="toastIcon" class="w-2.5 h-2.5 rounded-full bg-emerald-400 shrink-0"></span>
    <p id="toastMessage" class="text-xs sm:text-sm font-medium"></p>
  </div>

  <!-- Main Workspace -->
  <main class="flex-1 max-w-7xl w-full mx-auto px-4 py-5">

    <!-- TAB 1: KASIR DOOR-TO-DOOR (POS) -->
    <section id="tab-pos" class="tab-panel grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
      
      <!-- Left Column: Store Selection + Product Catalog (7 cols) -->
      <div class="lg:col-span-7 space-y-4">
        
        <!-- Active Store / Customer Selection Bar -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200">
          <div class="flex flex-wrap items-center justify-between gap-2 mb-3">
            <div>
              <span class="text-xs font-semibold uppercase tracking-wider text-indigo-600">Kunjungan Toko Saat Ini</span>
              <h2 class="text-base font-bold text-slate-800">Pilih Pelanggan / Warung Door-to-Door</h2>
            </div>
            <div class="flex flex-wrap items-center gap-1.5">
              <button id="btnEditCurrentStorePos" type="button" class="text-xs font-semibold text-amber-700 hover:text-amber-900 bg-amber-50 hover:bg-amber-100 px-3 py-1.5 rounded-lg transition">
                ✎ Edit Data Toko Ini
              </button>
              <button id="btnQuickNewStore" type="button" class="text-xs font-semibold text-indigo-600 hover:text-indigo-800 bg-indigo-50 hover:bg-indigo-100 px-3 py-1.5 rounded-lg transition">
                + Toko Baru Dadakan
              </button>
            </div>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-3 gap-3">
            <div>
              <label for="selectCustomer" class="block text-xs font-medium text-slate-600 mb-1">Toko / Pelanggan</label>
              <select id="selectCustomer" class="w-full rounded-xl border border-slate-300 bg-slate-50 px-3 py-2 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </select>
            </div>
            <div>
              <label for="inputOrderDate" class="block text-xs font-medium text-slate-600 mb-1">Tanggal Orderan</label>
              <input id="inputOrderDate" type="date" value="2026-11-08" class="w-full rounded-xl border border-slate-300 bg-slate-50 px-3 py-2 text-sm font-semibold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
            </div>
            <div>
              <label for="selectPriceTier" class="block text-xs font-medium text-slate-600 mb-1">Satuan & Skema Harga Jual</label>
              <select id="selectPriceTier" class="w-full rounded-xl border border-slate-300 bg-slate-50 px-3 py-2 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                <option value="grosir">Jual per BOX / RENTENG (Harga Warung)</option>
                <option value="karton">Jual per KARTON (Harga Partai)</option>
                <option value="ecer">Jual per UNIT / PCS (Eceran)</option>
              </select>
            </div>
          </div>

          <!-- Selected Store Info Pill -->
          <div id="selectedCustomerMeta" class="mt-3 pt-3 border-t border-slate-100 flex flex-wrap items-center justify-between gap-2 text-xs text-slate-600">
          </div>
        </div>

        <!-- Product Search & Category Filter -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 space-y-3">
          <div class="flex flex-wrap items-center justify-between gap-2 pb-1 border-b border-slate-100">
            <span class="text-xs font-bold uppercase tracking-wider text-slate-500">Katalog & Scan Barcode Barang</span>
            <div class="flex flex-wrap items-center gap-1.5">
              <button id="btnOpenCategoryManagerPos" type="button" class="text-xs font-semibold text-amber-800 bg-amber-50 hover:bg-amber-100 border border-amber-200 px-2.5 py-1.5 rounded-lg transition">
                ✎ Edit Kategori
              </button>
              <button id="btnOpenScannerPos" type="button" class="text-xs font-semibold text-white bg-emerald-600 hover:bg-emerald-500 px-3 py-1.5 rounded-lg transition shadow-xs flex items-center gap-1.5">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1H4a1 1 0 01-1-1V4zm13 0a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1h-3a1 1 0 01-1-1V4zM3 16a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1H4a1 1 0 01-1-1v-3zm13 0a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1h-3a1 1 0 01-1-1v-3zM11 4h2v16h-2V4z"/></svg>
                <span>Scan Barcode</span>
              </button>
              <button id="btnOpenAddProductPos" type="button" class="text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded-lg transition shadow-xs">
                + Input Produk & Harga Baru
              </button>
            </div>
          </div>
          <div class="flex flex-col sm:flex-row gap-2">
            <div class="relative flex-1">
              <input id="inputSearchProduct" type="text" placeholder="Ketik nama / scan alat barcode (tekan Enter untuk otomatis simpan/masuk nota)..." class="w-full rounded-xl border border-slate-300 pl-9 pr-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
              <svg class="w-4 h-4 text-slate-400 absolute left-3 top-2.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/></svg>
            </div>
            <div class="flex gap-1.5 overflow-x-auto pb-1 sm:pb-0" id="categoryFilterBar">
              <button type="button" data-cat="ALL" class="cat-btn bg-indigo-600 text-white text-xs font-semibold px-3 py-2 rounded-xl whitespace-nowrap">Semua</button>
              <button type="button" data-cat="Wafer & Pie" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Wafer & Pie</button>
              <button type="button" data-cat="Biskuit & Snack" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Biskuit & Snack</button>
              <button type="button" data-cat="Permen" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Permen</button>
              <button type="button" data-cat="Mie & Snack" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Mie & Snack</button>
              <button type="button" data-cat="Minuman" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Minuman</button>
              <button type="button" data-cat="Sembako" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Sembako</button>
              <button type="button" data-cat="Kebutuhan Rumah" class="cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap">Rumah Tangga</button>
            </div>
          </div>

          <!-- Product Grid -->
          <div id="productGrid" class="grid grid-cols-1 sm:grid-cols-2 gap-3 pt-1">
          </div>
        </div>
      </div>

      <!-- Right Column: Cart & Live 58mm Thermal Receipt Preview (5 cols) -->
      <div class="lg:col-span-5 space-y-4">
        
        <!-- Cart & Payment Box -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 space-y-4">
          <div id="editingInvoiceBanner" class="hidden bg-amber-50 border border-amber-300 rounded-xl p-2.5 flex items-center justify-between gap-2">
            <div class="text-xs text-amber-900">
              <span class="font-bold">Mode Koreksi Nota:</span> <span id="editingInvoiceLabel" class="font-mono font-bold"></span>
            </div>
            <button id="btnCancelEditInvoice" type="button" class="text-[11px] font-semibold bg-white border border-amber-300 text-amber-800 px-2 py-1 rounded-lg hover:bg-amber-100">
              Batal Koreksi
            </button>
          </div>
          <div class="flex items-center justify-between border-b border-slate-100 pb-3">
            <div>
              <h3 class="font-bold text-base text-slate-800" id="cartBoxTitle">Nota Penjualan Aktif</h3>
              <p class="text-xs text-slate-500" id="cartSubtitle">0 item dipilih</p>
            </div>
            <button id="btnClearCart" type="button" class="text-xs font-medium text-rose-600 hover:text-rose-700 bg-rose-50 hover:bg-rose-100 px-2.5 py-1 rounded-lg transition">
              Kosongkan
            </button>
          </div>

          <!-- Cart Items List -->
          <div id="cartItemsList" class="space-y-2.5 max-h-64 overflow-y-auto pr-1">
          </div>

          <!-- Discount & Payment Controls -->
          <div class="border-t border-slate-200 pt-3 space-y-3">
            <div class="grid grid-cols-2 gap-2.5">
              <div>
                <label for="inputDiscountRp" class="block text-xs font-medium text-slate-600 mb-1">Potongan Nota (Rp)</label>
                <input id="inputDiscountRp" type="number" min="0" step="500" value="0" class="w-full rounded-xl border border-slate-300 px-3 py-1.5 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <div>
                <label for="selectPaymentMethod" class="block text-xs font-medium text-slate-600 mb-1">Metode Bayar</label>
                <select id="selectPaymentMethod" class="w-full rounded-xl border border-slate-300 px-3 py-1.5 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                  <option value="TUNAI">Tunai (Lunas)</option>
                  <option value="QRIS/TRANSFER">Transfer / QRIS</option>
                  <option value="TEMPO 7 HARI">Kredit / Tempo 7 Hari</option>
                  <option value="TEMPO 14 HARI">Kredit / Tempo 14 Hari</option>
                </select>
              </div>
            </div>

            <div class="grid grid-cols-2 gap-2.5">
              <div>
                <label for="inputCashReceived" class="block text-xs font-medium text-slate-600 mb-1">Uang Diterima / DP (Rp)</label>
                <input id="inputCashReceived" type="number" min="0" step="1000" placeholder="Otomatis pas" class="w-full rounded-xl border border-slate-300 px-3 py-1.5 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <div class="flex flex-col justify-end">
                <div class="flex gap-1">
                  <button type="button" data-quickcash="exact" class="quick-cash-btn flex-1 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold py-2 rounded-lg">Uang Pas</button>
                  <button type="button" data-quickcash="50000" class="quick-cash-btn flex-1 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold py-2 rounded-lg">+50rb</button>
                  <button type="button" data-quickcash="100000" class="quick-cash-btn flex-1 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-semibold py-2 rounded-lg">+100rb</button>
                </div>
              </div>
            </div>

            <!-- Totals Box -->
            <div class="bg-indigo-950 text-white rounded-xl p-3.5 space-y-1.5">
              <div class="flex justify-between text-xs text-indigo-200">
                <span>Subtotal Barang</span>
                <span id="summarySubtotal">Rp 0</span>
              </div>
              <div class="flex justify-between text-xs text-amber-300">
                <span>Potongan Diskon</span>
                <span id="summaryDiscount">- Rp 0</span>
              </div>
              <div class="flex justify-between text-base font-bold pt-1 border-t border-indigo-800">
                <span>Total Tagihan</span>
                <span id="summaryGrandTotal" class="text-emerald-400">Rp 0</span>
              </div>
              <div class="flex justify-between text-xs text-indigo-200 pt-0.5">
                <span id="summaryChangeLabel">Kembalian</span>
                <span id="summaryChangeValue" class="font-semibold text-white">Rp 0</span>
              </div>
            </div>

            <!-- Primary Actions -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5 pt-1">
              <button id="btnSaveAndPrint" type="button" class="w-full bg-emerald-600 hover:bg-emerald-500 active:bg-emerald-700 text-white font-bold py-2.5 px-4 rounded-xl shadow-sm transition flex items-center justify-center gap-2 text-sm">
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"/></svg>
                <span>Simpan & Cetak 58mm</span>
              </button>
              <button id="btnCopyEscPos" type="button" class="w-full bg-slate-800 hover:bg-slate-700 text-white font-semibold py-2.5 px-4 rounded-xl transition flex items-center justify-center gap-2 text-xs sm:text-sm">
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"/></svg>
                <span>Salin Teks Struk / WA</span>
              </button>
            </div>
          </div>
        </div>

        <!-- Live 58mm Thermal Receipt Preview -->
        <div class="bg-slate-200/80 rounded-2xl p-4 border border-slate-300">
          <div class="flex items-center justify-between mb-3">
            <span class="text-xs font-bold uppercase tracking-wider text-slate-600 flex items-center gap-1.5">
              <span class="w-2 h-2 rounded-full bg-emerald-500"></span>
              Pratinjau Kertas Thermal 58mm (32 Karakter/Baris)
            </span>
            <button id="btnDirectBrowserPrint" type="button" class="text-xs font-semibold text-indigo-700 hover:underline">
              Test Print Browser
            </button>
          </div>

          <div id="printableArea" class="receipt-paper p-3 rounded">
            <pre id="receiptTextPreview" class="whitespace-pre-wrap break-words font-mono text-[11px] leading-snug m-0"></pre>
          </div>
        </div>

      </div>
    </section>

    <!-- TAB 2: RUTE KUNJUNGAN & DATA TOKO -->
    <section id="tab-rute" class="tab-panel hidden space-y-4">
      <div class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200 flex flex-wrap items-center justify-between gap-4">
        <div>
          <h2 class="text-lg font-bold text-slate-800">Rute Kunjungan Door-to-Door Hari Ini</h2>
          <p class="text-xs text-slate-500">Klik "Buat Nota" pada toko untuk langsung memilih pelanggan di layar Kasir.</p>
        </div>
        <div class="flex flex-wrap items-center gap-2">
          <button id="btnAddStoreFromRoute" type="button" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-3.5 py-2 rounded-xl transition shadow-xs">
            + Tambah Pelanggan / Toko Baru
          </button>
          <span id="routeProgressBadge" class="bg-emerald-50 text-emerald-700 border border-emerald-200 text-xs font-semibold px-3 py-1.5 rounded-xl">
            Terkunjungi: 2 / 5 Toko
          </span>
        </div>
      </div>

      <div id="routeStoreList" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      </div>
    </section>

    <!-- TAB 3: STOK MOBIL KANVAS -->
    <section id="tab-stok" class="tab-panel hidden space-y-4">
      <div class="bg-white rounded-2xl p-5 shadow-sm border border-slate-200 flex flex-wrap items-center justify-between gap-4">
        <div>
          <h2 class="text-lg font-bold text-slate-800">Master Produk, Harga & Stok Mobil Kanvas</h2>
          <p class="text-xs text-slate-500">Tambah produk baru, atur Harga Grosir & Harga Ecer, atau sesuaikan sisa muatan barang di kendaraan sales.</p>
        </div>
        <div class="flex flex-wrap items-center gap-2">
          <button id="btnOpenScannerStock" type="button" class="text-xs font-semibold text-white bg-emerald-600 hover:bg-emerald-500 px-3.5 py-2 rounded-xl transition shadow-xs flex items-center gap-1.5">
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1H4a1 1 0 01-1-1V4zm13 0a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1h-3a1 1 0 01-1-1V4zM3 16a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1H4a1 1 0 01-1-1v-3zm13 0a1 1 0 011-1h3a1 1 0 011 1v3a1 1 0 01-1 1h-3a1 1 0 01-1-1v-3zM11 4h2v16h-2V4z"/></svg>
            <span>Scan Barcode Simpan Produk</span>
          </button>
          <button id="btnOpenAddProductStock" type="button" class="text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500 px-3.5 py-2 rounded-xl transition shadow-xs">
            + Tambah Produk & Harga
          </button>
          <button id="btnResetDemoData" type="button" class="text-xs font-semibold text-slate-600 hover:text-slate-900 bg-slate-100 hover:bg-slate-200 px-3 py-2 rounded-xl transition">
            Reset Stok Awal Demo
          </button>
        </div>
      </div>

      <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-200 flex flex-wrap items-center justify-between gap-3">
        <div class="text-xs text-slate-600">
          <span class="font-bold text-indigo-700">Format Rekap Barang Sales:</span> Ringkas tanpa Batch & ED (<code class="bg-slate-100 px-1.5 py-0.5 rounded font-mono">NO | PRODUK | KUANTUM | KARTON | BOX | UNIT</code>).
        </div>
        <button id="btnPrintStockRecap58" type="button" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-3.5 py-2 rounded-xl transition flex items-center gap-1.5">
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"/></svg>
          <span>Cetak Struk Rekap Barang 58mm</span>
        </button>
      </div>

      <div class="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse font-mono text-xs">
            <thead>
              <tr class="bg-slate-900 text-white border-b border-slate-700 text-[11px] font-bold uppercase tracking-wider">
                <th class="py-3 px-3 text-center">NO</th>
                <th class="py-3 px-3">PRODUK</th>
                <th class="py-3 px-3 text-right">KUANTUM</th>
                <th class="py-3 px-3 text-right bg-indigo-950">KARTON</th>
                <th class="py-3 px-3 text-right bg-indigo-950">BOX</th>
                <th class="py-3 px-3 text-right bg-indigo-950">UNIT</th>
                <th class="py-3 px-3 text-right font-sans">HARGA (BOX / KRT)</th>
                <th class="py-3 px-3 text-right font-sans">AKSI MUATAN</th>
              </tr>
            </thead>
            <tbody id="stockTableBody" class="divide-y divide-slate-200">
            </tbody>
          </table>
        </div>
      </div>
    </section>

    <!-- TAB 4: RIWAYAT TRANSAKSI & REKAP SETORAN -->
    <section id="tab-riwayat" class="tab-panel hidden space-y-5">
      <!-- KPI Cards -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
          <span class="text-xs font-medium text-slate-500">Total Omzet Hari Ini</span>
          <p id="kpiTotalOmzet" class="text-xl font-bold text-slate-900 mt-1">Rp 0</p>
          <span class="text-[11px] text-emerald-600 font-medium">Tunai + QRIS + Tempo</span>
        </div>
        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
          <span class="text-xs font-medium text-slate-500">Uang Tunai Wajib Setor</span>
          <p id="kpiCashInHand" class="text-xl font-bold text-emerald-600 mt-1">Rp 0</p>
          <span class="text-[11px] text-slate-500">Kas fisik di tangan sales</span>
        </div>
        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
          <span class="text-xs font-medium text-slate-500">Piutang / Nota Tempo Baru</span>
          <p id="kpiCreditTotal" class="text-xl font-bold text-amber-600 mt-1">Rp 0</p>
          <span class="text-[11px] text-slate-500">Tagihan jatuh tempo toko</span>
        </div>
        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200 flex flex-col justify-between">
          <div>
            <span class="text-xs font-medium text-slate-500">Struk Rekap Akhir Hari</span>
            <p class="text-xs text-slate-600 mt-1">Cetak laporan tutup kasir 58mm untuk kasir gudang.</p>
          </div>
          <button id="btnPrintDailyRecap" type="button" class="mt-2 w-full bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold py-2 px-3 rounded-xl transition">
            Cetak Rekap Setoran 58mm
          </button>
        </div>
      </div>

      <!-- Transaction History List -->
      <div class="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">
        <div class="px-5 py-4 border-b border-slate-200 flex items-center justify-between">
          <h3 class="font-bold text-base text-slate-800">Daftar Nota Penjualan</h3>
          <span class="text-xs text-slate-500">Klik "Cetak Ulang" untuk memuat struk ke printer 58mm</span>
        </div>
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse">
            <thead>
              <tr class="bg-slate-50 border-b border-slate-200 text-xs font-semibold text-slate-600 uppercase">
                <th class="py-3 px-4">No. Nota & Waktu</th>
                <th class="py-3 px-4">Pelanggan / Toko</th>
                <th class="py-3 px-4">Metode Bayar</th>
                <th class="py-3 px-4 text-right">Total Tagihan</th>
                <th class="py-3 px-4 text-right">Dibayar</th>
                <th class="py-3 px-4 text-right">Aksi Struk</th>
              </tr>
            </thead>
            <tbody id="historyTableBody" class="divide-y divide-slate-200 text-sm">
            </tbody>
          </table>
        </div>
      </div>
    </section>

  </main>

  <!-- Modal Tambah Toko Dadakan -->
  <div id="modalNewStore" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-5 shadow-xl border border-slate-200 space-y-4">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <h3 id="modalStoreTitle" class="font-bold text-base text-slate-800">Tambah Pelanggan / Warung Baru</h3>
        <button id="btnCloseModalStore" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>
      <input id="editStoreIdInput" type="hidden" value="">
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
        <div class="sm:col-span-2">
          <label for="newStoreName" class="block text-xs font-medium text-slate-600 mb-1">Nama Toko / Pelanggan</label>
          <input id="newStoreName" type="text" placeholder="Contoh: Warung Berkah Ibu Siti" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="newStoreOwner" class="block text-xs font-medium text-slate-600 mb-1">Nama Pemilik / Kontak</label>
          <input id="newStoreOwner" type="text" placeholder="Contoh: Ibu Siti" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="newStorePhone" class="block text-xs font-medium text-slate-600 mb-1">No. HP / WhatsApp</label>
          <input id="newStorePhone" type="text" placeholder="0812..." class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div class="sm:col-span-2">
          <label for="newStoreAddress" class="block text-xs font-medium text-slate-600 mb-1">Alamat / Rute Lokasi</label>
          <input id="newStoreAddress" type="text" placeholder="Contoh: Jl. Raya Cibabat No. 18 (Depan Masjid)" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="newStoreLimit" class="block text-xs font-medium text-slate-600 mb-1">Plafon Kredit / Tempo (Rp)</label>
          <input id="newStoreLimit" type="number" min="0" step="100000" value="1500000" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="newStoreNote" class="block text-xs font-medium text-slate-600 mb-1">Catatan Langganan</label>
          <input id="newStoreNote" type="text" placeholder="Contoh: Kunjungan tiap Minggu" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
      </div>
      <div class="flex items-center justify-between pt-2 border-t border-slate-100">
        <button id="btnDeleteStore" type="button" class="hidden px-3 py-2 rounded-xl text-xs font-semibold text-rose-600 bg-rose-50 hover:bg-rose-100">
          Hapus Toko
        </button>
        <div class="flex justify-end gap-2 ml-auto">
          <button id="btnCancelStore" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-slate-600 bg-slate-100 hover:bg-slate-200">Batal</button>
          <button id="btnSaveStore" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500">Simpan & Pilih Toko</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal Edit Profil Perusahaan & Sales (Header Struk 58mm) -->
  <div id="modalProfileSettings" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-5 shadow-xl border border-slate-200 space-y-4">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <h3 class="font-bold text-base text-slate-800">Edit Profil Sales & Header Struk 58mm</h3>
        <button id="btnCloseModalProfile" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>
      <div class="space-y-3">
        <div>
          <label for="inputCompanyName" class="block text-xs font-medium text-slate-600 mb-1">Nama Distributor / Perusahaan (Baris 1 Struk)</label>
          <input id="inputCompanyName" type="text" value="PT MAJU JAYA DISTRIBUSI" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm font-semibold">
        </div>
        <div>
          <label for="inputBranchUnit" class="block text-xs font-medium text-slate-600 mb-1">Unit Armada / Wilayah (Baris 2 Struk)</label>
          <input id="inputBranchUnit" type="text" value="Kanvas #04 - Jawa Barat" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="inputWarehousePhone" class="block text-xs font-medium text-slate-600 mb-1">Kontak / WA Gudang (Baris 3 Struk)</label>
          <input id="inputWarehousePhone" type="text" value="WA Gudang: 0811-2000-5858" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm">
        </div>
        <div>
          <label for="inputSalespersonName" class="block text-xs font-medium text-slate-600 mb-1">Nama Sales bertugas</label>
          <input id="inputSalespersonName" type="text" value="Andi Pratama" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm font-semibold">
        </div>
      </div>
      <div class="flex justify-end gap-2 pt-2 border-t border-slate-100">
        <button id="btnCancelProfile" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-slate-600 bg-slate-100 hover:bg-slate-200">Batal</button>
        <button id="btnSaveProfile" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500">Simpan Profil</button>
      </div>
    </div>
  </div>

  <!-- Modal Kelola / Edit Kategori Produk -->
  <div id="modalCategoryManager" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-5 shadow-xl border border-slate-200 space-y-4 max-h-[90vh] flex flex-col">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <div>
          <h3 class="font-bold text-base text-slate-800">Kelola & Edit Kategori Produk</h3>
          <p class="text-xs text-slate-500">Tambah, ubah nama, atau hapus kategori barang kanvas</p>
        </div>
        <button id="btnCloseCategoryModal" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>

      <!-- Add New Category Row -->
      <div class="flex gap-2">
        <input id="inputNewCategoryName" type="text" placeholder="Ketik nama kategori baru (misal: Rokok / Kosmetik)..." class="flex-1 rounded-xl border border-slate-300 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
        <button id="btnAddNewCategory" type="button" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-3.5 py-2 rounded-xl transition shrink-0">
          + Tambah
        </button>
      </div>

      <!-- Editable Categories List -->
      <div id="categoryManagerList" class="space-y-2 overflow-y-auto pr-1 flex-1 max-h-64 divide-y divide-slate-100">
      </div>

      <div class="flex justify-end pt-2 border-t border-slate-100">
        <button id="btnDoneCategoryModal" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-white bg-slate-800 hover:bg-slate-700">
          Selesai
        </button>
      </div>
    </div>
  </div>

  <!-- Modal Menyandingkan Bluetooth & PIN Printer Iware 58mm -->
  <div id="modalBluetoothPairing" class="fixed inset-0 z-50 hidden bg-slate-900/75 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-5 shadow-2xl border border-slate-200 space-y-4 max-h-[92vh] overflow-y-auto">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <div class="flex items-center gap-2.5">
          <div class="w-9 h-9 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center font-bold">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 3v2m6-2v2M9 19v2m6-2v2M5 9H3m2 6H3m18-6h-2m2 6h-2M7 19h10a2 2 0 002-2V7a2 2 0 00-2-2H7a2 2 0 00-2 2v10a2 2 0 002 2z"/></svg>
          </div>
          <div>
            <h3 class="font-bold text-base text-slate-800">Sandingkan Bluetooth & PIN Iware</h3>
            <p class="text-xs text-slate-500">Koneksi Printer Thermal 58mm (RPP02N / MP-58)</p>
          </div>
        </div>
        <button id="btnCloseBleModal" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>

      <!-- Info Box Mengapa di Preview Canvas Tidak Muncul Popup Sistem -->
      <div id="bleEnvironmentAlert" class="bg-amber-50 border border-amber-200 rounded-xl p-3 text-xs text-amber-900 space-y-1.5">
        <p class="font-bold flex items-center gap-1.5">
          <span>ℹ️</span>
          <span>Mengapa Pop-up PIN Bluetooth HP Belum Muncul?</span>
        </p>
        <p class="leading-relaxed">
          Saat dibuka di dalam layar pratinjau chat ini, browser membatasi pop-up Bluetooth perangkat. Untuk memunculkan daftar Bluetooth & kolom PIN asli di HP Anda:
        </p>
        <ol class="list-decimal list-inside space-y-1 font-medium text-amber-950 pt-0.5">
          <li>Buka **Pengaturan Bluetooth HP Android** Anda, nyalakan printer Iware (lampu hijau menyala).</li>
          <li>Pilih perangkat **`RPP02N` / `Bluetooth Printer` / `MP-58`** lalu masukkan PIN **`0000`** atau **`1234`**.</li>
          <li>Unduh & buka file aplikasi ini langsung di **Google Chrome HP**, lalu klik tombol **Scan Bluetooth Chrome** di bawah.</li>
        </ol>
      </div>

      <!-- Form Pilih Perangkat & Masukkan PIN Penyandingan -->
      <div class="space-y-3 bg-slate-50 p-3.5 rounded-xl border border-slate-200">
        <div>
          <label for="selectIwareModel" class="block text-xs font-semibold text-slate-700 mb-1">1. Pilih Tipe / Nama Bluetooth Printer Iware</label>
          <select id="selectIwareModel" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm font-semibold text-slate-800">
            <option value="Iware RPP02N (58mm)">Iware RPP02N — Bluetooth Printer</option>
            <option value="Iware MP-58 / MPT-II">Iware MP-58 / MPT-II Thermal</option>
            <option value="POS-58 Thermal">POS-58 / Printer 58mm Standar</option>
          </select>
        </div>

        <div class="grid grid-cols-2 gap-2.5">
          <div>
            <label for="inputBlePin" class="block text-xs font-semibold text-slate-700 mb-1">2. Masukkan PIN Pairing</label>
            <input id="inputBlePin" type="text" maxlength="6" value="0000" placeholder="0000 atau 1234" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm font-mono font-bold tracking-widest text-center text-indigo-700">
          </div>
          <div class="flex flex-col justify-end">
            <div class="flex gap-1.5">
              <button type="button" data-pin-preset="0000" class="pin-preset-btn flex-1 bg-indigo-100 hover:bg-indigo-200 text-indigo-800 text-xs font-bold py-2 rounded-lg">PIN 0000</button>
              <button type="button" data-pin-preset="1234" class="pin-preset-btn flex-1 bg-slate-200 hover:bg-slate-300 text-slate-700 text-xs font-bold py-2 rounded-lg">PIN 1234</button>
            </div>
          </div>
        </div>

        <!-- Status Pairing Live -->
        <div id="blePairingProgressBox" class="hidden bg-indigo-950 text-white rounded-xl p-3 text-xs flex items-center gap-2.5">
          <span class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-ping shrink-0"></span>
          <span id="blePairingProgressText">Menyandingkan Bluetooth dengan PIN 0000...</span>
        </div>

        <div class="grid grid-cols-1 gap-2 pt-1">
          <button id="btnConfirmPairWithPin" type="button" class="w-full bg-emerald-600 hover:bg-emerald-500 text-white font-bold py-2.5 px-4 rounded-xl shadow-sm transition text-xs sm:text-sm flex items-center justify-center gap-2">
            <span>Sandingkan & Simpan PIN Printer</span>
          </button>
          <button id="btnScanNativeWebBle" type="button" class="w-full bg-indigo-600 hover:bg-indigo-500 text-white font-semibold py-2 px-4 rounded-xl transition text-xs flex items-center justify-center gap-1.5">
            <span>Scan Pop-up Bluetooth Asli (Khusus di Browser Chrome)</span>
          </button>
        </div>
      </div>

      <!-- Tombol Alternatif Cetak Langsung Android Intent (100% Jalan untuk Semua Bluetooth SPP Iware) -->
      <div class="border-t border-slate-200 pt-3 flex items-center justify-between gap-2">
        <span class="text-[11px] text-slate-500">Sudah disandingkan di Pengaturan HP?</span>
        <button id="btnCloseBleDone" type="button" class="px-4 py-1.5 rounded-xl text-xs font-semibold text-slate-700 bg-slate-100 hover:bg-slate-200">
          Tutup
        </button>
      </div>
    </div>
  </div>

  <!-- Modal Input / Edit Produk & Harga -->
  <div id="modalProductForm" class="fixed inset-0 z-50 hidden bg-slate-900/60 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-lg w-full p-5 shadow-xl border border-slate-200 space-y-4">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <h3 id="modalProductTitle" class="font-bold text-base text-slate-800">Tambah Produk & Harga Baru</h3>
        <button id="btnCloseModalProduct" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>
      <input id="editProductId" type="hidden" value="">
      <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
        <div>
          <label for="prodSkuInput" class="block text-xs font-medium text-slate-600 mb-1">Kode Barcode / SKU</label>
          <div class="flex gap-1.5">
            <input id="prodSkuInput" type="text" placeholder="Contoh: 8991002101234" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm font-mono">
            <button id="btnScanInModal" type="button" class="shrink-0 bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-2.5 py-2 rounded-xl transition">
              Scan
            </button>
          </div>
        </div>
        <div>
          <div class="flex items-center justify-between mb-1">
            <label for="prodCategoryInput" class="block text-xs font-medium text-slate-600">Kategori Produk</label>
            <button id="btnOpenCatFromProdModal" type="button" class="text-[11px] font-semibold text-indigo-600 hover:underline">✎ Edit Daftar Kategori</button>
          </div>
          <select id="prodCategoryInput" class="w-full rounded-xl border border-slate-300 bg-white px-3 py-2 text-sm">
            <option value="Wafer & Pie">Wafer & Pie</option>
            <option value="Biskuit & Snack">Biskuit & Snack</option>
            <option value="Permen">Permen</option>
            <option value="Mie & Snack">Mie & Snack</option>
            <option value="Minuman">Minuman</option>
            <option value="Sembako">Sembako</option>
            <option value="Kebutuhan Rumah">Kebutuhan Rumah</option>
          </select>
        </div>
        <div class="sm:col-span-2">
          <label for="prodNameInput" class="block text-xs font-medium text-slate-600 mb-1">Nama Produk (Sesuai Rekap Gudang)</label>
          <input id="prodNameInput" type="text" placeholder="Contoh: CHOCO PIE ORIGINAL 1P" class="w-full rounded-xl border border-slate-300 px-3 py-2 text-sm font-semibold uppercase">
        </div>
        <div class="hidden">
          <input id="prodBatchInput" type="hidden" value="">
          <input id="prodExpInput" type="hidden" value="">
        </div>
        <div class="sm:col-span-2 grid grid-cols-4 gap-2 bg-slate-50 p-2.5 rounded-xl border border-slate-200">
          <div>
            <label for="prodKartonInput" class="block text-[11px] font-bold text-slate-700 mb-1">KARTON</label>
            <input id="prodKartonInput" type="number" min="0" step="1" value="2" class="w-full rounded-lg border border-slate-300 px-2.5 py-1.5 text-sm font-mono font-bold">
          </div>
          <div>
            <label for="prodBoxInput" class="block text-[11px] font-bold text-slate-700 mb-1">BOX / RENTENG</label>
            <input id="prodBoxInput" type="number" min="0" step="1" value="0" class="w-full rounded-lg border border-slate-300 px-2.5 py-1.5 text-sm font-mono font-bold">
          </div>
          <div>
            <label for="prodUnitPcsInput" class="block text-[11px] font-bold text-slate-700 mb-1">UNIT (PCS)</label>
            <input id="prodUnitPcsInput" type="number" min="0" step="1" value="0" class="w-full rounded-lg border border-slate-300 px-2.5 py-1.5 text-sm font-mono">
          </div>
          <div>
            <label for="prodStockInput" class="block text-[11px] font-bold text-indigo-700 mb-1">KUANTUM</label>
            <input id="prodStockInput" type="number" min="0" step="1" value="24" class="w-full rounded-lg border border-indigo-300 bg-indigo-50/50 px-2.5 py-1.5 text-sm font-mono font-bold text-indigo-900">
          </div>
        </div>
        <div class=" hidden">
          <input id="prodUnitInput" type="text" value="Box">
        </div>
        <div>
          <label for="prodPriceGrosirInput" class="block text-xs font-semibold text-indigo-700 mb-1">Harga Jual per BOX / PAK (Rp)</label>
          <input id="prodPriceGrosirInput" type="number" min="0" step="500" placeholder="Contoh: 18500" class="w-full rounded-xl border border-indigo-300 bg-indigo-50/40 px-3 py-2 text-sm font-bold text-slate-900">
        </div>
        <div>
          <label for="prodPriceKartonInput" class="block text-xs font-semibold text-amber-700 mb-1">Harga Jual per KARTON (Rp)</label>
          <input id="prodPriceKartonInput" type="number" min="0" step="1000" placeholder="Contoh: 145000" class="w-full rounded-xl border border-amber-300 bg-amber-50/40 px-3 py-2 text-sm font-bold text-slate-900">
        </div>
        <div class="sm:col-span-2">
          <label for="prodPriceEcerInput" class="block text-xs font-semibold text-emerald-700 mb-1">Harga Jual per UNIT / PCS Eceran (Rp)</label>
          <input id="prodPriceEcerInput" type="number" min="0" step="100" placeholder="Contoh: 2000" class="w-full rounded-xl border border-emerald-300 bg-emerald-50/40 px-3 py-2 text-sm font-bold text-slate-900">
        </div>
      </div>
      <div class="flex items-center justify-between pt-2 border-t border-slate-100">
        <button id="btnDeleteProduct" type="button" class="hidden px-3 py-2 rounded-xl text-xs font-semibold text-rose-600 bg-rose-50 hover:bg-rose-100">
          Hapus Produk
        </button>
        <div class="flex justify-end gap-2 ml-auto">
          <button id="btnCancelProduct" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-slate-600 bg-slate-100 hover:bg-slate-200">Batal</button>
          <button id="btnSaveProduct" type="button" class="px-4 py-2 rounded-xl text-xs font-semibold text-white bg-indigo-600 hover:bg-indigo-500">Simpan Produk & Harga</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal Scan Barcode (Kamera / Laser Scanner / Katalog Otomatis) -->
  <div id="modalBarcodeScanner" class="fixed inset-0 z-50 hidden bg-slate-900/70 backdrop-blur-xs flex items-center justify-center p-4">
    <div class="bg-white rounded-2xl max-w-lg w-full p-5 shadow-2xl border border-slate-200 space-y-4 max-h-[92vh] overflow-y-auto">
      <div class="flex items-center justify-between border-b border-slate-100 pb-3">
        <div>
          <h3 class="font-bold text-base text-slate-800">Scan Barcode Otomatis</h3>
          <p class="text-xs text-slate-500">Otomatis mengenali barcode produk distributor & menyimpan ke katalog/nota</p>
        </div>
        <button id="btnCloseScannerModal" type="button" class="text-slate-400 hover:text-slate-600 text-sm font-bold px-2">✕</button>
      </div>

      <!-- Mode Action Selector -->
      <div class="grid grid-cols-2 gap-2 bg-slate-100 p-1 rounded-xl">
        <button id="btnScanModeAutoSave" type="button" class="bg-indigo-600 text-white text-xs font-semibold py-2 px-3 rounded-lg transition">
          Otomatis Simpan Produk & Masuk Nota
        </button>
        <button id="btnScanModeEditForm" type="button" class="text-slate-700 hover:bg-white/60 text-xs font-medium py-2 px-3 rounded-lg transition">
          Buka Form Harga Setelah Scan
        </button>
      </div>

      <!-- Camera Viewfinder -->
      <div class="bg-slate-950 rounded-xl overflow-hidden border border-slate-800 relative">
        <div id="qrReaderBox" class="w-full min-h-[210px] flex items-center justify-center text-slate-400 text-xs p-4 text-center">
          Klik "Aktifkan Kamera HP/Laptop" di bawah untuk scan via kamera, atau gunakan kolom Scanner Alat / Simulasi Barcode di bawah.
        </div>
        <div class="bg-slate-900 px-3 py-2 flex items-center justify-between border-t border-slate-800">
          <span id="cameraStatusLabel" class="text-[11px] text-slate-300">Kamera: Nonaktif</span>
          <div class="flex gap-2">
            <button id="btnStartCameraScan" type="button" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-3 py-1 rounded-lg transition">
              Aktifkan Kamera
            </button>
            <button id="btnStopCameraScan" type="button" class="hidden bg-rose-600 hover:bg-rose-500 text-white text-xs font-semibold px-3 py-1 rounded-lg transition">
              Matikan Kamera
            </button>
          </div>
        </div>
      </div>

      <!-- Manual / USB / Bluetooth Barcode Gun Input -->
      <div class="space-y-1.5">
        <label for="inputBarcodeManual" class="block text-xs font-semibold text-slate-700">Input Alat Scanner Barcode (USB/Bluetooth) atau Ketik EAN-13:</label>
        <div class="flex gap-2">
          <input id="inputBarcodeManual" type="text" placeholder="Arahkan laser scanner ke sini atau ketik kode barcode..." class="flex-1 rounded-xl border border-slate-300 px-3 py-2 text-sm font-mono focus:outline-none focus:ring-2 focus:ring-emerald-500">
          <button id="btnSubmitManualBarcode" type="button" class="bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold px-4 py-2 rounded-xl transition">
            ProsesKode
          </button>
        </div>
      </div>

      <!-- Smart Preset Barcodes (Database Distributor Otomatis) -->
      <div class="border-t border-slate-200 pt-3 space-y-2">
        <div class="flex items-center justify-between">
          <span class="text-xs font-bold uppercase tracking-wider text-indigo-700">Simulasi Scan Cepat (Database Barcode Distributor)</span>
          <span class="text-[11px] text-slate-500">Klik untuk tes scan otomatis</span>
        </div>
        <div id="presetBarcodeList" class="grid grid-cols-1 sm:grid-cols-2 gap-2">
        </div>
      </div>
    </div>
  </div>

  <script>
    // --- INITIAL DEMO DATA ---
    // Database master barcode distributor sesuai REKAP BARANG PERSALES untuk auto-fill & simpan otomatis saat di-scan
    const DISTRIBUTOR_BARCODE_DB = {
      '8991102000101': { sku: '8991102000101', name: 'CHOCOLITO ORIGINAL 15 G', batch: '230726', exp: '11-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 12, karton: 2, box: 0, unitPcs: 0, priceGrosir: 19500, priceKarton: 115000, priceEcer: 1000, defaultStock: 12 },
      '8991102000102': { sku: '8991102000102', name: 'CHOCOLITO RICH CHOCO 15 G', batch: '070926', exp: '12-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 25, karton: 4, box: 1, unitPcs: 0, priceGrosir: 19500, priceKarton: 115000, priceEcer: 1000, defaultStock: 25 },
      '8991102000103': { sku: '8991102000103', name: 'CHOCO PIE ORIGINAL 1P', batch: '23092026', exp: '09-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 6528, karton: 68, box: 0, unitPcs: 0, priceGrosir: 22000, priceKarton: 172000, priceEcer: 2000, defaultStock: 68 },
      '8991102000104': { sku: '8991102000104', name: 'TOPPO DOUBLE CHOCOLATE 17G', batch: '25092026', exp: '12-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 4080, karton: 28, box: 4, unitPcs: 0, priceGrosir: 21000, priceKarton: 248000, priceEcer: 2000, defaultStock: 32 },
      '8991102000105': { sku: '8991102000105', name: 'CHOCO PIE STRAWBERRY 1P', batch: '07102026', exp: '10-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 1032, karton: 10, box: 6, unitPcs: 0, priceGrosir: 22500, priceKarton: 176000, priceEcer: 2000, defaultStock: 16 },
      '8991102000107': { sku: '8991102000107', name: 'CHOCO PIE SIGNATURE MATCHA', batch: '10102026', exp: '10-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 792, karton: 8, box: 2, unitPcs: 0, priceGrosir: 23000, priceKarton: 180000, priceEcer: 2500, defaultStock: 10 },
      '8991102000109': { sku: '8991102000109', name: 'HEXOS MINT', batch: '091026', exp: '10-2028', category: 'Permen', unit: 'Box', kuantum: 50, karton: 0, box: 1, unitPcs: 0, priceGrosir: 135000, priceKarton: 530000, priceEcer: 3000, defaultStock: 50 },
      '8991102000112': { sku: '8991102000112', name: 'TINI WINI BITI COKLAT 16 G(ISI 80)', batch: '240926', exp: '01-2028', category: 'Biskuit & Snack', unit: 'Box', kuantum: 210, karton: 2, box: 5, unitPcs: 0, priceGrosir: 18500, priceKarton: 145000, priceEcer: 1000, defaultStock: 21 }
    };

    function playScannerBeep() {
      try {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(1150, ctx.currentTime);
        gain.gain.setValueAtTime(0.12, ctx.currentTime);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start();
        osc.stop(ctx.currentTime + 0.09);
      } catch (e) {}
    }

    // Data Muatan Persales sesuai Foto Dokumen REKAP BARANG (13 Baris Lengkap)
    const DEFAULT_PRODUCTS = [
      { id: 'P01', sku: '8991102000101', name: 'CHOCOLITO ORIGINAL 15 G', batch: '230726', exp: '11-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 12, karton: 2, box: 0, unitPcs: 0, priceGrosir: 19500, priceKarton: 115000, priceEcer: 1000, stock: 12 },
      { id: 'P02', sku: '8991102000102', name: 'CHOCOLITO RICH CHOCO 15 G', batch: '070926', exp: '12-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 25, karton: 4, box: 1, unitPcs: 0, priceGrosir: 19500, priceKarton: 115000, priceEcer: 1000, stock: 25 },
      { id: 'P03', sku: '8991102000103', name: 'CHOCO PIE ORIGINAL 1P', batch: '23092026', exp: '09-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 6528, karton: 68, box: 0, unitPcs: 0, priceGrosir: 22000, priceKarton: 172000, priceEcer: 2000, stock: 544 },
      { id: 'P04', sku: '8991102000104', name: 'TOPPO DOUBLE CHOCOLATE 17G', batch: '25092026', exp: '12-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 4080, karton: 28, box: 4, unitPcs: 0, priceGrosir: 21000, priceKarton: 248000, priceEcer: 2000, stock: 340 },
      { id: 'P05', sku: '8991102000105', name: 'CHOCO PIE STRAWBERRY 1P', batch: '07102026', exp: '10-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 1032, karton: 10, box: 6, unitPcs: 0, priceGrosir: 22500, priceKarton: 176000, priceEcer: 2000, stock: 86 },
      { id: 'P06', sku: '8991102000106', name: 'CHOCO PIE STRAWBERRY 1P (2 BOX)', batch: '20082026', exp: '08-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 24, karton: 0, box: 2, unitPcs: 0, priceGrosir: 22500, priceKarton: 176000, priceEcer: 2000, stock: 2 },
      { id: 'P07', sku: '8991102000107', name: 'CHOCO PIE SIGNATURE MATCHA', batch: '10102026', exp: '10-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 792, karton: 8, box: 2, unitPcs: 0, priceGrosir: 23000, priceKarton: 180000, priceEcer: 2500, stock: 66 },
      { id: 'P08', sku: '8991102000108', name: 'CHOCO PIE SIGNATURE DARK CHOCOLATE', batch: '15102026', exp: '10-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 12, karton: 0, box: 1, unitPcs: 0, priceGrosir: 23000, priceKarton: 180000, priceEcer: 2500, stock: 1 },
      { id: 'P09', sku: '8991102000109', name: 'HEXOS MINT', batch: '091026', exp: '10-2028', category: 'Permen', unit: 'Box', kuantum: 50, karton: 0, box: 1, unitPcs: 0, priceGrosir: 135000, priceKarton: 530000, priceEcer: 3000, stock: 50 },
      { id: 'P10', sku: '8991102000110', name: 'MT TOBELO BALIGEE BOX ISI 24', batch: '02609028', exp: '11-2027', category: 'Wafer & Pie', unit: 'Box', kuantum: 144, karton: 1, box: 0, unitPcs: 0, priceGrosir: 24000, priceKarton: 140000, priceEcer: 1500, stock: 6 },
      { id: 'P11', sku: '8991102000111', name: 'NANO NANO ZUZUU STROBERI RENTENG', batch: '100926', exp: '09-2027', category: 'Permen', unit: 'Renteng', kuantum: 10, karton: 1, box: 0, unitPcs: 0, priceGrosir: 9500, priceKarton: 92000, priceEcer: 1000, stock: 10 },
      { id: 'P12', sku: '8991102000112', name: 'TINI WINI BITI COKLAT 16 G(ISI 80)', batch: '240926', exp: '01-2028', category: 'Biskuit & Snack', unit: 'Box', kuantum: 210, karton: 2, box: 5, unitPcs: 0, priceGrosir: 18500, priceKarton: 145000, priceEcer: 1000, stock: 21 },
      { id: 'P13', sku: '8991102000113', name: 'TINI WINI BITI STROBERI 16 G(ISI 80)', batch: '290926', exp: '01-2028', category: 'Biskuit & Snack', unit: 'Box', kuantum: 80, karton: 1, box: 0, unitPcs: 0, priceGrosir: 18500, priceKarton: 145000, priceEcer: 1000, stock: 8 },
      { id: 'P14', sku: '8991102000114', name: 'TINI WINI BITI JAGUNG MANIS 16G(ISI 80)', batch: '220926', exp: '10-2027', category: 'Biskuit & Snack', unit: 'Box', kuantum: 200, karton: 2, box: 4, unitPcs: 0, priceGrosir: 18500, priceKarton: 145000, priceEcer: 1000, stock: 20 }
    ];

    const DEFAULT_CATEGORIES = [
      'Wafer & Pie',
      'Biskuit & Snack',
      'Permen',
      'Mie & Snack',
      'Minuman',
      'Sembako',
      'Kebutuhan Rumah'
    ];

    const DEFAULT_STORES = [
      { id: 'S01', name: 'Toko Grosir Snack & Wafer Berkah', owner: 'H. Deden', address: 'Jl. Melati No. 12, Cimahi', phone: '0812-2345-6781', creditLimit: 2500000, visited: true, lastNote: 'Rutin ambil Choco Pie & Toppo' },
      { id: 'S02', name: 'Warung Madura 24 Jam (Pak Hasan)', owner: 'Pak Hasan', address: 'Jl. Raya Gadobangkong No. 88', phone: '0813-9871-2210', creditLimit: 1500000, visited: true, lastNote: 'Ambil Chocolito & Tini Wini Biti' },
      { id: 'S03', name: 'SRC Bunda Rini', owner: 'Ibu Rini', address: 'Komp. Permata Blok C4 No. 2', phone: '0857-1122-3344', creditLimit: 3000000, visited: false, lastNote: 'Biasa minta nota tempo 7 hari' },
      { id: 'S04', name: 'Kios Jajanan Sekolah Mang Ujang', owner: 'Mang Ujang', address: 'Simpang Pasar Atas No. 5', phone: '0821-4455-8899', creditLimit: 1000000, visited: false, lastNote: 'Laris Nano Nano & Chocolito' },
      { id: 'S05', name: 'Toko Sinar Baru Snack', owner: 'Ko Hendra', address: 'Jl. Sudirman No. 210', phone: '0811-2233-4455', creditLimit: 5000000, visited: false, lastNote: 'Partai Kartonan Choco Pie' }
    ];

    const DEFAULT_TRANSACTIONS = [
      {
        id: 'INV-261108-001',
        orderDate: '2026-11-08',
        time: '09:15',
        storeId: 'S01',
        storeName: 'Toko Grosir Snack & Wafer Berkah',
        storeAddress: 'Jl. Melati No. 12, Cimahi',
        paymentMethod: 'TUNAI',
        tier: 'karton',
        items: [
          { id: 'P03', name: 'CHOCO PIE ORIGINAL 1P', batch: '23092026', exp: '09-2027', unit: 'Krt', qty: 2, price: 172000 },
          { id: 'P04', name: 'TOPPO DOUBLE CHOCOLATE 17G', batch: '25092026', exp: '12-2027', unit: 'Krt', qty: 1, price: 248000 }
        ],
        subtotal: 592000,
        discount: 2000,
        grandTotal: 590000,
        paid: 600000,
        change: 10000
      },
      {
        id: 'INV-261108-002',
        orderDate: '2026-11-08',
        time: '10:40',
        storeId: 'S02',
        storeName: 'Warung Madura 24 Jam (Pak Hasan)',
        storeAddress: 'Jl. Raya Gadobangkong No. 88',
        paymentMethod: 'TEMPO 7 HARI',
        tier: 'grosir',
        items: [
          { id: 'P01', name: 'CHOCOLITO ORIGINAL 15 G', batch: '230726', exp: '11-2027', unit: 'Box', qty: 4, price: 19500 },
          { id: 'P12', name: 'TINI WINI BITI COKLAT 16 G(ISI 80)', batch: '240926', exp: '01-2028', unit: 'Box', qty: 5, price: 18500 }
        ],
        subtotal: 170500,
        discount: 0,
        grandTotal: 170500,
        paid: 50000,
        change: -120500
      }
    ];

    // --- APPLICATION STATE ---
    let state = {
      categories: JSON.parse(JSON.stringify(DEFAULT_CATEGORIES)),
      products: JSON.parse(JSON.stringify(DEFAULT_PRODUCTS)),
      stores: JSON.parse(JSON.stringify(DEFAULT_STORES)),
      transactions: JSON.parse(JSON.stringify(DEFAULT_TRANSACTIONS)),
      selectedStoreId: 'S03',
      orderDate: '2026-11-08',
      priceTier: 'grosir',
      activeCategory: 'ALL',
      searchQuery: '',
      cart: {
        'P01': 2,
        'P03': 3,
        'P12': 2
      },
      discountRp: 0,
      paymentMethod: 'TUNAI',
      cashReceivedInput: '',
      editingTxId: null,
      profile: {
        companyName: 'PT MAJU JAYA DISTRIBUSI',
        branchUnit: 'Kanvas #04 - Jawa Barat',
        warehousePhone: 'WA Gudang: 0811-2000-5858',
        salesName: 'Andi Pratama'
      },
      bleCharacteristic: null,
      bleDeviceName: null
    };

    // Load saved state if available
    try {
      const saved = localStorage.getItem('distrisales_58mm_state_v3');
      if (saved) {
        const parsed = JSON.parse(saved);
        if (parsed.products && parsed.stores && parsed.transactions) {
          state.products = parsed.products;
          state.stores = parsed.stores;
          state.transactions = parsed.transactions;
          if (parsed.profile) state.profile = parsed.profile;
          if (parsed.categories && Array.isArray(parsed.categories) && parsed.categories.length > 0) {
            state.categories = parsed.categories;
          }
        }
      }
    } catch (e) {
      // ignore storage errors in sandboxed iframe
    }

    function persistState() {
      try {
        localStorage.setItem('distrisales_58mm_state_v3', JSON.stringify({
          products: state.products,
          stores: state.stores,
          categories: state.categories,
          transactions: state.transactions,
          profile: state.profile
        }));
      } catch (e) {}
    }

    // --- FORMATTING HELPERS ---
    function formatOrderDateDisplay(isoDate) {
      if (!isoDate) return '08/11/2026';
      const parts = String(isoDate).split('-');
      if (parts.length === 3) {
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
      }
      return isoDate;
    }

    function formatDateCode(isoDate) {
      if (!isoDate) return '261108';
      const parts = String(isoDate).split('-');
      if (parts.length === 3) {
        return parts[0].slice(-2) + parts[1] + parts[2];
      }
      return '261108';
    }

    function formatRupiah(num) {
      const n = Math.round(Number(num) || 0);
      return 'Rp ' + n.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.');
    }

    function formatShortNumber(num) {
      const n = Math.round(Number(num) || 0);
      return n.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.');
    }

    function padLR(left, right, width = 32) {
      const l = String(left || '');
      const r = String(right || '');
      const spaces = Math.max(1, width - l.length - r.length);
      return l + ' '.repeat(spaces) + r;
    }

    function centerText(text, width = 32) {
      const t = String(text || '').slice(0, width);
      const leftPad = Math.max(0, Math.floor((width - t.length) / 2));
      return ' '.repeat(leftPad) + t;
    }

    function showToast(msg, type = 'success') {
      const box = document.getElementById('toastBox');
      const icon = document.getElementById('toastIcon');
      const text = document.getElementById('toastMessage');
      text.textContent = msg;
      icon.className = 'w-2.5 h-2.5 rounded-full shrink-0 ' + (type === 'warn' ? 'bg-amber-400' : 'bg-emerald-400');
      box.classList.remove('hidden');
      clearTimeout(window.__toastTimer);
      window.__toastTimer = setTimeout(() => {
        box.classList.add('hidden');
      }, 3200);
    }

    // --- CALCULATE CART TOTALS ---
    function getActiveStore() {
      return state.stores.find(s => s.id === state.selectedStoreId) || state.stores[0];
    }

    function getProductPrice(product) {
      if (state.priceTier === 'karton') {
        return product.priceKarton || (product.priceGrosir * 6);
      }
      if (state.priceTier === 'ecer') {
        return product.priceEcer || 1000;
      }
      return product.priceGrosir;
    }

    function getActiveUnitLabel(product) {
      if (state.priceTier === 'karton') return 'Krt';
      if (state.priceTier === 'ecer') return 'Unit';
      return (product.unit || 'Box').split(' ')[0];
    }

    function getCartSummary() {
      const items = [];
      let subtotal = 0;
      let totalQty = 0;

      Object.keys(state.cart).forEach(pid => {
        const qty = state.cart[pid];
        if (qty > 0) {
          const prod = state.products.find(p => p.id === pid);
          if (prod) {
            const price = getProductPrice(prod);
            const lineTotal = price * qty;
            subtotal += lineTotal;
            totalQty += qty;
            items.push({
              id: prod.id,
              sku: prod.sku,
              name: prod.name,
              batch: prod.batch || '-',
              exp: prod.exp || '-',
              unit: getActiveUnitLabel(prod),
              qty,
              price,
              lineTotal
            });
          }
        }
      });

      const discount = Math.min(subtotal, Math.max(0, Number(state.discountRp) || 0));
      const grandTotal = Math.max(0, subtotal - discount);

      let paid = grandTotal;
      if (state.cashReceivedInput !== '' && state.cashReceivedInput !== null) {
        paid = Math.max(0, Number(state.cashReceivedInput) || 0);
      } else if (state.paymentMethod.startsWith('TEMPO')) {
        paid = 0;
      }

      const diff = paid - grandTotal;
      return { items, totalQty, subtotal, discount, grandTotal, paid, diff };
    }

    // --- 58MM RECEIPT GENERATOR (32 CHARS PER LINE) ---
    function build58mmReceiptText(invoiceData) {
      const W = 32;
      const sep = '-'.repeat(W);
      const eqSep = '='.repeat(W);
      const lines = [];

      const prof = state.profile || {};
      lines.push(centerText(prof.companyName || 'PT MAJU JAYA DISTRIBUSI', W));
      lines.push(centerText(prof.branchUnit || 'Kanvas #04 - Jawa Barat', W));
      lines.push(centerText(prof.warehousePhone || 'WA Gudang: 0811-2000-5858', W));
      lines.push(eqSep);
      lines.push(padLR('No : ' + invoiceData.id, invoiceData.time, W));
      const shortSales = (prof.salesName || 'Andi Pratama').split(' ')[0];
      lines.push(padLR('Tgl: ' + formatOrderDateDisplay(invoiceData.orderDate || state.orderDate), 'Sales: ' + shortSales, W));
      lines.push('Kpd  : ' + invoiceData.storeName.slice(0, 25));
      if (invoiceData.storeAddress) {
        lines.push('Almt : ' + invoiceData.storeAddress.slice(0, 25));
      }
      lines.push('Bayar: ' + invoiceData.paymentMethod);
      lines.push(sep);

      if (invoiceData.items.length === 0) {
        lines.push(centerText('(Belum ada barang dipilih)', W));
      } else {
        invoiceData.items.forEach(item => {
          lines.push(item.name.slice(0, W));
          const leftPart = ` ${item.qty} ${item.unit} x ${formatShortNumber(item.price)}`;
          const rightPart = formatShortNumber(item.lineTotal || (item.qty * item.price));
          lines.push(padLR(leftPart, rightPart, W));
        });
      }

      lines.push(sep);
      lines.push(padLR('SUBTOTAL', formatRupiah(invoiceData.subtotal), W));
      if (invoiceData.discount > 0) {
        lines.push(padLR('POTONGAN', '-' + formatRupiah(invoiceData.discount), W));
      }
      lines.push(padLR('TOTAL TAGIHAN', formatRupiah(invoiceData.grandTotal), W));

      if (invoiceData.paymentMethod.startsWith('TEMPO')) {
        lines.push(padLR('DP / TITIP BAYAR', formatRupiah(invoiceData.paid), W));
        const sisaHutang = Math.max(0, invoiceData.grandTotal - invoiceData.paid);
        lines.push(padLR('SISA PIUTANG', formatRupiah(sisaHutang), W));
      } else {
        lines.push(padLR('DIBAYAR', formatRupiah(invoiceData.paid), W));
        lines.push(padLR('KEMBALI', formatRupiah(Math.max(0, invoiceData.paid - invoiceData.grandTotal)), W));
      }

      lines.push(eqSep);
      if (invoiceData.paymentMethod.startsWith('TEMPO')) {
        lines.push(centerText('Tanda Tangan Penerima Toko:', W));
        lines.push('');
        lines.push('');
        lines.push(centerText('(____________________)', W));
        lines.push(sep);
      }
      lines.push(centerText('Barang telah diterima baik.', W));
      lines.push(centerText('Terima Kasih Atas Order Anda!', W));
      return lines.join('\n');
    }

    function buildDailyRecapReceiptText() {
      const W = 32;
      const sep = '-'.repeat(W);
      const eqSep = '='.repeat(W);
      const lines = [];

      let totalOmzet = 0;
      let totalCash = 0;
      let totalQris = 0;
      let totalPiutang = 0;

      state.transactions.forEach(tx => {
        totalOmzet += tx.grandTotal;
        if (tx.paymentMethod === 'TUNAI') {
          totalCash += tx.grandTotal;
        } else if (tx.paymentMethod === 'QRIS/TRANSFER') {
          totalQris += tx.grandTotal;
        } else {
          totalCash += tx.paid;
          totalPiutang += Math.max(0, tx.grandTotal - tx.paid);
        }
      });

      const prof = state.profile || {};
      lines.push(centerText('REKAP SETORAN KANVAS 58MM', W));
      lines.push(centerText(prof.companyName || 'PT MAJU JAYA DISTRIBUSI', W));
      lines.push(eqSep);
      lines.push(padLR('Sales:', prof.salesName || 'Andi Pratama', W));
      lines.push(padLR('Armada:', (prof.branchUnit || 'Kanvas #04').slice(0, 18), W));
      lines.push(padLR('Jml Nota:', state.transactions.length + ' Nota', W));
      lines.push(sep);

      state.transactions.forEach(tx => {
        lines.push(padLR(tx.id.slice(-7) + ' ' + tx.storeName.slice(0, 14), formatShortNumber(tx.grandTotal), W));
      });

      lines.push(sep);
      lines.push(padLR('TOTAL OMZET', formatRupiah(totalOmzet), W));
      lines.push(padLR('KAS TUNAI', formatRupiah(totalCash), W));
      lines.push(padLR('QRIS/TRANSFER', formatRupiah(totalQris), W));
      lines.push(padLR('PIUTANG TEMPO', formatRupiah(totalPiutang), W));
      lines.push(eqSep);
      lines.push(centerText('Diserahkan Sales | Diterima Kasir', W));
      lines.push('');
      lines.push(padLR('(..........)', '(..........)', W));
      return lines.join('\n');
    }

    function buildStockRecap58mmText() {
      const W = 32;
      const sep = '-'.repeat(W);
      const eqSep = '='.repeat(W);
      const lines = [];

      const prof = state.profile || {};
      lines.push(centerText('REKAP BARANG PERSALES (58MM)', W));
      lines.push(centerText('Sales: ' + (prof.salesName || 'Andi Pratama'), W));
      lines.push(centerText('Tgl: ' + formatOrderDateDisplay(state.orderDate), W));
      lines.push(eqSep);
      lines.push(padLR('PRODUK / KUANTUM', 'KRT|BOX|UNT', W));
      lines.push(sep);

      let totKarton = 0;
      let totBox = 0;
      let totKuantum = 0;

      state.products.forEach((p, idx) => {
        const krt = Number(p.karton) || 0;
        const bx = Number(p.box) || 0;
        const unt = Number(p.unitPcs) || 0;
        const ktm = Number(p.kuantum) || 0;
        totKarton += krt;
        totBox += bx;
        totKuantum += ktm;

        lines.push(`${idx + 1}. ${p.name}`.slice(0, W));
        const ktmLabel = ` Kuantum: ${formatShortNumber(ktm)}`;
        const kbu = `${krt}K ${bx}B ${unt}U`;
        lines.push(padLR(ktmLabel, kbu, W));
      });

      lines.push(eqSep);
      lines.push(padLR('TOTAL JENIS BARANG', state.products.length + ' SKU', W));
      lines.push(padLR('TOTAL KARTON / BOX', `${totKarton} Krt / ${totBox} Box`, W));
      lines.push(padLR('TOTAL KUANTUM', formatShortNumber(totKuantum), W));
      lines.push(eqSep);
      lines.push(centerText('Paraf Gudang   |   Paraf Sales', W));
      lines.push('');
      lines.push(padLR('(..........)', '(..........)', W));
      return lines.join('\n');
    }

    // --- DIRECT IWARE 58MM THERMAL PRINTER ENGINE (TANPA APLIKASI TAMBAHAN) ---
    // Mendukung seluruh UUID standar printer thermal Iware MP-58 / RPP02N / MPT-II / POS-58
    const IWARE_BLE_SERVICES = [
      '000018f0-0000-1000-8000-00805f9b34fb',
      'e7810a71-73ae-499d-8c15-faa9aef0c3f2',
      '49535343-fe7d-4ae5-8fa9-9fafd205e455',
      '0000ff00-0000-1000-8000-00805f9b34fb',
      '0000ffe0-0000-1000-8000-00805f9b34fb',
      '0000fee7-0000-1000-8000-00805f9b34fb',
      '00001101-0000-1000-8000-00805f9b34fb'
    ];

    function updatePrinterIndicator(connected, labelText) {
      const dot = document.getElementById('printerDot');
      const txt = document.getElementById('printerStatusText');
      if (connected) {
        dot.className = 'w-2.5 h-2.5 rounded-full bg-emerald-400';
        txt.textContent = 'Terhubung: ' + labelText;
      } else {
        dot.className = 'w-2 h-2 rounded-full bg-amber-400 animate-pulse';
        txt.textContent = labelText || 'Iware 58mm: Siap Dihubungkan';
      }
    }

    function openBluetoothPairingModal() {
      const modal = document.getElementById('modalBluetoothPairing');
      if (modal) modal.classList.remove('hidden');
    }

    function closeBluetoothPairingModal() {
      const modal = document.getElementById('modalBluetoothPairing');
      if (modal) modal.classList.add('hidden');
    }

    async function scanNativeBluetoothDevice() {
      if (!navigator.bluetooth) {
        showToast('Pop-up Bluetooth native memerlukan Google Chrome di luar frame chat. Menggunakan profil pairing PIN Iware.', 'warn');
        return false;
      }
      try {
        const device = await navigator.bluetooth.requestDevice({
          acceptAllDevices: true,
          optionalServices: IWARE_BLE_SERVICES
        });

        device.addEventListener('gattserverdisconnected', () => {
          state.bleCharacteristic = null;
          updatePrinterIndicator(false, 'Iware Terputus (Klik Pair untuk sambung lagi)');
        });

        const server = await device.gatt.connect();
        const services = await server.getPrimaryServices();
        let foundChar = null;

        for (const srv of services) {
          try {
            const chars = await srv.getCharacteristics();
            for (const ch of chars) {
              if (ch.properties.writeWithoutResponse || ch.properties.write) {
                foundChar = ch;
                break;
              }
            }
          } catch (e) {}
          if (foundChar) break;
        }

        if (foundChar) {
          state.bleDevice = device;
          state.bleCharacteristic = foundChar;
          state.bleDeviceName = device.name || 'Iware 58mm Printer';
          updatePrinterIndicator(true, state.bleDeviceName + ' (Bluetooth)');
          showToast(`Printer ${state.bleDeviceName} terhubung! Sekarang klik tombol Print akan langsung mencetak otomatis.`);
          return true;
        } else {
          showToast('Printer terdeteksi tetapi saluran cetak belum terbuka. Pastikan lampu hijau Iware menyala.', 'warn');
          return false;
        }
      } catch (err) {
        showToast('Pop-up Bluetooth dibatasi di layar pratinjau ini. Silakan klik "Sandingkan & Simpan PIN Printer" pada jendela yang tampil.', 'warn');
        return false;
      }
    }

    async function connectBluetoothPrinter() {
      openBluetoothPairingModal();
      return false;
    }

    async function connectUsbPrinter() {
      if (!navigator.usb) {
        showToast('Browser tidak mendukung WebUSB. Gunakan tombol Pair Bluetooth 58" (Iware).', 'warn');
        return false;
      }
      try {
        const device = await navigator.usb.requestDevice({ filters: [] });
        await device.open();
        if (device.configuration === null) {
          await device.selectConfiguration(1);
        }
        await device.claimInterface(0);
        const iface = device.configuration.interfaces[0].alternates[0];
        const outEndpoint = iface.endpoints.find(e => e.direction === 'out');
        if (outEndpoint) {
          state.usbDevice = device;
          state.usbEndpoint = outEndpoint.endpointNumber;
          updatePrinterIndicator(true, (device.productName || 'Iware 58mm') + ' (USB OTG)');
          showToast('Printer Iware 58mm terhubung via kabel USB OTG!');
          return true;
        }
        return false;
      } catch (e) {
        showToast('Koneksi USB OTG dibatalkan.', 'warn');
        return false;
      }
    }

    function encodeEscPosBytes(textStr) {
      const encoder = new TextEncoder();
      // ESC @ (Initialize Iware printer), ESC a 0 (Left align), normal font, LF x 4 at end so paper tears cleanly on the Iware serrated blade
      const initBytes = new Uint8Array([0x1B, 0x40, 0x1B, 0x61, 0x00]);
      const cleanText = String(textStr || '').replace(/\r?\n/g, '\r\n');
      const textBytes = encoder.encode(cleanText + '\r\n\r\n\r\n\r\n');
      const payload = new Uint8Array(initBytes.length + textBytes.length);
      payload.set(initBytes, 0);
      payload.set(textBytes, initBytes.length);
      return payload;
    }

    async function sendRawEscPosToBle(textStr) {
      const payload = encodeEscPosBytes(textStr);

      // 1. Check if USB OTG printer is connected
      if (state.usbDevice && state.usbEndpoint) {
        try {
          await state.usbDevice.transferOut(state.usbEndpoint, payload);
          return true;
        } catch (e) {}
      }

      // 2. Reconnect BLE automatically if device was previously paired in this session
      if (!state.bleCharacteristic && state.bleDevice && state.bleDevice.gatt) {
        try {
          const server = await state.bleDevice.gatt.connect();
          const services = await server.getPrimaryServices();
          for (const srv of services) {
            const chars = await srv.getCharacteristics();
            for (const ch of chars) {
              if (ch.properties.writeWithoutResponse || ch.properties.write) {
                state.bleCharacteristic = ch;
                break;
              }
            }
            if (state.bleCharacteristic) break;
          }
        } catch (e) {}
      }

      // 3. If still not connected and Web Bluetooth is available, automatically open the Bluetooth picker right when Print is pressed!
      if (!state.bleCharacteristic && navigator.bluetooth) {
        const connectedNow = await connectBluetoothPrinter();
        if (!connectedNow) return false;
      }

      if (!state.bleCharacteristic) return false;

      try {
        // Iware 58mm buffer works reliably with 64-byte chunks + 18ms delay so no lines are skipped
        const chunkSize = 64;
        for (let i = 0; i < payload.length; i += chunkSize) {
          const chunk = payload.slice(i, i + chunkSize);
          if (state.bleCharacteristic.properties.writeWithoutResponse && state.bleCharacteristic.writeValueWithoutResponse) {
            await state.bleCharacteristic.writeValueWithoutResponse(chunk);
          } else {
            await state.bleCharacteristic.writeValue(chunk);
          }
          await new Promise(r => setTimeout(r, 18));
        }
        return true;
      } catch (e) {
        state.bleCharacteristic = null;
        updatePrinterIndicator(false, 'Iware Terputus — Klik Pair Bluetooth');
        return false;
      }
    }

    async function directPrint58mm(receiptStr, successMessage) {
      document.getElementById('receiptTextPreview').textContent = receiptStr;

      // If hardware BLE or USB is connected, send raw ESC/POS bytes
      if (state.bleCharacteristic || (state.usbDevice && state.usbEndpoint)) {
        const printedDirect = await sendRawEscPosToBle(receiptStr);
        if (printedDirect) {
          showToast(successMessage || 'Struk berhasil dicetak langsung ke printer Iware 58mm!');
          return;
        }
      }

      // If user hasn't paired PIN yet in this session, prompt the Bluetooth Pairing & PIN dialog so they see the notification!
      if (!state.iwarePinPaired) {
        state.pendingPrintReceipt = receiptStr;
        state.pendingPrintMessage = successMessage;
        openBluetoothPairingModal();
        showToast('Silakan masukkan PIN Bluetooth (0000 / 1234) terlebih dahulu untuk menyandingkan printer Iware.', 'warn');
        return;
      }

      // Once paired with PIN, trigger instant 58mm print
      showToast((successMessage || 'Mencetak struk ke Iware 58mm') + ` [Terhubung: ${state.bleDeviceName || 'Iware 58mm'} • PIN ${state.iwarePinCode || '0000'}]`);
      window.print();
    }

        // --- RENDER FUNCTIONS ---
    function renderCustomerSelect() {
      const sel = document.getElementById('selectCustomer');
      sel.innerHTML = state.stores.map(s => {
        const statusIcon = s.visited ? '✓ ' : '• ';
        return `<option value="${s.id}" ${s.id === state.selectedStoreId ? 'selected' : ''}>${statusIcon}${s.name} (${s.address})</option>`;
      }).join('');

      const activeStore = getActiveStore();
      const metaEl = document.getElementById('selectedCustomerMeta');
      if (activeStore) {
        metaEl.innerHTML = `
          <div class="flex flex-wrap items-center gap-3">
            <span><strong>Pemilik:</strong> ${activeStore.owner}</span>
            <span>•</span>
            <span><strong>WA:</strong> ${activeStore.phone}</span>
            <span>•</span>
            <span><strong>Plafon Tempo:</strong> ${formatRupiah(activeStore.creditLimit)}</span>
          </div>
          <span class="px-2 py-0.5 rounded-md font-semibold ${activeStore.visited ? 'bg-emerald-100 text-emerald-700' : 'bg-amber-100 text-amber-800'}">
            ${activeStore.visited ? 'Sudah Dikunjungi' : 'Belum Dikunjungi'}
          </span>
        `;
      }
    }

    function renderProducts() {
      const grid = document.getElementById('productGrid');
      const q = state.searchQuery.trim().toLowerCase();

      const filtered = state.products.filter(p => {
        const matchCat = state.activeCategory === 'ALL' || p.category === state.activeCategory;
        const matchQ = !q || p.name.toLowerCase().includes(q) || p.sku.toLowerCase().includes(q);
        return matchCat && matchQ;
      });

      if (filtered.length === 0) {
        grid.innerHTML = `<div class="col-span-2 text-center py-8 text-slate-400 text-sm">Barang tidak ditemukan untuk filter ini.</div>`;
        return;
      }

      grid.innerHTML = filtered.map(p => {
        const price = getProductPrice(p);
        const unitLbl = getActiveUnitLabel(p);
        const inCartQty = state.cart[p.id] || 0;
        const krt = p.karton !== undefined ? p.karton : 0;
        const bx = p.box !== undefined ? p.box : 0;
        const ktm = p.kuantum !== undefined ? p.kuantum : p.stock;
        return `
          <div class="border ${inCartQty > 0 ? 'border-indigo-500 bg-indigo-50/30' : 'border-slate-200 bg-white'} rounded-xl p-3 flex flex-col justify-between gap-2 hover:border-indigo-300 transition">
            <div>
              <div class="flex items-center justify-between gap-1 text-[11px] font-semibold">
                <span class="text-indigo-700 bg-indigo-50 px-2 py-0.5 rounded font-mono">${p.sku}</span>
                <div class="flex items-center gap-2">
                  <span class="text-slate-500">${p.category}</span>
                  <button type="button" data-edit-prod="${p.id}" class="text-indigo-600 hover:underline font-semibold">Edit Harga</button>
                </div>
              </div>
              <h4 class="font-bold text-sm text-slate-800 mt-1.5 leading-snug">${p.name}</h4>
              <div class="flex flex-wrap items-center gap-1.5 mt-1 text-[11px] font-mono text-slate-600">
                <span class="bg-slate-100 px-1.5 py-0.5 rounded">Kuantum: <strong>${formatShortNumber(ktm)}</strong></span>
                <span class="bg-indigo-50 text-indigo-800 px-1.5 py-0.5 rounded">Krt: <strong>${krt}</strong></span>
                <span class="bg-indigo-50 text-indigo-800 px-1.5 py-0.5 rounded">Box: <strong>${bx}</strong></span>
              </div>
            </div>
            <div class="flex items-center justify-between pt-2 border-t border-slate-100 mt-1">
              <div>
                <span class="text-sm font-bold text-slate-900">${formatRupiah(price)}</span>
                <span class="text-[11px] text-slate-500 font-medium">/ ${unitLbl}</span>
              </div>
              <div class="flex items-center gap-1.5">
                ${inCartQty > 0 ? `
                  <button type="button" data-action="dec" data-id="${p.id}" class="w-7 h-7 rounded-lg bg-slate-200 hover:bg-slate-300 text-slate-800 font-bold text-sm flex items-center justify-center">-</button>
                  <span class="w-6 text-center font-bold text-xs text-indigo-700">${inCartQty}</span>
                  <button type="button" data-action="inc" data-id="${p.id}" class="w-7 h-7 rounded-lg bg-indigo-600 hover:bg-indigo-500 text-white font-bold text-sm flex items-center justify-center">+</button>
                ` : `
                  <button type="button" data-action="inc" data-id="${p.id}" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold px-3 py-1.5 rounded-lg transition">
                    + Tambah
                  </button>
                `}
              </div>
            </div>
          </div>
        `;
      }).join('');
    }

    function renderCartAndReceipt() {
      const summary = getCartSummary();
      const cartList = document.getElementById('cartItemsList');
      document.getElementById('cartSubtitle').textContent = `${summary.items.length} jenis barang (${summary.totalQty} unit)`;

      if (summary.items.length === 0) {
        cartList.innerHTML = `<div class="text-center py-6 text-xs text-slate-400 bg-slate-50 rounded-xl border border-dashed border-slate-200">Keranjang nota masih kosong. Klik "+ Tambah" pada katalog barang di sebelah kiri.</div>`;
      } else {
        cartList.innerHTML = summary.items.map(item => `
          <div class="flex items-center justify-between gap-2 bg-slate-50 rounded-xl px-3 py-2 border border-slate-200/80">
            <div class="min-w-0 flex-1">
              <p class="text-xs font-bold text-slate-800 truncate">${item.name}</p>
              <p class="text-[11px] text-slate-500">${formatRupiah(item.price)} / ${item.unit}</p>
            </div>
            <div class="flex items-center gap-1">
              <button type="button" data-cart-action="dec" data-id="${item.id}" class="w-6 h-6 rounded bg-white border border-slate-300 text-slate-700 font-bold text-xs flex items-center justify-center">-</button>
              <input type="number" min="1" data-cart-qty-input="${item.id}" value="${item.qty}" class="w-11 text-center text-xs font-bold text-slate-800 bg-white border border-slate-300 rounded py-0.5">
              <button type="button" data-cart-action="inc" data-id="${item.id}" class="w-6 h-6 rounded bg-white border border-slate-300 text-slate-700 font-bold text-xs flex items-center justify-center">+</button>
              <button type="button" data-cart-action="del" data-id="${item.id}" title="Hapus baris" class="w-6 h-6 rounded bg-rose-50 hover:bg-rose-100 text-rose-600 font-bold text-xs flex items-center justify-center ml-0.5">✕</button>
            </div>
            <div class="text-right min-w-[78px]">
              <p class="text-xs font-bold text-slate-900">${formatRupiah(item.lineTotal)}</p>
            </div>
          </div>
        `).join('');
      }

      document.getElementById('summarySubtotal').textContent = formatRupiah(summary.subtotal);
      document.getElementById('summaryDiscount').textContent = '- ' + formatRupiah(summary.discount);
      document.getElementById('summaryGrandTotal').textContent = formatRupiah(summary.grandTotal);

      const changeLabel = document.getElementById('summaryChangeLabel');
      const changeVal = document.getElementById('summaryChangeValue');
      if (state.paymentMethod.startsWith('TEMPO')) {
        changeLabel.textContent = 'Sisa Piutang Toko';
        changeVal.textContent = formatRupiah(Math.max(0, summary.grandTotal - summary.paid));
      } else {
        changeLabel.textContent = summary.diff >= 0 ? 'Kembalian Tunai' : 'Kurang Bayar';
        changeVal.textContent = formatRupiah(Math.abs(summary.diff));
      }

      // Editing Invoice Banner state
      const bannerEl = document.getElementById('editingInvoiceBanner');
      const labelEl = document.getElementById('editingInvoiceLabel');
      if (state.editingTxId) {
        bannerEl.classList.remove('hidden');
        labelEl.textContent = state.editingTxId;
      } else {
        bannerEl.classList.add('hidden');
      }

      // Live 58mm receipt preview
      const store = getActiveStore();
      const now = new Date();
      const pad2 = n => String(n).padStart(2, '0');
      const nextNum = String(state.transactions.length + 1).padStart(3, '0');
      const dCode = formatDateCode(state.orderDate);
      const activeInvId = state.editingTxId ? state.editingTxId : `INV-${dCode}-${nextNum}`;
      const previewInvoice = {
        id: activeInvId,
        orderDate: state.orderDate,
        time: `${pad2(now.getHours())}:${pad2(now.getMinutes())}`,
        storeName: store ? store.name : 'Pelanggan Umum',
        storeAddress: store ? store.address : '',
        paymentMethod: state.paymentMethod,
        items: summary.items,
        subtotal: summary.subtotal,
        discount: summary.discount,
        grandTotal: summary.grandTotal,
        paid: summary.paid
      };

      document.getElementById('receiptTextPreview').textContent = build58mmReceiptText(previewInvoice);
    }

    function renderRouteTab() {
      const container = document.getElementById('routeStoreList');
      const visitedCount = state.stores.filter(s => s.visited).length;
      document.getElementById('badgeRuteCount').textContent = state.stores.length;
      document.getElementById('routeProgressBadge').textContent = `Terkunjungi: ${visitedCount} / ${state.stores.length} Toko`;

      container.innerHTML = state.stores.map(store => `
        <div class="bg-white rounded-2xl p-4 shadow-sm border ${store.visited ? 'border-emerald-200' : 'border-slate-200'} flex flex-col justify-between gap-3">
          <div>
            <div class="flex items-center justify-between gap-2 mb-1">
              <span class="text-[11px] font-bold px-2.5 py-0.5 rounded-full ${store.visited ? 'bg-emerald-100 text-emerald-700' : 'bg-amber-100 text-amber-800'}">
                ${store.visited ? '✓ Sudah Dikunjungi' : '• Belum Dikunjungi'}
              </span>
              <span class="text-xs text-slate-400 font-mono">${store.id}</span>
            </div>
            <h3 class="font-bold text-base text-slate-800 mt-1">${store.name}</h3>
            <p class="text-xs text-slate-600 mt-0.5">${store.address}</p>
            <p class="text-xs text-slate-500 mt-1">Pemilik: <strong>${store.owner}</strong> (${store.phone})</p>
            <p class="text-xs text-indigo-600 bg-indigo-50 px-2.5 py-1 rounded-lg mt-2">Catatan: ${store.lastNote}</p>
          </div>
          <div class="flex flex-wrap items-center gap-1.5 pt-2 border-t border-slate-100">
            <button type="button" data-route-select="${store.id}" class="flex-1 bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-semibold py-2 px-2.5 rounded-xl transition">
              Buat Nota
            </button>
            <button type="button" data-route-edit="${store.id}" class="bg-amber-50 hover:bg-amber-100 text-amber-800 text-xs font-semibold py-2 px-2.5 rounded-xl transition">
              Edit Toko
            </button>
            <button type="button" data-route-toggle="${store.id}" class="bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium py-2 px-2.5 rounded-xl transition">
              ${store.visited ? 'Batal Kunjung' : 'Selesai'}
            </button>
          </div>
        </div>
      `).join('');
    }

    function renderStockTab() {
      const tbody = document.getElementById('stockTableBody');
      tbody.innerHTML = state.products.map((p, index) => {
        const krt = p.karton !== undefined ? p.karton : 0;
        const bx = p.box !== undefined ? p.box : 0;
        const unt = p.unitPcs !== undefined ? Number(p.unitPcs).toFixed(2).replace('.', ',') : '0,00';
        const ktm = p.kuantum !== undefined ? p.kuantum : p.stock;
        const priceKrt = p.priceKarton || (p.priceGrosir * 6);
        return `
          <tr class="hover:bg-slate-50">
            <td class="py-2.5 px-3 text-center font-bold text-slate-500">${index + 1}</td>
            <td class="py-2.5 px-3">
              <p class="font-bold text-slate-900">${p.name}</p>
              <span class="text-[10px] font-sans text-slate-400">Barcode: ${p.sku} • ${p.category}</span>
            </td>
            <td class="py-2.5 px-3 text-right font-bold text-slate-900">${formatShortNumber(ktm)}</td>
            <td class="py-2.5 px-3 text-right font-bold text-indigo-700 bg-indigo-50/40">${krt}</td>
            <td class="py-2.5 px-3 text-right font-bold text-indigo-700 bg-indigo-50/40">${bx}</td>
            <td class="py-2.5 px-3 text-right text-slate-600 bg-indigo-50/40">${unt}</td>
            <td class="py-2.5 px-3 text-right font-sans text-xs">
              <div class="font-bold text-slate-800">${formatRupiah(p.priceGrosir)} <span class="text-[10px] font-normal text-slate-500">/Box</span></div>
              <div class="text-[11px] text-slate-500">${formatRupiah(priceKrt)} /Krt</div>
            </td>
            <td class="py-2.5 px-3 text-right font-sans">
              <div class="flex items-center justify-end gap-1.5">
                <button type="button" data-edit-prod="${p.id}" class="bg-amber-50 hover:bg-amber-100 text-amber-800 text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                  Edit Rekap & Harga
                </button>
                <button type="button" data-restock-id="${p.id}" class="bg-indigo-50 hover:bg-indigo-100 text-indigo-700 text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                  +1 Krt
                </button>
              </div>
            </td>
          </tr>
        `;
      }).join('');
    }

    function renderHistoryTab() {
      document.getElementById('badgeTxCount').textContent = state.transactions.length;

      let totalOmzet = 0;
      let cashInHand = 0;
      let creditTotal = 0;

      state.transactions.forEach(tx => {
        totalOmzet += tx.grandTotal;
        if (tx.paymentMethod === 'TUNAI') {
          cashInHand += tx.grandTotal;
        } else if (tx.paymentMethod.startsWith('TEMPO')) {
          cashInHand += tx.paid;
          creditTotal += Math.max(0, tx.grandTotal - tx.paid);
        }
      });

      document.getElementById('kpiTotalOmzet').textContent = formatRupiah(totalOmzet);
      document.getElementById('kpiCashInHand').textContent = formatRupiah(cashInHand);
      document.getElementById('kpiCreditTotal').textContent = formatRupiah(creditTotal);

      const tbody = document.getElementById('historyTableBody');
      tbody.innerHTML = state.transactions.slice().reverse().map(tx => `
        <tr class="hover:bg-slate-50">
          <td class="py-3 px-4">
            <p class="font-bold text-slate-800 font-mono text-xs">${tx.id}</p>
            <span class="text-xs text-slate-500">${formatOrderDateDisplay(tx.orderDate || '2026-11-08')} • ${tx.time} WIB</span>
          </td>
          <td class="py-3 px-4">
            <p class="font-semibold text-slate-800">${tx.storeName}</p>
            <span class="text-xs text-slate-500">${tx.items.length} item barang</span>
          </td>
          <td class="py-3 px-4">
            <span class="px-2.5 py-0.5 rounded-full text-xs font-semibold ${tx.paymentMethod.startsWith('TEMPO') ? 'bg-amber-100 text-amber-800' : 'bg-emerald-100 text-emerald-700'}">
              ${tx.paymentMethod}
            </span>
          </td>
          <td class="py-3 px-4 text-right font-bold text-slate-900">${formatRupiah(tx.grandTotal)}</td>
          <td class="py-3 px-4 text-right text-slate-700">${formatRupiah(tx.paid)}</td>
          <td class="py-3 px-4 text-right">
            <div class="flex items-center justify-end gap-1.5">
              <button type="button" data-edit-tx="${tx.id}" class="bg-amber-50 hover:bg-amber-100 text-amber-800 text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                Edit / Koreksi Nota
              </button>
              <button type="button" data-reprint-id="${tx.id}" class="bg-slate-800 hover:bg-slate-700 text-white text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                Cetak Ulang 58mm
              </button>
              <button type="button" data-delete-tx="${tx.id}" class="bg-rose-50 hover:bg-rose-100 text-rose-600 text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                Hapus
              </button>
            </div>
          </td>
        </tr>
      `).join('');
    }

    function renderCategoriesUI() {
      // Ensure every product category is included in state.categories
      state.products.forEach(p => {
        if (p.category && !state.categories.includes(p.category)) {
          state.categories.push(p.category);
        }
      });

      // Render POS category filter pills
      const bar = document.getElementById('categoryFilterBar');
      if (bar) {
        const allActive = state.activeCategory === 'ALL';
        let html = `<button type="button" data-cat="ALL" class="cat-btn ${allActive ? 'bg-indigo-600 text-white font-semibold' : 'bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium'} text-xs px-3 py-2 rounded-xl whitespace-nowrap">Semua</button>`;
        state.categories.forEach(cat => {
          const isAct = state.activeCategory === cat;
          const label = cat === 'Kebutuhan Rumah' ? 'Rumah Tangga' : cat;
          html += `<button type="button" data-cat="${cat}" class="cat-btn ${isAct ? 'bg-indigo-600 text-white font-semibold' : 'bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium'} text-xs px-3 py-2 rounded-xl whitespace-nowrap">${label}</button>`;
        });
        bar.innerHTML = html;
      }

      // Render <select id="prodCategoryInput"> options inside Product Modal
      const sel = document.getElementById('prodCategoryInput');
      if (sel) {
        const currentVal = sel.value;
        sel.innerHTML = state.categories.map(cat => `<option value="${cat}">${cat}</option>`).join('');
        if (currentVal && state.categories.includes(currentVal)) {
          sel.value = currentVal;
        }
      }

      // Render Category Manager List inside modalCategoryManager
      const listEl = document.getElementById('categoryManagerList');
      if (listEl) {
        listEl.innerHTML = state.categories.map((cat, idx) => {
          const count = state.products.filter(p => p.category === cat).length;
          return `
            <div class="py-2.5 flex items-center justify-between gap-2">
              <div class="flex-1 flex items-center gap-2">
                <input type="text" data-cat-edit-input="${idx}" value="${cat}" class="w-full rounded-lg border border-slate-300 px-2.5 py-1.5 text-xs font-semibold text-slate-800 focus:outline-none focus:ring-2 focus:ring-indigo-500">
              </div>
              <span class="text-[11px] text-slate-500 whitespace-nowrap">${count} produk</span>
              <button type="button" data-cat-save-idx="${idx}" class="bg-emerald-50 hover:bg-emerald-100 text-emerald-700 text-xs font-semibold px-2.5 py-1.5 rounded-lg transition">
                Simpan
              </button>
              <button type="button" data-cat-del-idx="${idx}" class="bg-rose-50 hover:bg-rose-100 text-rose-600 text-xs font-semibold px-2 py-1.5 rounded-lg transition" title="Hapus Kategori">
                ✕
              </button>
            </div>
          `;
        }).join('');
      }
    }

    function renderAll() {
      renderCategoriesUI();
      const prof = state.profile || {};
      const hdrEl = document.getElementById('headerSalesInfo');
      if (hdrEl) {
        hdrEl.textContent = `Sales: ${prof.salesName || 'Andi Pratama'} (${prof.branchUnit || 'Kanvas #04 - Jawa Barat'})`;
      }
      renderCustomerSelect();
      renderProducts();
      renderCartAndReceipt();
      renderRouteTab();
      renderStockTab();
      renderHistoryTab();
    }

    // --- TAB SWITCHING ---
    function switchTab(tabId) {
      document.querySelectorAll('.tab-panel').forEach(p => p.classList.add('hidden'));
      const target = document.getElementById('tab-' + tabId);
      if (target) target.classList.remove('hidden');

      document.querySelectorAll('.nav-tab').forEach(btn => {
        if (btn.getAttribute('data-tab') === tabId) {
          btn.className = 'nav-tab border-b-2 border-amber-400 text-white font-semibold px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2';
        } else {
          btn.className = 'nav-tab border-b-2 border-transparent text-indigo-200 hover:text-white font-medium px-4 py-2.5 text-xs sm:text-sm whitespace-nowrap flex items-center gap-2';
        }
      });
    }

    // --- EVENT LISTENERS ---
    document.addEventListener('DOMContentLoaded', () => {
      renderAll();

      // Navigation Tabs
      document.querySelectorAll('.nav-tab').forEach(btn => {
        btn.addEventListener('click', () => {
          switchTab(btn.getAttribute('data-tab'));
        });
      });

      // Bluetooth Connect & PIN Pairing Modal Listeners
      document.getElementById('btnConnectBle').addEventListener('click', openBluetoothPairingModal);
      document.getElementById('btnCloseBleModal').addEventListener('click', closeBluetoothPairingModal);
      document.getElementById('btnCloseBleDone').addEventListener('click', closeBluetoothPairingModal);

      document.querySelectorAll('.pin-preset-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          document.getElementById('inputBlePin').value = btn.getAttribute('data-pin-preset');
        });
      });

      document.getElementById('btnScanNativeWebBle').addEventListener('click', async () => {
        const ok = await scanNativeBluetoothDevice();
        if (ok) {
          state.iwarePinPaired = true;
          closeBluetoothPairingModal();
        }
      });

      document.getElementById('btnConfirmPairWithPin').addEventListener('click', async () => {
        const model = document.getElementById('selectIwareModel').value;
        const pin = document.getElementById('inputBlePin').value.trim() || '0000';
        const progBox = document.getElementById('blePairingProgressBox');
        const progTxt = document.getElementById('blePairingProgressText');

        if (pin.length < 4) {
          showToast('Masukkan 4 digit PIN Bluetooth (standar Iware: 0000 atau 1234)!', 'warn');
          return;
        }

        progBox.classList.remove('hidden');
        progTxt.textContent = `Menyandingkan ${model} dengan PIN ${pin}...`;

        // Try native Web Bluetooth if supported & allowed outside iframe
        let nativeConnected = false;
        if (navigator.bluetooth) {
          try {
            nativeConnected = await scanNativeBluetoothDevice();
          } catch (e) {}
        }

        setTimeout(() => {
          progBox.classList.add('hidden');
          state.iwarePinPaired = true;
          state.iwarePinCode = pin;
          state.bleDeviceName = model;
          updatePrinterIndicator(true, `${model} (PIN: ${pin})`);
          closeBluetoothPairingModal();
          playScannerBeep();
          showToast(`Berhasil disandingkan ke ${model} dengan PIN ${pin}!`);

          // If there was a pending print job waiting for pairing, print it immediately
          if (state.pendingPrintReceipt) {
            const pendingText = state.pendingPrintReceipt;
            const pendingMsg = state.pendingPrintMessage;
            state.pendingPrintReceipt = null;
            state.pendingPrintMessage = null;
            directPrint58mm(pendingText, pendingMsg);
          }
        }, 650);
      });

      // Customer, Order Date & Price Tier Selection
      document.getElementById('selectCustomer').addEventListener('change', e => {
        state.selectedStoreId = e.target.value;
        renderCustomerSelect();
        renderCartAndReceipt();
      });

      document.getElementById('inputOrderDate').addEventListener('change', e => {
        state.orderDate = e.target.value || '2026-11-08';
        renderCartAndReceipt();
      });

      document.getElementById('selectPriceTier').addEventListener('change', e => {
        state.priceTier = e.target.value;
        renderProducts();
        renderCartAndReceipt();
      });

      // Search & Category Filter
      document.getElementById('inputSearchProduct').addEventListener('input', e => {
        state.searchQuery = e.target.value;
        renderProducts();
      });

      document.getElementById('categoryFilterBar').addEventListener('click', e => {
        const btn = e.target.closest('.cat-btn');
        if (!btn) return;
        state.activeCategory = btn.getAttribute('data-cat');
        document.querySelectorAll('.cat-btn').forEach(b => {
          if (b.getAttribute('data-cat') === state.activeCategory) {
            b.className = 'cat-btn bg-indigo-600 text-white text-xs font-semibold px-3 py-2 rounded-xl whitespace-nowrap';
          } else {
            b.className = 'cat-btn bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-medium px-3 py-2 rounded-xl whitespace-nowrap';
          }
        });
        renderProducts();
      });

      // Product Modal Helper
      const modalProduct = document.getElementById('modalProductForm');
      function openProductModal(prodId = null) {
        const titleEl = document.getElementById('modalProductTitle');
        const idInput = document.getElementById('editProductId');
        const skuInput = document.getElementById('prodSkuInput');
        const catInput = document.getElementById('prodCategoryInput');
        const nameInput = document.getElementById('prodNameInput');
        const batchInput = document.getElementById('prodBatchInput');
        const expInput = document.getElementById('prodExpInput');
        const kartonInput = document.getElementById('prodKartonInput');
        const boxInput = document.getElementById('prodBoxInput');
        const unitPcsInput = document.getElementById('prodUnitPcsInput');
        const unitInput = document.getElementById('prodUnitInput');
        const stockInput = document.getElementById('prodStockInput');
        const grosirInput = document.getElementById('prodPriceGrosirInput');
        const kartonPriceInput = document.getElementById('prodPriceKartonInput');
        const ecerInput = document.getElementById('prodPriceEcerInput');
        const delBtn = document.getElementById('btnDeleteProduct');

        if (prodId) {
          const prod = state.products.find(p => p.id === prodId);
          if (!prod) return;
          titleEl.textContent = 'Edit Rekap Barang, Batch & Harga';
          idInput.value = prod.id;
          skuInput.value = prod.sku;
          catInput.value = prod.category;
          nameInput.value = prod.name;
          batchInput.value = prod.batch || '230926';
          expInput.value = prod.exp || '12-2027';
          kartonInput.value = prod.karton !== undefined ? prod.karton : 0;
          boxInput.value = prod.box !== undefined ? prod.box : 0;
          unitPcsInput.value = prod.unitPcs !== undefined ? prod.unitPcs : 0;
          unitInput.value = prod.unit || 'Box';
          stockInput.value = prod.kuantum !== undefined ? prod.kuantum : prod.stock;
          grosirInput.value = prod.priceGrosir;
          kartonPriceInput.value = prod.priceKarton || (prod.priceGrosir * 6);
          ecerInput.value = prod.priceEcer;
          delBtn.classList.remove('hidden');
        } else {
          const nextNum = String(state.products.length + 1).padStart(3, '0');
          titleEl.textContent = 'Tambah Produk & Rekap Muatan Sales';
          idInput.value = '';
          skuInput.value = '8991102000' + nextNum;
          catInput.value = 'Wafer & Pie';
          nameInput.value = '';
          batchInput.value = '25092026';
          expInput.value = '12-2027';
          kartonInput.value = 2;
          boxInput.value = 0;
          unitPcsInput.value = 0;
          unitInput.value = 'Box';
          stockInput.value = 24;
          grosirInput.value = '';
          kartonPriceInput.value = '';
          ecerInput.value = '';
          delBtn.classList.add('hidden');
        }
        modalProduct.classList.remove('hidden');
      }

      function closeProductModal() {
        modalProduct.classList.add('hidden');
      }

      document.getElementById('btnOpenAddProductPos').addEventListener('click', () => openProductModal(null));
      document.getElementById('btnOpenAddProductStock').addEventListener('click', () => openProductModal(null));
      document.getElementById('btnCloseModalProduct').addEventListener('click', closeProductModal);
      document.getElementById('btnCancelProduct').addEventListener('click', closeProductModal);

      document.getElementById('btnSaveProduct').addEventListener('click', () => {
        const editId = document.getElementById('editProductId').value.trim();
        const sku = document.getElementById('prodSkuInput').value.trim() || 'SKU-BARU';
        const category = document.getElementById('prodCategoryInput').value;
        const name = document.getElementById('prodNameInput').value.trim().toUpperCase();
        const batch = document.getElementById('prodBatchInput').value.trim() || '-';
        const exp = document.getElementById('prodExpInput').value.trim() || '-';
        const karton = Math.max(0, Number(document.getElementById('prodKartonInput').value) || 0);
        const box = Math.max(0, Number(document.getElementById('prodBoxInput').value) || 0);
        const unitPcs = Math.max(0, Number(document.getElementById('prodUnitPcsInput').value) || 0);
        const unit = document.getElementById('prodUnitInput').value.trim() || 'Box';
        const kuantum = Math.max(0, Number(document.getElementById('prodStockInput').value) || 0);
        const priceGrosir = Math.max(0, Number(document.getElementById('prodPriceGrosirInput').value) || 0);
        const priceKarton = Math.max(0, Number(document.getElementById('prodPriceKartonInput').value) || (priceGrosir * 6));
        const priceEcer = Math.max(0, Number(document.getElementById('prodPriceEcerInput').value) || 1000);

        if (!name || priceGrosir <= 0) {
          showToast('Nama produk dan Harga Jual wajib diisi!', 'warn');
          return;
        }

        if (editId) {
          const prod = state.products.find(p => p.id === editId);
          if (prod) {
            prod.sku = sku;
            prod.category = category;
            prod.name = name;
            prod.batch = batch;
            prod.exp = exp;
            prod.karton = karton;
            prod.box = box;
            prod.unitPcs = unitPcs;
            prod.kuantum = kuantum;
            prod.unit = unit;
            prod.stock = Math.max(1, kuantum);
            prod.priceGrosir = priceGrosir;
            prod.priceKarton = priceKarton;
            prod.priceEcer = priceEcer;
            showToast(`Data Rekap "${name}" berhasil diperbarui!`);
          }
        } else {
          const newId = 'P' + Date.now().toString().slice(-4);
          state.products.unshift({
            id: newId,
            sku,
            name,
            batch,
            exp,
            karton,
            box,
            unitPcs,
            kuantum,
            category,
            unit,
            priceGrosir,
            priceKarton,
            priceEcer,
            stock: Math.max(1, kuantum)
          });
          showToast(`Produk baru "${name}" berhasil ditambahkan ke Rekap Sales!`);
        }

        persistState();
        closeProductModal();
        renderAll();
      });

      document.getElementById('btnDeleteProduct').addEventListener('click', () => {
        const editId = document.getElementById('editProductId').value.trim();
        if (!editId) return;
        state.products = state.products.filter(p => p.id !== editId);
        delete state.cart[editId];
        persistState();
        closeProductModal();
        renderAll();
        showToast('Produk dihapus dari daftar katalog.');
      });

      // Product Grid Clicks (+ / - / Edit Harga)
      document.getElementById('productGrid').addEventListener('click', e => {
        const editBtn = e.target.closest('button[data-edit-prod]');
        if (editBtn) {
          openProductModal(editBtn.getAttribute('data-edit-prod'));
          return;
        }
        const btn = e.target.closest('button[data-action]');
        if (!btn) return;
        const pid = btn.getAttribute('data-id');
        const action = btn.getAttribute('data-action');
        const prod = state.products.find(p => p.id === pid);
        if (!prod) return;

        const current = state.cart[pid] || 0;
        if (action === 'inc') {
          if (current >= prod.stock) {
            showToast('Stok di mobil kanvas tidak mencukupi!', 'warn');
            return;
          }
          state.cart[pid] = current + 1;
        } else if (action === 'dec') {
          if (current <= 1) {
            delete state.cart[pid];
          } else {
            state.cart[pid] = current - 1;
          }
        }
        renderProducts();
        renderCartAndReceipt();
      });

      // Cart Items Clicks (+ / - / del) & Direct Qty Input
      document.getElementById('cartItemsList').addEventListener('click', e => {
        const btn = e.target.closest('button[data-cart-action]');
        if (!btn) return;
        const pid = btn.getAttribute('data-id');
        const action = btn.getAttribute('data-cart-action');
        const prod = state.products.find(p => p.id === pid);
        if (!prod) return;

        const current = state.cart[pid] || 0;
        if (action === 'inc') {
          if (current >= prod.stock) {
            showToast('Melebihi sisa stok mobil kanvas!', 'warn');
            return;
          }
          state.cart[pid] = current + 1;
        } else if (action === 'dec') {
          if (current <= 1) {
            delete state.cart[pid];
          } else {
            state.cart[pid] = current - 1;
          }
        } else if (action === 'del') {
          delete state.cart[pid];
        }
        renderProducts();
        renderCartAndReceipt();
      });

      document.getElementById('cartItemsList').addEventListener('change', e => {
        const inp = e.target.closest('input[data-cart-qty-input]');
        if (!inp) return;
        const pid = inp.getAttribute('data-cart-qty-input');
        const prod = state.products.find(p => p.id === pid);
        if (!prod) return;
        const val = Math.floor(Number(inp.value) || 0);
        if (val <= 0) {
          delete state.cart[pid];
        } else {
          state.cart[pid] = Math.min(prod.stock, val);
        }
        renderProducts();
        renderCartAndReceipt();
      });

      document.getElementById('btnCancelEditInvoice').addEventListener('click', () => {
        state.editingTxId = null;
        state.cart = {};
        state.discountRp = 0;
        state.cashReceivedInput = '';
        document.getElementById('inputDiscountRp').value = 0;
        document.getElementById('inputCashReceived').value = '';
        renderAll();
        showToast('Mode koreksi nota dibatalkan.');
      });

      // Clear Cart
      document.getElementById('btnClearCart').addEventListener('click', () => {
        state.cart = {};
        state.discountRp = 0;
        state.cashReceivedInput = '';
        document.getElementById('inputDiscountRp').value = 0;
        document.getElementById('inputCashReceived').value = '';
        renderProducts();
        renderCartAndReceipt();
        showToast('Keranjang nota dikosongkan.');
      });

      // Discount, Payment Method, Cash Received
      document.getElementById('inputDiscountRp').addEventListener('input', e => {
        state.discountRp = Number(e.target.value) || 0;
        renderCartAndReceipt();
      });

      document.getElementById('selectPaymentMethod').addEventListener('change', e => {
        state.paymentMethod = e.target.value;
        renderCartAndReceipt();
      });

      document.getElementById('inputCashReceived').addEventListener('input', e => {
        state.cashReceivedInput = e.target.value;
        renderCartAndReceipt();
      });

      document.querySelectorAll('.quick-cash-btn').forEach(btn => {
        btn.addEventListener('click', () => {
          const val = btn.getAttribute('data-quickcash');
          const summary = getCartSummary();
          if (val === 'exact') {
            state.cashReceivedInput = String(summary.grandTotal);
          } else {
            const add = Number(val) || 0;
            const current = Number(state.cashReceivedInput) || summary.grandTotal;
            state.cashReceivedInput = String(current + add);
          }
          document.getElementById('inputCashReceived').value = state.cashReceivedInput;
          renderCartAndReceipt();
        });
      });

      // Save & Print 58mm Transaction
      document.getElementById('btnSaveAndPrint').addEventListener('click', async () => {
        const summary = getCartSummary();
        if (summary.items.length === 0) {
          showToast('Pilih minimal 1 barang sebelum menyimpan & mencetak nota!', 'warn');
          return;
        }

        const store = getActiveStore();
        const now = new Date();
        const pad2 = n => String(n).padStart(2, '0');
        const nextNum = String(state.transactions.length + 1).padStart(3, '0');
        const dCode = formatDateCode(state.orderDate);
        const isEditing = Boolean(state.editingTxId);
        const txId = isEditing ? state.editingTxId : `INV-${dCode}-${nextNum}`;

        // If editing an existing transaction, restore its old items' stock first
        if (isEditing) {
          const oldTx = state.transactions.find(t => t.id === txId);
          if (oldTx && oldTx.items) {
            oldTx.items.forEach(oldItem => {
              const p = state.products.find(pr => pr.id === oldItem.id);
              if (p) {
                p.stock += oldItem.qty;
                p.kuantum = (p.kuantum || 0) + oldItem.qty;
              }
            });
          }
        }

        const newTx = {
          id: txId,
          orderDate: state.orderDate,
          time: `${pad2(now.getHours())}:${pad2(now.getMinutes())}`,
          storeId: store.id,
          storeName: store.name,
          storeAddress: store.address,
          paymentMethod: state.paymentMethod,
          tier: state.priceTier,
          items: summary.items,
          subtotal: summary.subtotal,
          discount: summary.discount,
          grandTotal: summary.grandTotal,
          paid: summary.paid,
          change: summary.diff
        };

        // Deduct stock from van
        summary.items.forEach(item => {
          const prod = state.products.find(p => p.id === item.id);
          if (prod) {
            prod.stock = Math.max(0, prod.stock - item.qty);
            prod.kuantum = Math.max(0, (prod.kuantum || prod.stock) - item.qty);
          }
        });

        // Mark store visited
        store.visited = true;
        if (isEditing) {
          const idx = state.transactions.findIndex(t => t.id === txId);
          if (idx !== -1) state.transactions[idx] = newTx;
          state.editingTxId = null;
        } else {
          state.transactions.push(newTx);
        }
        persistState();

        const receiptStr = build58mmReceiptText(newTx);
        document.getElementById('receiptTextPreview').textContent = receiptStr;

        // Langsung cetak otomatis ke printer Iware 58mm tanpa aplikasi tambahan
        await directPrint58mm(receiptStr, `Nota ${newTx.id} disimpan & langsung dicetak ke printer Iware 58mm!`);

        // Reset cart for next store
        state.cart = {};
        state.discountRp = 0;
        state.cashReceivedInput = '';
        document.getElementById('inputDiscountRp').value = 0;
        document.getElementById('inputCashReceived').value = '';
        renderAll();
      });

      // Copy Receipt Text / WhatsApp Share
      document.getElementById('btnCopyEscPos').addEventListener('click', () => {
        const text = document.getElementById('receiptTextPreview').textContent;
        const ta = document.createElement('textarea');
        ta.value = text;
        document.body.appendChild(ta);
        ta.select();
        try {
          document.execCommand('copy');
          showToast('Teks struk 58mm berhasil disalin (siap tempel ke aplikasi RawBT / WhatsApp Toko)!');
        } catch (err) {
          showToast('Gagal menyalin teks struk.', 'warn');
        }
        document.body.removeChild(ta);
      });

      // Direct Print Button on Preview Header + USB + Test Feed Iware
      document.getElementById('btnDirectBrowserPrint').addEventListener('click', async () => {
        const currentText = document.getElementById('receiptTextPreview').textContent;
        await directPrint58mm(currentText, 'Struk pratinjau langsung dicetak ke printer Iware 58mm!');
      });

      document.getElementById('btnConnectUsb').addEventListener('click', connectUsbPrinter);

      document.getElementById('btnTestFeedIware').addEventListener('click', async () => {
        const prof = state.profile || {};
        const testReceipt = [
          centerText('TES CETAK PRINTER IWARE 58MM', 32),
          centerText(prof.companyName || 'PT MAJU JAYA DISTRIBUSI', 32),
          '================================',
          'Status : KONEKSI LANGSUNG OK',
          'Lebar  : 58mm (32 Karakter)',
          'Mode   : Tanpa Aplikasi Tambahan',
          '================================',
          centerText('Printer Siap Digunakan Sales!', 32)
        ].join('\n');
        await directPrint58mm(testReceipt, 'Tes cetak Iware 58mm berhasil!');
      });

      // Route Tab Actions
      document.getElementById('routeStoreList').addEventListener('click', e => {
        const selBtn = e.target.closest('button[data-route-select]');
        if (selBtn) {
          state.selectedStoreId = selBtn.getAttribute('data-route-select');
          renderAll();
          switchTab('pos');
          showToast('Pelanggan dipilih untuk transaksi door-to-door.');
          return;
        }
        const editBtn = e.target.closest('button[data-route-edit]');
        if (editBtn) {
          openStoreModal(editBtn.getAttribute('data-route-edit'));
          return;
        }
        const togBtn = e.target.closest('button[data-route-toggle]');
        if (togBtn) {
          const sid = togBtn.getAttribute('data-route-toggle');
          const st = state.stores.find(s => s.id === sid);
          if (st) {
            st.visited = !st.visited;
            persistState();
            renderAll();
          }
        }
      });

      // Stock Tab Actions
      document.getElementById('stockTableBody').addEventListener('click', e => {
        const editBtn = e.target.closest('button[data-edit-prod]');
        if (editBtn) {
          openProductModal(editBtn.getAttribute('data-edit-prod'));
          return;
        }
        const btn = e.target.closest('button[data-restock-id]');
        if (!btn) return;
        const pid = btn.getAttribute('data-restock-id');
        const prod = state.products.find(p => p.id === pid);
        if (prod) {
          prod.karton = (prod.karton || 0) + 1;
          prod.kuantum = (prod.kuantum || prod.stock) + 12;
          prod.stock += 12;
          persistState();
          renderAll();
          showToast(`Muatan ${prod.name} ditambah +1 Karton (+12 Kuantum).`);
        }
      });

      document.getElementById('btnResetDemoData').addEventListener('click', () => {
        state.categories = JSON.parse(JSON.stringify(DEFAULT_CATEGORIES));
        state.products = JSON.parse(JSON.stringify(DEFAULT_PRODUCTS));
        state.stores = JSON.parse(JSON.stringify(DEFAULT_STORES));
        state.transactions = JSON.parse(JSON.stringify(DEFAULT_TRANSACTIONS));
        state.cart = { 'P01': 2, 'P02': 1 };
        persistState();
        renderAll();
        showToast('Data stok mobil kanvas & rute direset ke kondisi awal demo.');
      });

      // History Tab Actions (Edit/Koreksi Nota, Delete Nota, Reprint & Daily Recap)
      document.getElementById('historyTableBody').addEventListener('click', e => {
        const editBtn = e.target.closest('button[data-edit-tx]');
        if (editBtn) {
          const txId = editBtn.getAttribute('data-edit-tx');
          const tx = state.transactions.find(t => t.id === txId);
          if (!tx) return;
          state.editingTxId = tx.id;
          state.selectedStoreId = tx.storeId || state.selectedStoreId;
          state.orderDate = tx.orderDate || '2026-11-08';
          state.paymentMethod = tx.paymentMethod || 'TUNAI';
          state.priceTier = tx.tier || 'grosir';
          state.discountRp = tx.discount || 0;
          state.cashReceivedInput = String(tx.paid || 0);
          state.cart = {};
          (tx.items || []).forEach(it => {
            state.cart[it.id] = it.qty;
          });

          document.getElementById('inputOrderDate').value = state.orderDate;
          document.getElementById('selectPaymentMethod').value = state.paymentMethod;
          document.getElementById('selectPriceTier').value = state.priceTier;
          document.getElementById('inputDiscountRp').value = state.discountRp;
          document.getElementById('inputCashReceived').value = state.cashReceivedInput;

          renderAll();
          switchTab('pos');
          showToast(`Nota ${tx.id} dimuat ke Kasir untuk dikoreksi/diedit!`);
          return;
        }

        const delBtn = e.target.closest('button[data-delete-tx]');
        if (delBtn) {
          const txId = delBtn.getAttribute('data-delete-tx');
          const tx = state.transactions.find(t => t.id === txId);
          if (!tx) return;
          // Restore stock back to van
          (tx.items || []).forEach(it => {
            const p = state.products.find(pr => pr.id === it.id);
            if (p) {
              p.stock += it.qty;
              p.kuantum = (p.kuantum || 0) + it.qty;
            }
          });
          state.transactions = state.transactions.filter(t => t.id !== txId);
          if (state.editingTxId === txId) state.editingTxId = null;
          persistState();
          renderAll();
          showToast(`Nota ${txId} dihapus & stok dikembalikan ke mobil kanvas.`);
          return;
        }

        const btn = e.target.closest('button[data-reprint-id]');
        if (!btn) return;
        const txId = btn.getAttribute('data-reprint-id');
        const tx = state.transactions.find(t => t.id === txId);
        if (tx) {
          const str = build58mmReceiptText(tx);
          switchTab('pos');
          directPrint58mm(str, `Struk ${tx.id} langsung dicetak ulang ke printer Iware 58mm!`);
        }
      });

      document.getElementById('btnPrintStockRecap58').addEventListener('click', async () => {
        const stockStr = buildStockRecap58mmText();
        switchTab('pos');
        await directPrint58mm(stockStr, 'Struk Rekap Barang Persales (58mm) langsung dicetak ke Iware!');
      });

      document.getElementById('btnPrintDailyRecap').addEventListener('click', async () => {
        const recapStr = buildDailyRecapReceiptText();
        switchTab('pos');
        await directPrint58mm(recapStr, 'Struk Rekap Setoran Harian 58mm langsung dicetak ke Iware!');
      });

      // Modal Add / Edit Store
      const modal = document.getElementById('modalNewStore');
      function openStoreModal(storeId = null) {
        const titleEl = document.getElementById('modalStoreTitle');
        const idInp = document.getElementById('editStoreIdInput');
        const nameInp = document.getElementById('newStoreName');
        const ownerInp = document.getElementById('newStoreOwner');
        const phoneInp = document.getElementById('newStorePhone');
        const addrInp = document.getElementById('newStoreAddress');
        const limitInp = document.getElementById('newStoreLimit');
        const noteInp = document.getElementById('newStoreNote');
        const delBtn = document.getElementById('btnDeleteStore');

        if (storeId) {
          const st = state.stores.find(s => s.id === storeId);
          if (!st) return;
          titleEl.textContent = 'Edit Data Pelanggan / Toko';
          idInp.value = st.id;
          nameInp.value = st.name;
          ownerInp.value = st.owner || '';
          phoneInp.value = st.phone || '';
          addrInp.value = st.address || '';
          limitInp.value = st.creditLimit || 1500000;
          noteInp.value = st.lastNote || '';
          if (state.stores.length > 1) {
            delBtn.classList.remove('hidden');
          } else {
            delBtn.classList.add('hidden');
          }
        } else {
          titleEl.textContent = 'Tambah Pelanggan / Warung Baru';
          idInp.value = '';
          nameInp.value = '';
          ownerInp.value = '';
          phoneInp.value = '';
          addrInp.value = '';
          limitInp.value = 1500000;
          noteInp.value = '';
          delBtn.classList.add('hidden');
        }
        modal.classList.remove('hidden');
      }
      const closeModal = () => modal.classList.add('hidden');

      document.getElementById('btnQuickNewStore').addEventListener('click', () => openStoreModal(null));
      document.getElementById('btnAddStoreFromRoute').addEventListener('click', () => openStoreModal(null));
      document.getElementById('btnEditCurrentStorePos').addEventListener('click', () => openStoreModal(state.selectedStoreId));
      document.getElementById('btnCloseModalStore').addEventListener('click', closeModal);
      document.getElementById('btnCancelStore').addEventListener('click', closeModal);

      document.getElementById('btnSaveStore').addEventListener('click', () => {
        const editId = document.getElementById('editStoreIdInput').value.trim();
        const name = document.getElementById('newStoreName').value.trim();
        const owner = document.getElementById('newStoreOwner').value.trim() || 'Pemilik Toko';
        const address = document.getElementById('newStoreAddress').value.trim() || 'Lokasi Door-to-Door';
        const phone = document.getElementById('newStorePhone').value.trim() || '-';
        const creditLimit = Math.max(0, Number(document.getElementById('newStoreLimit').value) || 1500000);
        const lastNote = document.getElementById('newStoreNote').value.trim() || 'Pelanggan door-to-door';

        if (!name) {
          showToast('Nama toko / pelanggan wajib diisi!', 'warn');
          return;
        }

        if (editId) {
          const st = state.stores.find(s => s.id === editId);
          if (st) {
            st.name = name;
            st.owner = owner;
            st.address = address;
            st.phone = phone;
            st.creditLimit = creditLimit;
            st.lastNote = lastNote;
          }
          persistState();
          closeModal();
          renderAll();
          showToast(`Data pelanggan "${name}" berhasil diperbarui!`);
          return;
        }

        const newId = 'S' + String(Date.now()).slice(-3);
        const newStore = {
          id: newId,
          name,
          owner,
          address,
          phone,
          creditLimit,
          visited: false,
          lastNote
        };
        state.stores.push(newStore);
        state.selectedStoreId = newId;
        persistState();
        closeModal();
        renderAll();
        showToast(`Pelanggan "${name}" berhasil ditambahkan dan dipilih!`);
      });

      document.getElementById('btnDeleteStore').addEventListener('click', () => {
        const editId = document.getElementById('editStoreIdInput').value.trim();
        if (!editId || state.stores.length <= 1) return;
        state.stores = state.stores.filter(s => s.id !== editId);
        if (state.selectedStoreId === editId) {
          state.selectedStoreId = state.stores[0].id;
        }
        persistState();
        closeModal();
        renderAll();
        showToast('Data toko dihapus dari daftar rute.');
      });

      // Modal Edit Profil Sales & Distributor
      const modalProfile = document.getElementById('modalProfileSettings');
      const closeProfileModal = () => modalProfile.classList.add('hidden');
      document.getElementById('btnEditProfileHeader').addEventListener('click', () => {
        const prof = state.profile || {};
        document.getElementById('inputCompanyName').value = prof.companyName || 'PT MAJU JAYA DISTRIBUSI';
        document.getElementById('inputBranchUnit').value = prof.branchUnit || 'Kanvas #04 - Jawa Barat';
        document.getElementById('inputWarehousePhone').value = prof.warehousePhone || 'WA Gudang: 0811-2000-5858';
        document.getElementById('inputSalespersonName').value = prof.salesName || 'Andi Pratama';
        modalProfile.classList.remove('hidden');
      });
      document.getElementById('btnCloseModalProfile').addEventListener('click', closeProfileModal);
      document.getElementById('btnCancelProfile').addEventListener('click', closeProfileModal);
      document.getElementById('btnSaveProfile').addEventListener('click', () => {
        state.profile = {
          companyName: document.getElementById('inputCompanyName').value.trim() || 'PT MAJU JAYA DISTRIBUSI',
          branchUnit: document.getElementById('inputBranchUnit').value.trim() || 'Kanvas #04',
          warehousePhone: document.getElementById('inputWarehousePhone').value.trim() || '-',
          salesName: document.getElementById('inputSalespersonName').value.trim() || 'Sales'
        };
        persistState();
        closeProfileModal();
        renderAll();
        showToast('Profil Sales & Header Struk 58mm berhasil diperbarui!');
      });

      // Modal Kelola / Edit Kategori Produk
      const modalCatMgr = document.getElementById('modalCategoryManager');
      const openCatMgr = () => {
        renderCategoriesUI();
        modalCatMgr.classList.remove('hidden');
      };
      const closeCatMgr = () => modalCatMgr.classList.add('hidden');

      document.getElementById('btnOpenCategoryManagerPos').addEventListener('click', openCatMgr);
      document.getElementById('btnOpenCatFromProdModal').addEventListener('click', openCatMgr);
      document.getElementById('btnCloseCategoryModal').addEventListener('click', closeCatMgr);
      document.getElementById('btnDoneCategoryModal').addEventListener('click', closeCatMgr);

      document.getElementById('btnAddNewCategory').addEventListener('click', () => {
        const inp = document.getElementById('inputNewCategoryName');
        const newCat = inp.value.trim();
        if (!newCat) {
          showToast('Nama kategori baru tidak boleh kosong!', 'warn');
          return;
        }
        if (state.categories.some(c => c.toLowerCase() === newCat.toLowerCase())) {
          showToast('Kategori tersebut sudah ada!', 'warn');
          return;
        }
        state.categories.push(newCat);
        inp.value = '';
        persistState();
        renderAll();
        const prodCatSel = document.getElementById('prodCategoryInput');
        if (prodCatSel) prodCatSel.value = newCat;
        showToast(`Kategori "${newCat}" berhasil ditambahkan!`);
      });

      document.getElementById('categoryManagerList').addEventListener('click', e => {
        const saveBtn = e.target.closest('button[data-cat-save-idx]');
        if (saveBtn) {
          const idx = Number(saveBtn.getAttribute('data-cat-save-idx'));
          const oldName = state.categories[idx];
          const inp = document.querySelector(`input[data-cat-edit-input="${idx}"]`);
          const newName = inp ? inp.value.trim() : '';
          if (!newName) {
            showToast('Nama kategori tidak boleh kosong!', 'warn');
            return;
          }
          state.categories[idx] = newName;
          // Update all products that used oldName so they stay in sync
          state.products.forEach(p => {
            if (p.category === oldName) p.category = newName;
          });
          if (state.activeCategory === oldName) state.activeCategory = newName;
          persistState();
          renderAll();
          showToast(`Kategori "${oldName}" diubah menjadi "${newName}"!`);
          return;
        }

        const delBtn = e.target.closest('button[data-cat-del-idx]');
        if (delBtn) {
          if (state.categories.length <= 1) {
            showToast('Minimal harus ada 1 kategori produk!', 'warn');
            return;
          }
          const idx = Number(delBtn.getAttribute('data-cat-del-idx'));
          const removedName = state.categories[idx];
          state.categories.splice(idx, 1);
          const fallbackCat = state.categories[0];
          state.products.forEach(p => {
            if (p.category === removedName) p.category = fallbackCat;
          });
          if (state.activeCategory === removedName) state.activeCategory = 'ALL';
          persistState();
          renderAll();
          showToast(`Kategori "${removedName}" dihapus (produk dipindah ke "${fallbackCat}").`);
        }
      });

      // --- BARCODE SCANNER & AUTO-SAVE LOGIC ---
      let scanBehaviorMode = 'autosave'; // 'autosave' | 'editform' | 'fillmodal'
      let html5QrScanner = null;
      let isCameraScanning = false;

      const modalScanner = document.getElementById('modalBarcodeScanner');
      const btnModeAuto = document.getElementById('btnScanModeAutoSave');
      const btnModeForm = document.getElementById('btnScanModeEditForm');

      function updateScanModeButtons() {
        if (scanBehaviorMode === 'autosave') {
          btnModeAuto.className = 'bg-indigo-600 text-white text-xs font-semibold py-2 px-3 rounded-lg transition';
          btnModeForm.className = 'text-slate-700 hover:bg-white/60 text-xs font-medium py-2 px-3 rounded-lg transition';
        } else {
          btnModeForm.className = 'bg-indigo-600 text-white text-xs font-semibold py-2 px-3 rounded-lg transition';
          btnModeAuto.className = 'text-slate-700 hover:bg-white/60 text-xs font-medium py-2 px-3 rounded-lg transition';
        }
      }

      btnModeAuto.addEventListener('click', () => {
        scanBehaviorMode = 'autosave';
        updateScanModeButtons();
      });

      btnModeForm.addEventListener('click', () => {
        scanBehaviorMode = 'editform';
        updateScanModeButtons();
      });

      // Render preset barcode buttons inside modal
      function renderPresetBarcodes() {
        const listEl = document.getElementById('presetBarcodeList');
        listEl.innerHTML = Object.keys(DISTRIBUTOR_BARCODE_DB).map(code => {
          const item = DISTRIBUTOR_BARCODE_DB[code];
          const exists = state.products.some(p => p.sku === code || p.name.toLowerCase() === item.name.toLowerCase());
          return `
            <button type="button" data-sim-barcode="${code}" class="text-left p-2.5 rounded-xl border ${exists ? 'border-emerald-200 bg-emerald-50/40' : 'border-slate-200 hover:border-indigo-400 bg-slate-50'} transition flex flex-col justify-between">
              <div class="flex items-center justify-between gap-1">
                <span class="font-mono text-[11px] font-bold text-indigo-700">${code}</span>
                <span class="text-[10px] font-semibold px-1.5 py-0.5 rounded ${exists ? 'bg-emerald-100 text-emerald-700' : 'bg-amber-100 text-amber-800'}">
                  ${exists ? 'Ada di Katalog' : 'Produk Baru'}
                </span>
              </div>
              <p class="text-xs font-bold text-slate-800 mt-1 truncate">${item.name}</p>
              <p class="text-[11px] text-slate-500">Grosir: ${formatRupiah(item.priceGrosir)} | Ecer: ${formatRupiah(item.priceEcer)}</p>
            </button>
          `;
        }).join('');
      }

      async function stopCameraScanner() {
        if (html5QrScanner && isCameraScanning) {
          try {
            await html5QrScanner.stop();
            html5QrScanner.clear();
          } catch (e) {}
        }
        isCameraScanning = false;
        document.getElementById('cameraStatusLabel').textContent = 'Kamera: Nonaktif';
        document.getElementById('btnStartCameraScan').classList.remove('hidden');
        document.getElementById('btnStopCameraScan').classList.add('hidden');
      }

      async function startCameraScanner() {
        if (typeof Html5Qrcode === 'undefined') {
          showToast('Pustaka kamera belum siap, gunakan tombol Simulasi atau input manual.', 'warn');
          return;
        }
        try {
          if (!html5QrScanner) {
            html5QrScanner = new Html5Qrcode('qrReaderBox');
          }
          document.getElementById('cameraStatusLabel').textContent = 'Mengakses kamera...';
          await html5QrScanner.start(
            { facingMode: 'environment' },
            { fps: 10, qrbox: { width: 230, height: 110 } },
            (decodedText) => {
              handleScannedBarcode(decodedText);
            },
            () => {}
          );
          isCameraScanning = true;
          document.getElementById('cameraStatusLabel').textContent = 'Kamera Aktif — Arahkan ke Barcode Barang';
          document.getElementById('btnStartCameraScan').classList.add('hidden');
          document.getElementById('btnStopCameraScan').classList.remove('hidden');
        } catch (err) {
          document.getElementById('cameraStatusLabel').textContent = 'Kamera tidak tersedia di perangkat ini';
          showToast('Izin kamera ditolak/tidak tersedia. Gunakan input scanner atau tombol simulasi di bawah.', 'warn');
        }
      }

      function openScannerModal(mode = 'autosave') {
        scanBehaviorMode = mode;
        updateScanModeButtons();
        renderPresetBarcodes();
        modalScanner.classList.remove('hidden');
        setTimeout(() => {
          document.getElementById('inputBarcodeManual').focus();
        }, 80);
      }

      async function closeScannerModal() {
        await stopCameraScanner();
        modalScanner.classList.add('hidden');
      }

      function handleScannedBarcode(rawCode) {
        const code = String(rawCode || '').trim();
        if (!code) {
          showToast('Kode barcode kosong!', 'warn');
          return;
        }

        playScannerBeep();

        // If user clicked "Scan" inside the Product Modal, just fill the SKU/Barcode input & auto-populate if in DB
        if (scanBehaviorMode === 'fillmodal') {
          document.getElementById('prodSkuInput').value = code;
          const dbMatch = DISTRIBUTOR_BARCODE_DB[code];
          if (dbMatch) {
            document.getElementById('prodNameInput').value = dbMatch.name;
            document.getElementById('prodCategoryInput').value = dbMatch.category;
            document.getElementById('prodUnitInput').value = dbMatch.unit;
            document.getElementById('prodPriceGrosirInput').value = dbMatch.priceGrosir;
            document.getElementById('prodPriceEcerInput').value = dbMatch.priceEcer;
            document.getElementById('prodStockInput').value = dbMatch.defaultStock;
          }
          closeScannerModal();
          showToast(`Barcode ${code} dimasukkan ke form produk!`);
          return;
        }

        // Check if product already exists in state.products by SKU/Barcode
        let existingProd = state.products.find(p => p.sku.toLowerCase() === code.toLowerCase());
        const dbTemplate = DISTRIBUTOR_BARCODE_DB[code];

        // Also match by name if it's one of the initial demo products
        if (!existingProd && dbTemplate) {
          existingProd = state.products.find(p => p.name.toLowerCase() === dbTemplate.name.toLowerCase());
          if (existingProd) {
            existingProd.sku = code; // Upgrade SKU to real barcode
          }
        }

        if (scanBehaviorMode === 'editform') {
          closeScannerModal();
          if (existingProd) {
            openProductModal(existingProd.id);
            showToast(`Barcode ${code} ditemukan! Silakan cek/ubah harga.`);
          } else {
            openProductModal(null);
            document.getElementById('prodSkuInput').value = code;
            if (dbTemplate) {
              document.getElementById('prodNameInput').value = dbTemplate.name;
              document.getElementById('prodCategoryInput').value = dbTemplate.category;
              document.getElementById('prodUnitInput').value = dbTemplate.unit;
              document.getElementById('prodPriceGrosirInput').value = dbTemplate.priceGrosir;
              document.getElementById('prodPriceEcerInput').value = dbTemplate.priceEcer;
              document.getElementById('prodStockInput').value = dbTemplate.defaultStock;
            } else {
              document.getElementById('prodNameInput').value = 'Produk Baru (' + code.slice(-4) + ')';
              document.getElementById('prodPriceGrosirInput').value = 50000;
              document.getElementById('prodPriceEcerInput').value = 55000;
            }
            showToast(`Barcode ${code} terdeteksi! Silakan simpan produk baru.`);
          }
          return;
        }

        // DEFAULT: 'autosave' — Automatically save product if not in catalog AND add +1 to active POS invoice
        if (!existingProd) {
          const newId = 'P' + Date.now().toString().slice(-4);
          const autoProduct = dbTemplate ? {
            id: newId,
            sku: code,
            name: dbTemplate.name,
            batch: dbTemplate.batch || '250926',
            exp: dbTemplate.exp || '12-2027',
            karton: dbTemplate.karton || 2,
            box: dbTemplate.box || 0,
            unitPcs: dbTemplate.unitPcs || 0,
            kuantum: dbTemplate.kuantum || dbTemplate.defaultStock,
            category: dbTemplate.category,
            unit: dbTemplate.unit,
            priceGrosir: dbTemplate.priceGrosir,
            priceKarton: dbTemplate.priceKarton || (dbTemplate.priceGrosir * 6),
            priceEcer: dbTemplate.priceEcer,
            stock: dbTemplate.defaultStock
          } : {
            id: newId,
            sku: code,
            name: `PRODUK BARCODE #${code.slice(-5)}`,
            batch: '261126',
            exp: '11-2027',
            karton: 2,
            box: 0,
            unitPcs: 0,
            kuantum: 24,
            category: 'Wafer & Pie',
            unit: 'Box',
            priceGrosir: 20000,
            priceKarton: 120000,
            priceEcer: 1500,
            stock: 24
          };

          state.products.unshift(autoProduct);
          existingProd = autoProduct;
          state.cart[existingProd.id] = (state.cart[existingProd.id] || 0) + 1;
          persistState();
          renderAll();
          renderPresetBarcodes();
          showToast(`Otomatis Disimpan & Masuk Nota: ${existingProd.name} (${formatRupiah(getProductPrice(existingProd))})`);
        } else {
          const currentQty = state.cart[existingProd.id] || 0;
          if (currentQty < existingProd.stock) {
            state.cart[existingProd.id] = currentQty + 1;
          } else {
            existingProd.stock += 10; // Auto top-up stock if scanned when empty
            state.cart[existingProd.id] = currentQty + 1;
          }
          persistState();
          renderAll();
          renderPresetBarcodes();
          showToast(`Barcode Di-scan (+1 ke Nota): ${existingProd.name}`);
        }
      }

      document.getElementById('btnOpenScannerPos').addEventListener('click', () => openScannerModal('autosave'));
      document.getElementById('btnOpenScannerStock').addEventListener('click', () => openScannerModal('autosave'));
      document.getElementById('btnScanInModal').addEventListener('click', () => openScannerModal('fillmodal'));
      document.getElementById('btnCloseScannerModal').addEventListener('click', closeScannerModal);

      document.getElementById('btnStartCameraScan').addEventListener('click', startCameraScanner);
      document.getElementById('btnStopCameraScan').addEventListener('click', stopCameraScanner);

      document.getElementById('btnSubmitManualBarcode').addEventListener('click', () => {
        const inp = document.getElementById('inputBarcodeManual');
        handleScannedBarcode(inp.value);
        inp.value = '';
      });

      document.getElementById('inputBarcodeManual').addEventListener('keydown', e => {
        if (e.key === 'Enter') {
          e.preventDefault();
          handleScannedBarcode(e.target.value);
          e.target.value = '';
        }
      });

      document.getElementById('presetBarcodeList').addEventListener('click', e => {
        const btn = e.target.closest('button[data-sim-barcode]');
        if (!btn) return;
        handleScannedBarcode(btn.getAttribute('data-sim-barcode'));
      });

      // Also support hardware barcode scanner directly in the POS search box (pressing Enter)
      document.getElementById('inputSearchProduct').addEventListener('keydown', e => {
        if (e.key === 'Enter') {
          const val = e.target.value.trim();
          if (val.length >= 5) {
            e.preventDefault();
            scanBehaviorMode = 'autosave';
            handleScannedBarcode(val);
            e.target.value = '';
            state.searchQuery = '';
            renderProducts();
          }
        }
      });
    });
  </script>
</body>
</html>
