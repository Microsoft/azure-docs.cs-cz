---
title: Řešení běžných problémů s připojením ke službě Azure SQL Database
description: Popisuje kroky pro řešení potíží s Azure SQL Databasem připojením a řešení problémů specifických pro Azure SQL Database nebo Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2020
ms.openlocfilehash: aa4bcee7a2eaf5e6ec11b9066ed6eca6b33bdba1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284117"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Řešení potíží s připojením a dalších chyb pomocí Azure SQL Database a spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Pokud dojde k chybě připojení k Azure SQL Database nebo spravované instanci Azure SQL, zobrazí se chybové zprávy. Tyto problémy s připojením mohou být způsobeny změnou konfigurace, nastavením brány firewall, vypršením časového limitu připojení, nesprávnými přihlašovacími informacemi nebo neúspěšným uplatněním osvědčených postupů a pokynů pro návrh během procesu [návrhu aplikace](develop-overview.md) . Kromě toho platí, že pokud je dosaženo maximálního limitu některých Azure SQL Database nebo zdrojů spravované instance SQL, nebudete se již moci připojit.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Chybové zprávy přechodného selhání (40197, 40613 a další)

Když ve službě SQL Database dojde k vysokému zatížení, infrastruktura Azure dokáže dynamicky rekonfigurovat servery.  Toto dynamické chování může způsobit ztrátu připojení klientského programu k databázi nebo instanci. Tento druh chybové podmínky se nazývá *přechodná chyba*. Události opětovné konfigurace databáze nastávají z důvodu plánované události (například upgrade softwaru) nebo neplánované události (například selhání procesu nebo vyrovnávání zatížení). Většina událostí opětovné konfigurace je obecně krátkodobá a měla by být dokončena během méně než 60 sekund. Tyto události mohou nicméně občas trvat déle, například když velká transakce způsobí dlouhotrvající obnovení. Následující tabulka uvádí různé přechodné chyby, které aplikace může obdržet při připojování k SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Seznam chybových kódů přechodných chyb

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 4060 |16 |Nelze otevřít databázi "%. &#x2a;ls" požadovanou pro přihlášení. Přihlášení se nezdařilo. Další informace najdete v tématu [chyby 4000 až 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) .|
| 40197 |17 |Služba zjistila chybu při zpracování vaší žádosti. Zkuste to prosím znovu. Kód chyby:% d.<br/><br/>Tato chyba se zobrazí, když dojde k výpadku služby kvůli softwarovým nebo hardwarovým upgradům, selháním hardwaru nebo jakýmkoli jiným problémům s podporou převzetí služeb při selhání. Kód chyby (% d) vložený v rámci zprávy chyby 40197 poskytuje další informace o druhu selhání nebo převzetí služeb při selhání. Některé příklady kódů chyb jsou vložené v rámci zprávy chyby 40197 jsou 40020, 40143, 40166 a 40540.<br/><br/>Po opětovném připojení se automaticky připojí k kopii databáze, která je v pořádku. Vaše aplikace musí zachytit chybu 40197, zaprotokolovat vložený kód chyby (% d) ve zprávě pro řešení potíží a zkusit se znovu připojit k SQL Database, dokud nebudou k dispozici prostředky a připojení se znovu naváže. Další informace najdete v tématu [přechodné chyby](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Služba je aktuálně zaneprázdněna. Požadavek opakujte po 10 sekundách. ID incidentu:% ls. Kód:% d. Další informace naleznete v tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md)|
| 40613 |17 |Databáze%. &#x2a;ls na serveru%. &#x2a;ls není aktuálně k dispozici. Zkuste prosím připojení znovu později. Pokud se problém opakuje, obraťte se na zákaznickou podporu a poskytněte mu ID trasování relace%. &#x2a;ls.<br/><br/> K této chybě může dojít, pokud již existuje existující vyhrazené připojení správce (DAC) pro databázi. Další informace najdete v tématu [přechodné chyby](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Požadavek nejde zpracovat. Pro zpracování požadavku není dostatek prostředků.<br/><br/>Služba je aktuálně zaneprázdněna. Opakujte prosím požadavek později. Další informace naleznete v tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md) |
| 49919 |16 |Nelze zpracovat žádost o vytvoření nebo aktualizaci. Pro předplatné% ld probíhá příliš mnoho probíhajících operací vytvoření nebo aktualizace.<br/><br/>Služba je zaneprázdněná zpracováním více žádostí o vytvoření nebo aktualizaci vašeho předplatného nebo serveru. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [Sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro operace, které čekají na zpracování. Počkejte, dokud nebudou dokončeny žádosti o vytvoření nebo aktualizaci, nebo odstraňte jednu z vašich čekajících žádostí a opakujte požadavek později. Další informace naleznete v tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md) |
| 49920 |16 |Požadavek nejde zpracovat. U předplatného% ld probíhá příliš mnoho operací.<br/><br/>Služba je zaneprázdněná zpracováním více požadavků pro toto předplatné. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [Sys. dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro stav operace. Počkejte na dokončení čekajících žádostí nebo odstraňte jednu z vašich čekajících žádostí a opakujte požadavek později. Další informace naleznete v tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md) |
| 4221 |16 |Přihlášení k sekundárnímu pro čtení se nezdařilo z důvodu dlouhého čekání na ' HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING '. Replika není k dispozici pro přihlášení, protože pro transakce, které byly v letadle při recyklování repliky, chybí verze řádků. Problém se dá vyřešit vrácením zpět nebo potvrzením aktivních transakcí na primární replice. Výskyty tohoto stavu lze minimalizovat tím, že se vyhnete dlouhým transakcím zápisu na primárním. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Postup řešení potíží s přechodným připojením

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://azure.microsoft.com/status) pro všechny známé výpadky, ke kterým došlo v době, během které aplikace ohlásila chyby.
2. Aplikace, které se připojují ke cloudové službě, jako je třeba Azure SQL Database, by měly očekávat události pravidelného překonfigurování a implementují logiku opakování pro zpracování těchto chyb, místo aby je zpřístupnění jako chyby aplikací pro uživatele.
3. Vzhledem k tomu, že se databáze blíží omezení prostředků, může se jednat o přechodný problém s připojením. Viz [omezení prostředků](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Pokud potíže s připojením trvají nebo pokud doba, po kterou vaše aplikace narazí na chybu, překračuje 60 sekund, nebo pokud se v daném dni zobrazí více výskytů chyby, zastavte žádost o podporu Azure výběrem možnosti **získat podporu** na webu [podpory Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementace logiky opakování

Důrazně doporučujeme, aby váš klientský program měl logiku opakování, aby mohl znovu navázat připojení, a to tak, že by došlo k přechodné chybě při jejich opravě.  Před prvním opakováním doporučujeme, abyste prodlevu zaznamenali na 5 sekund. Opakuje se pokus po zpoždění kratším než 5 sekund při zahlcení cloudové služby. U každého následného opakování by se mělo zpozdit exponenciálně, maximálně 60 sekund.

Příklady kódu logiky opakování naleznete zde:

- [Odolné připojení k SQL pomocí ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Odolné připojení k SQL pomocí PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Další informace o zpracování přechodných chyb ve vaší aplikaci najdete v [řešení potíží s přechodnými chybami připojení SQL Database](troubleshoot-common-connectivity-issues.md)

K dispozici je diskuze o *době blokování* pro klienty, kteří používají ADO.NET, v části [sdružování připojení (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Při navazování připojení k serveru došlo k chybě související se sítí nebo ke konkrétní instanci.

K tomuto problému dochází, pokud se aplikace nemůže připojit k serveru.

Pokud chcete tento problém vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Server/instance se nepodařilo najít nebo není přístupná (chyby 26, 40, 10053).

### <a name="error-26-error-locating-server-specified"></a>Chyba 26: Chyba při hledání zadaného serveru

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Chyba 40: Nelze otevřít připojení k serveru

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Chyba 10053: při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu.

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

K těmto potížím dochází, pokud se aplikace nemůže připojit k serveru.

Pokud chcete tyto problémy vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Nejde se připojit k serveru kvůli problémům s bránou firewall.

### <a name="error-40615-cannot-connect-to--servername-"></a>Chyba 40615: Nelze se připojit k serveru < servername >

Chcete-li tento problém vyřešit, [nakonfigurujte nastavení brány firewall na SQL Database prostřednictvím Azure Portal](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Chyba 5: Nelze se připojit k serveru < servername >

Pokud chcete tento problém vyřešit, ujistěte se, že je port 1433 otevřený pro odchozí připojení na všech branách firewall mezi klientem a internetem.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nepovedlo se přihlásit k serveru (chyby 18456, 40531).

### <a name="login-failed-for-user--user-name-"></a>Přihlášení uživatele < uživatelské jméno > se nezdařilo.

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Pokud chcete tento problém vyřešit, požádejte správce služby, aby vám poskytl platné uživatelské jméno a heslo.

Správce služby obvykle může pomocí následujících kroků přidat přihlašovací údaje pro přihlášení:

1. Přihlaste se k serveru pomocí SQL Server Management Studio (SSMS).
2. Spusťte následující dotaz SQL ke kontrole, jestli je přihlašovací jméno zakázané:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Pokud odpovídající jméno je zakázané, povolte ho pomocí následujícího příkazu:

   ```sql
   Alter login <User name> enable
   ```

4. Pokud uživatelské jméno přihlášení SQL neexistuje, vytvořte ho pomocí následujících kroků:

   1. V SSMS dvakrát klikněte na **zabezpečení** a rozbalte ho.
   2. Klikněte pravým tlačítkem na **přihlašovací údaje**a potom vyberte **nové přihlášení**.
   3. Ve vygenerovaném skriptu pomocí zástupných symbolů upravte a spusťte následující dotaz SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Dvakrát klikněte na **databáze**.
6. Vyberte databázi, ke které chcete udělit oprávnění uživatele.
7. Dvakrát klikněte na **zabezpečení**.
8. Klikněte pravým tlačítkem na **Uživatelé**a pak vyberte **Nový uživatel**.
9. Ve vygenerovaném skriptu pomocí zástupných symbolů upravte a spusťte následující dotaz SQL:

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
   > `sp_addrolemember`K mapování konkrétních uživatelů na konkrétní databázové role můžete také použít.

Další informace najdete v tématu [Správa databází a přihlášení v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Vypršení časového limitu připojení – chyby

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): vypršel časový limit připojení.

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): vypršel časový limit.

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. entity. Core. EntityException: došlo k chybě základního poskytovatele při otevření.

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nelze se připojit k názvu < serveru >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

K těmto výjimkám může dojít buď kvůli problémům s připojením nebo dotazům. Pokud se chcete ujistit, že k této chybě dochází v důsledku potíží s připojením, přečtěte si téma [ověření, jestli chyba způsobila problém s připojením](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

K vypršení časového limitu připojení dojde, protože se aplikace nemůže připojit k serveru. Pokud chcete tento problém vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Chyby zásad správného řízení prostředků

### <a name="error-10928-resource-id-d"></a>Chyba 10928: ID prostředku:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Pokud chcete tento problém obejít, zkuste jednu z následujících metod:

- Ověřte, zda existují dlouho běžící dotazy.

  > [!NOTE]
  > Jedná se o minimalist přístup, který nemusí problém vyřešit.

1. Spusťte následující dotaz SQL pro kontrolu zobrazení [Sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , abyste viděli všechny blokující požadavky:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Určete **vstupní vyrovnávací paměť** pro blokování Headers.
3. Nalaďte dotaz na blokování hlav.

   Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

Pokud databáze konzistentně dosáhne svého limitu bez ohledu na blokující a dlouhotrvající dotazy, zvažte upgrade na edici s více zdroji [verzí](https://azure.microsoft.com/pricing/details/sql-database/).

Další informace o zobrazeních dynamické správy najdete v tématu [zobrazení dynamické správy systému](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Další informace o omezeních databáze najdete v tématu  [SQL Database omezení prostředků pro servery](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Chyba 10929: ID prostředku: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Chyba 40501: služba je momentálně zaneprázdněná.

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Toto je chyba omezování modulu, což znamená, že se překročila omezení prostředků.

Další informace o omezeních prostředků najdete v tématu [omezení prostředků logického SQL serveru](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Chyba 40544: databáze dosáhla kvóty velikosti

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

K této chybě dochází, pokud databáze dosáhla kvóty velikosti.

Následující kroky vám pomůžou vyřešit problém nebo vám poskytnou další možnosti:

1. Ověřte aktuální velikost databáze pomocí řídicího panelu v Azure Portal.

   > [!NOTE]
   > Chcete-li zjistit, které tabulky spotřebovávají nejvíce místa a jsou proto potenciálními kandidáty pro vyčištění, spusťte následující dotaz SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Pokud aktuální velikost nepřekračuje maximální velikost podporovanou vaší edicí, můžete použít příkaz ALTER DATABASE a zvýšit tak nastavení MAXSIZE.
3. Pokud databáze již překročila maximální podporovanou velikost vaší edice, zkuste použít jeden nebo několik následujících kroků:

   - Provádějte normální aktivity čištění databáze. Například vyčistěte nechtěné údaje pomocí zkrácení, odstranění nebo přesunutím dat pomocí služba SSIS (SQL Server Integration Services) (SSIS) nebo programu hromadného kopírování (BCP).
   - Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení.
   - Informace o škálování databáze najdete v tématu [škálování prostředků jedné databáze](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) a [škálování prostředků elastického fondu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Chyba 40549: relace je ukončena, protože máte dlouhotrvající transakci.

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Při opakovaném výskytu této chyby zkuste problém vyřešit pomocí následujících kroků:

1. Pokud chcete zobrazit otevřené relace, které mají pro sloupec total_elapsed_time vysokou hodnotu, zkontrolujte zobrazení sys. dm_exec_requests. Tuto kontrolu proveďte spuštěním následujícího skriptu SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Určete vstupní vyrovnávací paměť pro dlouho běžící dotaz.
3. Nalaďte dotaz.

Zvažte také dávkování dotazů. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Chyba 40551: relace byla ukončena z důvodu nadměrného využití databáze TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Pokud chcete tento problém obejít, postupujte podle těchto kroků:

1. Změňte dotazy tak, aby se snížilo využití místa v dočasné tabulce.
2. Vyřaďte dočasné objekty, které už nejsou potřeba.
3. Zkraťte tabulky nebo odeberte nepoužité tabulky.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Chyba 40552: relace byla ukončena z důvodu nadměrného využití místa v protokolu transakcí.

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Při řešení tohoto problému zkuste použít následující metody:

- K tomuto problému může dojít z důvodu operací vložení, aktualizace nebo odstranění.
Pokuste se snížit počet řádků, které jsou okamžitě provozovány implementací dávkování nebo rozdělení do několika menších transakcí.
- K tomuto problému může dojít kvůli operacím opětovného sestavení indexu. Pokud chcete tento problém obejít, ujistěte se, že počet řádků, které jsou v tabulce ovlivněny * (Průměrná velikost pole, které je aktualizované v bajtech + 80) < 2 gigabajty (GB).

  > [!NOTE]
  > V případě opětovného sestavení indexu by měla být průměrná velikost aktualizovaného pole nahrazena průměrnou velikostí indexu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Chyba 40553: relace byla ukončena z důvodu nadměrného využití paměti

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Pokud chcete tento problém obejít, zkuste optimalizovat dotaz.

Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabulka dalších chybových zpráv zásad správného řízení prostředků

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 10928 |20 |ID prostředku:% d. Limit% s pro databázi je% d a byl dosažen. Další informace najdete v tématu [SQL Database omezení prostředků pro databáze s jednou a ve fondu](resource-limits-logical-server.md).<br/><br/>ID prostředku indikuje prostředek, který dosáhl limitu. Pro pracovní vlákna, ID prostředku = 1. Pro relace, ID prostředku = 2.<br/><br/>Další informace o této chybě a o tom, jak ji vyřešit, najdete v těchto tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md) |
| 10929 |20 |ID prostředku:% d. Minimální záruka% s je% d, maximální limit je% d a aktuální využití databáze je% d. Server je však v současnosti příliš zaneprázdněn, aby podporoval žádosti větší než% d pro tuto databázi. ID prostředku indikuje prostředek, který dosáhl limitu. Pro pracovní vlákna, ID prostředku = 1. Pro relace, ID prostředku = 2. Další informace naleznete v tématech: <br/>&bull;&nbsp; [Omezení prostředků logického SQL serveru](resource-limits-logical-server.md)<br/>&bull;&nbsp; [Omezení založené na DTU pro izolované databáze](service-tiers-dtu.md)<br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md) <br/>V opačném případě zkuste to prosím znovu později. |
| 40544 |20 |Databáze dosáhla kvóty velikosti. Můžete rozdělit nebo odstranit data, vyřadit indexy nebo si v dokumentaci vyhledat možná řešení. Informace o škálování databáze najdete v tématu [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).|
| 40549 |16 |Relace je ukončena, protože máte dlouhotrvající transakci. Zkuste zkrátit transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](../performance-improve-use-batching.md).|
| 40550 |16 |Relace byla ukončena, protože získala příliš mnoho zámků. Zkuste číst nebo upravit méně řádků v jedné transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](../performance-improve-use-batching.md).|
| 40551 |16 |Relace byla ukončena z důvodu nadměrného `TEMPDB` využití. Zkuste upravit dotaz, aby se snížilo využití místa na dočasné tabulce.<br/><br/>Pokud používáte dočasné objekty, můžete ušetřit místo v `TEMPDB` databázi tak, že vyřadíte dočasné objekty, které už relace nepotřebují. Další informace o využití databáze tempdb v SQL Database najdete v tématu [databáze tempdb v SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Relace byla ukončena z důvodu nadměrného využití místa v protokolu transakcí. Zkuste upravit menší počet řádků v jedné transakci. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](../performance-improve-use-batching.md).<br/><br/>Pokud provedete hromadné vložení pomocí `bcp.exe` nástroje nebo `System.Data.SqlClient.SqlBulkCopy` třídy, zkuste použít `-b batchsize` `BatchSize` Možnosti nebo a omezit tak počet zkopírovaných řádků na server v každé transakci. Při opakovaném sestavování indexu pomocí `ALTER INDEX` příkazu zkuste použít `REBUILD WITH ONLINE = ON` možnost. Informace o velikostech protokolů transakcí pro model nákupu vCore naleznete v tématu: <br/>&bull;&nbsp; [limity založené na Vcore pro jednotlivé databáze](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Omezení prostředků spravované instance Azure SQL](../managed-instance/resource-limits.md)|
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz pro zpracování méně řádků.<br/><br/>Snížení počtu `ORDER BY` `GROUP BY` operací a operací v kódu Transact-SQL snižuje požadavky na paměť v dotazu. Informace o škálování databáze najdete v tématu [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Chyby elastického fondu

Následující chyby souvisejí s vytvářením a používáním elastických fondů:

| Kód chyby | Závažnost | Popis | Nápravná opatření |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Elastický fond dosáhl svého limitu úložiště. Využití úložiště pro elastický fond nemůže přesáhnout (% d) MB. Došlo k pokusu o zápis dat do databáze, když bylo dosaženo limitu úložiště elastického fondu. Informace o omezeních prostředků najdete v těchto tématech: <br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md) <br/> |Pokud je to možné, zvažte zvýšení DTU a/nebo Přidání úložiště do elastického fondu, pokud je to možné, abyste zvýšili jeho limit úložiště, omezili jste úložiště používané jednotlivými databázemi v elastickém fondu nebo z elastického fondu odebíráte databáze. Škálování elastického fondu najdete v tématu [škálování prostředků elastického fondu](elastic-pool-scale.md).|
| 10929 | 16 |Minimální záruka% s je% d, maximální limit je% d a aktuální využití databáze je% d. Server je však v současnosti příliš zaneprázdněn, aby podporoval žádosti větší než% d pro tuto databázi. Informace o omezeních prostředků najdete v těchto tématech: <br/>&bull;&nbsp; [Omezení pro elastické fondy založené na DTU](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [omezení pro elastické fondy založené na Vcore](resource-limits-vcore-elastic-pools.md) <br/> V opačném případě zkuste to prosím znovu později. Minimální počet jednotek DTU/vCore na databázi; DTU/vCore Max na databázi. Celkový počet souběžných pracovních procesů (požadavků) napříč všemi databázemi v elastickém fondu se pokusil překročit limit fondu. |Pokud je to možné, můžete zvýšit DTU nebo virtuální jádra elastického fondu, pokud je to možné, aby se zvýšil jeho limit pracovního procesu, nebo odebrat databáze z elastického fondu. |
| 40844 | 16 |Databáze% ls na serveru% LS je databáze edice% LS v elastickém fondu a nemůže mít relaci průběžného kopírování.  |Není k dispozici |
| 40857 | 16 |Nenašel se elastický fond pro server:% ls, název elastického fondu:% ls. Zadaný elastický fond na zadaném serveru neexistuje. | Zadejte platný název elastického fondu. |
| 40858 | 16 |Elastický fond% LS už na serveru:% LS existuje. Zadaný elastický fond již na určeném serveru existuje. | Zadejte nový název elastického fondu. |
| 40859 | 16 |Elastický fond nepodporuje úroveň služby% ls. Zadaná úroveň služby není podporovaná pro zřizování elastického fondu. |Zadejte správnou edici nebo ponechte prázdnou vrstvu služby, aby používala výchozí úroveň služby. |
| 40860 | 16 |Kombinace elastického fondu% LS a cíle služby% ls není platná. Elastický fond a úroveň služby lze zadat společně pouze v případě, že je typ prostředku zadán jako ' ElasticPool '. |Zadejte správnou kombinaci elastického fondu a úrovně služeb. |
| 40861 | 16 |Edice databáze%. *ls se nesmí lišit od úrovně služby elastického fondu, která je%.* ls. Edice databáze se liší od úrovně služby elastického fondu. |Nezadávejte edici databáze, která se liší od úrovně služby elastického fondu.  Všimněte si, že není nutné zadávat edici databáze. |
| 40862 | 16 |Pokud je zadaný cíl služby elastického fondu, musí být zadaný název elastického fondu. Cíl služby elastického fondu neidentifikuje jednoznačně elastický fond. |Pokud používáte cíl služby elastického fondu, zadejte název elastického fondu. |
| 40864 | 16 |DTU pro elastický fond musí být minimálně (% d) DTU pro úroveň služby%. * ls. Probíhá pokus o nastavení DTU pro elastický fond pod minimálním limitem. |Opakujte nastavení DTU pro elastický fond alespoň na minimální limit. |
| 40865 | 16 |DTU pro elastický fond nemůže přesáhnout (% d) DTU pro úroveň služby%. * ls. Došlo k pokusu o nastavení DTU pro elastický fond nad rámec maximálního limitu. |Opakujte nastavení DTU pro elastický fond tak, aby nepřesahoval maximální limit. |
| 40867 | 16 |Maximální počet jednotek DTU na databázi musí být minimálně (% d) pro úroveň služby%. * ls. Byl proveden pokus o nastavení maximálního počtu jednotek DTU na databázi pod podporovaným limitem. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40868 | 16 |Maximální počet jednotek DTU na databázi nemůže být větší než (% d) pro úroveň služby%. * ls. Byl proveden pokus o nastavení maximálního počtu jednotek DTU na databázi nad rámec podporovaného limitu. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40870 | 16 |Minimální počet jednotek DTU na databázi nemůže být větší než (% d) pro úroveň služby%. * ls. Došlo k pokusu o nastavení minimální hodnoty DTU na databázi nad rámec podporovaného limitu. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40873 | 16 |Počet databází (% d) a minimální počet jednotek DTU na databázi (% d) nesmí překročit DTU elastického fondu (% d). Probíhá pokus o určení minimálního počtu DTU pro databáze v elastickém fondu, které přesahují DTU elastického fondu. | Zvažte zvýšení DTU elastického fondu nebo snižte minimální počet jednotek DTU na databázi nebo snižte počet databází v elastickém fondu. |
| 40877 | 16 |Elastický fond nelze odstranit pouze v případě, že neobsahuje žádné databáze. Elastický fond obsahuje jednu nebo více databází, a proto jej nelze odstranit. |Chcete-li odstranit databáze z elastického fondu, odstraňte je. |
| 40881 | 16 |Elastický fond%. * ls dosáhl svého limitu počtu databází.  Omezení počtu databází pro elastický fond nemůže překročit (% d) pro elastický fond s (% d) DTU. Došlo k pokusu o vytvoření nebo přidání databáze do elastického fondu, pokud bylo dosaženo limitu počtu databází elastického fondu. | Zvažte zvýšení DTU elastického fondu, pokud je to možné, aby se zvýšil limit databáze nebo odebraly databáze z elastického fondu. |
| 40889 | 16 |DTU nebo úložiště pro elastický fond%. * ls nejde snížit, protože by neposkytoval dostatek úložného prostoru pro své databáze. Probíhá pokus o snížení limitu úložiště elastického fondu pod využitím úložiště. | Zvažte snížení využití úložiště pro jednotlivé databáze v elastickém fondu nebo odebírání databází z fondu, aby se snížila velikost DTU nebo úložiště. |
| 40891 | 16 |Minimální počet jednotek DTU na databázi (% d) nemůže být vyšší než maximální počet jednotek DTU na databázi (% d). Došlo k pokusu o nastavení minimální hodnoty DTU na databázi vyšší než maximální počet jednotek DTU na databázi. |Zajistěte, aby minimální počet jednotek DTU na databázi nepřesáhl maximální hodnotu DTU na databázi. |
| Bude doplněno | 16 |Velikost úložiště pro jednotlivé databáze v elastickém fondu nemůže překročit maximální velikost povolenou pro elastický fond úrovně služby%. * ls. Maximální velikost databáze překračuje maximální velikost povolenou vrstvou služby elastického fondu. |Nastavte maximální velikost databáze v rámci omezení maximální velikosti povolené úrovní služby elastického fondu. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Databázi "Master" požadovanou v rámci přihlášení nelze otevřít. Přihlášení nebylo úspěšné.

K tomuto problému dochází, protože účet nemá oprávnění pro přístup k hlavní databázi. Ve výchozím nastavení se ale SQL Server Management Studio (SSMS) pokusí připojit k hlavní databázi.

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Na přihlašovací obrazovce SSMS vyberte **Možnosti**a pak vyberte **Vlastnosti připojení**.
2. V poli **připojit k databázi** zadejte výchozí název databáze uživatele jako výchozí přihlašovací databázi a pak vyberte **připojit**.

   ![Vlastnosti připojení](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Ověřte, jestli je chyba způsobená problémem připojení.

Chcete-li ověřit, zda je chyba způsobena problémem s připojením, zkontrolujte trasování zásobníku pro rámce, které ukazují volání pro otevření připojení, podobně jako následující (Poznamenejte si odkaz na třídu **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Pokud je výjimka aktivována při potížích s dotazem, všimnete si zásobníku volání, který je podobný následujícímu (Poznamenejte si odkaz na třídu **SqlCommand** ). V této situaci [budete ladit dotazy](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Další pokyny k vyladění výkonu najdete v následujících zdrojích informací:

- [Jak spravovat indexy a statistiky Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Ruční ladění výkonu dotazů v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Monitorování Azure SQL Database výkonu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Provozování úložiště dotazů v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Postup řešení běžných potíží s připojením

1. Zajistěte, aby byl na aplikačním serveru povolený protokol TCP/IP jako klientský protokol. Další informace najdete v tématu [Konfigurace klientských protokolů](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na aplikačních serverech, na kterých nemáte nainstalované nástroje SQL Tools, ověřte, že je povolený protokol TCP/IP, a to spuštěním **cliconfg.exe** (SQL Server nástroj klientské sítě).
2. Zkontrolujte připojovací řetězec aplikace, abyste se ujistili, že je správně nakonfigurovaný. Ujistěte se například, že připojovací řetězec Určuje správný port (1433) a plně kvalifikovaný název serveru.
Viz [získat informace o připojení](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Zkuste zvýšit hodnotu časového limitu připojení. Doporučujeme použít časový limit připojení aspoň 30 sekund.
4. Otestujte připojení mezi aplikačním serverem a Azure SQL Database pomocí [nástroje SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), souboru UDL, příkazem testu a protokolu Telnet. Další informace najdete v tématu [Poradce při potížích s připojením](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) a [diagnostiku](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)problémů s připojením.

   > [!NOTE]
   > V rámci kroků pro řešení potíží můžete také otestovat připojení na jiném klientském počítači.

5. Jako osvědčený postup se ujistěte, že je nastavená logika opakování. Další informace o logice opakování najdete v tématu [řešení přechodných chyb a chyb připojení k SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Pokud tyto kroky problém nevyřeší, zkuste shromáždit více dat a pak se obraťte na podporu. Pokud je vaše aplikace cloudová služba, povolte protokolování. Tento krok vrátí časové razítko UTC selhání. Kromě toho SQL Database vrátí ID trasování. Tyto informace mohou využívat [služby zákaznické podpory společnosti Microsoft](https://azure.microsoft.com/support/options/) .

Další informace o tom, jak povolit protokolování, najdete v tématu [Povolení protokolování diagnostiky pro aplikace v Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Další kroky

- [Architektura připojení Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
- [Azure SQL Database a Azure synapse Analytics – ovládací prvky přístupu k síti](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
