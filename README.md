<!doctype html>
<html lang="tr">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Fumigasyon Stok Takip</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    * { box-sizing: border-box; }
    body {
      margin: 0; padding: 0; font-family: 'Inter', sans-serif;
      background: #090d16; color: #f1f5f9; display: flex; min-height: 100vh;
    }

    /* Sol Menü / Sidebar */
    aside {
      width: 270px; background: #111827; border-right: 1px solid #1f2937;
      padding: 30px 20px; display: flex; flex-direction: column; gap: 12px;
      flex-shrink: 0;
    }
    h1 { color: #34d399; margin: 0 0 10px 0; font-size: 20px; font-weight: 700; line-height: 1.3; }

    .menu {
      display: flex; flex-direction: column; gap: 6px; overflow-y: auto; max-height: calc(100vh - 120px);
      padding-right: 4px;
    }
    .menu button {
      width: 100%; text-align: left; padding: 12px 14px;
      background: transparent; color: #94a3b8; border: 1px solid transparent; cursor: pointer;
      border-radius: 8px; font-size: 14px; font-weight: 600;
      transition: all 0.2s ease;
      touch-action: manipulation;
    }
    .menu button:hover { background: #1f2937; color: #f1f5f9; }
    .menu button.aktif-menu { background: #059669; color: white; border-color: #059669; }

    /* Menü Grup Tasarımları (Açılır/Kapanır) */
    .menu-group {
      display: flex; flex-direction: column; gap: 4px; margin: 4px 0;
    }
    .menu-group-header {
      display: flex; align-items: center; justify-content: space-between;
      padding: 12px 14px; font-size: 14px; font-weight: 600; color: #2dd4bf; cursor: pointer;
      border-radius: 8px; transition: background 0.2s;
      touch-action: manipulation;
    }
    .menu-group-header:hover { background: #1f2937; }
    .menu-toggle {
      color: #2dd4bf; font-weight: 700; font-size: 16px;
    }
    
    /* DÜZELTME: Alt menüler başlangıçta flex olarak tanımlı ve gizleme kontrolü JS ile yapılıyor */
    .menu-sub-items {
      display: flex; flex-direction: column; gap: 4px; padding-left: 12px;
    }
    .menu-sub-btn {
      padding: 10px 12px !important; font-size: 13px !important; color: #94a3b8 !important;
    }
    .badge-yeni {
      background: #84cc16; color: #0f172a; font-size: 11px; font-weight: 700;
      padding: 1px 8px; border-radius: 9999px; margin-left: 6px; text-transform: lowercase;
    }

    /* Alt Menü / Sekmeler */
    .sub-menu {
      display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 20px; border-bottom: 1px solid #1f2937; padding-bottom: 15px;
    }
    .sub-menu button {
      padding: 10px 14px; background: #1f2937; color: #94a3b8; border: 1px solid #374151; cursor: pointer;
      border-radius: 6px; font-size: 13px; font-weight: 600; transition: all 0.2s;
      touch-action: manipulation;
    }
    .sub-menu button:hover { background: #374151; color: #f1f5f9; }
    .sub-menu button.aktif-sub { background: #059669; color: white; border-color: #059669; }

    .ayarlar-tab { display: none; }
    .ayarlar-tab.aktif { display: block; }

    /* Ana İçerik Alanı */
    main {
      flex: 1; padding: 30px; width: 100%; min-width: 0;
    }

    .sayfa {
      display: none; background: #111827; padding: 30px;
      border-radius: 16px; border: 1px solid #1f2937;
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.3), 0 8px 10px -6px rgba(0, 0, 0, 0.3);
    }
    .sayfa.aktif { display: block; animation: fadeIn 0.3s ease-in-out; }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(4px); }
      to { opacity: 1; transform: translateY(0); }
    }

    h2 { font-size: 18px; color: #f8fafc; margin-top: 0; margin-bottom: 20px; border-bottom: 1px solid #1f2937; padding-bottom: 10px; }

    .form-grid {
      display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 15px;
      max-width: 900px; padding-bottom: 25px;
      border-bottom: 1px solid #1f2937; margin-bottom: 25px;
    }

    .form-group { display: flex; flex-direction: column; }
    .form-group.full { grid-column: 1 / -1; max-width: 100%; }

    label {
      font-size: 13px; font-weight: 600;
      margin-bottom: 6px; color: #94a3b8;
    }

    input, select, textarea {
      width: 100%; padding: 12px 14px; border-radius: 8px;
      border: 1px solid #374151; background: #1f2937; color: #f9fafb;
      font-size: 14px; font-family: 'Inter', sans-serif;
      transition: border-color 0.2s, box-shadow 0.2s;
    }
    input:focus, select:focus, textarea:focus {
      outline: none; border-color: #10b981; box-shadow: 0 0 0 3px rgba(16, 185, 129, 0.2);
    }

    .btn-container { grid-column: 1 / -1; display: flex; align-items: flex-end; gap: 10px; }
    .kaydet {
      width: auto; padding: 12px 24px; border: 0; background: #059669;
      color: white; font-weight: 600; cursor: pointer; border-radius: 8px;
      transition: background 0.2s; touch-action: manipulation;
    }
    .kaydet:hover { background: #047857; }

    .sil {
      background: rgba(239, 68, 68, 0.15); color: #f87171; border: 0;
      width: auto; padding: 8px 12px; cursor: pointer;
      border-radius: 6px; font-size: 13px; font-weight: 500;
      transition: background 0.2s; touch-action: manipulation;
    }
    .sil:hover { background: rgba(239, 68, 68, 0.3); }

    .arama-kutusu { margin-bottom: 0; max-width: 300px; }

    .table-responsive { overflow-x: auto; -webkit-overflow-scrolling: touch; }
    table { width: 100%; border-collapse: collapse; margin-top: 10px; font-size: 14px; }
    th, td { padding: 12px 15px; text-align: left; border-bottom: 1px solid #1f2937; }
    th { background: #1f2937; font-weight: 600; color: #cbd5e1; }
    tr:hover { background: rgba(255, 255, 255, 0.02); }

    .action-bar { display: flex; gap: 15px; align-items: center; margin-bottom: 15px; flex-wrap: wrap; }
    .btn-secondary { background: #334155; color: #f1f5f9; border: none; padding: 12px 18px; border-radius: 8px; cursor: pointer; font-size: 14px; font-weight: 600; touch-action: manipulation; }
    .btn-secondary:hover { background: #475569; }

    .custom-dropdown { position: relative; display: inline-block; width: 240px; }
    .dropdown-header {
      background: #1f2937; border: 1px solid #374151; padding: 12px 14px; border-radius: 8px;
      cursor: pointer; display: flex; justify-content: space-between; align-items: center;
      font-size: 14px; color: #f9fafb; font-weight: 500; user-select: none; touch-action: manipulation;
    }
    .dropdown-header:hover { border-color: #4b5563; }
    .dropdown-menu {
      display: none; position: absolute; top: calc(100% + 4px); left: 0; right: 0;
      background: #111827; border: 1px solid #374151; border-radius: 8px; z-index: 50;
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5); padding: 6px;
    }
    .dropdown-menu.acik { display: block; }
    .dropdown-item {
      display: flex; align-items: center; gap: 10px; padding: 12px; cursor: pointer;
      font-size: 13px; color: #cbd5e1; border-radius: 6px; touch-action: manipulation;
    }
    .dropdown-item:hover { background: #1f2937; color: #f9fafb; }
    .dropdown-item input[type="checkbox"] { width: 18px; height: 18px; accent-color: #059669; cursor: pointer; }
    .dropdown-divider { height: 1px; background: #1f2937; margin: 4px 0; }

    .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 25px; }
    .stat-card { background: #1f2937; padding: 20px; border-radius: 12px; border: 1px solid #374151; }
    .stat-card h3 { margin: 0 0 8px 0; font-size: 13px; color: #94a3b8; font-weight: 600; }
    .stat-card .value { font-size: 22px; font-weight: 700; color: #34d399; margin: 0; }

    .dashboard-grid { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; margin-top: 20px; }
    .dash-box { background: #1f2937; border: 1px solid #374151; border-radius: 12px; padding: 20px; }
    .dash-box h3 { margin-top: 0; font-size: 15px; color: #34d399; border-bottom: 1px solid #374151; padding-bottom: 8px; }
    
    .kritik-item {
      background: rgba(239, 68, 68, 0.1); border-left: 4px solid #ef4444; padding: 10px 12px; margin-bottom: 8px; border-radius: 0 6px 6px 0; font-size: 13px; display: flex; justify-content: space-between; align-items: center;
    }

    .hizli-butonlar { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 15px; }
    .hizli-btn {
      background: #059669; color: white; border: none; padding: 12px; border-radius: 8px; font-weight: 600; cursor: pointer; text-align: center; font-size: 13px; transition: background 0.2s;
      touch-action: manipulation;
    }
    .hizli-btn:hover { background: #047857; }

    /* Mobil Düzenlemeler */
    @media (max-width: 900px) {
      .dashboard-grid { grid-template-columns: 1fr; }
    }
    @media (max-width: 768px) {
      body { flex-direction: column; min-height: auto; }
      aside { width: 100%; padding: 15px; border-right: none; border-bottom: 1px solid #1f2937; }
      .menu { max-height: none; }
      main { padding: 15px; }
      .sayfa { padding: 15px; }
      .form-grid { grid-template-columns: 1fr; }
      .custom-dropdown { width: 100%; }
      .arama-kutusu { max-width: 100%; width: 100%; }
    }
  </style>
</head>

<body>
  <aside>
    <h1>Fumigasyon Stok Takip</h1>
    <div class="menu">
      <button id="btn-anasayfa" class="menu-btn aktif-menu" onclick="sayfaAc('anasayfa')">Ana Sayfa & Uyarılar</button>
      <button id="btn-musteriler" class="menu-btn" onclick="sayfaAc('musteriler')">Müşteriler</button>
      <button id="btn-tedarikciler" class="menu-btn" onclick="sayfaAc('tedarikciler')">Tedarikçiler</button>
      <button id="btn-alislar" class="menu-btn" onclick="sayfaAc('alislar')">Alışlar</button>
      
      <!-- Ürünler Menü Grubu -->
      <div class="menu-group">
        <div class="menu-group-header" onclick="menuGrupToggle('menuSubItems', 'menuToggleIcon')">
          <span style="display: flex; align-items: center; gap: 8px;">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="color: #2dd4bf;"><path d="M20.59 13.41l-7.17 7.17a2 2 0 0 1-2.83 0L2 12V2h10l8.59 8.59a2 2 0 0 1 0 2.82z"></path><line x1="7" y1="7" x2="7.01" y2="7"></line></svg>
            Ürünler
          </span>
          <span class="menu-toggle" id="menuToggleIcon">−</span>
        </div>
        <div class="menu-sub-items" id="menuSubItems">
          <button id="btn-urun-tanimlari" class="menu-btn menu-sub-btn" onclick="sayfaAc('urun-tanimlari')">Ürün / Hizmet Tanımları</button>
          <button id="btn-depolar" class="menu-btn menu-sub-btn" onclick="sayfaAc('depolar')">Depolar</button>
          <button id="btn-uretim" class="menu-btn menu-sub-btn" onclick="sayfaAc('uretim')">Üretim</button>
          <button id="btn-ozel-fiyatlar" class="menu-btn menu-sub-btn" onclick="sayfaAc('ozel-fiyatlar')">Özel Fiyat Listeleri <span class="badge-yeni">yeni</span></button>
        </div>
      </div>

      <button id="btn-stok" class="menu-btn" onclick="sayfaAc('stok')">İlaç Stoğu</button>
      <button id="btn-cikis" class="menu-btn" onclick="sayfaAc('cikis')">Depo Çıkış</button>
      <button id="btn-satislar" class="menu-btn" onclick="sayfaAc('satislar')">Satışlar</button>
      <button id="btn-teklifler" class="menu-btn" onclick="sayfaAc('teklifler')">Teklifler</button>

      <!-- Raporlar Menü Grubu -->
      <div class="menu-group">
        <div class="menu-group-header" onclick="menuGrupToggle('raporSubItems', 'raporToggleIcon')">
          <span style="display: flex; align-items: center; gap: 8px;">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" style="color: #2dd4bf;"><line x1="18" y1="20" x2="18" y2="10"></line><line x1="12" y1="20" x2="12" y2="4"></line><line x1="6" y1="20" x2="6" y2="14"></line></svg>
            Raporlar
          </span>
          <span class="menu-toggle" id="raporToggleIcon">−</span>
        </div>
        <div class="menu-sub-items" id="raporSubItems">
          <button id="btn-rapor-satis-alis" class="menu-btn menu-sub-btn" onclick="sayfaAc('rapor-satis-alis')">Satışlar - Alışlar</button>
          <button id="btn-rapor-finansal" class="menu-btn menu-sub-btn" onclick="sayfaAc('rapor-finansal')">Finansal Raporlar</button>
          <button id="btn-rapor-stok" class="menu-btn menu-sub-btn" onclick="sayfaAc('rapor-stok')">Stok Raporları</button>
          <button id="btn-rapor-musteri" class="menu-btn menu-sub-btn" onclick="sayfaAc('rapor-musteri')">Müşteri Listesi</button>
        </div>
      </div>

      <button id="btn-kayitlar" class="menu-btn" onclick="sayfaAc('kayitlar')">Kayıtlar</button>
      <button id="btn-ayarlar" class="menu-btn" onclick="sayfaAc('ayarlar')">Ayarlar</button>
      <button id="btn-notlar" class="menu-btn" onclick="sayfaAc('notlar')">Notlar / Yedek</button>
    </div>
  </aside>

  <main>
    <!-- ANA SAYFA & UYARILAR -->
    <section id="anasayfa" class="sayfa aktif">
      <h2>Operasyon Paneli & Akıllı Uyarılar</h2>
      
      <div class="stats-grid">
        <div class="stat-card">
          <h3>Toplam Müşteri</h3>
          <p class="value" id="dashMusteri">0</p>
        </div>
        <div class="stat-card">
          <h3>Aktif İlaç Çeşidi</h3>
          <p class="value" id="dashStokCesit">0</p>
        </div>
        <div class="stat-card">
          <h3>Toplam Teklifler</h3>
          <p class="value" id="dashTeklif">0</p>
        </div>
        <div class="stat-card">
          <h3>Toplam Satış Cirosu</h3>
          <p class="value" id="dashCiro">₺0,00</p>
        </div>
      </div>

      <div class="dashboard-grid">
        <div class="dash-box">
          <h3>⚠️ Kritik Stok Alarmları (Miktar ≤ 5)</h3>
          <div id="kritikStokListesi" style="margin-top: 10px;">
            <p style="color: #94a3b8; font-size: 13px;">Kritik seviyede ürün bulunmuyor.</p>
          </div>
        </div>

        <div class="dash-box">
          <h3>⚡ Hızlı İşlemler</h3>
          <div class="hizli-butonlar">
            <button class="hizli-btn" onclick="sayfaAc('cikis')">Hızlı İlaç Çıkışı</button>
            <button class="hizli-btn" onclick="sayfaAc('stok')">Stok Ekle</button>
            <button class="hizli-btn" onclick="sayfaAc('satislar')">Satış Yap</button>
            <button class="hizli-btn" onclick="sayfaAc('teklifler')">Teklif Oluştur</button>
          </div>
        </div>
      </div>
    </section>

    <!-- Müşteriler Sayfası -->
    <section id="musteriler" class="sayfa">
      <h2>Müşteri Yönetimi</h2>
      <form id="musteriForm" class="form-grid" onsubmit="musteriEkle(event)">
        <div class="form-group full">
          <label>Müşteri veya firma adı</label>
          <div style="display: flex; gap: 10px;">
            <input id="yeniMusteri" required placeholder="Örn. ABC Gıda Ltd. Şti." style="flex: 1;">
            <button class="kaydet" type="submit">Kaydet</button>
          </div>
        </div>
      </form>
      
      <div class="action-bar">
        <input type="text" id="musteriAra" class="arama-kutusu" placeholder="Müşteri ara..." oninput="guncelle()">
      </div>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Müşteri Adı</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="musteriTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Tedarikçiler Sayfası -->
    <section id="tedarikciler" class="sayfa">
      <h2>Tedarikçi Yönetimi</h2>
      <form id="tedarikciForm" class="form-grid" onsubmit="tedarikciEkle(event)">
        <div class="form-group full">
          <label>Tedarikçi veya firma adı</label>
          <div style="display: flex; gap: 10px;">
            <input id="yeniTedarikci" required placeholder="Örn. Kimya A.Ş." style="flex: 1;">
            <button class="kaydet" type="submit">Kaydet</button>
          </div>
        </div>
      </form>

      <div class="action-bar">
        <input type="text" id="tedarikciAra" class="arama-kutusu" placeholder="Tedarikçi ara..." oninput="guncelle()">
      </div>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Tedarikçi Adı</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="tedarikciTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Alışlar Sayfası -->
    <section id="alislar" class="sayfa">
      <h2>İlaç Alış İşlemleri</h2>
      <form id="alisForm" class="form-grid" onsubmit="alisEkle(event)">
        <div class="form-group">
          <label>Tedarikçi</label>
          <select id="alisTedarikci" required></select>
        </div>
        <div class="form-group">
          <label>Alınan İlaç</label>
          <select id="alisIlac" required></select>
        </div>
        <div class="form-group">
          <label>Miktar</label>
          <input id="alisMiktar" type="number" min="0.01" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Birim Fiyat (₺)</label>
          <input id="alisBirimFiyat" type="number" min="0" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Tarih</label>
          <input id="alisTarih" type="date" required>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Alışı Kaydet ve Stoğa Ekle</button>
        </div>
      </form>

      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th>Tarih</th><th>Tedarikçi</th><th>İlaç</th>
              <th>Miktar</th><th>Birim Fiyat</th><th>Toplam Tutar</th><th style="width: 100px;">İşlem</th>
            </tr>
          </thead>
          <tbody id="alisTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Ürün / Hizmet Tanımları Sayfası -->
    <section id="urun-tanimlari" class="sayfa">
      <h2>Ürün / Hizmet Tanımları</h2>
      <form id="urunTanimiForm" class="form-grid" onsubmit="urunTanimiEkle(event)">
        <div class="form-group">
          <label>Ürün / Hizmet Adı</label>
          <input id="tanimAd" required placeholder="Örn. Tahıl Fumigasyon Hizmeti">
        </div>
        <div class="form-group">
          <label>Tür</label>
          <select id="tanimTur">
            <option>Ürün / İlaç</option>
            <option>Hizmet</option>
          </select>
        </div>
        <div class="form-group">
          <label>Satış Fiyatı (₺)</label>
          <input id="tanimFiyat" type="number" min="0" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Tanım Ekle</button>
        </div>
      </form>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Adı</th><th>Tür</th><th>Satış Fiyatı</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="urunTanimiTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Depolar Sayfası -->
    <section id="depolar" class="sayfa">
      <h2>Depo Yönetimi</h2>
      <form id="depoForm" class="form-grid" onsubmit="depoEkle(event)">
        <div class="form-group full">
          <label>Depo Adı / Konumu</label>
          <div style="display: flex; gap: 10px;">
            <input id="depoAd" required placeholder="Örn. Merkez Depo / Silo 2" style="flex: 1;">
            <button class="kaydet" type="submit">Depo Ekle</button>
          </div>
        </div>
      </form>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Depo Adı</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="depoTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Üretim Sayfası -->
    <section id="uretim" class="sayfa">
      <h2>Üretim Takibi</h2>
      <form id="uretimForm" class="form-grid" onsubmit="uretimEkle(event)">
        <div class="form-group">
          <label>Üretilen Ürün / Karışım</label>
          <input id="uretimUrun" required placeholder="Ürün adı">
        </div>
        <div class="form-group">
          <label>Miktar</label>
          <input id="uretimMiktar" type="number" min="0.01" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Tarih</label>
          <input id="uretimTarih" type="date" required>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Üretimi Kaydet</button>
        </div>
      </form>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Tarih</th><th>Ürün / Karışım</th><th>Miktar</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="uretimTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Özel Fiyat Listeleri Sayfası -->
    <section id="ozel-fiyatlar" class="sayfa">
      <h2>Özel Fiyat Listeleri <span class="badge-yeni" style="font-size:12px;">yeni</span></h2>
      <form id="ozelFiyatForm" class="form-grid" onsubmit="ozelFiyatEkle(event)">
        <div class="form-group">
          <label>Müşteri</label>
          <select id="ozelMusteri" required></select>
        </div>
        <div class="form-group">
          <label>İlgili Ürün / İlaç</label>
          <input id="ozelUrun" required placeholder="Ürün adı">
        </div>
        <div class="form-group">
          <label>Özel Fiyat (₺)</label>
          <input id="ozelFiyatDeger" type="number" min="0" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Özel Fiyat Tanımla</button>
        </div>
      </form>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Müşteri</th><th>Ürün / Hizmet</th><th>Özel Fiyat</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="ozelFiyatTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- İlaç Stoğu Sayfası -->
    <section id="stok" class="sayfa">
      <h2>İlaç Stokları</h2>
      <form id="stokForm" class="form-grid" onsubmit="stokEkle(event)">
        <div class="form-group">
          <label>İlaç Adı</label>
          <input id="ilacAdi" required placeholder="Örn. Alüminyum Fosfit">
        </div>
        <div class="form-group">
          <label>Miktar</label>
          <input id="stokMiktar" type="number" min="0.01" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Birim</label>
          <select id="birim">
            <option>kg</option>
            <option>litre</option>
            <option>adet</option>
            <option>tablet</option>
            <option>tüp</option>
          </select>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Stoğa Ekle</button>
        </div>
      </form>

      <div class="action-bar">
        <input type="text" id="stokAra" class="arama-kutusu" placeholder="İlaç ara..." oninput="guncelle()">
        <button class="btn-secondary" onclick="window.print()">Sayfayı Yazdır</button>
      </div>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>İlaç Adı</th><th>Stok Miktarı</th><th>Birim</th><th style="width: 100px;">İşlem</th></tr>
          </thead>
          <tbody id="stokTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Depo Çıkış Sayfası -->
    <section id="cikis" class="sayfa">
      <h2>Kullanım / Depodan Çıkış</h2>
      <form id="cikisForm" class="form-grid" onsubmit="cikisYap(event)">
        <div class="form-group">
          <label>İlaç</label>
          <select id="cikisIlac" required></select>
        </div>
        <div class="form-group">
          <label>Depodan Alan Kişi</label>
          <input id="alanKisi" required placeholder="Ad Soyad">
        </div>
        <div class="form-group">
          <label>Müşteri</label>
          <select id="cikisMusteri" required></select>
        </div>
        <div class="form-group">
          <label>Kullanım Yeri</label>
          <input id="kullanimYeri" required placeholder="Örn. Depo 3 / Silo A">
        </div>
        <div class="form-group">
          <label>Kullanılan Miktar</label>
          <input id="cikisMiktar" type="number" min="0.01" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Tarih</label>
          <input id="cikisTarih" type="date" required>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Stoktan Düş ve Kaydet</button>
        </div>
      </form>
    </section>

    <!-- Satışlar Sayfası -->
    <section id="satislar" class="sayfa">
      <h2>Satış İşlemleri</h2>
      <form id="satisForm" class="form-grid" onsubmit="satisEkle(event)">
        <div class="form-group">
          <label>Satılan İlaç</label>
          <select id="satisIlac" required></select>
        </div>
        <div class="form-group">
          <label>Müşteri</label>
          <select id="satisMusteri" required></select>
        </div>
        <div class="form-group">
          <label>Belge Tipi</label>
          <select id="satisBelgeTipi" required>
            <option>Sipariş</option>
            <option>İrsaliyeleşmiş</option>
            <option>Faturalaşmış</option>
          </select>
        </div>
        <div class="form-group">
          <label>Satılan Miktar</label>
          <input id="satisMiktar" type="number" min="0.01" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Birim Fiyat (₺)</label>
          <input id="birimFiyat" type="number" min="0" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Başlangıç Tarihi</label>
          <input id="baslangicTarih" type="date" required>
        </div>
        <div class="form-group">
          <label>Bitiş Tarihi</label>
          <input id="bitisTarih" type="date" required>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Satışı Kaydet ve Stoktan Düş</button>
        </div>
      </form>

      <!-- Filtreleme Alanı ve Arama Çubuğu -->
      <div class="action-bar">
        <!-- Belge Tipleri Açılır Checkbox Filtresi -->
        <div class="custom-dropdown" id="filtreDropdownContainer">
          <div class="dropdown-header" onclick="filtreDropdownToggle()">
            <span id="filtreSecimText">Tüm Belge Tipleri</span>
            <span id="filtreOk" style="font-size: 12px;">⌄</span>
          </div>
          <div class="dropdown-menu" id="filtreDropdownMenu">
            <label class="dropdown-item">
              <input type="checkbox" id="filtreTumunuSec" onchange="filtreTumunuSec(this)" checked> Tümünü Seç
            </label>
            <div class="dropdown-divider"></div>
            <label class="dropdown-item">
              <input type="checkbox" class="belge-filtre-cb" value="Sipariş" checked onchange="filtreCheckboxDegisti()"> Sipariş
            </label>
            <label class="dropdown-item">
              <input type="checkbox" class="belge-filtre-cb" value="İrsaliyeleşmiş" checked onchange="filtreCheckboxDegisti()"> İrsaliyeleşmiş
            </label>
            <label class="dropdown-item">
              <input type="checkbox" class="belge-filtre-cb" value="Faturalaşmış" checked onchange="filtreCheckboxDegisti()"> Faturalaşmış
            </label>
          </div>
        </div>

        <input type="text" id="satisAra" class="arama-kutusu" placeholder="Satışlarda ara (müşteri, ilaç)..." oninput="guncelle()" style="flex: 1; min-width: 220px;">
      </div>

      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th>Başlangıç</th><th>Bitiş</th><th>Belge Tipi</th><th>İlaç</th>
              <th>Miktar</th><th>Müşteri</th>
              <th>Birim Fiyat</th><th>Toplam Tutar</th><th style="width: 100px;">İşlem</th>
            </tr>
          </thead>
          <tbody id="satisTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Teklifler Sayfası -->
    <section id="teklifler" class="sayfa">
      <h2>Fiyat Teklifleri</h2>
      <form id="teklifForm" class="form-grid" onsubmit="teklifEkle(event)">
        <div class="form-group">
          <label>Müşteri</label>
          <select id="teklifMusteri" required></select>
        </div>
        <div class="form-group">
          <label>Hizmet / Konu</label>
          <input id="teklifKonu" required placeholder="Örn. Tahıl Fumigasyonu">
        </div>
        <div class="form-group">
          <label>Toplam Tutar (₺)</label>
          <input id="teklifTutar" type="number" min="0" step="0.01" required placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Tarih</label>
          <input id="teklifTarih" type="date" required>
        </div>
        <div class="form-group full">
          <label>Açıklama / Şartlar</label>
          <textarea id="teklifAciklama" rows="3" placeholder="Teklif detayları..."></textarea>
        </div>
        <div class="form-group btn-container">
          <button class="kaydet" type="submit">Teklif Oluştur</button>
        </div>
      </form>

      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th>Tarih</th><th>Müşteri</th><th>Konu</th>
              <th>Tutar</th><th>Açıklama</th><th style="width: 100px;">İşlem</th>
            </tr>
          </thead>
          <tbody id="teklifTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- RAPORLAR ALT SAYFALARI -->
    
    <!-- 1. Satışlar - Alışlar Raporu -->
    <section id="rapor-satis-alis" class="sayfa">
      <h2>Satışlar - Alışlar Karşılaştırma Raporu</h2>
      <div class="stats-grid">
        <div class="stat-card">
          <h3>Toplam Alış Tutarı</h3>
          <p class="value" id="rapToplamAlis" style="color:#ef4444;">₺0,00</p>
        </div>
        <div class="stat-card">
          <h3>Toplam Satış Tutarı</h3>
          <p class="value" id="rapToplamSatis" style="color:#34d399;">₺0,00</p>
        </div>
        <div class="stat-card">
          <h3>Net Bakiye / Kar</h3>
          <p class="value" id="rapNetKar">₺0,00</p>
        </div>
      </div>
      <div class="action-bar">
        <button class="btn-secondary" onclick="window.print()">Raporu Yazdır</button>
      </div>
    </section>

    <!-- 2. Finansal Raporlar -->
    <section id="rapor-finansal" class="sayfa">
      <h2>Finansal Durum Raporu</h2>
      <div class="stats-grid">
        <div class="stat-card">
          <h3>Toplam Satış Cirosu</h3>
          <p class="value" id="finToplamCiro">₺0,00</p>
        </div>
        <div class="stat-card">
          <h3>Teklif Tutarı Toplamı</h3>
          <p class="value" id="finTeklifTutar">₺0,00</p>
        </div>
        <div class="stat-card">
          <h3>Ortalama İşlem Tutarı</h3>
          <p class="value" id="finOrtalamaIslem">₺0,00</p>
        </div>
      </div>
    </section>

    <!-- 3. Stok Raporları -->
    <section id="rapor-stok" class="sayfa">
      <h2>Stok Durum Raporu</h2>
      <div class="stats-grid">
        <div class="stat-card">
          <h3>Toplam Ürün Kalemi</h3>
          <p class="value" id="stokKalemSayisi">0</p>
        </div>
        <div class="stat-card">
          <h3>Kritik Stok Takibi</h3>
          <p class="value" id="stokKritikSayisi" style="color:#f59e0b;">0</p>
        </div>
      </div>
      <h3>Detaylı Stok Listesi</h3>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Ürün / İlaç Adı</th><th>Mevcut Miktar</th><th>Birim</th></tr>
          </thead>
          <tbody id="stokRaporTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- 4. Müşteri Listesi Raporu -->
    <section id="rapor-musteri" class="sayfa">
      <h2>Müşteri Listesi ve Özet Raporu</h2>
      <div class="stats-grid">
        <div class="stat-card">
          <h3>Kayıtlı Müşteri Sayısı</h3>
          <p class="value" id="rapMusteriSayisi">0</p>
        </div>
      </div>
      <div class="table-responsive">
        <table>
          <thead>
            <tr><th>Müşteri Adı</th><th>Yapılan Satış Sayısı</th></tr>
          </thead>
          <tbody id="musteriRaporTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Kullanım Kayıtları Sayfası -->
    <section id="kayitlar" class="sayfa">
      <h2>Depo Kullanım Kayıtları</h2>
      <div class="table-responsive">
        <table>
          <thead>
            <tr>
              <th>Tarih</th><th>İlaç</th><th>Miktar</th>
              <th>Alan Kişi</th><th>Müşteri</th><th>Kullanım Yeri</th>
            </tr>
          </thead>
          <tbody id="hareketTablo"></tbody>
        </table>
      </div>
    </section>

    <!-- Ayarlar Sayfası -->
    <section id="ayarlar" class="sayfa">
      <h2>Sistem Ayarları</h2>
      <div class="sub-menu">
        <button id="subbtn-genel" class="sub-btn aktif-sub" onclick="ayrTabAc('genel')">Genel ve Yedek</button>
        <button id="subbtn-efatura" class="sub-btn" onclick="ayrTabAc('efatura')">E-Fatura</button>
        <button id="subbtn-kullanicilar" class="sub-btn" onclick="ayrTabAc('kullanicilar')">Kullanıcılar</button>
        <button id="subbtn-tanimlar" class="sub-btn" onclick="ayrTabAc('tanimlar')">Tanımlar</button>
        <button id="subbtn-epos" class="sub-btn" onclick="ayrTabAc('epos')">E-POS Ayarları</button>
        <button id="subbtn-sablonlar" class="sub-btn" onclick="ayrTabAc('sablonlar')">Teklif ve Özel Şablonlar</button>
        <button id="subbtn-etiket" class="sub-btn" onclick="ayrTabAc('etiket')">Etiket Şablonları</button>
      </div>

      <!-- Genel Ayarlar / Yedekleme -->
      <div id="ayr-genel" class="ayarlar-tab aktif">
        <h3 style="font-size:15px; color:#cbd5e1; margin-bottom:15px;">Veri Yönetimi</h3>
        <div style="display: flex; gap: 15px; flex-wrap: wrap;">
          <button class="btn-secondary" onclick="verileriDisariAktar()">Verileri Dışa Aktar (Yedekle)</button>
          <button class="btn-secondary" onclick="document.getElementById('jsonDosya').click()" style="background: #1e3a8a;">Verileri İçe Aktar (Geri Yükle)</button>
          <input type="file" id="jsonDosya" accept=".json" style="display:none" onchange="verileriIceAktar(event)">
          <button class="sil" onclick="verileriSifirla()" style="padding: 10px 18px; font-size: 14px;">Tüm Verileri Sıfırla</button>
        </div>
      </div>

      <!-- E-Fatura -->
      <div id="ayr-efatura" class="ayarlar-tab">
        <h3 style="font-size:15px; color:#cbd5e1; margin-bottom:15px;">E-Fatura / E-Arşiv Entegrasyonu</h3>
        <form id="efaturaForm" class="form-grid" onsubmit="event.preventDefault(); alert('E-Fatura ayarları kaydedildi.');">
          <div class="form-group">
            <label>Entegratör Firma</label>
            <select id="efatEntegrator">
              <option>Seçiniz</option>
              <option>Logo Yazılım</option>
              <option>EDM Bilişim</option>
              <option>İzibiz</option>
              <option>Uyumsoft</option>
            </select>
          </div>
          <div class="form-group">
            <label>API Kullanıcı Adı</label>
            <input id="efatKullanici" placeholder="API Kullanıcı adı">
          </div>
          <div class="form-group">
            <label>API Şifresi</label>
            <input id="efatSifre" type="password" placeholder="Şifre">
          </div>
          <div class="form-group btn-container">
            <button class="kaydet" type="submit">Ayarları Kaydet</button>
          </div>
        </form>
      </div>

      <!-- Kullanıcılar -->
      <div id="ayr-kullanicilar" class="ayarlar-tab">
        <h3 style="font-size:15px; color:#cbd5e1; margin-bottom:15px;">Kullanıcı Yönetimi</h3>
        <form id="kullaniciEkleForm" class="form-grid" onsubmit="event.preventDefault(); alert('Kullanıcı eklendi.');">
          <div class="form-group">
            <label>Ad Soyad</label>
            <input id="kulAd" required placeholder="Personel Adı">
          </div>
          <div class="form-group">
            <label>Yetki Seviyesi</label>
            <select id="kulYetki">
              <option>Depo Personeli</option>
              <option>Satış Temsilcisi</option>
              <option>Yönetici</option>
            </select>
          </div>
          <div class="form-group btn-container">
            <button class="kaydet" type="submit">Kullanıcı Ekle</button>
          </div>
        </form>
      </div>

      <!-- Diğer Ayar Sekmeleri -->
      <div id="ayr-tanimlar" class="ayarlar-tab">
        <p style="color:#94a3b8;">Genel tanım ve parametre ayarları buradan yapılandırılır.</p>
      </div>
      <div id="ayr-epos" class="ayarlar-tab">
        <p style="color:#94a3b8;">Sanal POS ve ödeme altyapısı ayarları.</p>
      </div>
      <div id="ayr-sablonlar" class="ayarlar-tab">
        <p style="color:#94a3b8;">Teklif ve yazdırma taslak düzenlemeleri.</p>
      </div>
      <div id="ayr-etiket" class="ayarlar-tab">
        <p style="color:#94a3b8;">Ürün ve varil etiket baskı şablonları.</p>
      </div>
    </section>

    <!-- Notlar / Yedek Sayfası -->
    <section id="notlar" class="sayfa">
      <h2>Hızlı Notlar & Çalışma Defteri</h2>
      <div class="form-group full">
        <textarea id="notAlani" rows="12" placeholder="Operasyon notları, hatırlatmalar veya yapılacaklar listesi..." style="line-height:1.6;"></textarea>
      </div>
      <div style="margin-top:15px;">
        <button class="kaydet" onclick="notKaydet()">Notları Kaydet</button>
      </div>
    </section>
  </main>

  <script>
    // Başlangıç Verileri ve Yerel Depolama (localStorage) Yapılandırması
    let veri = JSON.parse(localStorage.getItem('fumigasyon_veri')) || {
      musteriler: ['ABC Gıda Ltd. Şti.', 'Ofi Fındık', 'Ferrero Fındık', 'Şenocak Fındık'],
      tedarikciler: ['Kimya A.Ş.', 'Bayer Crop', 'Kobi İlaç'],
      stoklar: [
        { id: 1, ilacAdi: 'Alüminyum Fosfit', miktar: 25.0, birim: 'kg' },
        { id: 2, ilacAdi: 'Magnezyum Fosfit', miktar: 4.0, birim: 'kg' },
        { id: 3, ilacAdi: 'Deltamethrin %2.5', miktar: 12.0, birim: 'litre' }
      ],
      alislar: [],
      cikislar: [],
      satislar: [],
      teklifler: [],
      urunTanimlari: [],
      depolar: [{ id: 1, ad: 'Merkez Depo' }],
      uretimler: [],
      ozelFiyatlar: [],
      notlar: ''
    };

    function verileriKaydet() {
      localStorage.setItem('fumigasyon_veri', JSON.stringify(veri));
      guncelle();
    }

    // Tarih Ayarlamaları
    document.addEventListener("DOMContentLoaded", () => {
      const bugun = new Date().toISOString().split('T')[0];
      ['alisTarih', 'uretimTarih', 'cikisTarih', 'baslangicTarih', 'bitisTarih', 'teklifTarih'].forEach(id => {
        const el = document.getElementById(id);
        if (el) el.value = bugun;
      });
      if (veri.notlar) document.getElementById('notAlani').value = veri.notlar;
      guncelle();

      // Dışarı tıklandığında filtre menüsünü kapatma
      document.addEventListener('click', (e) => {
        const container = document.getElementById('filtreDropdownContainer');
        if (container && !container.contains(e.target)) {
          const menu = document.getElementById('filtreDropdownMenu');
          if (menu) menu.classList.remove('acik');
        }
      });
    });

    // Menü ve Navigasyon Mantığı
    function sayfaAc(sayfaId) {
      document.querySelectorAll('.sayfa').forEach(s => s.classList.remove('aktif'));
      document.querySelectorAll('.menu-btn').forEach(b => b.classList.remove('aktif-menu'));
      
      const hedefSayfa = document.getElementById(sayfaId);
      if (hedefSayfa) hedefSayfa.classList.add('aktif');

      const hedefBtn = document.getElementById('btn-' + sayfaId);
      if (hedefBtn) hedefBtn.classList.add('aktif-menu');

      // Mobilde tıklanınca içeriğe yumuşak kaydırma
      if (window.innerWidth <= 768) {
        window.scrollTo({ top: document.querySelector('main').offsetTop - 10, behavior: 'smooth' });
      }
    }

    function ayrTabAc(tabId) {
      document.querySelectorAll('.ayarlar-tab').forEach(t => t.classList.remove('aktif'));
      document.querySelectorAll('.sub-btn').forEach(b => b.classList.remove('aktif-sub'));

      document.getElementById('ayr-' + tabId).classList.add('aktif');
      document.getElementById('subbtn-' + tabId).classList.add('aktif-sub');
    }

    /* DÜZELTME: getComputedStyle ile mevcut görünürlük durumunun doğru tespiti sağlandı */
    function menuGrupToggle(subContainerId, iconId) {
      const subItems = document.getElementById(subContainerId);
      const icon = document.getElementById(iconId);
      const currentDisplay = window.getComputedStyle(subItems).display;
      
      if (currentDisplay === 'none') {
        subItems.style.display = 'flex';
        icon.innerText = '−';
      } else {
        subItems.style.display = 'none';
        icon.innerText = '+';
      }
    }

    // Belge Tipi Filtre Aç/Kapat ve Kontrolleri
    function filtreDropdownToggle() {
      const menu = document.getElementById('filtreDropdownMenu');
      menu.classList.toggle('acik');
    }

    function filtreTumunuSec(masterCb) {
      const cbs = document.querySelectorAll('.belge-filtre-cb');
      cbs.forEach(cb => cb.checked = masterCb.checked);
      filtreGuncelleMetin();
      guncelle();
    }

    function filtreCheckboxDegisti() {
      const cbs = document.querySelectorAll('.belge-filtre-cb');
      const masterCb = document.getElementById('filtreTumunuSec');
      const tumuSecili = Array.from(cbs).every(cb => cb.checked);
      
      masterCb.checked = tumuSecili;
      filtreGuncelleMetin();
      guncelle();
    }

    function filtreGuncelleMetin() {
      const cbs = document.querySelectorAll('.belge-filtre-cb');
      const secilenler = Array.from(cbs).filter(cb => cb.checked).map(cb => cb.value);
      const textEl = document.getElementById('filtreSecimText');

      if (secilenler.length === cbs.length) {
        textEl.innerText = "Tüm Belge Tipleri";
      } else if (secilenler.length === 0) {
        textEl.innerText = "Seçilmedi";
      } else if (secilenler.length === 1) {
        textEl.innerText = secilenler[0];
      } else {
        textEl.innerText = secilenler.length + " Belge Tipi Seçili";
      }
    }

    // CRUD ve Form İşlemleri
    function musteriEkle(e) {
      e.preventDefault();
      const input = document.getElementById('yeniMusteri');
      if (input.value.trim()) {
        veri.musteriler.push(input.value.trim());
        input.value = '';
        verileriKaydet();
      }
    }
    function musteriSil(index) {
      veri.musteriler.splice(index, 1);
      verileriKaydet();
    }

    function tedarikciEkle(e) {
      e.preventDefault();
      const input = document.getElementById('yeniTedarikci');
      if (input.value.trim()) {
        veri.tedarikciler.push(input.value.trim());
        input.value = '';
        verileriKaydet();
      }
    }
    function tedarikciSil(index) {
      veri.tedarikciler.splice(index, 1);
      verileriKaydet();
    }

    function stokEkle(e) {
      e.preventDefault();
      const ad = document.getElementById('ilacAdi').value.trim();
      const miktar = parseFloat(document.getElementById('stokMiktar').value);
      const birim = document.getElementById('birim').value;

      const varMidir = veri.stoklar.find(s => s.ilacAdi.toLowerCase() === ad.toLowerCase());
      if (varMidir) {
        varMidir.miktar += miktar;
      } else {
        veri.stoklar.push({ id: Date.now(), ilacAdi: ad, miktar, birim });
      }
      document.getElementById('stokForm').reset();
      verileriKaydet();
    }
    function stokSil(id) {
      veri.stoklar = veri.stoklar.filter(s => s.id !== id);
      verileriKaydet();
    }

    function alisEkle(e) {
      e.preventDefault();
      const tedarikci = document.getElementById('alisTedarikci').value;
      const ilac = document.getElementById('alisIlac').value;
      const miktar = parseFloat(document.getElementById('alisMiktar').value);
      const birimFiyat = parseFloat(document.getElementById('alisBirimFiyat').value);
      const tarih = document.getElementById('alisTarih').value;

      veri.alislar.push({
        id: Date.now(), tarih, tedarikci, ilac, miktar, birimFiyat, toplam: miktar * birimFiyat
      });

      const stokUrun = veri.stoklar.find(s => s.ilacAdi === ilac);
      if (stokUrun) stokUrun.miktar += miktar;

      document.getElementById('alisForm').reset();
      verileriKaydet();
    }
    function alisSil(id) {
      veri.alislar = veri.alislar.filter(a => a.id !== id);
      verileriKaydet();
    }

    function cikisYap(e) {
      e.preventDefault();
      const ilac = document.getElementById('cikisIlac').value;
      const alanKisi = document.getElementById('alanKisi').value.trim();
      const musteri = document.getElementById('cikisMusteri').value;
      const kullanimYeri = document.getElementById('kullanimYeri').value.trim();
      const miktar = parseFloat(document.getElementById('cikisMiktar').value);
      const tarih = document.getElementById('cikisTarih').value;

      const stokUrun = veri.stoklar.find(s => s.ilacAdi === ilac);
      if (stokUrun && stokUrun.miktar >= miktar) {
        stokUrun.miktar -= miktar;
        veri.cikislar.push({ id: Date.now(), tarih, ilac, miktar, alanKisi, musteri, kullanimYeri });
        document.getElementById('cikisForm').reset();
        verileriKaydet();
        alert('Depo çıkışı yapıldı ve stoktan düşüldü.');
      } else {
        alert('Yetersiz stok! Mevcut stok: ' + (stokUrun ? stokUrun.miktar : 0));
      }
    }

    function satisEkle(e) {
      e.preventDefault();
      const baslangic = document.getElementById('baslangicTarih').value;
      const bitis = document.getElementById('bitisTarih').value;

      if (bitis < baslangic) {
        alert("Bitiş tarihi Başlangıç tarihinden önce olamaz");
        return;
      }

      const ilac = document.getElementById('satisIlac').value;
      const musteri = document.getElementById('satisMusteri').value;
      const belgeTipi = document.getElementById('satisBelgeTipi').value;
      const miktar = parseFloat(document.getElementById('satisMiktar').value);
      const birimFiyat = parseFloat(document.getElementById('birimFiyat').value);

      const stokUrun = veri.stoklar.find(s => s.ilacAdi === ilac);
      if (stokUrun && stokUrun.miktar >= miktar) {
        stokUrun.miktar -= miktar;
        veri.satislar.push({
          id: Date.now(), baslangic, bitis, belgeTipi, ilac, musteri, miktar, birimFiyat, toplam: miktar * birimFiyat
        });
        document.getElementById('satisForm').reset();
        
        const bugun = new Date().toISOString().split('T')[0];
        document.getElementById('baslangicTarih').value = bugun;
        document.getElementById('bitisTarih').value = bugun;

        verileriKaydet();
        alert('Satış kaydedildi.');
      } else {
        alert('Yetersiz stok! Mevcut stok: ' + (stokUrun ? stokUrun.miktar : 0));
      }
    }

    function satisSil(id) {
      const satisKaydi = veri.satislar.find(s => s.id === id);
      if (satisKaydi) {
        const stokUrun = veri.stoklar.find(s => s.ilacAdi === satisKaydi.ilac);
        if (stokUrun) {
          stokUrun.miktar += satisKaydi.miktar;
        }
      }
      veri.satislar = veri.satislar.filter(s => s.id !== id);
      verileriKaydet();
    }

    function teklifEkle(e) {
      e.preventDefault();
      const musteri = document.getElementById('teklifMusteri').value;
      const konu = document.getElementById('teklifKonu').value.trim();
      const tutar = parseFloat(document.getElementById('teklifTutar').value);
      const tarih = document.getElementById('teklifTarih').value;
      const aciklama = document.getElementById('teklifAciklama').value.trim();

      veri.teklifler.push({ id: Date.now(), tarih, musteri, konu, tutar, aciklama });
      document.getElementById('teklifForm').reset();
      verileriKaydet();
    }
    function teklifSil(id) {
      veri.teklifler = veri.teklifler.filter(t => t.id !== id);
      verileriKaydet();
    }

    function urunTanimiEkle(e) {
      e.preventDefault();
      const ad = document.getElementById('tanimAd').value.trim();
      const tur = document.getElementById('tanimTur').value;
      const fiyat = parseFloat(document.getElementById('tanimFiyat').value);
      veri.urunTanimlari.push({ id: Date.now(), ad, tur, fiyat });
      document.getElementById('urunTanimiForm').reset();
      verileriKaydet();
    }
    function urunTanimiSil(id) {
      veri.urunTanimlari = veri.urunTanimlari.filter(u => u.id !== id);
      verileriKaydet();
    }

    function depoEkle(e) {
      e.preventDefault();
      const ad = document.getElementById('depoAd').value.trim();
      veri.depolar.push({ id: Date.now(), ad });
      document.getElementById('depoForm').reset();
      verileriKaydet();
    }
    function depoSil(id) {
      veri.depolar = veri.depolar.filter(d => d.id !== id);
      verileriKaydet();
    }

    function uretimEkle(e) {
      e.preventDefault();
      const urun = document.getElementById('uretimUrun').value.trim();
      const miktar = parseFloat(document.getElementById('uretimMiktar').value);
      const tarih = document.getElementById('uretimTarih').value;
      veri.uretimler.push({ id: Date.now(), urun, miktar, tarih });
      document.getElementById('uretimForm').reset();
      verileriKaydet();
    }

    function ozelFiyatEkle(e) {
      e.preventDefault();
      const musteri = document.getElementById('ozelMusteri').value;
      const urun = document.getElementById('ozelUrun').value.trim();
      const fiyat = parseFloat(document.getElementById('ozelFiyatDeger').value);
      veri.ozelFiyatlar.push({ id: Date.now(), musteri, urun, fiyat });
      document.getElementById('ozelFiyatForm').reset();
      verileriKaydet();
    }

    function notKaydet() {
      veri.notlar = document.getElementById('notAlani').value;
      verileriKaydet();
      alert('Notlar kaydedildi.');
    }

    // Ekran Güncelleme ve Görünüm Oluşturma
    function guncelle() {
      const mSelects = ['cikisMusteri', 'satisMusteri', 'teklifMusteri', 'ozelMusteri'];
      mSelects.forEach(id => {
        const el = document.getElementById(id);
        if (el) el.innerHTML = veri.musteriler.map(m => `<option>${m}</option>`).join('');
      });

      const tSelect = document.getElementById('alisTedarikci');
      if (tSelect) tSelect.innerHTML = veri.tedarikciler.map(t => `<option>${t}</option>`).join('');

      const iSelects = ['alisIlac', 'cikisIlac', 'satisIlac'];
      iSelects.forEach(id => {
        const el = document.getElementById(id);
        if (el) el.innerHTML = veri.stoklar.map(s => `<option>${s.ilacAdi}</option>`).join('');
      });

      const mAra = (document.getElementById('musteriAra')?.value || '').toLowerCase();
      document.getElementById('musteriTablo').innerHTML = veri.musteriler
        .filter(m => m.toLowerCase().includes(mAra))
        .map((m, i) => `<tr><td>${m}</td><td><button class="sil" onclick="musteriSil(${i})">Sil</button></td></tr>`).join('');

      const tAra = (document.getElementById('tedarikciAra')?.value || '').toLowerCase();
      document.getElementById('tedarikciTablo').innerHTML = veri.tedarikciler
        .filter(t => t.toLowerCase().includes(tAra))
        .map((t, i) => `<tr><td>${t}</td><td><button class="sil" onclick="tedarikciSil(${i})">Sil</button></td></tr>`).join('');

      const sAra = (document.getElementById('stokAra')?.value || '').toLowerCase();
      document.getElementById('stokTablo').innerHTML = veri.stoklar
        .filter(s => s.ilacAdi.toLowerCase().includes(sAra))
        .map(s => `<tr><td><strong>${s.ilacAdi}</strong></td><td>${s.miktar}</td><td>${s.birim}</td><td><button class="sil" onclick="stokSil(${s.id})">Sil</button></td></tr>`).join('');

      document.getElementById('alisTablo').innerHTML = veri.alislar.map(a => 
        `<tr><td>${a.tarih}</td><td>${a.tedarikci}</td><td>${a.ilac}</td><td>${a.miktar}</td><td>₺${a.birimFiyat.toFixed(2)}</td><td>₺${a.toplam.toFixed(2)}</td><td><button class="sil" onclick="alisSil(${a.id})">Sil</button></td></tr>`
      ).join('');

      // Satışlar Filtreleme Mantığı
      const satisAraMetin = (document.getElementById('satisAra')?.value || '').toLowerCase();
      const secilenBelgeTipleri = Array.from(document.querySelectorAll('.belge-filtre-cb'))
        .filter(cb => cb.checked)
        .map(cb => cb.value);

      const filtrelenmisSatislar = veri.satislar.filter(s => {
        const tip = s.belgeTipi || 'Sipariş';
        const tipUygun = secilenBelgeTipleri.includes(tip);
        const aramaUygun = s.musteri.toLowerCase().includes(satisAraMetin) || s.ilac.toLowerCase().includes(satisAraMetin);
        return tipUygun && aramaUygun;
      });

      document.getElementById('satisTablo').innerHTML = filtrelenmisSatislar.map(s => 
        `<tr><td>${s.baslangic}</td><td>${s.bitis}</td><td><span style="background: rgba(16, 185, 129, 0.15); color: #34d399; padding: 2px 8px; border-radius: 4px; font-size: 12px; font-weight: 600;">${s.belgeTipi || 'Sipariş'}</span></td><td>${s.ilac}</td><td>${s.miktar}</td><td>${s.musteri}</td><td>₺${s.birimFiyat.toFixed(2)}</td><td>₺${s.toplam.toFixed(2)}</td><td><button class="sil" onclick="satisSil(${s.id})">Sil</button></td></tr>`
      ).join('');

      document.getElementById('teklifTablo').innerHTML = veri.teklifler.map(t => 
        `<tr><td>${t.tarih}</td><td>${t.musteri}</td><td>${t.konu}</td><td>₺${t.tutar.toFixed(2)}</td><td>${t.aciklama}</td><td><button class="sil" onclick="teklifSil(${t.id})">Sil</button></td></tr>`
      ).join('');

      document.getElementById('hareketTablo').innerHTML = veri.cikislar.map(c => 
        `<tr><td>${c.tarih}</td><td>${c.ilac}</td><td>${c.miktar}</td><td>${c.alanKisi}</td><td>${c.musteri}</td><td>${c.kullanimYeri}</td></tr>`
      ).join('');

      document.getElementById('urunTanimiTablo').innerHTML = veri.urunTanimlari.map(u => 
        `<tr><td>${u.ad}</td><td>${u.tur}</td><td>₺${u.fiyat.toFixed(2)}</td><td><button class="sil" onclick="urunTanimiSil(${u.id})">Sil</button></td></tr>`
      ).join('');

      document.getElementById('depoTablo').innerHTML = veri.depolar.map(d => 
        `<tr><td>${d.ad}</td><td><button class="sil" onclick="depoSil(${d.id})">Sil</button></td></tr>`
      ).join('');

      document.getElementById('uretimTablo').innerHTML = veri.uretimler.map(u => 
        `<tr><td>${u.tarih}</td><td>${u.urun}</td><td>${u.miktar}</td><td>-</td></tr>`
      ).join('');

      document.getElementById('ozelFiyatTablo').innerHTML = veri.ozelFiyatlar.map(o => 
        `<tr><td>${o.musteri}</td><td>${o.urun}</td><td>₺${o.fiyat.toFixed(2)}</td><td>-</td></tr>`
      ).join('');

      const toplamCiro = veri.satislar.reduce((acc, s) => acc + s.toplam, 0);
      const toplamAlis = veri.alislar.reduce((acc, a) => acc + a.toplam, 0);
      const toplamTeklif = veri.teklifler.reduce((acc, t) => acc + t.tutar, 0);

      document.getElementById('dashCiro').innerText = '₺' + toplamCiro.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      document.getElementById('dashMusteri').innerText = veri.musteriler.length;
      document.getElementById('dashStokCesit').innerText = veri.stoklar.length;
      document.getElementById('dashTeklif').innerText = veri.teklifler.length;

      document.getElementById('rapToplamAlis').innerText = '₺' + toplamAlis.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      document.getElementById('rapToplamSatis').innerText = '₺' + toplamCiro.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      
      const netKar = toplamCiro - toplamAlis;
      const netKarEl = document.getElementById('rapNetKar');
      netKarEl.innerText = '₺' + netKar.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      netKarEl.style.color = netKar >= 0 ? '#34d399' : '#ef4444';

      document.getElementById('finToplamCiro').innerText = '₺' + toplamCiro.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      document.getElementById('finTeklifTutar').innerText = '₺' + toplamTeklif.toLocaleString('tr-TR', { minimumFractionDigits: 2 });
      
      const ortIslem = veri.satislar.length > 0 ? (toplamCiro / veri.satislar.length) : 0;
      document.getElementById('finOrtalamaIslem').innerText = '₺' + ortIslem.toLocaleString('tr-TR', { minimumFractionDigits: 2 });

      document.getElementById('stokKalemSayisi').innerText = veri.stoklar.length;
      const kritikStoklar = veri.stoklar.filter(s => s.miktar <= 5);
      document.getElementById('stokKritikSayisi').innerText = kritikStoklar.length;

      document.getElementById('stokRaporTablo').innerHTML = veri.stoklar.map(s => 
        `<tr><td>${s.ilacAdi}</td><td>${s.miktar}</td><td>${s.birim}</td></tr>`
      ).join('');

      document.getElementById('rapMusteriSayisi').innerText = veri.musteriler.length;
      document.getElementById('musteriRaporTablo').innerHTML = veri.musteriler.map(m => {
        const satisSayisi = veri.satislar.filter(s => s.musteri === m).length;
        return `<tr><td>${m}</td><td>${satisSayisi} adet satış</td></tr>`;
      }).join('');

      const kritikContainer = document.getElementById('kritikStokListesi');
      if (kritikStoklar.length > 0) {
        kritikContainer.innerHTML = kritikStoklar.map(s => `
          <div class="kritik-item">
            <span><strong>${s.ilacAdi}</strong></span>
            <span style="color:#ef4444; font-weight:700;">Kritik Stok: ${s.miktar} ${s.birim}</span>
          </div>
        `).join('');
      } else {
        kritikContainer.innerHTML = '<p style="color: #94a3b8; font-size: 13px;">Kritik seviyede ürün bulunmuyor.</p>';
      }
    }

    function verileriDisariAktar() {
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(veri, null, 2));
      const downloadAnchor = document.createElement('a');
      downloadAnchor.setAttribute("href", dataStr);
      downloadAnchor.setAttribute("download", "fumigasyon_stok_yedek_" + new Date().toISOString().split('T')[0] + ".json");
      document.body.appendChild(downloadAnchor);
      downloadAnchor.click();
      downloadAnchor.remove();
    }

    function verileriIceAktar(e) {
      const file = e.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function(evt) {
        try {
          veri = JSON.parse(evt.target.result);
          verileriKaydet();
          alert('Veriler başarıyla içe aktarıldı!');
        } catch (err) {
          alert('Geçersiz JSON dosyası!');
        }
      };
      reader.readAsText(file);
    }

    function verileriSifirla() {
      if (confirm('Tüm kayıtların silineceğinden emin misiniz? Bu işlem geri alınamaz!')) {
        localStorage.removeItem('fumigasyon_veri');
        location.reload();
      }
    }
  </script>
</body>
</html>
