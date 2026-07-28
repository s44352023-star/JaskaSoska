Mana siz talab qilgan barcha talablarni (10 ta mahsulot, kategoriya filtri, narx oralig'i slideri, early return, switch pattern, index o'rniga id va boshqalar) o'z ichiga olgan mukammal va zamonaviy React dasturi.

1. App.jsx (Asosiy Komponent va Filter Mantiqi)
JavaScript
import { useState } from 'react';
import ProductList from './ProductList';
import './App.css';

// 1. Kamida 10 ta mahsulot (id, nomi, narx, kategoriya, mavjud)
const PRODUCTS_DATA = [
  { id: 1, nomi: 'MacBook Pro 16', narx: 2400, kategoriya: 'Noutbuk', mavjud: true },
  { id: 2, nomi: 'iPhone 15 Pro', narx: 1200, kategoriya: 'Telefon', mavjud: true },
  { id: 3, nomi: 'AirPods Pro 2', narx: 250, kategoriya: 'Aksessuar', mavjud: false },
  { id: 4, nomi: 'Samsung Galaxy S24', narx: 1000, kategoriya: 'Telefon', mavjud: true },
  { id: 5, nomi: 'Dell XPS 15', narx: 1800, kategoriya: 'Noutbuk', mavjud: true },
  { id: 6, nomi: 'Magic Mouse', narx: 100, kategoriya: 'Aksessuar', mavjud: true },
  { id: 7, nomi: 'Sony WH-1000XM5', narx: 400, kategoriya: 'Aksessuar', mavjud: false },
  { id: 8, nomi: 'iPad Air', narx: 700, kategoriya: 'Planshet', mavjud: true },
  { id: 9, nomi: 'Logitech MX Master 3S', narx: 120, kategoriya: 'Aksessuar', mavjud: true },
  { id: 10, nomi: 'ASUS ROG Zephyrus', narx: 2100, kategoriya: 'Noutbuk', mavjud: false },
];

function App() {
  // Simulyatsiya qilingan holatlar
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  // Filter state'lari
  const [selectedCategory, setSelectedCategory] = useState('Barchasi');
  const [maxPrice, setMaxPrice] = useState(3000);

  // UI ko'rinishini boshqarish uchun Switch pattern state'i ('grid' yoki 'list')
  const [viewMode, setViewMode] = useState('grid');

  // 2. Early return pattern (Loading / Error holatlari)
  if (loading) {
    return <div className="center-message">⏳ Ma'lumotlar yuklanmoqda...</div>;
  }

  if (error) {
    return <div className="center-message error">❌ Xatolik yuz berdi: {error}</div>;
  }

  // 3. Filter mantiqi (Kategoriya va Narx bo'yicha)
  const filteredProducts = PRODUCTS_DATA.filter((product) => {
    const matchesCategory = selectedCategory === 'Barchasi' || product.kategoriya === selectedCategory;
    const matchesPrice = product.narx <= maxPrice;
    return matchesCategory && matchesPrice;
  });

  return (
    <div className="app-container">
      <header className="header">
        <h1>🛍️ Elektronika Do'koni</h1>
        <p>Mahsulotlar filtri va Switch Pattern</p>
      </header>

      {/* --- FILTERLAR PANELİ --- */}
      <div className="filter-panel">
        {/* Kategoriya Dropdown */}
        <div className="filter-group">
          <label>Kategoriya:</label>
          <select 
            value={selectedCategory} 
            onChange={(e) => setSelectedCategory(e.target.value)}
          >
            <option value="Barchasi">Barchasi</option>
            <option value="Noutbuk">Noutbuk</option>
            <option value="Telefon">Telefon</option>
            <option value="Aksessuar">Aksessuar</option>
            <option value="Planshet">Planshet</option>
          </select>
        </div>

        {/* Narx Oralig'i Slideri */}
        <div className="filter-group">
          <label>Maksimal narx: <strong>${maxPrice}</strong></label>
          <input 
            type="range" 
            min="50" 
            max="3000" 
            step="50"
            value={maxPrice} 
            onChange={(e) => setMaxPrice(Number(e.target.value))} 
          />
        </div>

        {/* Switch Pattern tugmalari (Ko'rinishni o'zgartirish) */}
        <div className="filter-group">
          <label>Ko'rinish (Switch):</label>
          <div className="switch-buttons">
            <button 
              className={viewMode === 'grid' ? 'active' : ''} 
              onClick={() => setViewMode('grid')}
            >
              Grid 📱
            </button>
            <button 
              className={viewMode === 'list' ? 'active' : ''} 
              onClick={() => setViewMode('list')}
            >
              List 📄
            </button>
            <button 
              className={viewMode === 'compact' ? 'active' : ''} 
              onClick={() => setViewMode('compact')}
            >
              Compact ⚡
            </button>
          </div>
        </div>
      </div>

      {/* --- MAHSULOTLAR RO'YXATI (Switch Pattern & Key yordamida) --- */}
      <ProductList products={filteredProducts} viewMode={viewMode} />
    </div>
  );
}

export default App;
2. ProductList.jsx (Switch Pattern va Bo'sh Holat)
JavaScript
function ProductList({ products, viewMode }) {
  // 4. Bo'sh holat (Empty State)
  if (products.length === 0) {
    return (
      <div className="empty-state">
        <h3>🔍 Topilmadi</h3>
        <p>Siz tanlagan shartlarga mos keluvchi mahsulot topilmadi. Filtrni o'zgartirib ko'ring.</p>
      </div>
    );
  }

  // 5. Switch Pattern — viewMode'ga qarab har xil UI tuzilmasini qaytarish
  switch (viewMode) {
    case 'grid':
      return (
        <div className="product-grid">
          {products.map((p) => (
            <div key={p.id} className={`product-card ${!p.mavjud ? 'not-available' : ''}`}>
              <span className="category-tag">{p.kategoriya}</span>
              <h3>{p.nomi}</h3>
              <p className="price">${p.narx}</p>
              {!p.mavjud && <span className="badge-out">Mavjud emas</span>}
            </div>
          ))}
        </div>
      );

    case 'list':
      return (
        <div className="product-list-view">
          {products.map((p) => (
            <div key={p.id} className={`product-list-item ${!p.mavjud ? 'not-available' : ''}`}>
              <div>
                <h4>{p.nomi}</h4>
                <span className="sub-text">{p.kategoriya}</span>
              </div>
              <div className="list-right">
                <span className="price">${p.narx}</span>
                {!p.mavjud ? <span className="badge-out">Tugagan</span> : <span className="badge-in">Sotuvda</span>}
              </div>
            </div>
          ))}
        </div>
      );

    case 'compact':
      return (
        <ul className="product-compact-list">
          {products.map((p) => (
            <li key={p.id} className={!p.mavjud ? 'not-available' : ''}>
              <span>{p.nomi} ({p.kategoriya})</span>
              <strong>${p.narx}</strong>
            </li>
          ))}
        </ul>
      );

    default:
      return null;
  }
}

export default ProductList;
3. App.css (Chiroyli Uslublar va Opacity Talabi)
CSS
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
  background-color: #f1f5f9;
  color: #1e293b;
  padding: 20px;
}

.app-container {
  max-width: 900px;
  margin: 0 auto;
}

.header {
  text-align: center;
  margin-bottom: 25px;
}

.header h1 {
  color: #0f172a;
  margin-bottom: 5px;
}

.header p {
  color: #64748b;
}

/* Filter Panel */
.filter-panel {
  background: #ffffff;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 20px;
  margin-bottom: 25px;
}

.filter-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.filter-group label {
  font-size: 0.9rem;
  font-weight: 600;
  color: #475569;
}

.filter-group select, .filter-group input[type="range"] {
  padding: 8px 12px;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  outline: none;
  font-size: 0.95rem;
}

/* Switch Buttons */
.switch-buttons {
  display: flex;
  gap: 5px;
}

.switch-buttons button {
  flex: 1;
  padding: 6px 10px;
  border: 1px solid #cbd5e1;
  background: #f8fafc;
  cursor: pointer;
  border-radius: 6px;
  font-weight: 600;
  font-size: 0.85rem;
  transition: all 0.2s;
}

.switch-buttons button.active {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
}

/* Mavjud bo'lmagan mahsulotlar uchun opacity va belgi talabi */
.not-available {
  opacity: 0.4;
  position: relative;
}

/* Grid View */
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 20px;
}

.product-card {
  background: #ffffff;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.05);
  border: 1px solid #e2e8f0;
  display: flex;
  flex-direction: column;
  gap: 10px;
  position: relative;
}

.category-tag {
  font-size: 0.75rem;
  background: #e0f2fe;
  color: #0284c7;
  padding: 3px 8px;
  border-radius: 4px;
  width: max-content;
  font-weight: 600;
}

.product-card h3 {
  font-size: 1.1rem;
  color: #1e293b;
}

.price {
  font-size: 1.25rem;
  font-weight: bold;
  color: #10b981;
}

.badge-out {
  background: #fee2e2;
  color: #ef4444;
  font-size: 0.75rem;
  padding: 2px 6px;
  border-radius: 4px;
  font-weight: bold;
  width: max-content;
}

.badge-in {
  background: #d1fae5;
  color: #059669;
  font-size: 0.75rem;
  padding: 2px 6px;
  border-radius: 4px;
  font-weight: bold;
}

/* List View */
.product-list-view {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.product-list-item {
  background: #ffffff;
  padding: 15px 20px;
  border-radius: 8px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #e2e8f0;
}

.list-right {
  display: flex;
  align-items: center;
  gap: 15px;
}

.sub-text {
  font-size: 0.85rem;
  color: #64748b;
}

/* Compact View */
.product-compact-list {
  background: #ffffff;
  border-radius: 8px;
  padding: 10px 20px;
  list-style: none;
  border: 1px solid #e2e8f0;
}

.product-compact-list li {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  border-bottom: 1px solid #f1f5f9;
}

.product-compact-list li:last-child {
  border-bottom: none;
}

/* Empty State */
.empty-state {
  text-align: center;
  padding: 50px 20px;
  background: #ffffff;
  border-radius: 12px;
  border: 2px dashed #cbd5e1;
  color: #64748b;
}

.empty-state h3 {
  color: #334155;
  margin-bottom: 8px;
}

.center-message {
  text-align: center;
  padding: 50px;
  font-size: 1.2rem;
  font-weight: 600;
}

.center-message.error {
  color: #ef4444;
}
