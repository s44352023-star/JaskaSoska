Mana barcha talablaringizni to'liq qamrab olgan, zamonaviy va mukammal formalı React komponenti (RegistrationForm).

RegistrationForm.jsx (Asosiy Forma Komponenti)
JavaScript
import { useState } from 'react';
import './Form.css';

function RegistrationForm() {
  // 1. Forma holati (Object State)
  const [formData, setFormData] = useState({
    ism: '',
    email: '',
    parol: '',
    parol2: '',
    yosh: '',
    jins: 'erkak',
    obuna: false,
    bio: ''
  });

  // 2. Boshqa yordamchi holatlar
  const [errors, setErrors] = useState({});
  const [isSubmitted, setIsSubmitted] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [successMessage, setSuccessMessage] = useState('');

  // 3. Bitta universal handleChange funksiyasi (name attribute orqali)
  const handleChange = (e) => {
    const { name, type, value, checked } = e.target;
    
    // Checkbox uchun checked, qolganlari uchun value olamiz
    const newValue = type === 'checkbox' ? checked : value;

    setFormData(prev => ({
      ...prev,
      [name]: newValue
    }));

    // Real-time validatsiya (har klavishada tekshiriladi, lekin xato faqat submitdan keyin chiqadi)
    validate({ ...formData, [name]: newValue });
  };

  // 4. Validatsiya funksiyasi
  const validate = (data) => {
    let newErrors = {};

    if (!data.ism.trim()) {
      newErrors.ism = "Ism kiritilishi shart.";
    }

    if (!data.email.includes('@')) {
      newErrors.email = "Noto'g'ri email manzil.";
    }

    if (data.parol.length < 6) {
      newErrors.parol = "Parol kamida 6 ta belgidan iborat bo'lishi kerak.";
    }

    if (data.parol !== data.parol2) {
      newErrors.parol2 = "Parollar mos kelmadi.";
    }

    if (!data.yosh || data.yosh < 18 || data.yosh > 100) {
      newErrors.yosh = "Yosh 18 va 100 oralig'ida bo'lishi kerak.";
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  // Forma haqiqatdan ham yaroqli ekanligini tekshirish (Submit tugmasi uchun)
  const isFormValid = Object.keys(errors).length === 0 && 
                      formData.ism && 
                      formData.email && 
                      formData.parol && 
                      formData.parol2 && 
                      formData.yosh;

  // 5. Submit hodisasi
  const handleSubmit = (e) => {
    e.preventDefault();
    setIsSubmitted(true); // Endi xato xabarlarini ko'rsatishga ruxsat beramiz

    if (!validate(formData)) return;

    setIsLoading(true);
    setSuccessMessage('');

    // Async submit imitatsiyasi (setTimeout)
    setTimeout(() => {
      setIsLoading(false);
      setSuccessMessage('🎉 Ro\'yxatdan muvaffaqiyatli o\'tdingiz!');
      // Formani tozalash istasangiz shu yerda setFormData qilish mumkin
    }, 2000);
  };

  return (
    <div className="form-container">
      <h2>Ro'yxatdan o'tish</h2>
      
      {successMessage && <div className="success-banner">{successMessage}</div>}

      <form onSubmit={handleSubmit}>
        {/* Ism (text) */}
        <div className="form-group">
          <label>Ism:</label>
          <input 
            type="text" 
            name="ism" 
            value={formData.ism} 
            onChange={handleChange} 
            placeholder="Ismingizni kiriting"
          />
          {isSubmitted && errors.ism && <span className="error-text">{errors.ism}</span>}
        </div>

        {/* Email (email) */}
        <div className="form-group">
          <label>Email:</label>
          <input 
            type="email" 
            name="email" 
            value={formData.email} 
            onChange={handleChange} 
            placeholder="example@mail.com"
          />
          {isSubmitted && errors.email && <span className="error-text">{errors.email}</span>}
        </div>

        {/* Parol (password) */}
        <div className="form-group">
          <label>Parol:</label>
          <input 
            type="password" 
            name="parol" 
            value={formData.parol} 
            onChange={handleChange} 
            placeholder="******"
          />
          {isSubmitted && errors.parol && <span className="error-text">{errors.parol}</span>}
        </div>

        {/* Parolni tasdiqlash (password) */}
        <div className="form-group">
          <label>Parolni tasdiqlang:</label>
          <input 
            type="password" 
            name="parol2" 
            value={formData.parol2} 
            onChange={handleChange} 
            placeholder="******"
          />
          {isSubmitted && errors.parol2 && <span className="error-text">{errors.parol2}</span>}
        </div>

        {/* Yosh (number) */}
        <div className="form-group">
          <label>Yosh:</label>
          <input 
            type="number" 
            name="yosh" 
            value={formData.yosh} 
            onChange={handleChange} 
            placeholder="18"
          />
          {isSubmitted && errors.yosh && <span className="error-text">{errors.yosh}</span>}
        </div>

        {/* Jins (radio) */}
        <div className="form-group">
          <label>Jins:</label>
          <div className="radio-group">
            <label>
              <input 
                type="radio" 
                name="jins" 
                value="erkak" 
                checked={formData.jins === 'erkak'} 
                onChange={handleChange} 
              /> Erkak
            </label>
            <label>
              <input 
                type="radio" 
                name="jins" 
                value="ayol" 
                checked={formData.jins === 'ayol'} 
                onChange={handleChange} 
              /> Ayol
            </label>
          </div>
        </div>

        {/* Bio (textarea) */}
        <div className="form-group">
          <label>O'zingiz haqingizda (Bio):</label>
          <textarea 
            name="bio" 
            value={formData.bio} 
            onChange={handleChange} 
            placeholder="Qisqacha ma'lumot..."
            rows="3"
          ></textarea>
        </div>

        {/* Obuna (checkbox) */}
        <div className="form-group checkbox-row">
          <input 
            type="checkbox" 
            name="obuna" 
            checked={formData.obuna} 
            onChange={handleChange} 
            id="obuna-id"
          />
          <label htmlFor="obuna-id">Yangiliklarga obuna bo'lish</label>
        </div>

        {/* Submit tugmasi (Disabled while invalid & Loading state) */}
        <button 
          type="submit" 
          disabled={!isFormValid || isLoading} 
          className="submit-btn"
        >
          {isLoading ? 'Yuklanmoqda...' : 'Ro\'yxatdan o\'tish'}
        </button>
      </form>
    </div>
  );
}

export default RegistrationForm;
Form.css (Dizayn va uslublar)
CSS
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
  background-color: #f1f5f9;
  padding: 30px 20px;
}

.form-container {
  max-width: 480px;
  margin: 0 auto;
  background: #ffffff;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05);
}

.form-container h2 {
  margin-bottom: 20px;
  color: #1e293b;
  text-align: center;
}

.form-group {
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.form-group label {
  font-size: 0.9rem;
  font-weight: 600;
  color: #475569;
}

.form-group input[type="text"],
.form-group input[type="email"],
.form-group input[type="password"],
.form-group input[type="number"],
.form-group textarea,
.form-group select {
  padding: 10px 12px;
  border: 1px solid #cbd5e1;
  border-radius: 8px;
  outline: none;
  font-size: 0.95rem;
  transition: border-color 0.2s;
}

.form-group input:focus,
.form-group textarea:focus {
  border-color: #3b82f6;
}

.radio-group {
  display: flex;
  gap: 20px;
  align-items: center;
  font-weight: normal;
}

.radio-group label {
  font-weight: normal;
  cursor: pointer;
  display: flex;
  gap: 6px;
  align-items: center;
}

.checkbox-row {
  flex-direction: row;
  align-items: center;
  gap: 10px;
  cursor: pointer;
}

.checkbox-row input {
  width: 18px;
  height: 18px;
  cursor: pointer;
}

.checkbox-row label {
  cursor: pointer;
}

.error-text {
  font-size: 0.8rem;
  color: #ef4444;
  font-weight: 500;
}

.success-banner {
  background: #d1fae5;
  color: #065f46;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  margin-bottom: 20px;
  font-weight: 600;
}

.submit-btn {
  width: 100%;
  padding: 12px;
  background: #3b82f6;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, opacity 0.2s;
  margin-top: 10px;
}

.submit-btn:hover:not(:disabled) {
  background: #2563eb;
}

.submit-btn:disabled {
  background: #94a3b8;
  cursor: not-allowed;
  opacity: 0.7;
}
