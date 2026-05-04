<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
  <title>CASIR | Canteen Smart Integrated Resto</title>
  <!-- Font Awesome & Google Fonts -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
  <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,600;14..32,700&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Inter', sans-serif;
    }
    body {
      background: #fef9e8;
      color: #2d2a24;
    }
    /* Navigation */
    .navbar {
      background: #ffb347;
      background: linear-gradient(135deg, #ff9a3c, #ff6f3c);
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
      gap: 1rem;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
    }
    .logo {
      font-size: 1.8rem;
      font-weight: 800;
      color: white;
      text-shadow: 2px 2px 0 #c13b00;
    }
    .nav-links {
      display: flex;
      gap: 1rem;
      flex-wrap: wrap;
    }
    .nav-btn {
      background: white;
      border: none;
      padding: 0.5rem 1.2rem;
      border-radius: 40px;
      font-weight: 600;
      cursor: pointer;
      transition: 0.2s;
      color: #ff6f3c;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    }
    .nav-btn.active {
      background: #2d2a24;
      color: white;
    }
    .nav-btn:hover {
      transform: translateY(-2px);
      background: #ffe2c9;
    }
    /* main container */
    .container {
      max-width: 1300px;
      margin: 2rem auto;
      padding: 0 1.5rem;
    }
    .page {
      display: none;
      animation: fade 0.3s ease;
    }
    .active-page {
      display: block;
    }
    @keyframes fade {
      from {opacity: 0; transform: translateY(8px);}
      to {opacity: 1;}
    }
    /* card grids */
    .card-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
      gap: 1.8rem;
      margin-top: 1.5rem;
    }
    .menu-card, .canteen-card {
      background: white;
      border-radius: 28px;
      padding: 1rem;
      box-shadow: 0 10px 20px rgba(0,0,0,0.05);
      transition: 0.2s;
      border: 1px solid #ffe0b5;
    }
    .menu-card:hover { transform: scale(1.01); background: #fffaf2; }
    .menu-title {
      font-weight: 800;
      font-size: 1.3rem;
    }
    .price {
      color: #ff6f3c;
      font-weight: bold;
      font-size: 1.2rem;
    }
    button {
      background: #ff9a3c;
      border: none;
      padding: 8px 16px;
      border-radius: 60px;
      font-weight: 600;
      cursor: pointer;
      margin-top: 8px;
      transition: 0.2s;
    }
    button:hover { background: #e07c2c; color: white; }
    .status-badge {
      background: #eee5da;
      border-radius: 20px;
      padding: 4px 12px;
      font-size: 0.8rem;
    }
    .cart-sidebar {
      background: #fff6ea;
      border-radius: 24px;
      padding: 1.2rem;
      margin-top: 2rem;
      border: 1px solid #ffdcb0;
    }
    hr { margin: 1rem 0; }
    input, select {
      padding: 8px 12px;
      border-radius: 40px;
      border: 1px solid #ffcf9a;
      background: white;
    }
    .flex-between {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .track-step {
      background: #e9e2d4;
      border-radius: 40px;
      padding: 0.5rem 1rem;
      margin: 0.5rem 0;
    }
    .admin-panel {
      background: #2d2a24;
      color: #fae6cf;
      padding: 1.5rem;
      border-radius: 28px;
    }
    @media (max-width: 700px) {
      .navbar { flex-direction: column; }
    }
  </style>
</head>
<body>
<div class="navbar">
  <div class="logo">🍱 CASIR • Kantin Sekolah</div>
  <div class="nav-links" id="navLinks"></div>
  <div><span id="userDisplay">👤 Guest</span></div>
</div>
<div class="container" id="appRoot"></div>

<script>
  // ----------------------------- DATA MODEL ---------------------------------
  // Canteens & menus
  const canteens = [
    { id: "c1", name: "Kantin Sehat HMC", menus: [{ id:"m1", name:"Nasi Ayam Bakar", price:15000},{ id:"m2", name:"Jus Alpukat", price:8000},{ id:"m3", name:"Salad Buah", price:12000}] },
    { id: "c2", name: "Dapur Nusantara", menus: [{ id:"m4", name:"Soto Ayam", price:13000},{ id:"m5", name:"Es Teh Tarik", price:5000},{ id:"m6", name:"Pisang Goreng", price:7000}] },
    { id: "c3", name: "Western Canteen", menus: [{ id:"m7", name:"Spaghetti", price:18000},{ id:"m8", name:"French Fries", price:10000},{ id:"m9", name:"Lemon Tea", price:6000}] }
  ];

  // 5 Ustadz membership (20% discount)
  const membershipUsers = [
    { id:1, name:"Ustadz Abdul Somad", email:"ustadz1@canteen.com", password:"pass1", role:"user", membership:true },
    { id:2, name:"Ustadz Hanan Attaki", email:"ustadz2@canteen.com", password:"pass2", role:"user", membership:true },
    { id:3, name:"Ustadz Adi Hidayat", email:"ustadz3@canteen.com", password:"pass3", role:"user", membership:true },
    { id:4, name:"Ustadz Das'ad Latif", email:"ustadz4@canteen.com", password:"pass4", role:"user", membership:true },
    { id:5, name:"Ustadz Khalid Basalamah", email:"ustadz5@canteen.com", password:"pass5", role:"user", membership:true }
  ];
  // admin dummy
  const adminUser = { id:99, name:"Admin CASIR", email:"admin@casir.com", password:"admin123", role:"admin", membership:false };

  let currentUser = null;         // logged user
  let cart = [];                  // { menuItem, canteenId, canteenName, quantity, basePrice }
  let orders = [];               // each order: { id, userId, items, totalPrice, finalPrice, deliveryMode, queueBooking, trackingStatus, createdAt, deliveryAddress }
  let nextOrderId = 101;
  
  // Helper: get membership discount
  function getDiscountedPrice(originalPrice, user) {
    if(user && user.membership) return originalPrice * 0.8;
    return originalPrice;
  }

  // peak hour fee (jam padat) Rp 2000 for online queue booking
  const PEAK_FEE = 2000;
  
  // ----------- RENDER NAVIGATION & PAGES ----------
  const pages = ["landing", "auth", "transaksi", "utama", "userAdmin", "menuKantin"];
  let activePage = "landing";
  
  function renderNav() {
    const navDiv = document.getElementById("navLinks");
    if (!navDiv) return;
    const pageNames = {
      landing: "🏠 Beranda", auth: "🔐 Login/Daftar", transaksi: "🧾 Transaksi",
      utama: "⭐ Utama", userAdmin: "👤 Profil & Admin", menuKantin: "🍽️ Menu Kantin"
    };
    let html = "";
    for (let p of pages) {
      html += `<button class="nav-btn ${activePage === p ? 'active' : ''}" data-page="${p}">${pageNames[p] || p}</button>`;
    }
    navDiv.innerHTML = html;
    document.querySelectorAll(".nav-btn").forEach(btn => {
      btn.addEventListener("click", (e) => {
        activePage = btn.dataset.page;
        renderNav();
        renderCurrentPage();
      });
    });
  }

  function renderCurrentPage() {
    const root = document.getElementById("appRoot");
    if (!root) return;
    if (activePage === "landing") root.innerHTML = renderLanding();
    else if (activePage === "auth") root.innerHTML = renderAuth();
    else if (activePage === "transaksi") root.innerHTML = renderTransaksi();
    else if (activePage === "utama") root.innerHTML = renderUtama();
    else if (activePage === "userAdmin") root.innerHTML = renderUserAdmin();
    else if (activePage === "menuKantin") root.innerHTML = renderMenuKantin();
    attachPageScripts();
  }

  // ------------- LANDING PAGE ----------
  function renderLanding() {
    return `
      <div style="text-align:center">
        <h1>🍲 Selamat Datang di CASIR</h1>
        <p>Canteen pintar dengan sistem membership, pesan antar, dan booking antrean online!</p>
        <div style="background:#fff2e0; border-radius:32px; padding:1.5rem; margin-top:1rem">
          <h3>✨ Keunggulan ✨</h3>
          <ul style="list-style:none; line-height:2"><li>✅ Membership khusus 5 Ustadz (diskon 20%)</li><li>✅ Biaya antrean jam padat hanya Rp2000</li><li>✅ Pesan antar & tracking delivery realtime</li><li>✅ CMS admin untuk kelola order</li><li>✅ Menu dari 3 kantin berbeda</li></ul>
        </div>
        <button id="gotoAuthBtn" style="margin-top:1.5rem;background:#ff6f3c;color:white">Mulai Belanja / Login</button>
      </div>
    `;
  }

  // ---------- AUTH (Login Register) -------------
  function renderAuth() {
    return `
      <div style="max-width:460px; margin:0 auto; background:white; border-radius:32px; padding:2rem; box-shadow:0 10px 20px rgba(0,0,0,0.1)">
        <h3>🔑 Login / Daftar Member</h3>
        <div id="authMsg" class="status-badge" style="margin:10px 0"></div>
        <input type="text" id="loginEmail" placeholder="Email" style="width:100%; margin:8px 0" /><br/>
        <input type="password" id="loginPass" placeholder="Password" style="width:100%; margin:8px 0"/><br/>
        <button id="doLoginBtn">Login</button>
        <hr/>
        <h4>Daftar Akun Baru (User biasa)</h4>
        <input type="text" id="regName" placeholder="Nama Lengkap" style="width:100%; margin:5px 0"/>
        <input type="email" id="regEmail" placeholder="Email" style="width:100%; margin:5px 0"/>
        <input type="password" id="regPass" placeholder="Password" style="width:100%; margin:5px 0"/>
        <button id="doRegisterBtn">Daftar</button>
        <p style="margin-top:12px; font-size:0.8rem">*Akun membership Ustadz: coba ustadz1@canteen.com / pass1 (diskon 20%)</p>
      </div>
    `;
  }

  // -------- UTAMA (Main/home) show canteen summary & promo ------
  function renderUtama() {
    if(!currentUser) return `<div class="status-badge">⚠️ Silakan login terlebih dahulu. <button id="forceAuth">Login</button></div>`;
    const userDiscount = currentUser.membership ? "✅ Aktif (diskon 20%)" : "❌ Tidak aktif";
    return `
      <h2>🏫 Halaman Utama CASIR</h2>
      <div style="background:#fdebd0; border-radius:24px; padding:1rem; margin:1rem 0">
        <p>👋 Halo, <strong>${currentUser.name}</strong> (${currentUser.role}) | Membership: ${userDiscount}</p>
        <p>🍱 Jelajahi menu kantin favoritmu, pesan antar, atau booking antrean online!</p>
      </div>
      <div class="card-grid">
        ${canteens.map(c => `<div class="canteen-card"><i class="fas fa-utensils"></i> <strong>${c.name}</strong><br/><span>${c.menus.length} menu tersedia</span><br/><button class="gotoCanteenBtn" data-cid="${c.id}">Lihat Menu</button></div>`).join('')}
      </div>
      <div class="cart-sidebar"><i class="fas fa-shopping-cart"></i> <strong>Keranjang Sementara</strong> <span id="cartPreview">${cart.length} item</span> <button id="goToTransaksiBtn">Ke Halaman Transaksi</button></div>
    `;
  }

  // ---------- MENU PER KANTIN (menu setiap kantin) ----------
  function renderMenuKantin() {
    if(!currentUser) return `<div>Silakan login untuk melihat menu dan pesan. <button id="forceAuth">Login</button></div>`;
    let html = `<h2>📋 Semua Menu Kantin</h2>`;
    for(let canteen of canteens) {
      html += `<div style="margin-top:2rem;"><h3>🍴 ${canteen.name}</h3><div class="card-grid">`;
      canteen.menus.forEach(menu => {
        const finalPriceDisplay = currentUser.membership ? Math.round(menu.price*0.8) : menu.price;
        html += `<div class="menu-card">
          <div class="menu-title">${menu.name}</div>
          <div class="price">Rp ${menu.price.toLocaleString()}</div>
          ${currentUser.membership ? `<small style="color:green">✨ Member price: Rp ${finalPriceDisplay}</small><br/>` : ''}
          <button class="addToCartBtn" data-canteen='${JSON.stringify(canteen)}' data-menu='${JSON.stringify(menu)}'>+ Tambah ke Keranjang</button>
        </div>`;
      });
      html += `</div></div>`;
    }
    html += `<div class="cart-sidebar"><strong>🛒 Keranjang saat ini:</strong> ${cart.length} item | <button id="gotoTransaksiFromMenu">Proses Transaksi</button></div>`;
    return html;
  }

  // ---------- TRANSACTION PAGE (with queue booking & delivery) ----------
  function renderTransaksi() {
    if(!currentUser) return `<div>Silakan login untuk transaksi. <button id="forceAuth">Login</button></div>`;
    let subtotal = 0;
    cart.forEach(item => { subtotal += item.basePrice * item.quantity; });
    let memberDiscountVal = 0;
    let finalAfterDiscount = subtotal;
    if(currentUser.membership) {
      finalAfterDiscount = subtotal * 0.8;
      memberDiscountVal = subtotal - finalAfterDiscount;
    }
    let deliveryFee = 0;
    let queueFee = 0;
    // form controls for delivery & queue
    return `
      <h2>🧾 Halaman Transaksi</h2>
      <div class="cart-sidebar">
        <h3>Keranjang Belanja</h3>
        ${cart.length === 0 ? "<p>Keranjang kosong. Tambahkan menu dari Menu Kantin.</p>" : 
          cart.map((it,idx)=> `<div class="flex-between"><span>${it.menuItem.name} x${it.quantity}</span><span>Rp ${(it.basePrice * it.quantity).toLocaleString()}</span></div>`).join('')}
        <hr/>
        <div class="flex-between"><span>Subtotal:</span><span>Rp ${subtotal.toLocaleString()}</span></div>
        ${currentUser.membership ? `<div class="flex-between" style="color:green"><span>Diskon Member (20%):</span><span>-Rp ${memberDiscountVal.toLocaleString()}</span></div>` : ''}
        <div class="flex-between"><strong>Total setelah diskon:</strong><strong>Rp ${finalAfterDiscount.toLocaleString()}</strong></div>
        <hr/>
        <div style="margin: 12px 0">
          <label><input type="checkbox" id="queueBookingCheckbox"> 📌 Booking Antrean Online (+Rp ${PEAK_FEE.toLocaleString()} biaya jam padat)</label><br/>
          <label><input type="checkbox" id="deliveryCheckbox"> 🛵 Pesan Antar (Delivery Order) - Gratis ongkir area sekolah</label>
          <div id="deliveryAddressDiv" style="display:none; margin-top:8px"><input type="text" id="deliveryAddress" placeholder="Alamat lengkap untuk delivery" style="width:100%"/></div>
        </div>
        <div><button id="confirmOrderBtn">✅ Konfirmasi & Buat Pesanan</button> <button id="clearCartBtn">🗑️ Kosongkan Keranjang</button></div>
      </div>
      <div style="margin-top:2rem">
        <h3>📦 Pesanan Saya & Tracking</h3>
        <div id="orderHistoryList"></div>
      </div>
    `;
  }

  // ---------- USER & ADMIN CMS page ----------
  function renderUserAdmin() {
    if(!currentUser) return `<div>Login untuk melihat profil. <button id="forceAuth">Login</button></div>`;
    let html = `<div><h2>👤 Profil User</h2><p>Nama: ${currentUser.name}</p><p>Email: ${currentUser.email}</p><p>Role: ${currentUser.role}</p><p>Membership: ${currentUser.membership ? "✅ AKTIF (diskon 20%)" : "❌ Biasa"}</p><button id="logoutBtn">Logout</button></div>`;
    if(currentUser.role === 'admin') {
      html += `<div class="admin-panel" style="margin-top:2rem"><h3>🛠️ CMS ADMIN - Manage Pesanan & update tracking</h3>
      <div id="adminOrdersList"></div></div>`;
    } else {
      html += `<div><h3>📜 Riwayat pesanan kamu</h3><div id="userOrderHistory"></div></div>`;
    }
    return html;
  }

  // Helper: create order and tracking
  function createOrderWithOptions(queueActive, deliveryActive, address) {
    if(!currentUser) return null;
    if(cart.length===0) { alert("Keranjang kosong"); return null; }
    let subtotal = cart.reduce((sum,i)=> sum + (i.basePrice * i.quantity),0);
    let afterMember = currentUser.membership ? subtotal * 0.8 : subtotal;
    let extraFee = 0;
    if(queueActive) extraFee += PEAK_FEE;
    let finalTotal = afterMember + extraFee;
    let orderId = nextOrderId++;
    const newOrder = {
      id: orderId,
      userId: currentUser.id,
      items: cart.map(i=>({ name: i.menuItem.name, quantity: i.quantity, price: i.basePrice })),
      subtotal: subtotal,
      discount: currentUser.membership ? subtotal - afterMember : 0,
      peakQueueFee: queueActive ? PEAK_FEE : 0,
      deliveryMode: deliveryActive,
      deliveryAddress: deliveryActive ? address : null,
      finalPrice: finalTotal,
      trackingStatus: "Order diterima",
      createdAt: new Date().toLocaleString(),
      statusHistory: ["Order diterima"]
    };
    orders.unshift(newOrder);
    cart = [];
    return newOrder;
  }

  function updateTracking(orderId, newStatus) {
    const order = orders.find(o=>o.id == orderId);
    if(order && currentUser && currentUser.role === 'admin') {
      order.trackingStatus = newStatus;
      if(!order.statusHistory) order.statusHistory = [];
      order.statusHistory.push(newStatus);
      alert(`Status pesanan #${orderId} diubah menjadi ${newStatus}`);
      renderCurrentPage();
    }
  }

  // Attach events after render
  function attachPageScripts() {
    // Global force auth
    document.querySelectorAll("#forceAuth, #gotoAuthBtn").forEach(btn => {
      if(btn) btn.addEventListener("click",()=>{ activePage = "auth"; renderNav(); renderCurrentPage(); });
    });
    if(activePage === "auth") {
      const loginBtn = document.getElementById("doLoginBtn");
      if(loginBtn) loginBtn.onclick = () => {
        const email = document.getElementById("loginEmail").value;
        const pwd = document.getElementById("loginPass").value;
        let user = [...membershipUsers, adminUser].find(u => u.email === email && u.password === pwd);
        if(user) { currentUser = {...user}; localStorage.setItem("casir_user", JSON.stringify({id:user.id,name:user.name,email:user.email,role:user.role,membership:user.membership}));
          document.getElementById("authMsg").innerHTML = "✅ Login berhasil!";
          activePage = "utama"; renderNav(); renderCurrentPage();
        } else { document.getElementById("authMsg").innerHTML = "❌ Gagal login / coba email ustadz"; }
      };
      const regBtn = document.getElementById("doRegisterBtn");
      if(regBtn) regBtn.onclick = () => {
        let name = document.getElementById("regName").value, email = document.getElementById("regEmail").value, pass = document.getElementById("regPass").value;
        if(name && email && pass) {
          let newUser = { id: Date.now(), name, email, password: pass, role:"user", membership:false };
          alert("Registrasi berhasil. Silakan login.");
        } else alert("Isi lengkap");
      };
    }
    // add to cart buttons
    document.querySelectorAll(".addToCartBtn").forEach(btn => {
      btn.addEventListener("click", (e) => {
        const canteenData = JSON.parse(btn.dataset.canteen);
        const menuData = JSON.parse(btn.dataset.menu);
        const existing = cart.find(i=> i.menuItem.id === menuData.id);
        if(existing) existing.quantity++;
        else cart.push({ menuItem: menuData, canteenId: canteenData.id, canteenName: canteenData.name, quantity:1, basePrice: menuData.price });
        alert(`${menuData.name} ditambahkan ke keranjang`);
        renderCurrentPage();
      });
    });
    document.querySelectorAll(".gotoCanteenBtn").forEach(btn => {
      btn.addEventListener("click",()=>{ activePage = "menuKantin"; renderNav(); renderCurrentPage(); });
    });
    const gotoTrans = document.getElementById("goToTransaksiBtn");
    if(gotoTrans) gotoTrans.addEventListener("click",()=>{ activePage="transaksi"; renderNav(); renderCurrentPage(); });
    const gotoTransMenu = document.getElementById("gotoTransaksiFromMenu");
    if(gotoTransMenu) gotoTransMenu.addEventListener("click",()=>{ activePage="transaksi"; renderNav(); renderCurrentPage(); });
    const clearCart = document.getElementById("clearCartBtn");
    if(clearCart) clearCart.onclick = () => { cart = []; renderCurrentPage(); };
    const confirmBtn = document.getElementById("confirmOrderBtn");
    if(confirmBtn && activePage === "transaksi") {
      confirmBtn.onclick = () => {
        const queueOn = document.getElementById("queueBookingCheckbox")?.checked;
        const deliveryOn = document.getElementById("deliveryCheckbox")?.checked;
        let address = "";
        if(deliveryOn) address = document.getElementById("deliveryAddress")?.value || "Kampus Building A";
        if(!deliveryOn && queueOn) alert("Biaya antrean Rp2000 akan ditambahkan (jam padat)");
        const orderMade = createOrderWithOptions(queueOn, deliveryOn, address);
        if(orderMade) alert(`Pesanan #${orderMade.id} berhasil! Total Rp ${orderMade.finalPrice.toLocaleString()}\nTracking: ${orderMade.trackingStatus}`);
        renderCurrentPage();
      };
      const deliveryCheck = document.getElementById("deliveryCheckbox");
      if(deliveryCheck) deliveryCheck.onchange = (e) => {
        const addrDiv = document.getElementById("deliveryAddressDiv");
        if(addrDiv) addrDiv.style.display = e.target.checked ? "block" : "none";
      };
    }
    // Tracking display on transaksi
    if(activePage === "transaksi") {
      const orderHistoryDiv = document.getElementById("orderHistoryList");
      if(orderHistoryDiv && orders.length) {
        let userOrders = orders.filter(o=>o.userId===currentUser?.id);
        orderHistoryDiv.innerHTML = userOrders.map(o=> `<div style="background:#f4ede3; border-radius:20px; padding:0.8rem; margin:0.5rem 0"><strong>Order #${o.id}</strong> | Total: Rp ${o.finalPrice.toLocaleString()} | ${o.deliveryMode ? '🚚 Delivery' : 'Ambil sendiri'} | Status: <span class="status-badge">${o.trackingStatus}</span><br/><small>Queue fee: ${o.peakQueueFee ? 'Rp2000' : 'Tidak'}</small><div>Tracking history: ${o.statusHistory?.join(' → ') || o.trackingStatus}</div></div>`).join('');
      } else if(orderHistoryDiv) orderHistoryDiv.innerHTML = "<i>Belum ada pesanan</i>";
    }
    // Admin CMS logic
    if(activePage === "userAdmin" && currentUser?.role === "admin") {
      const adminDiv = document.getElementById("adminOrdersList");
      if(adminDiv) {
        adminDiv.innerHTML = orders.map(o=> `<div style="background:#3e3a33; margin:0.8rem 0; padding:1rem; border-radius:20px"><strong>Order #${o.id}</strong> | UserID: ${o.userId} | Total: Rp${o.finalPrice} | Status: ${o.trackingStatus}<br/>
        <select id="statusSelect_${o.id}"><option>Order diterima</option><option>Sedang disiapkan</option><option>Siap diambil / Sedang dikirim</option><option>Selesai</option></select>
        <button class="updateTrackBtn" data-id="${o.id}">Update Status</button><br/>${o.deliveryMode ? `Alamat: ${o.deliveryAddress}` : ''}</div>`).join('');
        document.querySelectorAll(".updateTrackBtn").forEach(btn => {
          btn.addEventListener("click",(e)=>{
            const orderId = parseInt(btn.dataset.id);
            const selectElem = document.getElementById(`statusSelect_${orderId}`);
            const newStat = selectElem.value;
            updateTracking(orderId, newStat);
          });
        });
      }
    } else if(activePage === "userAdmin" && currentUser && currentUser.role !== "admin") {
      const historyDiv = document.getElementById("userOrderHistory");
      if(historyDiv) {
        let userOrd = orders.filter(o=>o.userId===currentUser.id);
        historyDiv.innerHTML = userOrd.map(o=> `<div>#${o.id} - ${o.trackingStatus} - Total Rp${o.finalPrice}</div>`).join('');
      }
    }
    const logout = document.getElementById("logoutBtn");
    if(logout) logout.onclick = () => { currentUser = null; cart = []; localStorage.removeItem("casir_user"); activePage="landing"; renderNav(); renderCurrentPage(); };
  }

  // load from storage
  function loadStoredUser() {
    const stored = localStorage.getItem("casir_user");
    if(stored) {
      try{
        const u = JSON.parse(stored);
        // find full from membership
        let full = [...membershipUsers, adminUser].find(x=>x.email === u.email);
        if(full) currentUser = {...full};
        else currentUser = u;
      }catch(e){}
    }
  }
  
  loadStoredUser();
  renderNav();
  renderCurrentPage();
</script>
</body>
</html>