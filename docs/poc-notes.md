# Нотатки PoC: що я побачив у запущеному застосунку

**Де перевіряла:** сторінки **Контакти** (`/contacts`) і **Співпраця** (`/cooperation`) — поведінку форми дивилася на обох.

---

## Проба 1: обов'язковий телефон

**Що змінив:** обов’язковий телефон на клієнті й сервері; `*` у лейблі телефону; переклади `phoneNumberRequired` (uk/en). За diff — також `ContactForm`, `validateContactData`, `useHandlePhoneInput`, `OfferCollaborationForm`, тести.

**Що побачив у браузері:**
- На **Контактах** і **Співпраці** форма **не пропускає незаповнений телефон** (візуально коректно після правок).
- Лейбл телефону з **`*`** (обов’язкове поле).
- *Інші сценарії телефону (пробіли, формат, en/uk тексти окремо) — не зафіксовано.*

**Що показали тести:** `validateContactData.test.ts` — **15/15 PASS** (лог нижче). Раніше в нотатці було «1 провален» — з логом не збігається; фактичний результат прогону — усі пройшли.

```
PS C:\5984\sdd-workshop-olga> npx jest app/lib/utils/validateContactData.test.ts --coverage=false
PASS  app/lib/utils/validateContactData.test.ts
  validateContactData
    √ should return no errors for valid input
    √ should return error if name is missing
    √ should return error if name is too short
    √ should return error if name is too long
    √ should return error if email is missing
    √ should return error if email is invalid
    √ should return error if phone number is missing
    √ should return error if phone number is too short
    √ should return error if phone number is too long
    √ should return error if message is missing
    √ should return error if message is too short
    √ should return error if message is too long
    √ should return multiple errors if all fields are invalid
    should return error if phone number has an incorrect format
      √ returns error for invalid phone: 380123456789
      √ returns error for invalid phone: +38012hello34

Test Suites: 1 passed, 1 total
Tests:       15 passed, 15 total
```

*Окремі прогони `ContactForm.test.tsx` / `useHandlePhoneInput.test.tsx` — не зафіксовано.*

---

## Проба 2: мінімальна довжина повідомлення

**Що змінив:** `MESSAGE_MIN_LENGTH` 10 → 5; `ContactForm` через `lengths`; переклади `messageMinLength`; RULE-19/20 у документації. За власними словами — **більше коригувань у тестах, ніж безпосередньо в коді.**

**Що побачив у браузері** (ті самі сторінки — **Контакти** і **Співпраця**):
- Під час вводу в поле повідомлення з’являється помилка, що **замало символів**.
- При **1 символі** у полі — текст помилки: **`The comment must contain at least 5 characters`** (інтерфейс перевіряла з цим текстом).
- При вводі **пробілу** — **`This field is required`**.
- **Не рахує кількість введених символів** (окремого лічильника / візуального підрахунку символів не бачила; поведінка з пробілом і одним символом — як вище).
- *Чи проходить відправка рівно з 5 символами; uk-текст тієї самої помилки — не зафіксовано.*

**Що показали тести:** повний прогін юніт-тестів utils — **31 suite, 200/200 PASS** (у т.ч. `validateContactData.test.ts`).

```
Test Suites: 31 passed, 31 total
Tests:       200 passed, 200 total
Time:        7.415 s
```

*Детальний лог кожного файлу — у попередній версії нотаток; окремий прогін лише `ContactForm.test.tsx` після зміни мінімуму — не зафіксовано.*