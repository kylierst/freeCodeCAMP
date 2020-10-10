# Операции разработчиков на freeCodeCamp.org

Это руководство поможет вам понять наш стек инфраструктуры и как мы поддерживаем наши платформы. Хотя это руководство не имеет исчерпывающих деталей для всех операций, оно может быть использовано в качестве справочной информации для вашего понимания систем.

Дайте нам знать, если у вас есть обратная связь или запросы, и мы будем рады уточнить.

## Как мы собираем, тестируем и развертываем кодовую базу?

Этот репозиторий постоянно строится, тестируется и развернут в **отдельных наборов инфраструктуры (серверов, баз данных, CDN и т.д.)**.

Это включает в себя три шага, которые должны выполняться последовательно:

1. Новые изменения (как исправления, так и функции) объединены в нашу ветку основного развития (`master`) с помощью запросов на слияние.
2. Эти изменения проводятся через серию автоматизированных тестов.
3. После прохождения тестов мы выпускаем изменения (или их обновить) для развертывания в нашей инфраструктуре.

#### Building the codebase - Mapping Git Branches to Deployments.

Обычно [`master`](https://github.com/freeCodeCamp/freeCodeCamp/tree/master) (ветка разработки по умолчанию) объединяется в ветку [`стадии производства`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging) раз в день и отпускается в изолированную инфраструктуру.

Это промежуточный релиз для наших разработчиков и добровольцев. Он также известен как наш "staging" или "beta" релиз.

Это идентично нашей живой среде производства на `freeCodeCamp.org`, за исключением того, что он использует отдельные базы данных, серверы, веб-прокси и т.д. Эта изоляция позволяет нам тестировать текущие разработки и возможности в "производстве", как сценарий, не затрагивая обычных пользователей платформ freeCodeCamp.org. Эта изоляция позволяет нам тестировать текущие разработки и возможности в "производстве", как сценарий, не затрагивая обычных пользователей платформ freeCodeCamp.org.

После того, как команда разработчиков [`@freeCodeCamp/dev-team`](https://github.com/orgs/freeCodeCamp/teams/dev-team/members) доволена изменениями на стартовой платформе, эти изменения перемещаются каждые несколько дней в [`производственно-текущую ветвь`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-current) ветку.

Это окончательный релиз, который перемещает изменения в наши производственные платформы на freeCodeCamp.org.

#### Тестирование изменений - Интеграция и Проверка приемки пользователей.

Мы используем различные уровни интеграции и приемки для проверки качества кода. Все наши тесты выполняются с помощью таких программ, как [Travis CI](https://travis-ci.org/freeCodeCamp/freeCodeCamp) и [Трубопроводов Лазурного берега](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp).

У нас есть модульные тесты для тестирования наших решений, серверных API и пользовательских интерфейсов клиентов. Это помогает нам протестировать интеграцию между различными компонентами.

> [!ПРИМЕЧАНИЕ] Мы также находимся в процессе написания тестов конечных пользователей, которые помогут воспроизвести сценарии реального мира, такие как обновление электронной почты или вызов API или сторонних сервисов.

Вместе эти тесты помогают избежать повторения проблем и убедиться, что мы не вводим ошибку во время работы над другой ошибкой или функцией.

#### Развертывание изменений - Передача изменений серверам.

Мы настроили программное обеспечение для непрерывной доставки, чтобы изменения в наших разработках и производственных серверах.

После того, как изменения вносятся в защищенные ветки релиза, для ветки автоматически запускается трубопровод сборки. Строительные трубопроводы отвечают за строительство артефактов и их хранение в холодное хранилище для последующего использования.

Спусковая линия сборки продолжает запускать соответствующий релиз трубопровода, если завершит успешный запуск. Релизующие трубопроводы отвечают за сбор артефактов, их перемещение на серверы и проживание.

Статус сборок и релизов [доступен здесь](#build-test-and-deployment-status).

## Запуск сборки, теста и развертывания.

В настоящее время только члены команды разработчиков могут толкнуть в производственные ветви. Изменения в `продукции-*` ветках могут высадиться только быстрым прямым слиянием с [`выше`](https://github.com/freeCodeCamp/freeCodeCamp).

> [!ПРИМЕЧАНИЕ] В предстоящие дни мы улучшили бы этот поток по запросам для улучшения управления доступом и прозрачности.

### Отправка изменений в отладочные приложения.

1. Настройте ваши пульты правильно.

   ```sh
   git удалённый -v
   ```

   **Результаты:**

   ```
   origin git@github.com:raisedadead/freeCodeCamp.git (fetch)
   происхождение git@github.com:raisedadead/freeCodeCamp.git (push)
   upstream git@github.com:freeCodeCamp/freeCodeCamp.git (fetch)
   upstream git@github.com:freeCodeCamp/freeCodeCamp.git (push)
   ```

2. Убедитесь, что ваша ветка `мастер-` нетронута и синхронизирована с исходным потоком.

   ```sh
   git checkout master
   git fetch --all --prune
   git reset --hard upstream/master
   ```

3. Проверьте, что CI Travis проходит на `главной ветке` для воспроизведения.

   Тесты [непрерывной интеграции](https://travis-ci.com/github/freeCodeCamp/freeCodeCamp/branches) должны быть зеленым и PASSING для `главной ветки`.

    <details> <summary> Проверка статуса на Travis CI (скриншот) </summary>
      <br>
      ![Проверить статус сборки на Travis CI](https://raw.githubusercontent.com/freeCodeCamp/freeCodeCamp/master/docs/images/devops/travis-build.png)
    </details>

   Если это не удается, вы должны прекратить и расследовать ошибки.

4. Подтвердите, что вы можете построить репозиторий локально.

   ```
   npm запуск чисто-и-разработка
   ```

5. Переместить изменения с `мастер-` на `стадии производства` через быстрое прямое слияние

   ```
   git checkout production-staging
   git merge master
   git push upstream
   ```

   > [!ПРИМЕЧАНИЕ] Вы не сможете принудительно выполнить push и если вы переписали историю в любом случае, эти команды будут ошибки.
   > 
   > Если они сделали, возможно, что-то сделал неправильно, и вы должны просто начать заново.

Вышеперечисленные шаги автоматически срабатывают на сборочной линии сборки для `производственной стадии` ветки. После завершения сборки артефакты сохраняются как `.zip` файлы в холодном хранилище для извлечения и использования позже.

Спутниковый трубопровод запускается автоматически при наличии свежего артефакта из подключенного трубопровода сборки. Для разбивки платформ, этот процесс не требует ручного одобрения и артефакты толкаются на серверы CDN и API.

> [!TIP|label:Estimates] Обычно запуск сборки занимает ~20-25 минут, после чего выполняется релиз, который занимает ~15-20 минут для клиента, и ~5-10 минут для доступности API. От кода, до того, как он живет на платформах, весь процесс занимает **~35-45 минут** в общей сложности.

### Отправка изменений в Производственные Прикладки.

Этот процесс в основном такой же, как и на постановке платформ, с несколькими дополнительными проверками. Это просто убедиться, что мы ничего не сломаем на freeCodeCamp.org , который может видеть сотни пользователей, использующих его в любой момент.

| НЕ выполняйте эти команды, если вы не подтвердили, что все работает на промежуточной платформе. Вы не должны пропускать или пропускать любые тесты при постановке, прежде чем продолжить. |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                                                                                                                                                           |


1. Убедитесь, что ваша ветка `в стадии производства` нетронута и синхронизирована с исходным потоком.

   ```sh
   git checkout production-staging
   git fetch --all --prune
   git reset --hard upstream/production-staging
   ```

2. Переместить изменения с `production-staging` на `production-current` через быстрое прямое слияние

   ```
   git checkout production-current
   git merge production-staging
   git push upstream
   ```

   > [!ПРИМЕЧАНИЕ] Вы не сможете принудительно выполнить push и если вы переписали историю в любом случае, эти команды будут ошибки.
   > 
   > Если они сделали, возможно, что-то сделал неправильно, и вы должны просто начать заново.

Вышеперечисленные шаги автоматически срабатывают на сборочной линии для `production-current` ветки. Как только артефакт сборки будет готов, он вызовет запуск на трубопроводе выпуска.

> [!TIP|label:Estimates] Обычно запуск сборки занимает ~20-25 минут.

**Дополнительные шаги для действий персонала**

Один из запущенных релизов срабатывает, члены команды разработчиков получат автоматизированную электронную почту ручного вмешательства. Они могут либо _одобрить_ или _отклонить_ релиз.

Если изменения работают хорошо и были протестированы на поэтапной платформе, то это может быть утверждено. Одобрение должно быть выдано в течение 4 часов после срабатывания релиза до его автоматического отказа. Персонал может запустить выпуск вручную для отклонённых запусков или ожидать следующего цикла выпуска.

Для сотрудников:

| Проверьте вашу электронную почту для прямой ссылки или [перейдите на панель управления релиза](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_release) после запуска сборки. |
|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|                                                                                                                                                                                    |


Как только один из сотрудников одобрит релиз, конвейер изменит текущие изменения на бесплатные серверы CDN и API. Они обычно занимают около 15-20 минут для клиента, и ~5 минут для доступных серверов API.

> [!TIP|label:Estimates] Запуск релиза обычно занимает ~15-20 минут для каждого экземпляра клиента и ~5-10 минут для каждого экземпляра API, который будет доступен в реальном времени. От кода, чтобы жить на производственных платформах, весь процесс занимает **~90-120 минут** в общей сложности (не считая время ожидания для утверждения персонала).

## Состояние построенного, теста и развертывания

Вот текущий тест, построение и развертывание в базе кода.

| Тип                 | Ветка                                                                                           | Статус                                                                                                                                                                                                                                               | Панель                                                                                        |
|:------------------- |:----------------------------------------------------------------------------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:--------------------------------------------------------------------------------------------- |
| Тест CI             | [`мастер`](https://github.com/freeCodeCamp/freeCodeCamp/tree/master)                            | ![Статус сборки Travis CI](https://travis-ci.com/freeCodeCamp/freeCodeCamp.svg?branch=master)                                                                                                                                                        | [Перейти к панели состояния](https://travis-ci.com/github/freeCodeCamp/freeCodeCamp/branches) |
| Тест CI             | [`производственный этап`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging) | ![Статус сборки Travis CI](https://travis-ci.com/freeCodeCamp/freeCodeCamp.svg?branch=production-staging)                                                                                                                                            | [Перейти к панели состояния](https://travis-ci.com/github/freeCodeCamp/freeCodeCamp/branches) |
| Собрать Трубопровод | [`производственный этап`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging) | [![Статус сборки](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_apis/build/status/dot-dev-ci?branchName=production-staging)](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_build/latest?definitionId=15&branchName=production-staging) | [Перейти к панели состояния](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_build)      |
| Релиз Трубопровода  | [`производственный этап`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging) |                                                                                                                                                                                                                                                      | [Перейти к панели состояния](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_release)    |
| Тест CI             | [`текущее производство`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-current)  | ![Статус сборки Travis CI](https://travis-ci.com/freeCodeCamp/freeCodeCamp.svg?branch=production-current)                                                                                                                                            | [Перейти к панели состояния](https://travis-ci.com/github/freeCodeCamp/freeCodeCamp/branches) |
| Собрать Трубопровод | [`текущее производство`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging)  | [![Статус сборки](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_apis/build/status/dot-org-ci?branchName=production-current)](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_build/latest?definitionId=17&branchName=production-current) | [Перейти к панели состояния](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_build)      |
| Релиз Трубопровода  | [`текущее производство`](https://github.com/freeCodeCamp/freeCodeCamp/tree/production-staging)  |                                                                                                                                                                                                                                                      | [Перейти к панели состояния](https://dev.azure.com/freeCodeCamp-org/freeCodeCamp/_release)    |

## Ранний доступ и бета-тестирование

Мы приветствуем вас в тестировании этих релизов в режиме **"публичное тестирование"** и получите ранний доступ к предстоящим функциям платформы. Иногда эти функции/изменения называются **следующими, бета-версиями,** и т.д.

Ваш вклад через отзывы и отчеты об ошибках поможет нам сделать производственные платформы на `freeCodeCamp. rg` более **устойчивых**, **совместимых** и **стабильных** для всех.

Мы благодарим вас за сообщение об ошибках, с которыми вы столкнулись, и помощь в улучшении работы freeCodeCamp.org. Вы скалите!

### Определение предстоящей версии платформ

Публичная бета-тестируемая версия доступна по адресу:

<h1 align="center"><a href='https://www.freecodecamp.dev' _target='blank'>freecodecamp.dev</a></h1>

> [!ПРИМЕЧАНИЕ] Имя домена отличается от **`свободного CodeCamp.org`**. Предназначен для предотвращения индексации поисковых систем и избежания путаницы для обычных пользователей платформы.

### Определение текущей версии платформ

**Текущая версия платформы всегда доступна на [`freeCodeCamp.org`](https://www.freecodecamp.org).**

dev-team объединяет изменения из `производственно-монтажной ветки` на `production-current` при выпуске изменений. Верхний коммит должен быть то, что вы видите в реальном времени на сайте.

Вы можете определить точную версию, развернутую посетив логи сборки и установки, доступные в разделе статуса. Кроме того, вы можете отправить нам запрос в [чате участников](https://gitter.im/FreeCodeCamp/Contributors) для подтверждения.

### Известные ограничения

При использовании бета-версии платформы существуют некоторые известные ограничения и tradeoff.

- #### Все данные / личный прогресс на этих бета-платформах `НЕ будут сохранены или перенесены` в производство.

  **Пользователи бета-версии будут иметь отдельный аккаунт от производства.** Бета-версия использует физически отдельную базу данных от производства. Это дает нам возможность избежать случайной потери данных или изменений. При необходимости команда dev может очистить базу данных на этой бета-версии.

- #### Нет гарантий относительно времени и надежности бета-версий.

  Ожидается, что развертывание будет частым и быстрыми итерациями, иногда в несколько раз в день. В результате в бета-версии будет неожиданно время простоя или неисправная функциональность.

- #### Не отправлять обычных пользователей на этот сайт в качестве меры подтверждения исправления

  Бета сайт является и всегда был для дополнения местного развития и тестирования, ничего другого. Это не обещание о том, что пришел, а взглянуть на то, что сейчас работает.

- #### Страница с знаком может отличаться от производства

   Мы используем арендатора теста для freecodecamp.dev на Auth0, и поэтому не имеют возможности установить свой домен. Это делает так, что все переадресации обратных вызовов и страницы входа по умолчанию отображаются в домене типа `https://freecodecamp-dev.auth0.com/`. Это не влияет на функциональность настолько близко к производству, насколько мы можем получить.

## Проблемы с представлением отчетности и оставлением отзывов

Пожалуйста, откройте новые вопросы для обсуждения и сообщения об ошибках. Вы можете написать их как **[`релиз: next/beta`](https://github.com/freeCodeCamp/freeCodeCamp/labels/release%3A%20next%2Fbeta)** для triage.

Вы можете отправить письмо на `dev[at]freecodecamp.org` если у вас есть какие-либо запросы. Как всегда, все уязвимости безопасности должны сообщаться `безопасности[at]freecodecamp.org` вместо публичного трекера и форума.