Mana barcha talablaringizni, shu jumladan useEffect, cleanup, AbortController, localStorage va race condition yechimlarini o'z ichiga olgan to'liq va mukammal kodlar to'plami.

1. Soat.jsx — Interval va Cleanup
Har sekundda yangilanib turuvchi soat va komponent o'chganda (unmount) intervalni to'xtatish (cleanup).

JavaScript
import { useState, useEffect } from 'react';

function Soat() {
  const [vaqt, setVaqt] = useState(new Date());

  useEffect(() => {
    console.log('Soat: useEffect ishga tushdi (Mount)');
    
    const timer = setInterval(() => {
      setVaqt(new Date());
    }, 1000);

    // Cleanup funksiyasi
    return () => {
      console.log('Soat: Cleanup ishlayapti (Unmount - interval tozalandi)');
      clearInterval(timer);
    };
  }, []);

  return (
    <div className="card">
      <h3>⏰ Jonli Soat</h3>
      <p style={{ fontSize: '1.5rem', fontWeight: 'bold' }}>
        {vaqt.toLocaleTimeString()}
      </p>
    </div>
  );
}

export default Soat;
2. OynaOlchami.jsx — Window Resize Listener
Brauzer oynasi o'lchami o'zgarganda uni kuzatish va event listener'ni tozalash.

JavaScript
import { useState, useEffect } from 'react';

function OynaOlchami() {
  const [kenglik, setKenglik] = useState(window.innerWidth);

  useEffect(() => {
    console.log('OynaOlchami: useEffect ishga tushdi');

    const handleResize = () => {
      setKenglik(window.innerWidth);
    };

    window.addEventListener('resize', handleResize);

    // Cleanup: Listener'ni olib tashlash
    return () => {
      console.log('OynaOlchami: Cleanup ishlayapti (removeEventListener)');
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return (
    <div className="card">
      <h3>📐 Oyna O'lchami</h3>
      <p>Hozirgi kenglik: <strong>{kenglik} px</strong></p>
    </div>
  );
}

export default OynaOlchami;
3. OnlaynUserlar.jsx — Polling (Interval bilan fetch)
Har 5 sekundda ma'lumotlarni yangilab turish (polling) va tozalash.

JavaScript
import { useState, useEffect } from 'react';

function OnlaynUserlar() {
  const [soni, setSoni] = useState(0);

  useEffect(() => {
    console.log('OnlaynUserlar: Polling boshlandi');

    const fetchUserlar = () => {
      // Simulyatsiya (API chaqiruvi o'rniga tasodifiy son)
      const tasodifiySon = Math.floor(Math.random() * 50) + 10;
      setSoni(tasodifiySon);
      console.log('OnlaynUserlar: Ma\'lumot yangilandi ->', tasodifiySon);
    };

    fetchUserlar(); // Birinchi marta darhol chaqirish
    const interval = setInterval(fetchUserlar, 5000);

    return () => {
      console.log('OnlaynUserlar: Cleanup (interval to\'xtatildi)');
      clearInterval(interval);
    };
  }, []);

  return (
    <div className="card">
      <h3>🟢 Onlayn Foydalanuvchilar (Polling)</h3>
      <p>Hozirda saytda: <strong>{soni} ta</strong> foydalanuvchi bor</p>
    </div>
  );
}

export default OnlaynUserlar;
4. Profil.jsx — Race Condition va AbortController
id o'zgarganda qaytadan fetch qilish, shuningdek AbortController yordamida eskirgan so'rovlarni bekor qilish (Race condition yechimi) hamda 3 ta holat (loading/error/data).

JavaScript
import { useState, useEffect } from 'react';

function Profil({ id }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    console.log(`Profil (${id}): useEffect ishga tushdi`);
    
    // AbortController yaratish
    const controller = new AbortController();
    const signal = controller.signal;

    setLoading(true);
    setError(null);

    fetch(`https://jsonplaceholder.typicode.com/users/${id}`, { signal })
      .then((res) => {
        if (!res.ok) throw new Error("Foydalanuvchi topilmadi!");
        return res.json();
      })
      .then((data) => {
        setUser(data);
        setLoading(false);
        console.log(`Profil (${id}): Ma'lumot keldi`, data.name);
      })
      .catch((err) => {
        if (err.name === 'AbortError') {
          console.log(`Profil (${id}): So'rov bekor qilindi (Abort)`);
        } else {
          setError(err.message);
          setLoading(false);
          console.log(`Profil (${id}): Xatolik yuz berdi`, err.message);
        }
      });

    // Cleanup: ID o'zgarsa yoki komponent unmount bo'lsa eski so'rovni to'xtatish
    return () => {
      console.log(`Profil (${id}): Cleanup (so'rov abort qilindi)`);
      controller.abort();
    };
  }, [id]);

  // 3 ta holat (loading / error / data)
  if (loading) return <div className="card">⏳ Profil yuklanmoqda...</div>;
  if (error) return <div className="card" style={{ color: 'red' }}>❌ Xato: {error}</div>;

  return (
    <div className="card">
      <h3>👤 Foydalanuvchi Profili (ID: {id})</h3>
      <p><strong>Ism:</strong> {user.name}</p>
      <p><strong>Email:</strong> {user.email}</p>
      <p><strong>Shahar:</strong> {user.address?.city}</p>
    </div>
  );
}

export default Profil;
5. App.jsx — Theme Storage va Birlashtirilgan Asosiy Komponent
localStorageda temani saqlash va yuqoridagi barcha komponentlarni birlashtirish.

JavaScript
import { useState, useEffect } from 'react';
import Soat from './Soat';
import OynaOlchami from './OynaOlchami';
import OnlaynUserlar from './OnlaynUserlar';
import Profil from './Profil';
import './App.css';

function App() {
  // 1. LocalStorage'dan temani o'qib olish (boshlang'ich qiymat sifatida)
  const [tema, setTema] = useState(() => {
    return localStorage.getItem('app-tema') || 'light';
  });

  const [profilId, setProfilId] = useState(1);

  // 2. Tema o'zgarganda localStorage'ga yozish
  useEffect(() => {
    console.log('App (Tema): useEffect ishga tushdi ->', tema);
    localStorage.setItem('app-tema', tema);
  }, [tema]);

  const temaAlmashtir = () => {
    setTema(prev => (prev === 'light' ? 'dark' : 'light'));
  };

  return (
    <div className={`app-container ${tema}`}>
      <header className="header">
        <h1>⚛️ React useEffect & Lifecycle</h1>
        <button className="btn-tema" onClick={temaAlmashtir}>
          {tema === 'light' ? '🌙 Tungi rejim' : '☀️ Kunduzgi rejim'}
        </button>
      </header>

      <div className="grid-layout">
        <Soat />
        <OynaOlchami />
        <OnlaynUserlar />
      </div>

      <div className="profil-section">
        <h2>Foydalanuvchini tanlang (Race Condition sinovi uchun tez-tez bosing):</h2>
        <div className="btn-group">
          {[1, 2, 3, 4, 5].map((num) => (
            <button 
              key={num} 
              className={profilId === num ? 'active' : ''}
              onClick={() => setProfilId(num)}
            >
              User {num}
            </button>
          ))}
        </div>
        
        <Profil id={profilId} />
      </div>
    </div>
  );
}

export default App;
6. App.css — Dizayn va Temalar
CSS
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.app-container {
  min-height: 100vh;
  padding: 30px;
  transition: background 0.3s, color 0.3s;
}

.app-container.light {
  background-color: #f8fafc;
  color: #1e293b;
}

.app-container.dark {
  background-color: #0f172a;
  color: #f8fafc;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  padding-bottom: 15px;
  border-bottom: 2px solid rgba(0,0,0,0.1);
}

.btn-tema {
  padding: 8px 16px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  font-weight: 600;
  background: #3b82f6;
  color: white;
}

.grid-layout {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.card {
  background: inherit;
  border: 1px solid rgba(100, 100, 100, 0.2);
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.05);
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.profil-section {
  background: inherit;
  border: 1px solid rgba(100, 100, 100, 0.2);
  padding: 20px;
  border-radius: 12px;
}

.btn-group {
  display: flex;
  gap: 10px;
  margin: 15px 0;
}

.btn-group button {
  padding: 8px 14px;
  border-radius: 6px;
  border: 1px solid #cbd5e1;
  background: transparent;
  color: inherit;
  cursor: pointer;
  font-weight: 600;
}

.btn-group button.active {
  background: #3b82f6;
  color: white;
  border-color: #3b82f6;
}
