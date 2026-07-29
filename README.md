// ════════════════════════════════════════════════════════════════════
// REVISION 2: Weather widget
// Modul 2: forms + lists + conditional + useEffect + fetch
// ════════════════════════════════════════════════════════════════════

import { useState, useEffect } from 'react';

const SHAHARLAR = {
  toshkent:  { lat: 41.31, lon: 69.24, nomi: "Toshkent" },
  samarqand: { lat: 39.65, lon: 66.97, nomi: "Samarqand" },
  buxoro:    { lat: 39.77, lon: 64.42, nomi: "Buxoro" },
  andijon:   { lat: 40.78, lon: 72.34, nomi: "Andijon" },
};

const KOD_ICON = (code) => {
  if (code === 0) return "☀️";
  if (code <= 3) return "⛅";
  if (code <= 48) return "🌫️";
  if (code <= 67) return "🌧️";
  if (code <= 77) return "❄️";
  if (code <= 99) return "⛈️";
  return "❔";
};

const HAFTAKUN = (sana) => {
  const kunlar = ["Yak","Du","Se","Ch","Pa","Ju","Sha"];
  return kunlar[new Date(sana).getDay()];
};

// ─────────────────────────────────────────────────────────────────────
// Komponentlar
// ─────────────────────────────────────────────────────────────────────

function ShaharTanlash({ qiymat, onChange }) {
  return (
    <select
      value={qiymat}
      onChange={(e) => onChange(e.target.value)}
      style={{ padding: 8, fontSize: 16 }}
    >
      {Object.entries(SHAHARLAR).map(([k, v]) => (
        <option key={k} value={k}>{v.nomi}</option>
      ))}
    </select>
  );
}

function JoriyOb({ data, shaharNomi }) {
  return (
    <div style={{ textAlign: "center", padding: 24 }}>
      <h2 style={{ margin: 0 }}>{shaharNomi}</h2>
      <div style={{ fontSize: 80 }}>{KOD_ICON(data.current.weather_code)}</div>
      <div style={{ fontSize: 48, fontWeight: "bold" }}>
        {Math.round(data.current.temperature_2m)}°
      </div>
    </div>
  );
}

function BeshKunPrognoz({ daily }) {
  return (
    <div>
      <h3>5 kunlik prognoz</h3>
      <ul style={{ listStyle: "none", padding: 0 }}>
        {daily.time.slice(0, 5).map((sana, i) => (
          <li key={sana} style={{
            display: "flex",
            justifyContent: "space-between",
            padding: 8,
            borderBottom: "1px solid #eee",
          }}>
            <span style={{ width: 40 }}>{HAFTAKUN(sana)}</span>
            <span style={{ fontSize: 24 }}>{KOD_ICON(daily.weather_code[i])}</span>
            <span>
              {Math.round(daily.temperature_2m_min[i])}°
              {" / "}
              <b>{Math.round(daily.temperature_2m_max[i])}°</b>
            </span>
          </li>
        ))}
      </ul>
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// App
// ─────────────────────────────────────────────────────────────────────

function App() {
  const [shahar, setShahar] = useState("toshkent");
  const [data, setData] = useState(null);
  const [yukla, setYukla] = useState(false);
  const [xato, setXato] = useState(null);
  const [yangilanish, setYangilanish] = useState(0);

  useEffect(() => {
    const { lat, lon } = SHAHARLAR[shahar];
    const ctrl = new AbortController();

    setYukla(true);
    setXato(null);

    const url =
      `https://api.open-meteo.com/v1/forecast` +
      `?latitude=${lat}&longitude=${lon}` +
      `&current=temperature_2m,weather_code` +
      `&daily=temperature_2m_max,temperature_2m_min,weather_code` +
      `&timezone=Asia/Tashkent`;

    fetch(url, { signal: ctrl.signal })
      .then(r => {
        if (!r.ok) throw new Error("Server xato: " + r.status);
        return r.json();
      })
      .then(d => {
        setData(d);
        setYukla(false);
      })
      .catch(e => {
        if (e.name === "AbortError") return;
        setXato(e.message);
        setYukla(false);
      });

    return () => ctrl.abort();
  }, [shahar, yangilanish]);

  return (
    <div style={{
      maxWidth: 360,
      margin: "20px auto",
      padding: 20,
      border: "1px solid #ddd",
      borderRadius: 12,
      fontFamily: "sans-serif",
    }}>
      <div style={{ display: "flex", gap: 8 }}>
        <ShaharTanlash qiymat={shahar} onChange={setShahar} />
        <button onClick={() => setYangilanish(y => y + 1)}>
          🔄
        </button>
      </div>

      {yukla && (
        <p style={{ textAlign: "center", marginTop: 20 }}>
          ⏳ Yuklanmoqda...
        </p>
      )}

      {xato && !yukla && (
        <div style={{ marginTop: 20, color: "red" }}>
          <p>❌ {xato}</p>
          <button onClick={() => setYangilanish(y => y + 1)}>
            Qayta urinish
          </button>
        </div>
      )}

      {data && !yukla && !xato && (
        <>
          <JoriyOb data={data} shaharNomi={SHAHARLAR[shahar].nomi} />
          <BeshKunPrognoz daily={data.daily} />
        </>
      )}
    </div>
  );
}

export default App;
