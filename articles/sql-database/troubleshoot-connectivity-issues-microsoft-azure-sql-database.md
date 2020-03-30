---
title: Poradce při potížích s běžným připojením k databázi SQL
description: Obsahuje postup řešení problémů s připojením databáze Azure SQL database a řešení dalších problémů s databází SQL.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208784"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Řešení potíží s připojením a dalších chyb ve službě Microsoft Azure SQL Database

Pokud připojení ke službě Azure SQL Database selže, zobrazí se chybové zprávy. Tyto problémy s připojením může být způsobeno azure SQL database rekonfigurace, nastavení brány firewall, časový limit připojení, nesprávné přihlašovací údaje nebo selhání použít osvědčené postupy a návrh pokyny během procesu [návrhu aplikace.](sql-database-develop-overview.md) Navíc pokud je dosaženo maximálního limitu pro některé prostředky Azure SQL Database, nemůžete se připojit k Azure SQL Database.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Přechodné chybové zprávy o chybách (40197, 40613 a další)

Když ve službě SQL Database dojde k vysokému zatížení, infrastruktura Azure dokáže dynamicky rekonfigurovat servery.  Toto dynamické chování může způsobit, že váš klientský program ztratí své připojení ke službě SQL Database. Tento druh chybového stavu se nazývá *přechodná chyba*. K událostem změny konfigurace databáze dochází z důvodu plánované události (například upgradu softwaru) nebo neplánované události (například selhání procesu nebo vyrovnávání zatížení). Většina událostí změny konfigurace je obvykle krátkodobá a měla by být dokončena maximálně za méně než 60 sekund. Tyto události však může někdy trvat déle dokončit, například když velké transakce způsobí dlouhotrvající obnovení. V následující tabulce jsou uvedeny různé přechodné chyby, které mohou aplikace přijímat při připojování k databázi SQL.

### <a name="list-of-transient-fault-error-codes"></a>Seznam kódů chyb přechodných chyb


| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 4060 |16 |Nelze otevřít databázi %.&#x2a;ls požadovanou přihlášením. Přihlášení se nezdařilo. Další informace naleznete v [tématu Chyby 4000 až 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Služba zjistila chybu při zpracování vašeho požadavku. Zkuste to prosím znovu. Kód chyby %d.<br/><br/>Tato chyba se zobrazí, když je služba vypnuta z důvodu upgradů softwaru nebo hardwaru, selhání hardwaru nebo jiných problémů s převzetím služeb při selhání. Kód chyby (%d) vložený do zprávy o chybě 40197] poskytuje další informace o druhu selhání nebo převzetí služeb při selhání, ke kterému došlo. Některé příklady kódů chyb jsou vloženy do zprávy o chybě 40197 jsou 40020, 40143, 40166 a 40540.<br/><br/>Opětovné připojení k serveru SQL Database automaticky připojí k funkční kopii databáze. Aplikace musí zachytit chybu 40197, protokolovat vložený kód chyby (%d) ve zprávě pro řešení potíží a zkuste se znovu připojit k databázi SQL, dokud nebudou k dispozici prostředky a připojení se znovu napojí. Další informace naleznete [v tématu Přechodné chyby](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Služba je aktuálně zaneprázdněna. Opakujte požadavek po 10 sekundách. ID incidentu: %ls. Kód: %d. Další informace naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Databáze %.&#x2a;ls na serveru %.&#x2a;ls není v současné době k dispozici. Zopakujte připojení později. Pokud problém přetrvává, obraťte se na zákaznickou podporu a zadejte id trasování relace %.&#x2a;ls.<br/><br/> K této chybě může dojít, pokud již existuje připojení vyhrazené správce (DAC) vytvořené k databázi. Další informace naleznete [v tématu Přechodné chyby](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Požadavek nejde zpracovat. Není dostatek prostředků pro zpracování požadavku.<br/><br/>Služba je aktuálně zaneprázdněna. Opakujte požadavek později. Další informace naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Nelze zpracovat požadavek na vytvoření nebo aktualizaci. Pro bíhá pro odběr %ld příliš mnoho operací vytváření nebo aktualizace.<br/><br/>Služba je zaneprázdněna zpracováním více požadavků na vytvoření nebo aktualizaci pro vaše předplatné nebo server. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro čekající operace. Počkejte, dokud nebudou dokončeny nevyřízené žádosti o vytvoření nebo aktualizaci, nebo odstraňte jeden z nevyřízených požadavků a opakujte požadavek později. Další informace naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Požadavek nejde zpracovat. Probíhá příliš mnoho operací pro odběr %ld.<br/><br/>Služba je zaneprázdněna zpracováním více požadavků pro toto předplatné. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro stav operace. Počkejte, až budou čekající požadavky dokončeny, nebo odstraňte jeden z nevyřízených požadavků a opakujte požadavek později. Další informace naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Přihlášení k sekundárníčtení se nezdařilo z důvodu dlouhého čekání na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika není k dispozici pro přihlášení, protože chybí verze řádků pro transakce, které byly v letu při recyklaci repliky. Problém lze vyřešit vrácením zpět nebo potvrzením aktivních transakcí v primární replice. Výskyty tohoto stavu lze minimalizovat tím, že se zabrání dlouhé transakce zápisu na primární. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení problémů s přechodným připojením

1. Zkontrolujte [Řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo během doby, během které byly chyby hlášeny aplikací.
2. Aplikace, které se připojují ke cloudové službě, jako je Azure SQL Database, by měly očekávat periodické události změny konfigurace a implementovat logiku opakování pro zpracování těchto chyb namísto jejich zobrazení jako chyby aplikace pro uživatele. 
3. Jako databáze se blíží jeho omezení prostředků, může se zdát, že přechodný problém připojení. Viz [Omezení prostředků](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Pokud problémy s připojením přetrvávají nebo pokud doba trvání, po kterou vaše aplikace narazí na chybu, překročí 60 sekund nebo pokud se v daný den zobrazí více výskytů chyby, nasuňte žádost o podporu Azure výběrem **možnosti Získat podporu** na webu [podpory Azure.](https://azure.microsoft.com/support/options)

#### <a name="implementing-retry-logic"></a>Implementace logiky opakování
Důrazně doporučujeme, aby klientský program má logiku opakování tak, aby mohl obnovit připojení po poskytnutí přechodné houštiny čas opravit sám.  Doporučujeme odložit před prvním opakováním 5 sekund. Opakování po zpoždění kratší než 5 sekund hrozí zahlcení cloudové služby. Pro každou další opakování zpoždění by měl růst exponenciálně, až maximálně 60 sekund.

Příklady kódu logiky opakování naleznete v tématu:
- [Připojení k SQL pružně pomocí ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Připojení k SQL s PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Další informace o zpracování přechodných chyb v kontrole aplikace
* [Poradce při potížích s chybami přechodných připojení k databázi SQL](sql-database-connectivity-issues.md)

Diskuse o *období blokování* pro klienty, kteří používají ADO.NET je k dispozici ve [sdružování připojení k serveru SQL Server (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Při navazování připojení k databázovému serveru SQL došlo k chybě související se sítí nebo instancí.

K problému dochází, pokud se aplikace nemůže připojit k serveru.

Chcete-li tento problém vyřešit, vyzkoušejte kroky (v uvedeném pořadí) v části [Kroky k opravě běžných problémů s připojením.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Server/instance nebyla nalezena nebo nebyla přístupná (chyby 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Chyba 26: Byl zadán server pro zjišťování chyb

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Chyba 40: Nelze otevřít připojení k serveru.

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Chyba 10053: Při příjmu výsledků ze serveru došlo k chybě na úrovni přenosu.

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

K těmto problémům dochází, pokud se aplikace nemůže připojit k serveru.

Chcete-li tyto problémy vyřešit, vyzkoušejte kroky (v uvedeném pořadí) v části [Kroky k opravě běžných problémů s připojením.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Nelze se připojit k serveru kvůli problémům s bránou firewall.

### <a name="error-40615-cannot-connect-to--servername-"></a>Chyba 40615: Nelze se připojit k < název > serveru

Chcete-li tento problém vyřešit, [nakonfigurujte nastavení brány firewall v databázi SQL prostřednictvím portálu Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Chyba 5: Nelze se připojit k < názvu serveru >

Chcete-li tento problém vyřešit, ujistěte se, že port 1433 je otevřen pro odchozí připojení na všech bran firewall mezi klientem a Internetem.

Další informace naleznete [v tématu Configure the Windows Firewall to allow SQL Server access](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nelze se přihlásit k serveru (chyby 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Přihlášení se nezdařilo pro uživatele '< uživatelské jméno >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Chcete-li tento problém vyřešit, obraťte se na správce služby poskytnout vám platné uživatelské jméno a heslo serveru SQL Server.

Správce služeb může obvykle přidat přihlašovací údaje pomocí následujících kroků:

1. Přihlaste se k serveru pomocí SQL Server Management Studio (SSMS).
2. Spusťte následující dotaz SQL a zkontrolujte, zda je přihlašovací jméno zakázáno:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Pokud odpovídající jméno je zakázané, povolte ho pomocí následujícího příkazu:

   ```sql
   Alter login <User name> enable
   ```

4. Pokud uživatelské jméno přihlášení SQL neexistuje, vytvořte ho následujícím postupem:

   1. V SSMS poklikejte na **Zabezpečení** a rozbalte ho.
   2. Pravým tlačítkem klikněte na **Přihlášení** a potom vyberte **Nové přihlášení**.
   3. Ve vygenerovaném skriptu se zástupnými symboly upravte a spusťte následující dotaz SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Dvakrát klikněte na **Databáze**.
6. Vyberte databázi, které chcete udělit oprávnění uživatele.
7. Dvakrát klikněte na **Zabezpečení**.
8. Pravým tlačítkem klikněte na **Uživatelé** a potom vyberte **Nový uživatel**.
9. Ve vygenerovaném skriptu se zástupnými symboly upravte a spusťte následující dotaz SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Můžete také `sp_addrolemember` namapovat konkrétní uživatele na konkrétní databázové role.

Další informace najdete [v tématu Správa databází a přihlášení v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Vypršení časového limitu připojení vypršela

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Časový limit připojení vypršel

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlExceptionException (0x80131904): Vypršel časový limit

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Podkladový zprostředkovatel selhal při otevření

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nelze se připojit k < > názvů serverů.

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Tyto výjimky může dojít z důvodu připojení nebo dotaz problémy. Chcete-li ověřit, že tato chyba je způsobena problémy s připojením, [přečtěte si téma Ověření, zda je chyba způsobena problémem s připojením](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

K časovým limitům připojení dochází, protože aplikace se nemůže připojit k serveru. Chcete-li tento problém vyřešit, vyzkoušejte kroky (v uvedeném pořadí) v části [Kroky k opravě běžných problémů s připojením.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Chyby zásad správného řízení zdrojů

### <a name="error-10928-resource-id-d"></a>Chyba 10928: ID prostředku: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Chcete-li tento problém vyřešit, vyzkoušejte jednu z následujících metod:

* Ověřte, zda existují dlouhotrvající dotazy.

  > [!NOTE]
  > Jedná se o minimalistický přístup, který nemusí problém vyřešit.

1. Chcete-li zobrazit všechny požadavky na blokování, spusťte následující dotaz SQL a zkontrolujte zobrazení [sys.dm_exec_requests:](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Určete **vstupní vyrovnávací paměť** pro blokátor hlavy.
3. Vylaďte dotaz na blokování hlavy.

   Podrobný postup řešení potíží naleznete v tématu [Je můj dotaz v cloudu v pořádku?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

Pokud databáze konzistentně dosahuje svého limitu i přes adresování blokování a dlouhotrvající dotazy, zvažte upgrade na edici s více prostředků [edice](https://azure.microsoft.com/pricing/details/sql-database/)).

Další informace o zobrazeních dynamické správy naleznete v [tématu Zobrazení dynamické správy systému](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Další informace o omezenídatabáze naleznete v [tématu omezení prostředků databáze SQL pro server Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Chyba 10929: ID prostředku: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Chyba 40501: Služba je aktuálně zaneprázdněna.

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Toto je chyba omezení motoru, což znamená, že jsou překročeny limity prostředků.

Další informace o omezeních prostředků naleznete v [tématu Omezení prostředků databázového serveru](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Chyba 40544: Databáze dosáhla kvóty velikosti.

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

K této chybě dochází, když databáze dosáhla kvóty velikosti.

Následující kroky vám mohou pomoci problém vyřešit nebo vám poskytnout další možnosti:

1. Zkontrolujte aktuální velikost databáze pomocí řídicího panelu na webu Azure Portal.

   > [!NOTE]
   > Chcete-li zjistit, které tabulky spotřebovávají nejvíce místa, a proto jsou potenciálními kandidáty pro vyčištění, spusťte následující dotaz SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Pokud aktuální velikost nepřesáhne maximální velikost podporovanou pro vaši edici, můžete pomocí funkce ALTER DATABASE zvýšit nastavení MAXSIZE.
3. Pokud databáze již překonala maximální podporovanou velikost pro edici, vyzkoušejte jeden nebo více z následujících kroků:

   * Provádějte normální aktivity čištění databáze. Například vyčistěte nežádoucí data pomocí zkrácení/odstranění nebo přesuňte data pomocí služby SQL Server Integration Services (SSIS) nebo nástroje programu hromadného kopírování (bcp).
   * Rozdělení nebo odstranění dat, přetažení indexů nebo prohledání možných řešení v dokumentaci.
   * Škálování databáze najdete v [tématu Škálování prostředků jedné databáze](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) a [škálování prostředků elastického fondu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Chyba 40549: Relace je ukončena, protože máte dlouhotrvající transakci.

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Pokud opakovaně dochází k této chybě, zkuste problém vyřešit následujícími kroky:

1. Zkontrolujte zobrazení sys.dm_exec_requests zobrazení všech otevřených relací, které mají vysokou hodnotu pro sloupec total_elapsed_time. Tuto kontrolu proveďte spuštěním následujícího skriptu SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Určete vstupní vyrovnávací paměť pro dlouhotrvající dotaz.
3. Vylaďte dotaz.

Zvažte také dávkování dotazů. Informace o dávkování naleznete v tématu [Jak pomocí dávkování zlepšit výkon aplikace databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Podrobný postup řešení potíží naleznete v tématu [Je můj dotaz v cloudu v pořádku?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Chyba 40551: Relace byla ukončena z důvodu nadměrného využití databáze TEMPDB.

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Chcete-li tento problém vyřešit, postupujte takto:

1. Změňte dotazy, abyste snížili dočasné využití místa v tabulce.
2. Zastavte dočasné objekty poté, co už nejsou potřeba.
3. Zkrátit tabulky nebo odebrat nepoužívané tabulky.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Chyba 40552: Relace byla ukončena z důvodu nadměrného využití místa protokolu transakcí.

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Při řešení tohoto problému zkuste použít následující metody:

* K problému může dojít z důvodu vložení, aktualizace nebo odstranění operací.
Pokuste se snížit počet řádků, které jsou provozovány okamžitě implementací dávkování nebo rozdělení do více menších transakcí.
* K problému může dojít z důvodu operace obnovení indexu. Chcete-li tento problém vyřešit, ujistěte se, že počet řádků, které jsou ovlivněny v tabulce * (průměrná velikost pole, které je aktualizováno v bajtů + 80) < 2 gigabajty (GB).

  > [!NOTE]
  > Pro opětovné sestavení indexu by měla být průměrná velikost aktualizovaného pole nahrazena průměrnou velikostí indexu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Chyba 40553: Relace byla ukončena z důvodu nadměrného využití paměti.

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Chcete-li tento problém vyřešit, pokuste se optimalizovat dotaz.

Podrobný postup řešení potíží naleznete v tématu [Je můj dotaz v cloudu v pořádku?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabulka dalších chybových zpráv zásad správného řízení prostředků

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 10928 |20 |ID prostředku: %d. Limit %s pro databázi je %d a bylo dosaženo. Další informace naleznete v [tématu omezení prostředků databáze SQL pro jednu a sdružené databáze](sql-database-resource-limits-database-server.md).<br/><br/>ID prostředku označuje prostředek, který dosáhl limitu. Pro pracovní podprocesy ID prostředku = 1. Pro relace ID prostředku = 2.<br/><br/>Další informace o této chybě a jeho řešení naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |ID prostředku: %d. Minimální záruka %s je %d, maximální limit je %d a aktuální využití databáze je %d. Server je však aktuálně příliš zaneprázdněn na to, aby pro tuto databázi podporoval požadavky větší než %d. ID prostředku označuje prostředek, který dosáhl limitu. Pro pracovní podprocesy ID prostředku = 1. Pro relace ID prostředku = 2. Další informace naleznete v tématu: <br/>&bull;&nbsp; [Omezení prostředků databázového serveru](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity založené na DTU pro jednotlivé databáze](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md). <br/>V opačném případě akci opakujte později. |
| 40544 |20 |Databáze dosáhla své velikosti kvóty. Rozdělení nebo odstranění dat, přetažení indexů nebo prohledání možných řešení v dokumentaci. Škálování databáze najdete v [tématu Škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Relace je ukončena, protože máte dlouhotrvající transakci. Zkuste transakci zkrátit. Informace o dávkování naleznete v tématu [Jak pomocí dávkování zlepšit výkon aplikace databáze SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Relace byla ukončena, protože získala příliš mnoho zámků. Zkuste číst nebo upravovat méně řádků v jedné transakci. Informace o dávkování naleznete v tématu [Jak pomocí dávkování zlepšit výkon aplikace databáze SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Relace byla ukončena z `TEMPDB` důvodu nadměrného využití. Zkuste upravit dotaz snížit využití dočasného místa v tabulce.<br/><br/>Pokud používáte dočasné objekty, ušetřete místo v `TEMPDB` databázi uvolněním dočasné objekty poté, co již nejsou potřebné relace. Další informace o využití databáze databáze SQL naleznete v tématu [Databáze Tempdb v databázi SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Relace byla ukončena z důvodu nadměrnévyužití místa protokolu transakcí. Zkuste upravit méně řádků v jedné transakci. Informace o dávkování naleznete v tématu [Jak pomocí dávkování zlepšit výkon aplikace databáze SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Pokud provádíte hromadné vložení `bcp.exe` pomocí `System.Data.SqlClient.SqlBulkCopy` nástroje nebo třídy, zkuste pomocí možností `-b batchsize` nebo `BatchSize` omezit počet řádků zkopírovaných na server v každé transakci. Pokud znovu vytváříte index `ALTER INDEX` s příkazem, `REBUILD WITH ONLINE = ON` zkuste použít možnost. Informace o velikostech transakčních protokolů pro nákupní model virtuálních jader naleznete v následujících tématech: <br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity pro elastické fondy založené na virtuálních jádrech](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz tak, aby zpracovával méně řádků.<br/><br/>Snížení počtu `ORDER BY` a `GROUP BY` operací v kódu Transact-SQL snižuje požadavky na paměť dotazu. Škálování databáze najdete v [tématu Škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Chyby elastického fondu

Následující chyby souvisejí s vytvářením a používáním elastických fondů:

| Kód chyby | Severity | Popis | Nápravná opatření |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Elastický fond dosáhl svého limitu úložiště. Využití úložiště pro elastický fond nesmí překročit (%d) MBs. Pokus o zápis dat do databáze po dosažení limitu úložiště elastického fondu. Informace o limitech prostředků naleznete v těchto tématech: <br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Zvažte zvýšení DTU a/nebo přidání úložiště do elastického fondu, pokud je to možné, aby se zvýšil jeho limit úložiště, snížit úložiště používané jednotlivými databázemi v rámci elastického fondu nebo odebrat databáze z elastického fondu. Pro škálování elastického fondu najdete [v tématu škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Minimální záruka %s je %d, maximální limit je %d a aktuální využití databáze je %d. Server je však aktuálně příliš zaneprázdněn na to, aby pro tuto databázi podporoval požadavky větší než %d. Informace o limitech prostředků naleznete v těchto tématech: <br/>&bull;&nbsp; [Limity pro elastické bazény založené na DTU](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na virtuálních jádrech pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). <br/> V opačném případě akci opakujte později. DTU / vCore min v databázi; DTU / vCore max na databázi. Celkový počet souběžných pracovníků (požadavků) ve všech databázích v elastickém fondu se pokusil překročit limit fondu. |Zvažte zvýšení DTU nebo virtuálníjádra elastického fondu, pokud je to možné, aby se zvýšil jeho limit pracovního procesu nebo odebrat databáze z elastického fondu. |
| 40844 | 16 |Databáze %ls na serveru %ls je databáze edice %ls v elastickém fondu a nemůže mít souvislý vztah kopírování.  |Není dostupné. |
| 40857 | 16 |Elastický fond nebyl nalezen pro server: %ls, název elastického fondu: %ls. Zadaný elastický fond na zadaném serveru neexistuje. | Zadejte platný název elastického fondu. |
| 40858 | 16 |Elastický fond %ls již na serveru existuje: %ls. Zadaný elastický fond již na zadaný matný soubor SQL Database server existuje. | Zadejte nový název elastického fondu. |
| 40859 | 16 |Elastický fond nepodporuje úroveň služby %ls. Zadaná úroveň služby není podporována pro zřizování elastického fondu. |Zadejte správnou edici nebo ponechte úroveň služby prázdnou, abyste použili výchozí úroveň služby. |
| 40860 | 16 |Kombinace elastického fondu %ls a cíle služby %ls je neplatná. Elastický fond a úroveň služby lze zadat společně pouze v případě, že typ prostředku je zadán jako "ElasticPool". |Zadejte správnou kombinaci elastického fondu a úrovně služby. |
| 40861 | 16 |Databáze vydání '%. *Ls" se nemůže lišit od úrovně služby elastického fondu, která je "%.* Ls". Edice databáze se liší od úrovně služby elastického fondu. |Nezadávejte databázovou edici, která se liší od úrovně služby elastického fondu.  Všimněte si, že databázové vydání není nutné zadat. |
| 40862 | 16 |Název fondu elastického fondu musí být zadán, pokud je zadán cíl služby elastického fondu. Cíl služby elastického fondu neidentifikuje jednoznačně elastický fond. |Zadejte název elastického fondu, pokud používáte účel služby elastického fondu. |
| 40864 | 16 |DTU pro elastický fond musí být alespoň (%d) DTU pro úroveň služby %.*ls. Pokus o nastavení DTU pro elastický fond pod minimální limit. |Opakujte nastavení DTU pro elastický fond alespoň minimální limit. |
| 40865 | 16 |DTU pro elastický fond nesmí překročit (%d) DTU pro úroveň služby %.*ls. Pokus o nastavení DTU pro elastický fond nad maximální limit. |Opakujte nastavení DTU pro elastický fond na hodnotu ne větší než maximální limit. |
| 40867 | 16 |Maximální hodnota DTU na databázi musí být alespoň (%d) pro úroveň služby %.*ls. Pokus o nastavení Maximální DTU na databázi pod podporovaný limit. | Zvažte použití úrovně služby elastického fondu, která podporuje požadované nastavení. |
| 40868 | 16 |Maximální hodnota DTU na databázi nesmí překročit (%d) pro úroveň služby %.*ls. Pokus o nastavení Maximální DTU na databázi nad podporovaný limit. | Zvažte použití úrovně služby elastického fondu, která podporuje požadované nastavení. |
| 40870 | 16 |Min DTU na databázi nesmí překročit (%d) pro úroveň služby %.*ls. Pokus o nastavení min DTU na databázi nad podporovaný limit. | Zvažte použití úrovně služby elastického fondu, která podporuje požadované nastavení. |
| 40873 | 16 |Počet databází (%d) a DTU min na databázi (%d) nesmí překročit jednotky DTU elastického fondu (%d). Pokus o zadání MTU pro databáze v elastickém fondu, který přesahuje DTU elastického fondu. | Zvažte zvýšení DTU elastického fondu nebo snížit MIN DTU na databázi nebo snížit počet databází v elastickém fondu. |
| 40877 | 16 |Elastický fond nelze odstranit, pokud neobsahuje žádné databáze. Elastický fond obsahuje jednu nebo více databází, a proto jej nelze odstranit. |Odeberte databáze z elastického fondu, abyste je mohli odstranit. |
| 40881 | 16 |Elastický fond %*ls dosáhl limitu počtu databází.  Limit počtu databází pro elastický fond nesmí překročit (%d) pro elastický fond s (%d) DTU. Pokus o vytvoření nebo přidání databáze do elastického fondu po dosažení limitu počtu databáze elastického fondu. | Zvažte zvýšení DTU elastického fondu, pokud je to možné, aby se zvýšil jeho limit databáze nebo odebrat databáze z elastického fondu. |
| 40889 | 16 |DTU nebo limit úložiště pro elastický fond %.*ls nelze snížit, protože by to neposkytovalo dostatečný úložný prostor pro jeho databáze. Pokus o snížení limitu úložiště elastického fondu pod jeho využití úložiště. | Zvažte snížení využití úložiště jednotlivých databází v elastickém fondu nebo odebrat databáze z fondu, aby se snížil jeho dtu nebo limit úložiště. |
| 40891 | 16 |Min DTU na databázi (%d) nesmí překročit maximální hodnotu DTU na databázi (%d). Pokus o nastavení MIN DTU na databázi vyšší než DTU max na databázi. |Ujistěte se, že min DTU na databáze nepřesahuje Maximální DTU na databázi. |
| Bude doplněno | 16 |Velikost úložiště pro jednotlivé databáze v elastickém fondu nesmí překročit maximální velikost povolenou elastickým fondem úrovně služby %.*ls. Maximální velikost databáze překračuje maximální velikost povolenou úrovní služby elastického fondu. |Nastavte maximální velikost databáze v mezích maximální velikosti povolené vrstvou služby elastického fondu. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Nelze otevřít databázi "master" požadované přihlášení. Přihlášení se nezdařilo.

K tomuto problému dochází, protože účet nemá oprávnění k přístupu k hlavní databázi. Ale ve výchozím nastavení SQL Server Management Studio (SSMS) se pokusí připojit k hlavní databázi.

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Na přihlašovací obrazovce ssms vyberte **Možnosti**a pak vyberte **Vlastnosti připojení**.
2. Do pole **Připojit k databázi** zadejte jako výchozí přihlašovací databázi výchozí název databáze uživatele a vyberte **připojit**.

   ![Vlastnosti připojení](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Zkontrolujte, zda je chyba způsobena problémem s připojením

Chcete-li ověřit, zda je chyba způsobena problémem s připojením, zkontrolujte trasování zásobníku pro rámce, které zobrazují volání k otevření připojení, jako jsou následující (všimněte si odkazu na třídu **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Když je výjimka spuštěna problémy s dotazem, všimnete si zásobníku volání, který je podobný následujícímu (všimněte si odkazu na třídu **SqlCommand).** V takovém případě [naladit dotazy](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Další pokyny k jemnému doladění výkonu naleznete v následujících zdrojích:

* [Jak udržovat indexy a statistiky Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ruční optimalizace výkonu dotazu v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Sledování výkonu Azure SQL Database pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Provoz úložiště dotazů v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Postup řešení běžných potíží s připojením

1. Ujistěte se, že protokol TCP/IP je povolen jako klientský protokol na aplikačním serveru. Další informace naleznete v [tématu Konfigurace klientských protokolů](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na aplikačních serverech, na kterých nejsou nainstalovány nástroje serveru SQL Server, ověřte, zda je protokol TCP/IP povolen spuštěním **nástroje cliconfg.exe** (SQL Server Client Network).
2. Zkontrolujte připojovací řetězec aplikace a ujistěte se, že je správně nakonfigurován. Například se ujistěte, že připojovací řetězec určuje správný port (1433) a plně kvalifikovaný název serveru.
Viz [Získání informací o připojení serveru SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Zkuste zvýšit hodnotu časového limitu připojení. Doporučujeme použít časový limit připojení v šakátu alespoň 30 sekund.
4. Otestujte připojení mezi aplikačním serverem a databází Azure SQL pomocí [aplikace SQL Server management Studio (SSMS),](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)souboru UDL, ping nebo telnetu. Další informace naleznete [v tématu Poradce při potížích s připojením serveru SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) a [Diagnostika pro problémy s připojením](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Jako krok řešení potíží můžete také otestovat připojení v jiném klientském počítači.

5. Jako osvědčený postup se ujistěte, že logika opakování je na místě. Další informace o logice opakování naleznete [v tématu Poradce při potížích s přechodovými chybami a chybami připojení k databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Pokud tyto kroky problém nevyřeší, pokuste se shromáždit další data a potom se obraťte na podporu. Pokud je vaše aplikace cloudová služba, povolte protokolování. Tento krok vrátí časové razítko UTC selhání. Navíc SQL Azure vrátí id trasování. Tyto informace mohou používat [služby podpory zákazníků](https://azure.microsoft.com/support/options/) společnosti Microsoft.

Další informace o povolení protokolování najdete v [tématu Povolení protokolování diagnostiky pro aplikace ve službě Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Další kroky

* [Architektura připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Ovládací prvky přístupu k síti Azure SQL Database a Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
