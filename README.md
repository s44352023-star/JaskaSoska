// ════════════════════════════════════════════════════════════════════
// DARS 2: Props va kompozitsiya
// ════════════════════════════════════════════════════════════════════

// ─────────────────────────────────────────────────────────────────────
// 1) Oddiy props — destructuring
// ─────────────────────────────────────────────────────────────────────

function Card({ sarlavha, matn }) {
  return (
    <div className="card">
      <h3>{sarlavha}</h3>
      <p>{matn}</p>
    </div>
  );
}

function App() {
  return (
    <div>
      <Card sarlavha="React" matn="UI kutubxonasi" />
      <Card sarlavha="Vite" matn="Tez build tool" />
      <Card sarlavha="JSX" matn="JS ichida HTML" />
    </div>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 2) Turli ma'lumot turlari
// ─────────────────────────────────────────────────────────────────────

function Profil({ ism, yosh, faol, sevganRanglar, profilRasm }) {
  return (
    <div className="profil">
      <img src={profilRasm} alt={ism} width={64} height={64} />
      <h2>{ism}</h2>
      <p>Yosh: {yosh}</p>
      <p>Holat: {faol ? "🟢 faol" : "⚫ noaktiv"}</p>
      <p>Ranglar: {sevganRanglar.join(", ")}</p>
    </div>
  );
}

function ProfilApp() {
  return (
    <Profil
      ism="Nigora"
      yosh={22}
      faol={true}
      sevganRanglar={["ko'k", "yashil"]}
      profilRasm="/avatar.png"
    />
  );
}

// ─────────────────────────────────────────────────────────────────────
// 3) Default props
// ─────────────────────────────────────────────────────────────────────

function Tugma({ label = "Tasdiqlash", turi = "primary", disabled = false, onClick }) {
  return (
    <button
      className={`btn btn-${turi}`}
      disabled={disabled}
      onClick={onClick}
    >
      {label}
    </button>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 4) Children + kompozitsiya
// ─────────────────────────────────────────────────────────────────────

function Layout({ children }) {
  return (
    <div className="container">
      <header className="header">Header</header>
      <main className="main">{children}</main>
      <footer className="footer">© 2026</footer>
    </div>
  );
}

function AppKompozitsiya() {
  return (
    <Layout>
      <h1>Asosiy sahifa</h1>
      <p>Mazmun bu yerda</p>
      <Tugma label="Boshlash" />
    </Layout>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 5) Rest props va spread (input wrapper)
// ─────────────────────────────────────────────────────────────────────

function FormaInput({ label, ...inputProps }) {
  return (
    <label className="forma-input">
      <span>{label}</span>
      <input {...inputProps} />
    </label>
  );
}

function FormaApp() {
  return (
    <form>
      <FormaInput label="Ism"     type="text"  placeholder="Olim" required />
      <FormaInput label="Email"   type="email" placeholder="x@y.uz" />
      <FormaInput label="Parol"   type="password" minLength={8} />
    </form>
  );
}

// ─────────────────────────────────────────────────────────────────────
// 6) Funksiya prop — child → parent xabar
// ─────────────────────────────────────────────────────────────────────

function HavolaTugmasi({ label, onClick }) {
  return (
    <button onClick={onClick} className="havola">
      {label}
    </button>
  );
}

function MenyuApp() {
  const tanlash = (nom) => alert(`Tanlandi: ${nom}`);

  return (
    <nav>
      <HavolaTugmasi label="Bosh"    onClick={() => tanlash("Bosh")} />
      <HavolaTugmasi label="Kurslar" onClick={() => tanlash("Kurslar")} />
      <HavolaTugmasi label="Profil"  onClick={() => tanlash("Profil")} />
    </nav>
  );
}

// ─────────────────────────────────────────────────────────────────────
// Ataylab xato — props'ni o'zgartirish
// ─────────────────────────────────────────────────────────────────────

/*
function CardXato({ sarlavha }) {
  sarlavha = "yangi";   // ❌ react qoidasiga zid
  return <h3>{sarlavha}</h3>;
}
// Sabab: props read-only. Agar o'zgartirish kerak bo'lsa — useState.
*/
