React state (holat) boshqaruvi va useState hook'ini mukammal tushunish uchun amaliy misollar va eng ko'p uchraydigan xatolar tahlili.

1. Counter — +/- /reset tugmalari
Oddiy raqamli holatni boshqarish va uni o'zgartirish.

JavaScript
import { useState } from 'react';

function Counter() {
  const [son, setSon] = useState(0);

  return (
    <div>
      <h2>Hisoblagich: {son}</h2>
      <button onClick={() => setSon(son + 1)}>+</button>
      <button onClick={() => setSon(son - 1)}>-</button>
      <button onClick={() => setSon(0)}>Reset</button>
    </div>
  );
}
2. DarkModeSwitch — Boolean toggle (useToggle pattern)
Foydalanuvchi interfeysining rejimini (kun/tun) o'zgartirish uchun mantiqiy (boolean) holat.

JavaScript
import { useState } from 'react';

function DarkModeSwitch() {
  const [isDarkMode, setIsDarkMode] = useState(false);

  // Toggle funksiyasi eski qiymatni teskarisiga o'giradi
  const toggleDarkMode = () => setIsDarkMode(prev => !prev);

  return (
    <div style={{ background: isDarkMode ? '#333' : '#FFF', color: isDarkMode ? '#FFF' : '#333', padding: '20px' }}>
      <p>Hozirgi rejim: {isDarkMode ? 'Tungi (Dark)' : 'Kunduzgi (Light)'}</p>
      <button onClick={toggleDarkMode}>Rejimni almashtirish</button>
    </div>
  );
}
3. SaloLogin — Input va Salomlashish (Controlled Input)
Forma elementlari qiymatini React state'i orqali nazorat qilish.

JavaScript
import { useState } from 'react';

function SaloLogin() {
  const [ism, setIsm] = useState('');

  return (
    <div>
      <input 
        type="text" 
        placeholder="Ismingizni kiriting..." 
        value={ism} 
        onChange={(e) => setIsm(e.target.value)} 
      />
      <h3>Salom, {ism || 'X'}!</h3>
    </div>
  );
}
4. TodoOddiy — Input, Add va Ro'yxat (Array State)
Massiv ko'rinishidagi holatni yangilash va element qo'shish.

JavaScript
import { useState } from 'react';

function TodoOddiy() {
  const [vazifa, setVazifa] = useState('');
  const [royxat, setRoyxat] = useState([]);

  const qoshish = () => {
    if (vazifa.trim() === '') return;
    setRoyxat([...royxat, vazifa]); // Yangi massiv yaratib qo'shish
    setVazifa(''); // Inputni tozalash
  };

  return (
    <div>
      <input 
        type="text" 
        value={vazifa} 
        onChange={(e) => setVazifa(e.target.value)} 
        placeholder="Yangi vazifa..."
      />
      <button onClick={qoshish}>Qo'shish</button>
      <ul>
        {royxat.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
5. Functional Update misoli (setSon(s => s + 1))
Oldingi holatga (previous state) tayanib yangilash zarur bo'lganda funksional usuldan foydalaniladi. Bu asinxron holatlarda xatoliklarning oldini oladi.

JavaScript
import { useState } from 'react';

function FunksionalYangilash() {
  const [son, setSon] = useState(0);

  const ertaolabBalandlash = () => {
    // Har safar eng oxirgi qiymatni kafolatli oladi
    setSon(s => s + 1);
    setSon(s => s + 1); // Natijada bir bosishda 2 taga oshadi
  };

  return (
    <div>
      <h3>Qiymat: {son}</h3>
      <button onClick={ertaolabBalandlash}>2 taga oshirish</button>
    </div>
  );
}
6. Object State misoli (Spread operator bilan)
Obyekt ko'rinishidagi state'ni yangilaganda faqat o'zgargan xususiyatni berib, qolganlarini saqlab qolish uchun spread (...) operatori ishlatiladi.

JavaScript
import { useState } from 'react';

function ObyektState() {
  const [foydalanuvchi, setFoydalanuvchi] = useState({
    ism: 'Alisher',
    yosh: 25,
    kasb: 'Dasturchi'
  });

  const yoshniYangilash = () => {
    setFoydalanuvchi(asosiyHolat => ({
      ...asosiyHolat, // Eski xususiyatlarni saqlab qolamiz
      yosh: asosiyHolat.yosh + 1 // Faqat yoshni o'zgartiramiz
    }));
  };

  return (
    <div>
      <p>Ism: {foydalanuvchi.ism}</p>
      <p>Yosh: {foydalanuvchi.yosh}</p>
      <p>Kasb: {foydalanuvchi.kasb}</p>
      <button onClick={yoshniYangilash}>Yoshni 1 ga oshirish</button>
    </div>
  );
}
7. Ataylab qilingan xato: onClick={setSon(son + 1)} va uning sababi (100/100 ball tahlil)
❌ Xato yozilgan kod:
JavaScript
function XatoMisol() {
  const [son, setSon] = useState(0);

  return (
    // XATO: Funksiya o'rniga uning natijasi to'g'ridan-to'g'ri berib yuborilgan
    <button onClick={setSon(son + 1)}>Bos meni</button>
  );
}
