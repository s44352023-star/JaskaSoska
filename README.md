// ════════════════════════════════════════════════════════════════════
// DARS 7: Custom hooks
// ════════════════════════════════════════════════════════════════════

import { useState, useEffect, useRef, useCallback } from 'react';

// ─────────────────────────────────────────────────────────────────────
// 1) useToggle — eng oddiy custom hook
// ─────────────────────────────────────────────────────────────────────

export function useToggle(initial = false) {
  const [val, setVal] = useState(initial);
  const toggle = useCallback(() => setVal(v => !v), []);
  return [val, toggle, setVal];
}

function PaneliMisoli() {
  const [ochiq, toggleOchiq] = useToggle(false);
  return (
    <div>
      <button onClick={toggleOchiq}>
        {ochiq ? "Yopish" : "Ochish"}
      </button>
      {ochiq && <p>Panel ochildi 🎉</p>}
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 2) useFetch — universal API hook
// ─────────────────────────────────────────────────────────────────────

export function useFetch(url) {
  const [data, setData] = useState(null);
  const [yukla, setYukla] = useState(false);
  const [xato, setXato] = useState(null);

  useEffect(() => {
    if (!url) return;

    const ctrl = new AbortController();
    setYukla(true);
    setXato(null);

    fetch(url, { signal: ctrl.signal })
      .then(r => {
        if (!r.ok) throw new Error("HTTP " + r.status);
        return r.json();
      })
      .then(setData)
      .catch(e => {
        if (e.name !== "AbortError") setXato(e.message);
      })
      .finally(() => {
        if (!ctrl.signal.aborted) setYukla(false);
      });

    return () => ctrl.abort();
  }, [url]);

  return { data, yukla, xato };
}

function FoydalanuvchiKarti({ id }) {
  const { data, yukla, xato } = useFetch(
    `https://jsonplaceholder.typicode.com/users/${id}`
  );

  if (yukla) return <p>Yuklanmoqda...</p>;
  if (xato) return <p>Xato: {xato}</p>;
  if (!data) return null;

  return (
    <div>
      <h2>{data.name}</h2>
      <p>{data.email}</p>
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 3) useLocalStorage
// ─────────────────────────────────────────────────────────────────────

export function useLocalStorage(key, initial) {
  const [val, setVal] = useState(() => {
    try {
      const saved = localStorage.getItem(key);
      return saved !== null ? JSON.parse(saved) : initial;
    } catch {
      return initial;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(val));
    } catch {
      // saqlash xato (quota, ssr)
    }
  }, [key, val]);

  return [val, setVal];
}

function TemaToggler() {
  const [tema, setTema] = useLocalStorage("tema", "yorug");
  return (
    <button onClick={() => setTema(tema === "yorug" ? "qorongi" : "yorug")}>
      Tema: {tema}
    </button>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 4) useDebounce — qidiruv uchun
// ─────────────────────────────────────────────────────────────────────

export function useDebounce(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);

  return debounced;
}

function Qidiruv() {
  const [matn, setMatn] = useState("");
  const debounced = useDebounce(matn, 500);

  const { data, yukla } = useFetch(
    debounced
      ? `https://jsonplaceholder.typicode.com/users?q=${debounced}`
      : null
  );

  return (
    <div>
      <input
        value={matn}
        onChange={(e) => setMatn(e.target.value)}
        placeholder="Qidir..."
      />
      <small>Real: "{matn}", debounced: "{debounced}"</small>
      {yukla && <p>...</p>}
      {data && (
        <ul>{data.slice(0, 5).map(u => <li key={u.id}>{u.name}</li>)}</ul>
      )}
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 5) usePrevious
// ─────────────────────────────────────────────────────────────────────

export function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; }, [value]);
  return ref.current;
}

function HisoblaganVaqolda() {
  const [son, setSon] = useState(0);
  const oldingi = usePrevious(son);

  return (
    <div>
      <p>Joriy: {son}, oldingi: {oldingi ?? "—"}</p>
      <button onClick={() => setSon(s => s + 1)}>+1</button>
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 6) useOnClickOutside — modal/dropdown uchun
// ─────────────────────────────────────────────────────────────────────

export function useOnClickOutside(ref, handler) {
  useEffect(() => {
    const fn = (e) => {
      if (ref.current && !ref.current.contains(e.target)) handler(e);
    };
    document.addEventListener("mousedown", fn);
    document.addEventListener("touchstart", fn);
    return () => {
      document.removeEventListener("mousedown", fn);
      document.removeEventListener("touchstart", fn);
    };
  }, [ref, handler]);
}

function Modal() {
  const [ochiq, setOchiq] = useState(false);
  const ref = useRef();

  useOnClickOutside(ref, () => setOchiq(false));

  return (
    <div>
      <button onClick={() => setOchiq(true)}>Modal ochish</button>
      {ochiq && (
        <div ref={ref} style={{
          padding: 24, border: "2px solid", margin: 16,
        }}>
          <h3>Modal!</h3>
          <p>Tashqarisini bosing yopish uchun</p>
        </div>
      )}
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 7) useMediaQuery — responsive
// ─────────────────────────────────────────────────────────────────────

export function useMediaQuery(query) {
  const [matches, setMatches] = useState(() => window.matchMedia(query).matches);

  useEffect(() => {
    const m = window.matchMedia(query);
    const handler = (e) => setMatches(e.matches);
    m.addEventListener("change", handler);
    return () => m.removeEventListener("change", handler);
  }, [query]);

  return matches;
}

function Responsive() {
  const mobile = useMediaQuery("(max-width: 768px)");
  return <p>Sizning ekraningiz: {mobile ? "📱 mobil" : "🖥️ desktop"}</p>;
}

// ─────────────────────────────────────────────────────────────────────
// ❌ Hook qoidalarini buzish
// ─────────────────────────────────────────────────────────────────────

/*
function Xato({ shart }) {
  if (shart) {
    const [v, setV] = useState(0);   // ❌ "Rendered fewer hooks"
  }
  for (let i = 0; i < 5; i++) {
    useEffect(() => {});            // ❌ aynan shu xato
  }
}
*/
