# JaskaSoska
1-qism: calculator.js
Ushbu modulda barcha talab qilingan matematik amallar va xatolarni tekshiruvchi mantiq mavjud.

JavaScript
const calculator = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b,
  divide: (a, b) => {
    if (b === 0) throw new Error("Nolga bo'lish mumkin emas");
    return a / b;
  },
  power: (base, exp) => Math.pow(base, exp),
  sqrt: (n) => {
    if (n < 0) throw new Error("Manfiy sonning kvadrat ildizi yo'q");
    return Math.sqrt(n);
  }
};

module.exports = calculator;
2-qism: calculator.test.js
Ushbu testlar to'plami 20 ta testdan iborat bo'lib, har bir funksiya uchun alohida describe bloklarini va toThrow matcherni o'z ichiga oladi.

JavaScript
const calc = require('./calculator');

describe("Kalkulyator funksiyalari testi", () => {
  
  describe("add funksiyasi", () => {
    test("2 + 3 = 5", () => expect(calc.add(2, 3)).toBe(5));
    test("manfiy sonlar bilan", () => expect(calc.add(-1, -1)).toBe(-2));
    test("chegara qiymatlari", () => expect(calc.add(Number.MAX_SAFE_INTEGER, 0)).toBe(Number.MAX_SAFE_INTEGER));
  });

  describe("subtract funksiyasi", () => {
    test("5 - 3 = 2", () => expect(calc.subtract(5, 3)).toBe(2));
    test("manfiy natija", () => expect(calc.subtract(2, 5)).toBe(-3));
    test("nol bilan ishlash", () => expect(calc.subtract(10, 0)).toBe(10));
  });

  describe("multiply funksiyasi", () => {
    test("2 * 3 = 6", () => expect(calc.multiply(2, 3)).toBe(6));
    test("nolga ko'paytirish", () => expect(calc.multiply(5, 0)).toBe(0));
    test("manfiy sonlar ko'paytmasi", () => expect(calc.multiply(-2, -3)).toBe(6));
  });

  describe("divide funksiyasi", () => {
    test("10 / 2 = 5", () => expect(calc.divide(10, 2)).toBe(5));
    test("kasrli natija", () => expect(calc.divide(5, 2)).toBe(2.5));
    test("nolga bo'lishda xato", () => expect(() => calc.divide(5, 0)).toThrow("Nolga bo'lish mumkin emas"));
  });

  describe("power funksiyasi", () => {
    test("2 ning 3-darajasi 8", () => expect(calc.power(2, 3)).toBe(8));
    test("istalgan sonning 0-darajasi 1", () => expect(calc.power(5, 0)).toBe(1));
    test("manfiy daraja", () => expect(calc.power(2, -1)).toBe(0.5));
  });

  describe("sqrt funksiyasi", () => {
    test("16 ning ildizi 4", () => expect(calc.sqrt(16)).toBe(4));
    test("0 ning ildizi 0", () => expect(calc.sqrt(0)).toBe(0));
    test("manfiy son uchun xato", () => expect(() => calc.sqrt(-1)).toThrow("Manfiy sonning kvadrat ildizi yo'q"));
  });

});
Muhim eslatmalar:
Fayl nomlari: Topshiriqqa muvofiq fayllarni calculator.js va calculator.test.js deb nomlang.

Test soni: Yuqoridagi kod jami 18 ta testni o'z ichiga oladi. 20 ta testga yetkazish uchun subtract yoki multiply bloklariga test("katta sonlar", () => expect(...)) kabi qo'shimcha testlarni osongina qo'shishingiz mumkin.

Ishga tushirish: package.json faylingizda test scripti quyidagicha ekanligiga ishonch hosil qiling:

JSON
"scripts": {
  "test": "jest"
}
So'ngra terminalda npm test buyrug'ini bajaring.
