# PCshop — каталог комплектуючих для ПК на Vue 3

Навчальний проєкт (КП № 2): каталог комплектуючих для комп'ютера з реактивним кошиком.

**Тема:** інтернет-магазин ПК-комплектуючих (процесори, відеокарти, материнські плати, пам'ять, накопичувачі, блоки живлення).

![Скриншот сайту](docs/screenshot.png)

## Як запустити

```bash
npm install
npm run dev
```

Сайт відкриється за адресою, яку покаже Vite (зазвичай http://localhost:5173).

Збірка для публікації:

```bash
npm run build
npm run preview
```

## Що реалізовано

| Вимога | Де в коді |
| --- | --- |
| Каталог із 14 позицій (назва, ціна, зображення, категорія), дані з JSON через `fetch` | `public/products.json`, `loadProducts()` в `src/App.vue` |
| Фільтр за категорією і пошук за назвою через `computed` | `filteredProducts` в `src/App.vue` |
| Кошик: додати, змінити кількість (+ / −), видалити; лічильник у шапці і сума оновлюються одразу | `cart`, `cartItems`, `cartCount`, `cartTotal` в `src/App.vue` |
| Власні компоненти з props і emit | `ProductCard`, `CartItem`, `CheckoutForm`, `ProductImage` в `src/components/` |
| Кошик у `localStorage` (через `watch`) | `watch([cart, appliedPromo], …)` в `src/App.vue` |
| Окремі повідомлення для порожнього кошика і «нічого не знайдено» | блоки `.state` в `src/App.vue` |
| Адаптивна верстка | `src/style.css` (mobile-first, брейкпоінти 640 і 900 px) |

Додатково (за бажанням):

- оформлення замовлення з перевіркою полів (ім'я, телефон, адреса) — `CheckoutForm.vue`;
- сортування за ціною;
- промокоди `PC10` (−10%) і `GAMER5` (−5%);
- плавні анімації списків (`TransitionGroup`), лічильники товарів у категоріях, бейджі «Хіт» / «Топ» / «Вигідно».

## Структура

```
public/
  products.json        дані каталогу
  images/              зображення товарів (по одному файлу на товар)
src/
  App.vue              стан, computed, watch, розмітка сторінки
  utils.js             formatPrice, pluralize, MAX_QTY
  style.css            стилі
  components/
    ProductCard.vue    картка товару (props: product, inCart; emit: add)
    CartItem.vue       рядок кошика (props: item; emit: increase, decrease, remove)
    CheckoutForm.vue   форма замовлення (props: total; emit: submit)
    ProductImage.vue   зображення з можливістю запасного варіанта
```

## Зображення

Для кожного товару в `public/images/` є своє зображення, а ім'я файлу збігається з назвою товару
(наприклад, `rtx-4060.svg`, `corsair-rm650.svg`). Шлях до зображення зберігається в полі `image`
у `public/products.json`.

Щоб замінити ілюстрацію на реальне фото, покладіть файл у `public/images/` і змініть поле `image`,
наприклад: `"image": "images/rtx-4060.jpg"`. Необов'язкове поле `fallback` задає запасне
зображення на випадок, якщо фото не завантажилось.

## Публікація на GitHub Pages

У репозиторії вже є `.github/workflows/deploy.yml`. Достатньо:

1. Завантажити проєкт у гілку `main`.
2. У налаштуваннях репозиторію відкрити **Settings → Pages** і в пункті **Source** вибрати **GitHub Actions**.
3. Після завершення workflow сайт з'явиться за адресою `https://<ваш-логін>.github.io/<назва-репозиторію>/`.

## Технології

Vue 3 (Composition API, `<script setup>`), Vite, чистий CSS.
