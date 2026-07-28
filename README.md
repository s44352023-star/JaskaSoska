Props — komponentlarga ma'lumot uzatish
ism, yosh, onClick

ism, yosh, onClick

ism, yosh, onClick

Parent App

Card

Card

Card

1-darsda biz statik <Salomlashish ism="Olim" /> yozdik. Endi nima uchun shunday ishladi va undan to'liq foydalanish vaqti. ism="Olim" — bu prop. Komponentlar bir-biriga shunday gaplashadi.

🏆 5 daqiqada g'alaba
BLOKA 1 — props qabul qilish
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
{ sarlavha, matn } — bu destructuring. props obyekti keladi va undan kerakli xususiyatlarni ajratib olamiz.

BLOKA 2 — turli ma'lumot turlari
function Profil({ ism, yosh, faol, sevganRanglar, profilRasm }) {
  return (
    <div>
      <img src={profilRasm} alt={ism} />
      <h2>{ism}</h2>
      <p>Yosh: {yosh}</p>
      <p>Holat: {faol ? "🟢 faol" : "⚫ noaktiv"}</p>
      <p>Ranglar: {sevganRanglar.join(", ")}</p>
    </div>
  );
}

<Profil
  ism="Nigora"
  yosh={22}
  faol={true}
  sevganRanglar={["ko'k", "yashil"]}
  profilRasm="/avatar.png"
/>
Diqqat: string'lardan tashqari hamma narsa {...} ichida. yosh={22} — son, faol={true} — boolean. yosh="22" deb yozsangiz — string keladi, son emas.

BLOKA 3 — children prop
function Layout({ children }) {
  return (
    <div className="container">
      <header>Header</header>
      <main>{children}</main>
      <footer>Footer</footer>
    </div>
  );
}

function App() {
  return (
    <Layout>
      <h1>Asosiy sahifa</h1>
      <p>Mazmun bu yerda</p>
    </Layout>
  );
}
children — bu komponent teglari orasidagi hammasi. Wrapper komponentlar (Modal, Card, Layout) shu bilan ishlaydi.

🐛 Ataylab xato
function Card({ sarlavha }) {
  sarlavha = "yangi sarlavha"; // ❌
  return <h3>{sarlavha}</h3>;
}
Sabab: Bu kod xato chiqarmaydi hozirgina, lekin bu — React qoidasiga zid: props read-only. Komponent o'z propsini o'zgartirmasin. Sabab: parent berdi, child o'zgartirsa, parent buni bilmaydi va sayt nomutanosib bo'ladi.

Agar value o'zgartirilishi kerak bo'lsa — useState ishlatish (3-darsda) yoki parent'ga callback yuborish.

Endi tushuntiramiz
1. Props bir tomonlama (one-way data flow)
Ma'lumot doim yuqoridan pastga oqadi: parent → child. Bu — React'ning eng muhim qoidalaridan biri.

App
 ├── Header
 │    └── Logo
 ├── Main
 │    ├── Card    ← App ma'lumotni shu yerga uzatadi
 │    └── List
 └── Footer
2. Destructuring vs to'liq props
// Destructuring (zamonaviy)
function Card({ sarlavha, matn }) {
  return <h3>{sarlavha} — {matn}</h3>;
}

// Eskirgan — to'liq props obyekti
function Card(props) {
  return <h3>{props.sarlavha} — {props.matn}</h3>;
}
Destructuring tavsiya — kodingiz oz va aniq.

3. Default qiymatlar
function Tugma({ label = "Tasdiqlash", turi = "primary", disabled = false }) {
  return (
    <button className={`btn btn-${turi}`} disabled={disabled}>
      {label}
    </button>
  );
}

<Tugma />                            // "Tasdiqlash"
<Tugma label="Bekor qilish" />       // "Bekor qilish"
<Tugma label="O'chirish" turi="danger" />
4. Rest props va spread
function Input({ label, ...boshqalari }) {
  return (
    <label>
      {label}
      <input {...boshqalari} />
    </label>
  );
}

<Input label="Ism" type="text" placeholder="Olim" required />
// label maxsus, qolgani input'ga uzatildi
5. Funksiya prop (event handler)
Prop sifatida funksiya ham yuborilishi mumkin. Bu — child'dan parent'ga "xabar yuborish" usuli:

function Tugma({ label, onClick }) {
  return <button onClick={onClick}>{label}</button>;
}

function App() {
  const salom = () => alert("Salom!");
  return <Tugma label="Salom" onClick={salom} />;
}
Soat: ma'lumot pastga, hodisa yuqoriga.

6. Kompozitsiya — slot pattern
function Card({ sarlavha, ozClick, children }) {
  return (
    <div className="card">
      <header>{sarlavha}</header>
      <div className="body">{children}</div>
    </div>
  );
}

// Children — har xil bo'lishi mumkin
<Card sarlavha="Profil">
  <img src="/avatar.png" />
  <p>Ism: Olim</p>
  <Tugma label="Tahrirlash" />
</Card>
📌 Bu darsdan keyin siz bilasizki
✅ Props — parent'dan child'ga ma'lumot. Read-only.
✅ Destructuring: function X({ a, b })
✅ String emas, hammasi {...} ichida
✅ Default qiymat: { ism = "Mehmon" }
✅ ...rest bilan qolgani uzatish
✅ children — wrapping komponentlar uchun
✅ Funksiya prop — child'dan parent'ga hodisa
💻
Код
Код
#2
jsx
 Копировать
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
