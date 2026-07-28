Mana siz talab qilgan barcha talablarni o'z ichiga olgan, zamonaviy va chiroyli dizayndagi to'liq React dasturi.

1. App.jsx (Asosiy Komponent va State Lifting)
JavaScript
import { useState } from 'react';
import Counter from './Counter';
import TodoList from './TodoList';
import './App.css';

function App() {
  // 1. State Lifting: Counter'lar holati yuqorida (App'da) saqlanadi
  const [c1, setC1] = useState(0);
  const [c2, setC2] = useState(5);
  const [c3, setC3] = useState(10);

  // 2. TodoList state
  const [todos, setTodos] = useState([]);
  const [inputVal, setInputVal] = useState('');

  // Todo qo'shish (Enter tugmasi va tugma uchun umumiy)
  const addTodo = () => {
    if (!inputVal.trim()) return;
    const newItem = {
      id: Date.now(),
      text: inputVal,
      completed: false,
    };
    setTodos([...todos, newItem]);
    setInputVal('');
  };

  const handleKeyDown = (e) => {
    if (e.key === 'Enter') {
      addTodo();
    }
  };

  // Todo'ni o'chirish
  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  // Todo statusini o'zgartirish (toggle)
  const toggleTodo = (id) => {
    setTodos(
      todos.map(todo => 
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  // 3. Derived State: Jami va qolgan todolar sonini hisoblash
  const totalTodos = todos.length;
  const completedTodos = todos.filter(t => t.completed).length;
  const remainingTodos = totalTodos - completedTodos;

  return (
    <div className="app-container">
      <header className="app-header">
        <h1>🚀 React State Boshqaruvi</h1>
        <p>State Lifting, Controlled Components va Derived State amalda</p>
      </header>

      {/* --- COUNTERS SECTION (State Lifting) --- */}
      <section className="card">
        <h2>📊 Controlled Counters (State Lifting)</h2>
        <div className="counters-grid">
          <Counter title="1-Hisoblagich" value={c1} onChange={setC1} />
          <Counter title="2-Hisoblagich" value={c2} onChange={setC2} />
          <Counter title="3-Hisoblagich" value={c3} onChange={setC3} />
        </div>
        <div className="total-counters">
          Jami yig'indi: <strong>{c1 + c2 + c3}</strong>
        </div>
      </section>

      {/* --- TODO LIST SECTION --- */}
      <section className="card">
        <h2>📝 Vazifalar Ro'yxati (TodoList)</h2>
        
        {/* Derived State ko'rsatkichlari */}
        <div className="stats-badge-container">
          <span className="badge total">Jami: {totalTodos}</span>
          <span className="badge active">Qolgan: {remainingTodos}</span>
          <span className="badge completed">Bajarilgan: {completedTodos}</span>
        </div>

        {/* Input va Enter key */}
        <div className="todo-input-group">
          <input 
            type="text" 
            value={inputVal} 
            onChange={(e) => setInputVal(e.target.value)}
            onKeyDown={handleKeyDown}
            placeholder="Yangi vazifa yozing va Enter bosing..." 
          />
          <button className="btn-primary" onClick={addTodo}>Qo'shish</button>
        </div>

        {/* Todo List komponenti */}
        <TodoList 
          todos={todos} 
          onDelete={deleteTodo} 
          onToggle={toggleTodo} 
        />
      </section>
    </div>
  );
}

export default App;
2. Counter.jsx (Controlled Counter Komponenti)
JavaScript
function Counter({ title, value, onChange }) {
  return (
    <div className="counter-box">
      <h4>{title}</h4>
      <div className="counter-controls">
        <button onClick={() => onChange(value - 1)}>-</button>
        <span className="counter-value">{value}</span>
        <button onClick={() => onChange(value + 1)}>+</button>
      </div>
      <button className="btn-reset" onClick={() => onChange(0)}>Reset</button>
    </div>
  );
}

export default Counter;
3. TodoList.jsx (Ro'yxat va Empty State)
JavaScript
function TodoList({ todos, onDelete, onToggle }) {
  // Bo'sh holat (Empty State) tekshiruvi
  if (todos.length === 0) {
    return (
      <div className="empty-state">
        <p>📭 Hech narsa yo'q</p>
      </div>
    );
  }

  return (
    <ul className="todo-list">
      {todos.map((todo) => (
        <li key={todo.id} className={`todo-item ${todo.completed ? 'completed' : ''}`}>
          <label className="todo-label">
            <input 
              type="checkbox" 
              checked={todo.completed} 
              onChange={() => onToggle(todo.id)} 
            />
            <span className="todo-text">{todo.text}</span>
          </label>
          <button className="btn-delete" onClick={() => onDelete(todo.id)}>O'chirish</button>
        </li>
      ))}
    </ul>
  );
}

export default TodoList;
4. App.css (Tashqi dizayn va uslublar)
CSS
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
  background-color: #f4f7f6;
  color: #333;
  padding: 20px;
}

.app-container {
  max-width: 700px;
  margin: 0 auto;
}

.app-header {
  text-align: center;
  margin-bottom: 30px;
}

.app-header h1 {
  color: #2c3e50;
  margin-bottom: 5px;
}

.app-header p {
  color: #7f8c8d;
}

.card {
  background: #ffffff;
  padding: 25px;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05);
  margin-bottom: 25px;
}

.card h2 {
  font-size: 1.2rem;
  margin-bottom: 15px;
  color: #34495e;
  border-bottom: 2px solid #ecf0f1;
  padding-bottom: 8px;
}

/* Counters Grid */
.counters-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  margin-bottom: 15px;
}

.counter-box {
  background: #f9f9f9;
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  border: 1px solid #e1e8ed;
}

.counter-box h4 {
  font-size: 0.9rem;
  color: #555;
  margin-bottom: 10px;
}

.counter-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-bottom: 10px;
}

.counter-controls button, .btn-reset, .btn-primary, .btn-delete {
  cursor: pointer;
  border: none;
  border-radius: 6px;
  font-weight: 600;
  transition: 0.2s;
}

.counter-controls button {
  background: #e2e8f0;
  width: 30px;
  height: 30px;
  font-size: 1rem;
}

.counter-controls button:hover {
  background: #cbd5e1;
}

.counter-value {
  font-size: 1.1rem;
  font-weight: bold;
}

.btn-reset {
  background: #fee2e2;
  color: #dc2626;
  padding: 5px 10px;
  font-size: 0.8rem;
  width: 100%;
}

.btn-reset:hover {
  background: #fecaca;
}

.total-counters {
  text-align: right;
  font-size: 0.95rem;
  color: #475569;
}

/* Stats Badges */
.stats-badge-container {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.badge {
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
}

.badge.total { background: #e0f2fe; color: #0369a1; }
.badge.active { background: #fef3c7; color: #b45309; }
.badge.completed { background: #d1fae5; color: #047857; }

/* Todo Input */
.todo-input-group {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.todo-input-group input {
  flex: 1;
  padding: 10px 15px;
  border: 1px solid #cbd5e1;
  border-radius: 8px;
  outline: none;
  font-size: 0.95rem;
  transition: border-color 0.2s;
}

.todo-input-group input:focus {
  border-color: #3b82f6;
}

.btn-primary {
  background: #3b82f6;
  color: white;
  padding: 0 20px;
}

.btn-primary:hover {
  background: #2563eb;
}

/* Todo List Items */
.todo-list {
  list-style: none;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 15px;
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  margin-bottom: 10px;
  transition: background 0.2s;
}

.todo-item:hover {
  background: #f1f5f9;
}

.todo-label {
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  flex: 1;
}

.todo-label input[type="checkbox"] {
  width: 18px;
  height: 18px;
  cursor: pointer;
}

/* Bajarilgan todo uchun chizib qo'yish (CSS) */
.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: #94a3b8;
}

.btn-delete {
  background: #fee2e2;
  color: #ef4444;
  padding: 6px 12px;
  font-size: 0.85rem;
}

.btn-delete:hover {
  background: #fecaca;
}

/* Empty State */
.empty-state {
  text-align: center;
  padding: 30px;
  color: #94a3b8;
  font-size: 1.1rem;
  background: #f8fafc;
  border: 2px dashed #cbd5e1;
  border-radius: 8px;
}
