

-Межа системи (Fitness Platform):
    Усередині межі знаходиться все, що ми розробляємо і піднімаємо самі: Frontend, Fitness Backend, Fitness DB та Redis Cache.
    Fitness Backend оформлено як окремий процес, тому всі його мережеві та міжпроцесні виходи йдуть через порти. PostgreSQL і Redis працюють як окремі процеси, тому вони стоять за портами бекенда, але все одно належать нашій системі.
    Зовні (<<external>>) винесено тільки сторонні сервіси, якими ми не керуємо.

-Модулі системи:
    1. Frontend (UI):
        Клієнтська частина в браузері, розбита на 7 окремих сторінок під основні сценарії:
        - Catalog Page — перегляд публічного каталогу і вибір програми.
        - Program Builder Page — створення, редагування, перегляд плану, публікація, архівація та видалення програм.
        - Workout Session Page — проведення тренування, запис підходів, робота з незавершеними сесіями та локальне збереження даних при втраті зв'язку.
        - Progress Page — перегляд динаміки робочої ваги по вправах.
        - History Page — історія завершених тренувань.
        - Profile Page — управління профілем та ролями.
        - Subscription Page — вибір тарифу й оплата підписки (у тому числі на інший номер телефону).
        Усі сторінки напряму ходять на бекенд через інтерфейс REST /api.

    2. Controllers:
        - Router — приймає користувацькі запити з порту HTTP in, на захищені маршрути підключає AuthMiddleware і передає запит у потрібний контролер.
        - AuthMiddleware — перевіряє лише наявність і валідність токена авторизації.
        - AuthController, ProgramController, WorkoutSessionController, AnalyticsController, SubscriptionController — приймають HTTP-запити від клієнта і передають у відповідні сервіси.
        - PaymentWebhookController — приймає асинхронні вебхуки від платіжної системи напряму через окремий порт webhook in в обхід Router і AuthMiddleware, перевіряє криптографічний підпис запиту і передає результат у SubscriptionService.
        - SubscriptionCronJob — фонова задача за розкладом, яка викликає SubscriptionService для переведення прострочених підписок у статуси grace та expired.

    3. Services:
        - AuthService — реєстрація, вхід, робота з ролями.
        - ProgramService — створення, архівація, видалення та публікація програм, перевірка ролі тренера перед публікацією через UserRepository, оновлення кешу каталогу через Cache out і виклик сповіщень.
        - WorkoutSessionService — перед стартом тренування перевіряє статус підписки (active / grace / expired) через SubscriptionService, контролює відсутність паралельних активних сесій, зберігає підходи та неповні тренування.
        - AnalyticsService — підрахунок динаміки робочої ваги на основі історії завершених сесій.
        - SubscriptionService — робота з тарифами та підписками, пошук отримувача за номером телефону, створення оплати через PSP out, активація підписки за вебхуком, обробка grace/expired і виклик NotificationService.
        - NotificationService — формування й відправка сповіщень через зовнішній Push Provider.

    4. Repositories (Data Access):
        Шар доступу до бази (поверх ORM Prisma), усі 4 репозиторії виходять у БД через порт SQL out:
        - UserRepository — User, Athlete, Trainer, Role, User_Roles.
        - ProgramRepository — Program, Program_Item, Workout, Workout_Item, Exercise.
        - WorkoutSessionRepository — Workout_Session, Workout_Session_Exercise, Workout_Session_Set.
        - SubscriptionRepository — Subscription, Tariff.

    5. Сховища даних (всередині системи):
        - Fitness DB (PostgreSQL) — основна база даних для всіх 15 сутностей з ER-моделі.
        - Redis Cache (кеш каталогу) — кеш для швидкого читання публічного каталогу програм, оновлюється з ProgramService через порт Cache out.

-Зовнішні інтеграції (<<external>>):
    - Payment Processor — зовнішній платіжний шлюз. Бекенд через порт PSP out запитує платіжну сторінку, користувач платить на стороні шлюзу, а підтвердження приходить назад на наш порт webhook in.
    - Push Provider — зовнішній сервіс для доставки push-сповіщень на пристрої користувачів (викликається з NotificationService через порт push out).

-Інтерфейси (Provided / Required):
    - REST /api: надає Fitness Backend (порт HTTP in), споживають 7 сторінок Frontend.
    - Webhook: надає Fitness Backend (порт webhook in), споживає Payment Processor.
    - Payments API: надає Payment Processor, споживає Fitness Backend (порт PSP out).
    - Push API: надає Push Provider, споживає Fitness Backend (порт push out).
    - Cache API: надає Redis Cache, споживає Fitness Backend (порт Cache out).
    - SQL: надає Fitness DB, споживає Fitness Backend (порт SQL out).
    Між внутрішніми компонентами бекенда (контролери, сервіси, репозиторії) використовуються звичайні залежності без зайвих інтерфейсів, оскільки це прямі виклики в межах одного процесу.

-Трасування до попередніх моделей:
    - Дані (ER-модель): усі 15 таблиць покриті 4 репозиторіями і зберігаються у Fitness DB.
    - Вимоги та Use Cases: усі сценарії користувачів і бізнес-правила проходять наскрізно від сторінок Frontend через контролери й сервіси до репозиторіїв.
    - Процес (Activity): загальна роль «Система» з Activity-діаграми публікації програми розкладена на конкретні компоненти: ProgramController, ProgramService, UserRepositor,→ ProgramRepository, Fitness DB, Redis Cache, Notification Service.
    - Взаємодія (Sequence): учасники Sequence-діаграми (Frontend, ProgramController, ProgramService, БД) напряму переходять у відповідні компоненти нової схеми, але з уточненням — прямі звернення ProgramService до бази винесені через ProgramRepository та UserRepository.
