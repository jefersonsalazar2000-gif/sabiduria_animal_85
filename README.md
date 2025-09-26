<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Sabiduría Animal • Tienda</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
  <style>
    body{margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Arial;background:#0b1020;color:#e6ecff}
    .header{display:flex;align-items:center;justify-content:space-between;padding:16px 22px;background:#121a33}
    .brand-text h1{margin:0;font-size:28px;background:linear-gradient(90deg,#ff5e00,#ffae00);-webkit-background-clip:text;color:transparent}
    .btn{padding:10px 14px;border-radius:10px;text-decoration:none;font-weight:600}
    .btn.amazon{background:linear-gradient(90deg,#ff5e00,#ffae00);color:#111}
    .search{display:flex;gap:8px;padding:20px}
    .search input,.search select{padding:10px;border-radius:8px;border:1px solid #2a3c7a;background:#0e1530;color:#dbe6ff}
    .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px;padding:20px}
    .card{background:#121a33;border-radius:14px;overflow:hidden;display:flex;flex-direction:column}
    .thumb{aspect-ratio:4/3;overflow:hidden}
    .thumb img{width:100%;height:100%;object-fit:cover;display:block}
    .content{padding:10px}
    .title{margin:0;font-weight:700}
    .meta{font-size:13px;color:#c3d0ff;display:flex;justify-content:space-between}
    .footer{text-align:center;color:#aaa;padding:20px;font-size:14px}
  </style>
</head>
<body>
<header class="header">
  <div class="brand-text"><h1>Sabiduría Animal</h1></div>
</header>

<section class="search">
  <input id="searchInput" type="search" placeholder="Buscar productos..."/>
  <select id="categorySelect">
    <option value="">Todas las categorías</option>
    <option value="Perros">Perros</option>
    <option value="Gatos">Gatos</option>
  </select>
  <select id="sort">
    <option value="relevance">Relevancia</option>
    <option value="price-asc">Precio: menor a mayor</option>
    <option value="price-desc">Precio: mayor a menor</option>
    <option value="name-asc">Nombre A–Z</option>
    <option value="name-desc">Nombre Z–A</option>
  </select>
</section>

<main>
  <div id="productList" class="grid"></div>
</main>

<footer class="footer">
  © <span id="year"></span> Sabiduría Animal · Afiliado de Amazon
</footer>

<script>
async function loadProducts(){
  const res = await fetch('products.json',{cache:'no-store'});
  const data = await res.json();
  window.PRODUCTS = data;
  render(data);
}
function cardHTML(p){
  return `
  <article class="card">
    <div class="thumb"><img src="${p.image}" alt="${p.name}"></div>
    <div class="content">
      <h4 class="title">${p.name}</h4>
      <div class="meta"><span>$${p.price.toFixed(2)}</span><span>${p.category}</span></div>
    </div>
    <div style="padding:10px">
      <a class="btn amazon" target="_blank" href="${p.link}">Ver en Amazon</a>
    </div>
  </article>`;
}
function render(list){document.getElementById('productList').innerHTML=list.map(cardHTML).join('')}
function sortProducts(arr,how){
  switch(how){
    case 'price-asc':return arr.sort((a,b)=>a.price-b.price);
    case 'price-desc':return arr.sort((a,b)=>b.price-a.price);
    case 'name-asc':return arr.sort((a,b)=>a.name.localeCompare(b.name));
    case 'name-desc':return arr.sort((a,b)=>b.name.localeCompare(a.name));
    default:return arr;
  }
}
document.getElementById('year').textContent=new Date().getFullYear();
document.getElementById('searchInput').addEventListener('input',applyFilters);
document.getElementById('categorySelect').addEventListener('change',applyFilters);
document.getElementById('sort').addEventListener('change',applyFilters);
function applyFilters(){
  let q=document.getElementById('searchInput').value.toLowerCase();
  let cat=document.getElementById('categorySelect').value;
  let sort=document.getElementById('sort').value;
  let arr=PRODUCTS.filter(p=>{
    let text=(p.name+" "+p.category).toLowerCase();
    let okQ=q?text.includes(q):true;
    let okC=cat?p.category===cat:true;
    return okQ&&okC;
  });
  render(sortProducts(arr,sort));
}
loadProducts();
</script>
</body>
</html>
