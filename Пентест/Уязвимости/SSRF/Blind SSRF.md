## Слепые уязвимости [[SSRF]]

Уязвимости SSRF вслепую возникают, если вы можете заставить приложение отправить внутренний HTTP-запрос на указанный URL-адрес, но ответ от внутреннего запроса не возвращается во внешнем ответе приложения.

Использовать SSRF вслепую сложнее, но иногда это приводит к полному удаленному выполнению кода на сервере или других внутренних компонентах.

Влияние слепых уязвимостей SSRF часто ниже, чем полностью осознанных уязвимостей SSRF, из-за их одностороннего характера. Их нельзя использовать простым способом для извлечения конфиденциальных данных из серверных систем, хотя в некоторых ситуациях их можно использовать для достижения полного удаленного выполнения кода.

## Как находить и использовать слепые уязвимости SSRF

Наиболее надежным способом обнаружения слепых уязвимостей SSRF является использование внеполосных методов ([[OAST]]). Это включает в себя попытку запуска HTTP-запроса к внешней системе, которую вы контролируете, и мониторинг сетевых взаимодействий с этой системой.

Самый простой и эффективный способ использовать внеполосные методы - это использование Burp Collaborator. Вы можете использовать [[Burp Collaborator]] для генерации уникальных доменных имен, отправки их в виде полезных данных в приложение и отслеживания любого взаимодействия с этими доменами. Если наблюдается входящий HTTP-запрос, исходящий от приложения, то оно уязвимо для [[SSRF]].


> [!NOTE] На заметку
> При тестировании уязвимостей SSRF обычно наблюдается поиск DNS для предоставленного домена-соавтора, но нет последующего HTTP-запроса. Обычно это происходит из-за того, что приложение пыталось отправить HTTP-запрос к домену, что вызвало первоначальный поиск DNS, но фактический HTTP-запрос был заблокирован фильтрацией сетевого уровня. Инфраструктура относительно часто разрешает исходящий DNS-трафик, поскольку это необходимо для очень многих целей, но блокирует HTTP-соединения с неожиданными адресатами.

Простое выявление уязвимости SSRF вслепую, которая может запускать внеполосные HTTP-запросы, само по себе не обеспечивает возможности использования. Поскольку вы не можете просмотреть ответ от внутреннего запроса, поведение не может быть использовано для изучения содержимого в системах, к которым может получить доступ сервер приложений. Однако его все еще можно использовать для поиска других уязвимостей на самом сервере или в других серверных системах. Вы можете вслепую сканировать внутреннее пространство IP-адресов, отправляя полезные данные, предназначенные для обнаружения хорошо известных уязвимостей. Если в этих полезных нагрузках также используются слепые внеполосные методы, то вы можете обнаружить критическую уязвимость на не исправленном внутреннем сервере.

Другой способ использования слепых уязвимостей SSRF - это заставить приложение подключиться к системе, находящейся под контролем злоумышленника, и возвращать вредоносные ответы HTTP-клиенту, который устанавливает соединение. Если вы сможете использовать серьезную уязвимость на стороне клиента в реализации HTTP сервера, вы сможете добиться удаленного выполнения кода в инфраструктуре приложения.