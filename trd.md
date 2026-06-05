# TRD — Техническое требование к комикс-приглашению «Эмоциональное Лидерство»

**Проект:** Комикс-приглашение на тренинг  
**Автор:** Жанна Адам  
**Версия:** 1.0  
**Дата:** 31 мая 2026  
**Статус:** В разработке

---

## 1. Архитектура решения

### Тип продукта
Статичный одностраничный HTML-файл (SPA без фреймворков). Не требует сервера, базы данных или backend. Распространяется как файл или по прямой ссылке.

### Стек
| Слой | Технология | Обоснование |
|------|-----------|-------------|
| Разметка | HTML5 | Нативная поддержка везде |
| Стили | CSS3 (inline `<style>`) | Один файл, без зависимостей |
| Иллюстрации | SVG (inline) | Масштабируемость, нет HTTP-запросов |
| Анимации | CSS keyframes | Нет JS-зависимостей, плавно |
| Интерактив | Vanilla JS (`<script>`) | Только форма отправки |
| Шрифты | Google Fonts CDN | Bangers, Oswald, Noto Serif JP |
| Мессенджер | WhatsApp API (`wa.me`) | Прямой переход, без backend |

---

## 2. Структура файла

```
zhanna-marvel-comic.html
│
├── <head>
│   ├── meta charset, viewport
│   ├── title
│   └── <link> Google Fonts (Bangers, Oswald, Noto Serif JP)
│
├── <style>
│   ├── CSS переменные (:root)
│   ├── Базовые стили (body, .comic-book)
│   ├── Компоненты (panel, cap, bub, sfx, btn)
│   └── CTA-блок (форма, кнопки)
│
├── <body>
│   ├── .marvel-banner        — верхняя полоса с логотипом
│   ├── .cover                — обложка с заголовком
│   ├── .grid                 — сетка панелей (6 панелей)
│   │   ├── Panel 1 (full)    — Пробка
│   │   ├── Panel 2 (left)    — Совещание
│   │   ├── Panel 3 (right)   — Шторм
│   │   ├── Panel 4 (full)    — Пауза
│   │   ├── Panel 5 (left)    — Осознание
│   │   └── Panel 6 (right)   — Результат
│   └── .cta-section          — блок записи
│       ├── event-bar         — дата/город/формат/для кого
│       ├── btn-wa            — кнопка WhatsApp
│       └── form-area         — форма + кнопка записи
│
└── <script>
    └── send()               — отправка формы в WhatsApp
```

---

## 3. CSS-архитектура

### 3.1 CSS-переменные
```css
:root {
  --marvel-red:    #e23636;   /* Основной акцент */
  --marvel-yellow: #f5c518;   /* Золото, CTA */
  --marvel-blue:   #1a6fb5;   /* Вторичный акцент */
  --marvel-dark:   #0d0d0d;   /* Фон тёмный */
  --marvel-ink:    #111;      /* Контуры, обводки */
  --marvel-paper:  #faf6e9;   /* Фон панелей */
  --marvel-orange: #f47b20;   /* Доп. акцент */
  --marvel-green:  #2d8a4e;   /* WhatsApp */
}
```

### 3.2 Сетка панелей
```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 5px;
  background: var(--marvel-ink);  /* Межпанельные линии */
  padding: 5px;
}
.full  { grid-column: 1 / -1; }  /* На всю ширину */
.panel { border: 3px solid var(--marvel-ink); }
```

### 3.3 Эффекты Ben-Day dots (текстура комикса)
```css
/* На каждой панели через ::after */
.panel::after {
  content: '';
  position: absolute;
  inset: 0;
  background-image: radial-gradient(
    circle, rgba(0,0,0,0.06) 1px, transparent 1px
  );
  background-size: 4px 4px;
  pointer-events: none;
  z-index: 0;
}
```

### 3.4 Анимации
```css
/* Появление панелей */
@keyframes marvelPop {
  from { opacity: 0; transform: scale(.92) rotate(-1deg); }
  to   { opacity: 1; transform: scale(1) rotate(0deg); }
}
.panel { animation: marvelPop .4s cubic-bezier(.34,1.56,.64,1) both; }
.panel:nth-child(1) { animation-delay: .05s; }
/* ... до .panel:nth-child(7) { animation-delay: .47s; } */

/* Кнопки тактильный эффект */
.btn-enroll:hover  { transform: translate(-2px,-2px); box-shadow: 5px 5px 0 var(--ink); }
.btn-enroll:active { transform: translate(2px,2px);  box-shadow: 1px 1px 0 var(--ink); }
```

---

## 4. SVG-иллюстрации

### 4.1 Принципы построения персонажа
Кот-самурай собирается из примитивов SVG без растровых изображений:

| Элемент | SVG-тег | Описание |
|---------|---------|----------|
| Голова | `<ellipse>` | rx/ry ~26/24 |
| Уши | `<polygon>` | 3 точки, треугольник |
| Ушная раковина | `<polygon>` | Внутренний треугольник, розовый |
| Повязка хатимаки | `<rect>` | rx=3, fill=#111 |
| Очки | `<circle>` | fill=none, stroke=#f5c518 |
| Глаза | `<ellipse>` | 3 слоя: белок / радужка / зрачок |
| Брови | `<path>` | stroke-only, меняются по эмоции |
| Усы | `<line>` | x4 (симметрично) |
| Тело | `<rect>` | Туловище кимоно |
| Кимоно-воротник | `<path>` | V-форма |
| Оби | `<rect>` | Широкий пояс, --marvel-yellow |
| Наплечники | `<path>` | Самурайские содэ |
| Лапы | `<path>` | stroke-linecap:round, толстые |
| Катана | `<line>` + `<ellipse>` | Клинок + цуба |

### 4.2 Эмоциональная матрица кота
| Панель | Эмоция | Брови | Глаза | Рот | Уши |
|--------|--------|-------|-------|-----|-----|
| I | Злость (пробка) | Крутые внутрь | Красная радужка | Прямая линия | Прямо |
| II | Раздражение | Нахмурены | Красная радужка | Плотно сжат | Прямо |
| III | Ярость | Экстремально | Красная, большая | Оскал | Вперёд |
| IV | Покой | Мягкие | Закрытые (дуга) | Улыбка | Опущены |
| V | Уверенность | Решительные | Синяя радужка | Смирк | Прямо |
| VI | Тепло | Мягкие | Синяя радужка | Широкая улыбка | Вперёд |

### 4.3 viewBox и масштабирование
- Панели полной ширины: `viewBox="0 0 720 290"`
- Панели половинной ширины: `viewBox="0 0 350 300"`
- Все SVG: `width="100%" height="auto"` → адаптивность

---

## 5. Интеграция WhatsApp

### 5.1 Прямая кнопка
```html
<a href="https://wa.me/77017169473?text=Привет%2C%20Жанна!%20Хочу%20записаться%20на%20тренинг%20Эмоциональное%20Лидерство%2031%20мая%20в%20Астане."
   target="_blank">
  НАПИСАТЬ В WHATSAPP
</a>
```

### 5.2 Отправка формы через WhatsApp
```javascript
function send() {
  var name  = document.getElementById('fn').value.trim();
  var phone = document.getElementById('fp').value.trim();

  if (!name || !phone) {
    alert('Заполните имя и телефон!');
    return;
  }

  var msg = encodeURIComponent(
    'Новая заявка!\nИмя: ' + name + '\nТелефон: ' + phone
  );

  window.open('https://wa.me/77017169473?text=' + msg, '_blank');

  document.getElementById('form').style.display = 'none';
  document.getElementById('ok').style.display   = 'block';
}
```

### 5.3 Формат сообщения, которое получает Жанна
```
Новая заявка!
Имя: [имя клиента]
Телефон: [номер клиента]
```

### 5.4 Номер телефона
```
Международный формат: +7 701 716 94 73
В wa.me URL: 77017169473 (без +)
```

---

## 6. Адаптивность

### 6.1 Брейкпоинты
| Ширина | Поведение |
|--------|----------|
| > 720px | Комикс центрирован, max-width: 720px |
| 500–720px | Полная ширина, сетка 1fr 1fr |
| < 500px | Форма и кнопки в column |

### 6.2 Media query
```css
@media (max-width: 500px) {
  .form-row { flex-direction: column; }
  .btns     { flex-direction: column; }
  .cover-title { font-size: 28px; }
}
```

### 6.3 Fluid шрифты
```css
.cover-title   { font-size: clamp(36px, 8vw, 58px); }
.cta-heading   { font-size: clamp(28px, 6vw, 48px); }
```

---

## 7. Производительность

| Параметр | Цель | Факт |
|----------|------|------|
| Размер файла | < 100 KB | ~57 KB |
| Внешние запросы | Только Google Fonts | 1 запрос |
| Растровые изображения | 0 | 0 |
| JavaScript | Минимум | ~20 строк |
| Время до интерактивности | < 2 сек на LTE | ~0.5 сек |

---

## 8. Шрифты

| Шрифт | Использование | Вес |
|-------|--------------|-----|
| Bangers | Заголовки, SFX, CTA | 400 |
| Oswald | Подписи, формы, капшены | 400, 600, 700 |
| Noto Serif JP | Японские иероглифы | 400, 700, 900 |

```html
<link href="https://fonts.googleapis.com/css2?
  family=Bangers
  &family=Oswald:wght@400;600;700
  &family=Noto+Serif+JP:wght@400;700;900
  &display=swap" rel="stylesheet">
```

**Fallback:** `cursive` для Bangers, `sans-serif` для Oswald.

---

## 9. Безопасность и приватность

- Данные формы **не сохраняются** — только передаются в WhatsApp
- Нет cookies, localStorage, sessionStorage
- Нет аналитики, трекеров, внешних скриптов
- Телефон Жанны виден в исходном коде — принято намеренно (открытый контакт)

---

## 10. Варианты распространения

| Способ | Плюсы | Минусы |
|--------|-------|--------|
| **Файл .html** (скачать и открыть) | Без хостинга | Нужно скачать |
| **GitHub Pages** | Бесплатно, HTTPS, ссылка | Нужен аккаунт GitHub |
| **Telegram** (прикрепить файл) | Быстро | Нужно открывать в браузере |
| **Notion / Tilda embed** | Встраивается на страницу | Ограничения iframe |
| **Netlify Drop** | Drag & drop, ссылка за 30 сек | Сторонний сервис |

**Рекомендация:** Netlify Drop — загрузить файл на [app.netlify.com/drop](https://app.netlify.com/drop), получить ссылку вида `https://xxxx.netlify.app` и поделиться в Telegram/WhatsApp.

---

## 11. Известные ограничения

- Google Fonts не загружается без интернета (шрифты деградируют до fallback)
- WhatsApp-форма открывает приложение/веб — пользователь должен нажать «Отправить» сам
- SVG-иллюстрации не являются фотореалистичными — это стилизация
- Ben-Day dots не видны на экранах с очень низким DPI (редкость)

---

## 12. Файлы проекта

| Файл | Описание | Статус |
|------|----------|--------|
| `zhanna-marvel-comic.html` | Marvel-версия (актуальная) | ✅ Готов |
| `zhanna-comic-v2.html` | Японская версия | ✅ Готов |
| `zhanna-samurai-invitation.html` | Версия с фото | ✅ Готов |
| `samurai-invitation.html` | Первый прототип | ✅ Архив |
| `prd.md` | Product Requirements | ✅ Готов |
| `trd.md` | Technical Requirements | ✅ Этот файл |

---

*TRD обновляется при изменении технических решений.*
