# ai-automation-studio
Modular n8n and AI automation platform for Polish SMBs with reusable workflows, CRM integrations, notifications, logging and reliability controls.
|

AI Automation Studio
Product, Business and Technical Overview · English / Polski / Українська
Version 1.0 · 17 July 2026

ENGLISH
1. Executive overview
AI Automation Studio designs and delivers modular n8n and AI automations for small and medium-sized businesses in Poland. The studio is not tied to one industry, one CRM or one workflow. Its product is a reusable automation platform: a shared technical core is configured for each client and combined with process-specific modules.
The business value is practical rather than experimental. The system should reduce manual data entry, shorten response time, prevent missed follow-ups, improve process visibility and produce reliable operational data. AI is used only where interpretation is needed; deterministic rules remain responsible for decisions that must be predictable, auditable and safe.

2. Mission and objectives
Mission: make useful business automation accessible to Polish SMBs without forcing them to buy a large enterprise platform or rebuild their entire operating model.
Primary objectives:
• turn recurring manual work into repeatable digital processes;
• connect fragmented forms, email, spreadsheets, calendars, CRM and databases;
• create a reusable product instead of a collection of unrelated custom workflows;
• deliver measurable outcomes in time, conversion, error rate, service quality or margin;
• create recurring revenue through implementation, maintenance and support;
• keep data ownership, operating cost and vendor dependence under control.

3. Target clients and operating principles
The initial market is private SMBs in Poland, especially companies processing at least dozens of digital requests, bookings, leads or orders per month. Priority goes to workflows with a clear trigger, repeated steps, measurable delay or error cost, and an identifiable process owner.
Operating principles:
• configure before custom-building;
• use one canonical event format across modules;
• prefer rules before AI when the rule is sufficient;
• keep a human approval step for financial, legal, sensitive or irreversible actions;
• start with free, open-source or local components when they satisfy requirements;
• collect the minimum necessary data and define retention periods;
• design every workflow for retry, duplicate protection, logging and recovery.

4. MVP product scope
The MVP consists of one shared core and three demonstrator modules.
Shared core:
• universal inputs: webhook, form, scheduled import, email or API adapter;
• normalization to a canonical event schema;
• client configuration and tenant-specific settings;
• deterministic rules and optional AI classification or extraction;
• CRM/database read and write layer;
• notification layer for email, messaging or internal alerts;
• structured logs, error handling and operational status;
• retry policy, idempotency keys and deduplication.
Start modules:
1. Booking and reminders — receive a booking request, validate it, create or update the record, schedule reminders and record delivery status.
2. Lead and CRM — capture a lead, normalize and enrich fields, classify urgency or intent, create the CRM record and assign the next action.
3. Order and reporting — receive an order or transaction event, validate and store it, update status and generate an operational summary.
Additional connectors, industry-specific features, complex two-way synchronization, voice agents, payments and autonomous high-risk decisions remain in the backlog until the core has been validated.

5. End-to-end processing model
Every automation follows the same logical path:
1. Receive an event and assign event_id, tenant_id, source and timestamp.
2. Validate authentication, required fields and payload size.
3. Normalize source-specific data into the canonical schema.
4. Check the idempotency key and reject or merge duplicates.
5. Load the client configuration, routing rules, templates and enabled modules.
6. Apply deterministic rules; call an AI model only for an explicitly allowed task.
7. Execute the selected business module.
8. Write the result to the CRM or database through a common data layer.
9. Send approved notifications.
10. Record status, timing, decision path and errors in structured logs.
11. Retry transient failures with limits; send permanent failures to a review queue.

6. Technical architecture
Orchestration layer: self-hosted n8n is the primary workflow engine. Workflows should be split into reusable sub-workflows: intake, normalization, configuration loading, routing, persistence, notifications and error handling. Business modules call these shared components rather than copying their logic.
Runtime and deployment: use containers and Docker Compose for a small initial deployment. Separate development and production configuration, pin tested component versions, store secrets outside workflow JSON and expose n8n only through HTTPS behind a reverse proxy.
Database: PostgreSQL is the system of record for tenants, events, idempotency keys, business entities, workflow states, notification attempts and audit logs. External CRM systems are adapters, not the only source of operational truth.
AI layer: define a provider-neutral interface. A local model served through Ollama may be used for suitable classification, extraction or drafting tasks. A cloud model can be an optional fallback when quality requirements justify it. Prompts, model settings and response schemas must be versioned. AI output must be validated before it affects downstream systems.
Queue and cache: start without extra infrastructure if load is low. Add Redis or a dedicated queue only when concurrency, rate limiting or workload isolation requires it.
Storage and reporting: keep structured business data in PostgreSQL. Store large files in a dedicated object store or the client’s approved Drive. Use database views and a lightweight reporting layer for operational dashboards.

7. Canonical data contracts
The core contract should include: event_id, tenant_id, event_type, source, occurred_at, received_at, actor, subject, contact, payload, attachments, consent context, correlation_id, idempotency_key, processing_status and schema_version. Each block must declare its input, output, possible statuses, retry behavior and failure ownership.
Statuses should be small and consistent: received, validated, normalized, routed, processing, waiting, completed, failed_retryable, failed_permanent and manual_review. Schema changes require versioning and backward compatibility rules.

8. Reliability and observability
Reliability is part of the product, not a later enhancement. Every inbound event receives an idempotency key. Writes use upsert or guarded transactions. Retries use bounded exponential backoff and distinguish temporary failures from invalid data. A dead-letter or manual-review queue retains unresolved failures.
Logs must be structured and searchable by tenant_id, event_id, workflow, module and correlation_id. Track execution count, success rate, failure rate, retry count, processing time, duplicate rate, notification delivery and manual interventions. Alerts should focus on actionable conditions: repeated failures, queue growth, authentication expiry or missing expected events.

9. Security, privacy and governance
Apply least privilege to credentials and database roles. Separate client data logically by tenant_id and, where risk requires it, physically. Encrypt traffic with TLS, protect backups, rotate secrets and maintain an access inventory. Do not place credentials or unnecessary personal data in logs.
For personal data, document purpose, legal basis, data categories, recipients, retention and deletion. Keep a human approval gate for outreach, financial changes, legal commitments, account deletion and other high-impact actions. AI must not independently make sensitive or irreversible decisions.

10. Delivery plan
Phase 1 — specification: define the canonical event, block contracts, tenant configuration, statuses, error policy and acceptance tests.
Phase 2 — shared core: implement intake, normalization, configuration, routing, persistence, notifications, logs, retry, idempotency and deduplication.
Phase 3 — demonstrators: build Booking/Reminders, Lead/CRM and Order/Reporting using only reusable core blocks plus thin adapters.
Phase 4 — quality: test happy paths, invalid input, duplicate events, timeouts, rate limits, partial failure, credential expiry and recovery.
Phase 5 — pilot: configure one real client process, establish a baseline, run supervised production and measure results.
Phase 6 — productization: document installation, configuration, monitoring, backup, incident response and module onboarding; move non-standard requests to backlog.

11. Definition of success
A module is accepted when it uses the shared contracts, has no uncontrolled duplicate effects, produces traceable status and errors, passes recovery tests, and has a measurable business KPI. The studio succeeds when a new client automation can be assembled mainly from existing blocks and configuration, with custom work limited to a thin adapter or a clearly justified backlog item.

12. Technical references
n8n hosting documentation: https://docs.n8n.io/hosting/
Docker Compose documentation: https://docs.docker.com/compose/
PostgreSQL documentation: https://www.postgresql.org/docs/current/
Ollama documentation: https://docs.ollama.com/

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

POLSKI
1. Podsumowanie
AI Automation Studio projektuje i wdraża modułowe automatyzacje n8n/AI dla małych i średnich firm w Polsce. Studio nie jest związane z jedną branżą, jednym CRM-em ani jednym procesem. Produktem jest wielokrotnego użytku platforma automatyzacji: wspólny rdzeń techniczny konfiguruje się dla klienta i łączy z modułami procesowymi.
Wartość biznesowa ma być praktyczna: mniej ręcznego przepisywania danych, krótszy czas reakcji, mniej utraconych follow-upów, lepsza widoczność procesu oraz wiarygodne dane operacyjne. AI jest używana tylko wtedy, gdy potrzebna jest interpretacja; decyzje wymagające przewidywalności, audytowalności i bezpieczeństwa pozostają oparte na regułach.

2. Misja i cele
Misja: udostępnić użyteczną automatyzację polskim MŚP bez konieczności kupowania dużej platformy enterprise lub przebudowy całego modelu operacyjnego.
Główne cele:
• zamiana powtarzalnej pracy ręcznej w powtarzalne procesy cyfrowe;
• połączenie formularzy, e-maila, arkuszy, kalendarzy, CRM i baz danych;
• budowa produktu wielokrotnego użytku zamiast zbioru niezależnych workflow;
• dostarczanie mierzalnych efektów w czasie, konwersji, liczbie błędów, jakości obsługi lub marży;
• rozwój przychodu cyklicznego z wdrożenia, utrzymania i wsparcia;
• kontrola własności danych, kosztów operacyjnych i zależności od dostawców.

3. Klienci docelowi i zasady działania
Pierwszym rynkiem są prywatne MŚP w Polsce, szczególnie firmy obsługujące co najmniej kilkadziesiąt cyfrowych zapytań, rezerwacji, leadów lub zamówień miesięcznie. Priorytet mają procesy z jasnym wyzwalaczem, powtarzalnymi krokami, mierzalnym kosztem opóźnienia lub błędu i wskazanym właścicielem procesu.
Zasady:
• najpierw konfiguracja, dopiero potem kod lub specjalna logika;
• jeden kanoniczny format zdarzenia dla wszystkich modułów;
• reguły przed AI, jeżeli reguła wystarcza;
• akceptacja człowieka dla działań finansowych, prawnych, wrażliwych lub nieodwracalnych;
• bezpłatne, open-source albo lokalne komponenty, gdy spełniają wymagania;
• minimalizacja danych i określone okresy retencji;
• retry, ochrona przed duplikatami, logowanie i odtwarzanie jako element każdego workflow.

4. Zakres produktu MVP
MVP składa się ze wspólnego rdzenia i trzech modułów demonstracyjnych.
Wspólny rdzeń:
• uniwersalne wejścia: webhook, formularz, import cykliczny, e-mail lub adapter API;
• normalizacja do kanonicznego schematu zdarzenia;
• konfiguracja klienta i ustawienia tenant-specific;
• reguły deterministyczne oraz opcjonalna klasyfikacja lub ekstrakcja AI;
• wspólna warstwa odczytu i zapisu CRM/bazy danych;
• powiadomienia e-mail, komunikatory i alerty wewnętrzne;
• ustrukturyzowane logi, obsługa błędów i status operacyjny;
• polityka retry, klucze idempotency i deduplikacja.
Moduły startowe:
1. Booking/Reminders — przyjęcie rezerwacji, walidacja, zapis lub aktualizacja rekordu, planowanie przypomnień i zapis statusu dostarczenia.
2. Lead/CRM — przyjęcie leada, normalizacja i wzbogacenie pól, klasyfikacja intencji lub pilności, utworzenie rekordu CRM i przypisanie następnego kroku.
3. Order/Reporting — przyjęcie zamówienia lub transakcji, walidacja, zapis, aktualizacja statusu i raport operacyjny.
Pozostałe konektory, funkcje branżowe, złożona synchronizacja dwukierunkowa, voice agents, płatności i autonomiczne decyzje wysokiego ryzyka pozostają w backlogu do czasu walidacji rdzenia.

5. Model przetwarzania end-to-end
Każda automatyzacja realizuje tę samą ścieżkę:
1. Odbiera zdarzenie i nadaje event_id, tenant_id, źródło i czas.
2. Sprawdza uwierzytelnienie, pola wymagane i rozmiar danych.
3. Normalizuje dane źródłowe do schematu kanonicznego.
4. Sprawdza idempotency key oraz odrzuca lub scala duplikaty.
5. Ładuje konfigurację klienta, reguły routingu, szablony i aktywne moduły.
6. Stosuje reguły; wywołuje AI tylko dla jawnie dozwolonego zadania.
7. Uruchamia wybrany moduł biznesowy.
8. Zapisuje wynik w CRM lub bazie przez wspólną warstwę danych.
9. Wysyła zatwierdzone powiadomienia.
10. Zapisuje status, czas, ścieżkę decyzji i błędy.
11. Ponawia błędy przejściowe w ograniczony sposób, a trwałe kieruje do ręcznej weryfikacji.

6. Architektura techniczna
Orkiestracja: self-hosted n8n jako główny silnik workflow. Procesy należy dzielić na wspólne sub-workflows: intake, normalizacja, konfiguracja, routing, zapis, powiadomienia i obsługa błędów. Moduły biznesowe wywołują wspólne elementy, zamiast kopiować ich logikę.
Uruchomienie: kontenery i Docker Compose dla małego pierwszego środowiska. Oddzielna konfiguracja development/production, przypięte i przetestowane wersje, sekrety poza JSON-em workflow oraz dostęp do n8n wyłącznie przez HTTPS i reverse proxy.
Baza danych: PostgreSQL jako system of record dla klientów, zdarzeń, kluczy idempotency, obiektów biznesowych, stanów workflow, prób powiadomień i logów audytowych. Zewnętrzne CRM-y są adapterami, a nie jedynym źródłem prawdy operacyjnej.
Warstwa AI: interfejs niezależny od dostawcy. Lokalny model przez Ollama może obsługiwać odpowiednie zadania klasyfikacji, ekstrakcji lub tworzenia draftów. Model chmurowy może być opcjonalnym fallbackiem, jeśli wymagania jakościowe to uzasadniają. Prompty, ustawienia modeli i schemat odpowiedzi muszą być wersjonowane, a wynik AI walidowany.
Kolejka i cache: na początku bez dodatkowej infrastruktury, jeśli obciążenie jest małe. Redis lub osobna kolejka dopiero wtedy, gdy wymagają tego współbieżność, rate limiting albo izolacja obciążeń.
Dane i raportowanie: dane strukturalne w PostgreSQL, większe pliki w zatwierdzonym storage lub Drive klienta, raporty z widoków bazodanowych i lekkiej warstwy dashboardów.

7. Kanoniczne kontrakty danych
Kontrakt rdzenia powinien obejmować: event_id, tenant_id, event_type, source, occurred_at, received_at, actor, subject, contact, payload, attachments, consent context, correlation_id, idempotency_key, processing_status i schema_version. Każdy blok deklaruje wejście, wyjście, statusy, zasady retry i odpowiedzialność za błąd.
Wspólne statusy: received, validated, normalized, routed, processing, waiting, completed, failed_retryable, failed_permanent i manual_review. Zmiana schematu wymaga wersjonowania i zasad kompatybilności wstecznej.

8. Niezawodność i obserwowalność
Każde zdarzenie otrzymuje klucz idempotency. Zapisy wykorzystują upsert albo chronione transakcje. Retry stosuje ograniczony exponential backoff i odróżnia błąd przejściowy od niepoprawnych danych. Nierozwiązane przypadki trafiają do dead-letter lub manual-review queue.
Logi są strukturalne i wyszukiwalne po tenant_id, event_id, workflow, module i correlation_id. Mierzymy liczbę wykonań, success rate, failure rate, retry, czas przetwarzania, duplikaty, dostarczenie powiadomień i ręczne interwencje. Alerty dotyczą warunków wymagających działania: powtarzalnych błędów, wzrostu kolejki, wygaśnięcia autoryzacji lub braku oczekiwanych zdarzeń.

9. Bezpieczeństwo, prywatność i nadzór
Uprawnienia i role bazodanowe zgodnie z zasadą least privilege. Logiczna separacja klientów przez tenant_id, a przy wyższym ryzyku również separacja fizyczna. TLS dla transmisji, chronione kopie zapasowe, rotacja sekretów i ewidencja dostępu. Bez haseł i zbędnych danych osobowych w logach.
Dla danych osobowych należy opisać cel, podstawę prawną, kategorie danych, odbiorców, retencję i usuwanie. Człowiek zatwierdza outreach, zmiany finansowe, zobowiązania prawne, usunięcie konta i inne działania o wysokim wpływie. AI nie podejmuje samodzielnie decyzji wrażliwych ani nieodwracalnych.

10. Plan realizacji
Etap 1 — specyfikacja: zdarzenie kanoniczne, kontrakty bloków, konfiguracja klienta, statusy, polityka błędów i testy akceptacyjne.
Etap 2 — rdzeń: intake, normalizacja, konfiguracja, routing, zapis, powiadomienia, logi, retry, idempotency i deduplikacja.
Etap 3 — demonstratory: Booking/Reminders, Lead/CRM oraz Order/Reporting zbudowane ze wspólnych bloków i cienkich adapterów.
Etap 4 — jakość: testy poprawnego przebiegu, błędnych danych, duplikatów, timeoutów, limitów API, częściowych awarii, wygaśnięcia credentiali i recovery.
Etap 5 — pilot: konfiguracja jednego realnego procesu klienta, baseline, nadzorowana produkcja i pomiar efektu.
Etap 6 — produkt: dokumentacja instalacji, konfiguracji, monitoringu, backupu, incident response i dodawania modułów; niestandardowe wymagania trafiają do backlogu.

11. Kryterium sukcesu
Moduł jest gotowy, gdy używa wspólnych kontraktów, nie powoduje niekontrolowanych duplikatów, ma śledzalne statusy i błędy, przechodzi testy recovery oraz posiada mierzalny KPI biznesowy. Studio osiąga cel, gdy nową automatyzację klienta można złożyć głównie z istniejących bloków i konfiguracji, a praca niestandardowa ogranicza się do cienkiego adaptera lub uzasadnionego elementu backlogu.

12. Źródła techniczne
Dokumentacja hostingu n8n: https://docs.n8n.io/hosting/
Docker Compose: https://docs.docker.com/compose/
PostgreSQL: https://www.postgresql.org/docs/current/
Ollama: https://docs.ollama.com/

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

УКРАЇНСЬКА
1. Загальний опис
AI Automation Studio проєктує та впроваджує модульні n8n/AI-автоматизації для малого й середнього бізнесу в Польщі. Студія не прив’язана до однієї галузі, CRM або конкретного workflow. Її продукт — повторно використовувана платформа автоматизації: спільне технічне ядро конфігурується для кожного клієнта й доповнюється процесними модулями.
Бізнес-цінність має бути практичною: менше ручного перенесення даних, швидша реакція, менше втрачених follow-up, краща видимість процесу та надійні операційні дані. AI використовується лише там, де потрібна інтерпретація; передбачувані, контрольовані й чутливі рішення залишаються за детермінованими правилами та людиною.

2. Місія та цілі
Місія: зробити корисну бізнес-автоматизацію доступною польським SMB без придбання великої enterprise-платформи або перебудови всієї операційної моделі.
Головні цілі:
• перетворювати повторювану ручну роботу на стабільні цифрові процеси;
• з’єднувати розрізнені форми, пошту, таблиці, календарі, CRM і БД;
• створювати повторно використовуваний продукт, а не набір незалежних workflow;
• давати вимірюваний результат у часі, конверсії, помилках, якості сервісу або маржі;
• формувати повторний дохід від впровадження, підтримки й обслуговування;
• контролювати власність даних, операційні витрати та vendor lock-in.

3. Цільові клієнти та принципи
Перший ринок — приватні SMB у Польщі, особливо компанії, які щомісяця обробляють щонайменше десятки цифрових звернень, бронювань, лідів або замовлень. Пріоритет мають процеси з чітким тригером, повторюваними кроками, вимірюваною ціною затримки чи помилки та визначеним власником процесу.
Принципи:
• спочатку конфігурація, потім кастомна розробка;
• один канонічний формат події для всіх модулів;
• rules before AI, якщо правила достатньо;
• людське підтвердження для фінансових, юридичних, чутливих і незворотних дій;
• безплатні, open-source або локальні компоненти, якщо вони закривають вимоги;
• мінімізація даних і визначені строки зберігання;
• retry, захист від дублів, логи й відновлення в кожному workflow.

4. Межі MVP
MVP складається зі спільного ядра та трьох демонстраційних модулів.
Спільне ядро:
• універсальні входи: webhook, форма, плановий імпорт, email або API-адаптер;
• нормалізація до канонічної схеми події;
• конфігурація клієнта й tenant-specific параметри;
• детерміновані правила та опційна AI-класифікація або extraction;
• спільний шар читання й запису CRM/БД;
• email, месенджери та внутрішні сповіщення;
• структуровані логи, помилки й операційні статуси;
• retry policy, idempotency keys і deduplication.
Стартові модулі:
1. Booking/Reminders — прийняти бронювання, перевірити, створити або оновити запис, запланувати нагадування й записати статус доставки.
2. Lead/CRM — прийняти лід, нормалізувати й доповнити поля, визначити намір або терміновість, створити запис CRM і наступну дію.
3. Order/Reporting — прийняти замовлення або транзакцію, перевірити й записати, оновити статус і створити операційний звіт.
Інші конектори, галузеві функції, складна двостороння синхронізація, voice agents, платежі та автономні рішення високого ризику залишаються в backlog до валідації ядра.

5. Наскрізна модель обробки
Кожна автоматизація проходить однаковий шлях:
1. Приймає подію та призначає event_id, tenant_id, source і timestamp.
2. Перевіряє автентифікацію, обов’язкові поля й розмір payload.
3. Нормалізує дані джерела в канонічну схему.
4. Перевіряє idempotency key і відхиляє або об’єднує дублікати.
5. Завантажує конфігурацію клієнта, routing rules, шаблони й активні модулі.
6. Застосовує правила; викликає AI лише для явно дозволеної задачі.
7. Виконує потрібний бізнес-модуль.
8. Записує результат у CRM або БД через спільний data layer.
9. Надсилає дозволені повідомлення.
10. Записує статус, час, шлях рішення та помилки.
11. Повторює тимчасові збої в межах ліміту, постійні передає на ручний розгляд.

6. Технічна архітектура
Оркестрація: self-hosted n8n як основний workflow engine. Процеси поділяються на повторно використовувані sub-workflows: intake, normalization, configuration loading, routing, persistence, notifications та error handling. Бізнес-модулі викликають спільні блоки, а не копіюють їхню логіку.
Запуск: контейнери й Docker Compose для невеликого стартового середовища. Окремі development і production конфігурації, зафіксовані перевірені версії, секрети поза workflow JSON, доступ до n8n лише через HTTPS і reverse proxy.
База даних: PostgreSQL як system of record для клієнтів, подій, idempotency keys, бізнес-сутностей, станів workflow, спроб повідомлень і audit logs. Зовнішні CRM — адаптери, а не єдине джерело операційної правди.
AI-шар: незалежний від провайдера інтерфейс. Локальна модель через Ollama може виконувати доречні задачі класифікації, extraction або створення drafts. Хмарна модель — опційний fallback, якщо це виправдано якістю. Prompts, model settings і response schemas версіонуються; AI-вихід обов’язково валідується.
Черга й кеш: старт без зайвої інфраструктури при низькому навантаженні. Redis або окрема queue додаються лише коли цього потребують concurrency, rate limiting або ізоляція навантаження.
Дані та звіти: структуровані дані в PostgreSQL, великі файли в погодженому object storage або Drive клієнта, операційні дашборди — на основі DB views і легкого reporting layer.

7. Канонічні контракти даних
Контракт ядра має містити: event_id, tenant_id, event_type, source, occurred_at, received_at, actor, subject, contact, payload, attachments, consent context, correlation_id, idempotency_key, processing_status і schema_version. Кожен блок декларує вхід, вихід, статуси, retry behavior і відповідальність за помилку.
Спільні статуси: received, validated, normalized, routed, processing, waiting, completed, failed_retryable, failed_permanent і manual_review. Зміни схеми потребують версіонування та правил backward compatibility.

8. Надійність і спостережуваність
Кожна подія отримує idempotency key. Запис використовує upsert або захищені транзакції. Retry працює з обмеженим exponential backoff і відрізняє тимчасовий збій від невалідних даних. Невирішені випадки переходять у dead-letter або manual-review queue.
Логи структуруються та шукаються за tenant_id, event_id, workflow, module і correlation_id. Вимірюються кількість запусків, success rate, failure rate, retry count, processing time, duplicate rate, доставка повідомлень і ручні втручання. Алерти мають сигналізувати лише про дієві проблеми: повторні збої, ріст черги, завершення авторизації або відсутність очікуваних подій.

9. Безпека, приватність і контроль
Принцип least privilege для credentials і ролей БД. Логічне розділення клієнтів через tenant_id, а за вищого ризику — фізичне. TLS для трафіку, захищені backups, ротація секретів та облік доступу. Credentials і зайві персональні дані не потрапляють у логи.
Для персональних даних документуються мета, правова підстава, категорії, отримувачі, retention і видалення. Людина підтверджує outreach, фінансові зміни, юридичні зобов’язання, видалення облікового запису та інші high-impact дії. AI не приймає самостійно чутливих або незворотних рішень.

10. План створення
Етап 1 — специфікація: канонічна подія, контракти блоків, конфігурація клієнта, статуси, error policy та acceptance tests.
Етап 2 — ядро: intake, normalization, configuration, routing, persistence, notifications, logs, retry, idempotency і deduplication.
Етап 3 — демонстратори: Booking/Reminders, Lead/CRM і Order/Reporting зі спільних блоків і тонких адаптерів.
Етап 4 — якість: happy path, невалідні дані, дублі, timeouts, API limits, partial failure, credential expiry і recovery.
Етап 5 — пілот: конфігурація одного реального процесу, baseline, контрольований production і вимірювання результату.
Етап 6 — продукт: документація інсталяції, конфігурації, моніторингу, backup, incident response і додавання модулів; нестандартні вимоги переходять у backlog.

11. Критерій успіху
Модуль готовий, коли використовує спільні контракти, не створює неконтрольованих дублів, має простежувані статуси й помилки, проходить recovery tests і має вимірюваний бізнес-KPI. Студія досягає мети, коли нову клієнтську автоматизацію можна скласти переважно з наявних блоків і конфігурації, а кастомна робота обмежується тонким адаптером або обґрунтованим backlog item.

12. Технічні джерела
Документація хостингу n8n: https://docs.n8n.io/hosting/
Docker Compose: https://docs.docker.com/compose/
PostgreSQL: https://www.postgresql.org/docs/current/
Ollama: https://docs.ollama.com/

