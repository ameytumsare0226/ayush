<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Garment Store — AI Financial Model</title>
<style>
*{box-sizing:border-box}body{margin:0;font-family:Arial,Helvetica,sans-serif;background:#f5f6f8;color:#1f2937}
header{background:#111827;color:#fff;padding:18px 28px;display:flex;justify-content:space-between;align-items:center}
header h1{margin:0;font-size:22px}header span{font-size:13px;opacity:.8}
nav{background:#fff;padding:12px 24px;border-bottom:1px solid #e5e7eb;display:flex;gap:8px;flex-wrap:wrap}
button{border:0;border-radius:8px;padding:10px 14px;cursor:pointer;font-weight:600;background:#e5e7eb;color:#111827}
button.active,button.primary{background:#111827;color:#fff}
main{max-width:1200px;margin:24px auto;padding:0 18px}.view{display:none}.view.active{display:block}
.cards{display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:14px;margin:18px 0}
.card{background:#fff;border:1px solid #e5e7eb;border-radius:12px;padding:16px;box-shadow:0 2px 7px rgba(0,0,0,.04)}
.card small{color:#6b7280}.card strong{display:block;font-size:23px;margin-top:7px}
.products{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px}
.product{background:#fff;border:1px solid #e5e7eb;border-radius:12px;padding:16px}
.product .pic{height:125px;border-radius:10px;background:linear-gradient(135deg,#e5e7eb,#f9fafb);display:flex;align-items:center;justify-content:center;font-size:42px;margin-bottom:12px}
.product h3{margin:5px 0}.muted{color:#6b7280;font-size:13px}.price{font-size:19px;font-weight:700;margin:9px 0}
select,input{padding:9px;border:1px solid #d1d5db;border-radius:7px;width:100%;margin:5px 0}
.row{display:grid;grid-template-columns:1fr 1fr;gap:10px}.toolbar{display:flex;gap:10px;flex-wrap:wrap;margin:12px 0}
table{width:100%;border-collapse:collapse;background:#fff;border-radius:10px;overflow:hidden}th,td{padding:10px;border-bottom:1px solid #e5e7eb;text-align:left;font-size:13px}th{background:#f3f4f6}
.panel{background:#fff;border:1px solid #e5e7eb;border-radius:12px;padding:18px;margin:16px 0}.pill{display:inline-block;padding:4px 8px;border-radius:20px;background:#eef2ff;font-size:12px}.low{background:#fee2e2;color:#991b1b}.good{background:#dcfce7;color:#166534}
#toast{position:fixed;right:18px;bottom:18px;background:#111827;color:#fff;padding:12px 16px;border-radius:8px;display:none}
@media(max-width:650px){.row{grid-template-columns:1fr}header{padding:15px}}
</style>
</head>
<body>
<header><div><h1>Garment Store</h1><span>AI-Assisted Financial & Inventory Model</span></div><span id="roleLabel">Customer Mode</span></header>
<nav>
<button class="active" onclick="show('store',this)">Customer Store</button>
<button onclick="show('cart',this)">Cart / Checkout</button>
<button onclick="show('dashboard',this)">Admin Dashboard</button>
<button onclick="show('inventory',this)">Inventory</button>
<button onclick="show('reports',this)">Reports</button>
</nav>
<main>
<section id="store" class="view active">
<h2>Shop by Category</h2>
<div class="toolbar">
<button onclick="filterProducts('All')">All</button><button onclick="filterProducts('Shirts')">Shirts</button>
<button onclick="filterProducts('T-Shirts')">T-Shirts</button><button onclick="filterProducts('Jeans')">Jeans</button>
<button onclick="filterProducts('Dresses')">Dresses</button>
</div>
<div id="products" class="products"></div>
</section>

<section id="cart" class="view">
<h2>Cart & Simulated Payment</h2><div id="cartBox" class="panel"></div>
<div class="panel"><h3>Checkout</h3><div class="row"><input id="custName" placeholder="Customer name"><select id="pay"><option>UPI</option><option>Card</option><option>Cash on Delivery</option></select></div>
<button class="primary" onclick="checkout()">Complete Simulated Payment</button></div>
</section>

<section id="dashboard" class="view">
<h2>Admin Dashboard</h2><div class="panel"><span class="pill">Admin 1</span> <span class="pill">Admin 2</span><p class="muted">Both admin users can access business reports and inventory information.</p></div>
<div class="cards" id="metrics"></div>
<div class="panel"><h3>Financial Terms</h3>
<p><b>Revenue:</b> money earned from sales.</p><p><b>COGS:</b> direct cost of garments sold.</p><p><b>Gross Profit:</b> Revenue − COGS.</p><p><b>Gross Margin:</b> Gross Profit ÷ Revenue × 100.</p><p><b>Inventory:</b> garments currently held for sale.</p></div>
</section>

<section id="inventory" class="view"><h2>Inventory Management</h2><div class="panel"><table><thead><tr><th>Product</th><th>Category</th><th>Size</th><th>Available</th><th>Price</th><th>Status</th></tr></thead><tbody id="invBody"></tbody></table></div></section>

<section id="reports" class="view"><h2>Detailed Reports</h2>
<div class="panel"><h3>Size-wise Sold vs Available</h3><table><thead><tr><th>Size</th><th>Units Sold</th><th>Units Available</th><th>Total Units</th><th>Sell-through %</th></tr></thead><tbody id="reportBody"></tbody></table></div>
<div class="panel"><h3>Product-wise Sales</h3><table><thead><tr><th>Product</th><th>Units Sold</th><th>Revenue</th><th>COGS</th><th>Gross Profit</th></tr></thead><tbody id="salesBody"></tbody></table></div>
</section>
</main>
<div id="toast"></div>
<script>
const products=[
{id:1,name:"Classic Oxford Shirt",cat:"Shirts",price:1499,cost:750,sizes:{S:8,M:12,L:9,XL:5},sold:{S:3,M:5,L:2,XL:1},icon:"👔"},
{id:2,name:"Premium Cotton T-Shirt",cat:"T-Shirts",price:899,cost:420,sizes:{S:15,M:18,L:12,XL:7},sold:{S:5,M:7,L:4,XL:2},icon:"👕"},
{id:3,name:"Slim Fit Jeans",cat:"Jeans",price:1999,cost:1050,sizes:{S:5,M:10,L:11,XL:6},sold:{S:1,M:4,L:5,XL:2},icon:"👖"},
{id:4,name:"Casual Summer Dress",cat:"Dresses",price:2299,cost:1200,sizes:{S:7,M:9,L:6,XL:2},sold:{S:2,M:3,L:2,XL:1},icon:"👗"}
];
let cart=[];
function money(n){return "₹"+n.toLocaleString("en-IN")}
function show(id,btn){
 document.querySelectorAll('.view').forEach(x=>x.classList.remove('active'));
 document.getElementById(id).classList.add('active');
 document.querySelectorAll('nav button').forEach(x=>x.classList.remove('active'));
 btn.classList.add('active');
 document.getElementById('roleLabel').textContent=(id==='store'||id==='cart')?'Customer Mode':'Admin Mode';
 if(id==='cart')renderCart(); if(id==='dashboard')renderDashboard(); if(id==='inventory')renderInventory(); if(id==='reports')renderReports();
}
function filterProducts(cat){
 const box=document.getElementById('products'); box.innerHTML="";
 products.filter(p=>cat==='All'||p.cat===cat).forEach(p=>{
  const opts=Object.keys(p.sizes).map(s=>`<option value="${s}">${s} — ${p.sizes[s]} available</option>`).join('');
  box.innerHTML+=`<div class="product"><div class="pic">${p.icon}</div><h3>${p.name}</h3><div class="muted">${p.cat}</div><div class="price">${money(p.price)}</div>
  <select id="size${p.id}">${opts}</select><input id="qty${p.id}" type="number" min="1" max="10" value="1">
  <button class="primary" onclick="addToCart(${p.id})">Add to Cart</button></div>`;
 });
}
function addToCart(id){
 const p=products.find(x=>x.id===id), size=document.getElementById('size'+id).value, qty=+document.getElementById('qty'+id).value;
 const available=p.sizes[size]; if(qty>available){toast("Not enough stock for this size.");return}
 cart.push({id,size,qty}); toast("Item added to cart.");
}
function renderCart(){
 const box=document.getElementById('cartBox');
 if(!cart.length){box.innerHTML="<p>Your cart is empty.</p>";return}
 let total=0, rows=cart.map((c,i)=>{let p=products.find(x=>x.id===c.id),v=p.price*c.qty;total+=v;return `<tr><td>${p.name}</td><td>${c.size}</td><td>${c.qty}</td><td>${money(v)}</td><td><button onclick="removeCart(${i})">Remove</button></td></tr>`}).join('');
 box.innerHTML=`<table><tr><th>Product</th><th>Size</th><th>Qty</th><th>Amount</th><th></th></tr>${rows}</table><h3>Total: ${money(total)}</h3>`;
}
function removeCart(i){cart.splice(i,1);renderCart()}
function checkout(){
 if(!cart.length){toast("Cart is empty.");return}
 cart.forEach(c=>{let p=products.find(x=>x.id===c.id);p.sizes[c.size]-=c.qty;p.sold[c.size]=(p.sold[c.size]||0)+c.qty});
 cart=[];renderCart();toast("Payment simulated successfully. Inventory updated.");
}
function totals(){
 let revenue=0,cogs=0,units=0;
 products.forEach(p=>Object.keys(p.sold).forEach(s=>{revenue+=p.sold[s]*p.price;cogs+=p.sold[s]*p.cost;units+=p.sold[s]}));
 return {revenue,cogs,profit:revenue-cogs,margin:revenue?((revenue-cogs)/revenue*100):0,units};
}
function renderDashboard(){
 let t=totals(),inv=products.reduce((a,p)=>a+Object.values(p.sizes).reduce((x,y)=>x+y,0),0);
 document.getElementById('metrics').innerHTML=[
 ["Revenue",money(t.revenue)],["COGS",money(t.cogs)],["Gross Profit",money(t.profit)],["Gross Margin",t.margin.toFixed(1)+"%"],["Units Sold",t.units],["Inventory Units",inv]
 ].map(x=>`<div class="card"><small>${x[0]}</small><strong>${x[1]}</strong></div>`).join('');
}
function renderInventory(){
 document.getElementById('invBody').innerHTML=products.flatMap(p=>Object.keys(p.sizes).map(s=>{
 let q=p.sizes[s], status=q<=5?"Low Stock":"In Stock";
 return `<tr><td>${p.name}</td><td>${p.cat}</td><td>${s}</td><td>${q}</td><td>${money(p.price)}</td><td><span class="pill ${q<=5?'low':'good'}">${status}</span></td></tr>`;
 })).join('');
}
function renderReports(){
 let sizes={S:[0,0],M:[0,0],L:[0,0],XL:[0,0]};
 products.forEach(p=>Object.keys(p.sizes).forEach(s=>{sizes[s][0]+=p.sold[s];sizes[s][1]+=p.sizes[s]}));
 document.getElementById('reportBody').innerHTML=Object.entries(sizes).map(([s,v])=>{
 let total=v[0]+v[1]; return `<tr><td>${s}</td><td>${v[0]}</td><td>${v[1]}</td><td>${total}</td><td>${total?(v[0]/total*100).toFixed(1):0}%</td></tr>`;
 }).join('');
 document.getElementById('salesBody').innerHTML=products.map(p=>{
 let sold=Object.values(p.sold).reduce((a,b)=>a+b,0),rev=sold*p.price,c=sold*p.cost;
 return `<tr><td>${p.name}</td><td>${sold}</td><td>${money(rev)}</td><td>${money(c)}</td><td>${money(rev-c)}</td></tr>`;
 }).join('');
}
function toast(t){let x=document.getElementById('toast');x.textContent=t;x.style.display='block';setTimeout(()=>x.style.display='none',1800)}
filterProducts('All');renderDashboard();
</script>
</body>
</html>
