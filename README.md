# JaskaSoska
// mathUtils.js — test qilinadigan modul
function sum(a, b) {
  return a + b;
}

function isEven(n) {
  return n % 2 === 0;
}

function divide(a, b) {
  if (b === 0) {
    throw new Error("Nolga bo'lish mumkin emas");
  }
  return a / b;
}

function factorial(n) {
  if (n < 0) throw new Error("Manfiy son uchun faktorial yo'q");
  if (n === 0 || n === 1) return 1;
  return n * factorial(n - 1);
}

module.exports = { sum, isEven, divide, factorial };

// mathUtils.test.js — Jest test fayli
const { sum, isEven, divide, factorial } = require('./mathUtils');

describe('mathUtils moduli', () => {
  describe('sum funksiyasi', () => {
    test('ikkita musbat sonni qo\'shadi', () => {
      expect(sum(2, 3)).toBe(5);
    });

    test('manfiy sonlarni ham to\'g\'ri qo\'shadi', () => {
      expect(sum(-2, -3)).toBe(-5);
    });

    test('nol bilan qo\'shish', () => {
      expect(sum(5, 0)).toBe(5);
    });
  });

  describe('isEven funksiyasi', () => {
    test('juft sonni aniqlaydi', () => {
      expect(isEven(4)).toBeTruthy();
    });

    test('toq sonni aniqlaydi', () => {
      expect(isEven(7)).toBeFalsy();
    });
  });

  describe('divide funksiyasi', () => {
    test('to\'g\'ri bo\'lish natijasi', () => {
      expect(divide(10, 2)).toBe(5);
    });

    test('nolga bo\'lishda xato tashlaydi', () => {
      expect(() => divide(10, 0)).toThrow("Nolga bo'lish mumkin emas");
    });
  });

  describe('factorial funksiyasi', () => {
    test('5! = 120', () => {
      expect(factorial(5)).toBe(120);
    });

    test('0! = 1', () => {
      expect(factorial(0)).toBe(1);
    });

    test('manfiy sonda xato', () => {
      expect(() => factorial(-1)).toThrow();
    });
  });
});

// Ishga tushirish: npx jest mathUtils.test.js --verbose
