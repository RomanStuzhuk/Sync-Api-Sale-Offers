# Документація API синхронізації товарів уцінки

## Ендпоїнти

### 1. Отримання JWT-токен
- **Метод:** `POST`
- **URL:** `{{url}}/api/sync/auth/login`
- **Тіло запиту (JSON):**
  ```json
  {
    "email": "testapi@test.com",
    "password": "somesecret"
  }
  ```
- **Опис:**  
  Використовується для аутентифікації. Повертає JWT токен для доступу до інших ендпоїнтів.

---

### 2. Оновлення JWT токена
- **Метод:** `POST`
- **URL:** `{{url}}/api/sync/auth/refresh`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Опис:**  
  Генерує новий JWT токен на основі поточного (якщо він ще дійсний).
  
---

### 3. Додавання / оновлення товарного офера
- **Метод:** `POST`
- **URL:** `{{url}}/api/sync/sale-offers/add-update-offers`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Тіло запиту (JSON):**  
  Див. [приклад у файлі колекції](https://github.com/RomanStuzhuk/Sync-Api-Sale-Offers/blob/main/SyncAPISaleOffers.postman_collection) (поля `external_id`, `external_url`, `price_before`, переклади, `addresses` тощо).
- **Опис:**  
  Створює новий офер для вказаних адрес.
  
---

### 4. Отримання списку оферів
- **Метод:** `GET`
- **URL:** `{{url}}/api/sync/sale-offers/list-offers`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Тіло запиту (JSON):**  
  Див. [приклад у файлі колекції](https://github.com/RomanStuzhuk/Sync-Api-Sale-Offers/blob/main/SyncAPISaleOffers.postman_collection) (поля `page`, `perPage`).
- **Опис:**  
  ООтримання списку оферів із перекладами та адресами.
  
---

### 5. Видалення офера
- **Метод:** `DELETE`
- **URL:** `{{url}}/api/sync/sale-offers/remove-offer/::external_id`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Опис:**  
  Видаляє офер за його `external_id`.
  
---

### 6. Видалення усіх оферів для магазину
- **Метод:** `DELETE`
- **URL:** `{{url}}/api/sync/sale-offers/remove-all-offers`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Опис:**  
  Видаляє всі офери магазину, додані через API.
  
---

### 7. Додавання адрес до офера
- **Метод:** `POST`
- **URL:** `{{url}}/api/sync/sale-offers/add-addresses/::external_id`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Тіло запиту (JSON):**  
  Див. [приклад у файлі колекції](https://github.com/RomanStuzhuk/Sync-Api-Sale-Offers/blob/main/SyncAPISaleOffers.postman_collection) (поля `city`, `address`).
- **Опис:**  
  Додає адреси до офера, за його `external_id`.
  
---

### 8. Видалення адрес офера
- **Метод:** `DELETE`
- **URL:** `{{url}}/api/sync/sale-offers/remove-addresses/::external_id`
- **Заголовок:**  
  `Authorization: Bearer <JWT_TOKEN>`
- **Тіло запиту (JSON):**  
  Див. [приклад у файлі колекції](https://github.com/RomanStuzhuk/Sync-Api-Sale-Offers/blob/main/SyncAPISaleOffers.postman_collection) (поля `city`, `address`).
- **Опис:**  
  Видаляє вказані адреси для офера, за його `external_id`.
  
---

## Валідація даних

### Загальні правила для оферів
| Поле                     | Тип даних      | Обмеження                                 | Обов'язкове? |
|--------------------------|----------------|-------------------------------------------|--------------|
| `image`                  | Рядок (URL)    | Довжина: 20-512 символів                  | Так          |
| `external_id`            | Рядок          | Довжина: 1-255 символів                   | Так          |
| `external_url`           | Рядок (URL)    | Довжина: 1-255 символів                   | Так          |
| `is_alcohol`             | Логічне        | `true` або `false`                        | Ні           |
| `price_before`           | Число          | Діапазон: 0.00 - 999999.99                | Так          |
| `price_after`            | Число          | Діапазон: 0.00 - 999999.99                | Так          |
| `sale_start_date`        | Рядок          | Формат: `YYYY-MM-DD`                      | Ні           |
| `sale_end_date`          | Рядок          | Формат: `YYYY-MM-DD`                      | Ні           |
| `addresses`              | Масив об’єктів | Мінімум 1 елемент                         | Так          |
| `addresses.*.city`       | Рядок          | Довжина: 2-512 символів                   | Так          |
| `addresses.*.address`    | Рядок          | Довжина: 2-512 символів                   | Так          |

---

### Переклади для оферів (наприклад, `uk`, `ru`)
| Поле          | Тип даних | Обмеження                     | Обов'язкове? |
|---------------|-----------|-------------------------------|--------------|
| `name`        | Рядок     | Максимум 255 символів         | Так          |
| `description` | Рядок     | Максимум 255 символів         | Ні           |

---

### Адреси для додавання та видалення
| `addresses`              | Масив об’єктів | Мінімум 1 елемент                         | Так          |
| `addresses.*.city`       | Рядок          | Довжина: 2-512 символів                   | Так          |
| `addresses.*.address`    | Рядок          | Довжина: 2-512 символів                   | Так          |

---

## Приклади помилок валідації
- Неправильний формат дати: `sale_start_date: "2025/07/01"` (має бути `YYYY-MM-DD`).
- Відсутнє обов’язкове поле: `external_id` не передано.
- Неприпустиме значення: `price_before: "20$"` (має бути числовим значенням без символів).
- Перевищення довжини: `image` посилання довше 512 символів.

## Приклад JSON
```{
    "offers" : [
        {
            "image" : "https://images.silpo.ua/v2/products/1600x1600/webp/7de92507-3a38-4ddd-b14f-be7cb182b15e.png",
            "external_id" : "silpo_test_1",
            "external_url" : "https://silpo.ua/product/avokado-ready-to-eat-787133",
            "is_alcohol" : false,
            "price_before" : 90.99,
            "price_after" : 66.99,
            "sale_start_date" : "2025-08-26",
            "sale_end_date" : "2025-08-31",
            "addresses" : [
                {
                    "city" : "Київ",
                    "address" : "вул. Білоруська, 2"
                },
                {
                    "city" : "Київ",
                    "address" : "вул. Підлісна, 1"
                }
            ],
            "uk" : {
                "name" : "Авокадо Ready to eat, 1шт/уп"
            },
            "ru" : {
                "name" : "Авокадо Ready to eat, 1шт/уп"
            }
        }
    ]
}
