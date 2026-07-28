// ════════════════════════════════════════════════════════════════════
// DARS 1: Vite + birinchi komponent (JSX)
// ════════════════════════════════════════════════════════════════════
//
// Terminal:
//   npm create vite@latest mening-app -- --template react
//   cd mening-app
//   npm install
//   npm run dev
//
// ─────────────────────────────────────────────────────────────────────

// src/App.jsx
function App() {
  const ism = "Olim";
  const yosh = 18;
  const sevgan = ["pizza", "burger", "pasta"];

  return (
    <div className="app">
      <h1>Salom, {ism}!</h1>
      <p>Yosh: {yosh}, balog'at: {yosh >= 18 ? "ha" : "yo'q"}</p>
      <p>Sevimli taom: {sevgan[0]}</p>
      <p>2 + 2 = {2 + 2}</p>
    </div>
  );
}

export default App;


// ─────────────────────────────────────────────────────────────────────
// Ko'p komponent — bitta App ichida
// ─────────────────────────────────────────────────────────────────────

function Salomlashish({ ism }) {
  return <h2>Salom, {ism}!</h2>;
}

function Footer() {
  return (
    <footer style={{ marginTop: 40, color: "gray" }}>
      © 2026 Mening saytim
    </footer>
  );
}

function AppKopKomponent() {
  return (
    <>
      <Salomlashish ism="Olim" />
      <Salomlashish ism="Vali" />
      <Salomlashish ism="Karim" />
      <Footer />
    </>
  );
}


// ─────────────────────────────────────────────────────────────────────
// JSX ichida JS — barcha imkoniyatlar
// ─────────────────────────────────────────────────────────────────────

function MaqolaKarti() {
  const sarlavha = "React boshlash";
  const tags = ["react", "jsx", "frontend"];
  const oqilgan = true;

  return (
    <article className={oqilgan ? "card read" : "card"}>
      <h3>{sarlavha.toUpperCase()}</h3>

      {/* Bu JSX komment */}

      <div>
        Teglar:
        {tags.map((t) => (
          <span key={t} style={{ marginLeft: 8 }}>#{t}</span>
        ))}
      </div>

      <p>Holat: {oqilgan && "✓ o'qilgan"}</p>
    </article>
  );
}


// ─────────────────────────────────────────────────────────────────────
// src/main.jsx — ildiz ulanish
// ─────────────────────────────────────────────────────────────────────

/*
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.jsx';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
*/


// ─────────────────────────────────────────────────────────────────────
// Ataylab xato — wrapping element yo'q
// ─────────────────────────────────────────────────────────────────────

/*
function Xato() {
  return (
    <h1>Sarlavha</h1>     // ❌ XATO
    <p>Matn</p>
  );
}
// Adjacent JSX elements must be wrapped in an enclosing tag
*/

// To'g'risi:
function Togri1() {
  return (
    <div>
      <h1>Sarlavha</h1>
      <p>Matn</p>
    </div>
  );
}

function Togri2() {
  return (
    <>  {/* Fragment — ortiqcha div yo'q */}
      <h1>Sarlavha</h1>
      <p>Matn</p>
    </>
  );
}
