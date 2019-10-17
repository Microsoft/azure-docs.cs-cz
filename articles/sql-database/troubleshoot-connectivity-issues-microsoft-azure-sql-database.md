---
title: Řešení potíží s připojením pomocí Microsoft Azure SQL Database | Microsoft Docs
description: Popisuje řešení potíží s připojením v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 9de6d85e1fc54d60f999cfa18665067b3998a432
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390663"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Řešení potíží s připojením pomocí Microsoft Azure SQL Database

Pokud připojení ke službě Azure SQL Database selže, zobrazí se chybové zprávy. Tyto problémy s připojením mohou být způsobeny SQL Azure rekonfigurace databáze, nastavením brány firewall, časovým limitem připojení nebo nesprávnými přihlašovacími informacemi. Pokud se navíc dosáhne maximálního limitu u některých prostředků Azure SQL Database, nemůžete se připojit k Azure SQL Database.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Chyba 40613: < > databáze na serveru < a > není aktuálně k dispozici.

**Podrobná chyba**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Řešení tohoto problému:

1. Podívejte se na [řídicí panel služby Microsoft Azure](https://status.azure.com/status) pro všechny známé výpadky. 
2. Pokud neexistují žádné známé výpadky, otevřete na [webu podpory Microsoft Azure](http://azure.microsoft.com/support/options) , kde otevřete případ podpory.

Další informace najdete v tématu [řešení potíží s informacemi o tom, že databáze na serveru není aktuálně k dispozici](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Při navazování připojení k SQL Server došlo k chybě související se sítí nebo instanci.

K tomuto problému dochází, pokud se aplikace nemůže připojit k serveru.

Pokud chcete tento problém vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Server nebyl nalezen nebo nebyl přístupný (chyba 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Chyba 26: Chyba při hledání zadaného serveru nebo instance

**Podrobná chyba**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Chyba 40: Nelze otevřít připojení k SQL Server

**Podrobná chyba**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Chyba 10053: při přijímání výsledků ze serveru došlo k chybě na úrovni přenosu.

**Podrobná chyba**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

K těmto chybám dochází, protože se aplikace nemůže připojit k serveru.

Pokud chcete tento problém vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Nejde se připojit k <servername> kvůli problémům s bránou firewall.

### <a name="error-40615-cannot-connect-to--servername-"></a>Chyba 40615: Nelze se připojit k serveru < servername >

Chcete-li tento problém vyřešit, [nakonfigurujte nastavení brány firewall na SQL Database prostřednictvím Azure Portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Chyba 5: Nelze se připojit k serveru < servername >

Pokud chcete tento problém vyřešit, ujistěte se, že je port 1433 otevřený pro odchozí připojení na všech branách firewall mezi klientem a internetem.

Další informace najdete v tématu [Konfigurace brány Windows Firewall tak, aby povolovala přístup k SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nepovedlo se přihlásit k serveru (chyby 18456, 40531).

### <a name="login-failed-for-user--user-name-"></a>Přihlášení uživatele < uživatelské jméno > se nezdařilo.

**Podrobná chyba**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Pokud chcete tento problém vyřešit, požádejte správce služby, aby vám poskytl platné SQL Server uživatelské jméno a heslo.

Správce služby obvykle může pomocí následujících kroků přidat přihlašovací údaje pro přihlášení:

1. Přihlaste se k serveru pomocí SQL Server Management Studio (SSMS).
2. Spusťte následující dotaz SQL ke kontrole, jestli je přihlašovací jméno zakázané:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Pokud odpovídající jméno je zakázané, povolte ho pomocí následujícího příkazu: 

   ```
   Alter login <User name> enable
   ```

4. Pokud uživatelské jméno přihlášení SQL neexistuje, vytvořte ho pomocí následujících kroků:

   1. V SSMS dvakrát klikněte na **zabezpečení** a rozbalte ho. 
   2. Pravým tlačítkem klikněte na **Přihlášení** a potom vyberte **Nové přihlášení**. 
   3. Ve vygenerovaném skriptu pomocí zástupných symbolů upravte a spusťte následující dotaz SQL:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Dvakrát klikněte na **Databáze**. 
6. Vyberte databázi, ke které chcete udělit oprávnění uživatele.
7. Dvakrát klikněte na **Zabezpečení**. 
8. Pravým tlačítkem klikněte na **Uživatelé** a potom vyberte **Nový uživatel**. 
9. Ve vygenerovaném skriptu pomocí zástupných symbolů upravte a spusťte následující dotaz SQL: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > K namapování konkrétních uživatelů na konkrétní databázové role můžete také použít `sp_addrolemember`.

Další informace najdete v tématu [Správa databází a přihlášení v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Vypršení časového limitu připojení – chyby

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): vypršel časový limit připojení.

**Podrobná chyba**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): vypršel časový limit.

**Podrobná chyba**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. entity. Core. EntityException: došlo k chybě základního poskytovatele při otevření.

**Podrobná chyba**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nelze se připojit k názvu < serveru >

**Podrobná chyba**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

K těmto výjimkám může dojít buď kvůli problémům s připojením nebo dotazům. Pokud se chcete ujistit, že k této chybě dochází v důsledku potíží s připojením, přečtěte si téma [ověření, jestli chyba způsobila problém s připojením](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

K vypršení časového limitu připojení dojde, protože se aplikace nemůže připojit k serveru. Pokud chcete tento problém vyřešit, zkuste postupovat podle kroků (v uvedeném pořadí) v části [Postup řešení běžných potíží s připojením](#steps-to-fix-common-connection-issues) .

## <a name="transient-errors-errors-40197-40545"></a>Přechodné chyby (chyby 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Chyba 40197: služba zjistila chybu při zpracování vaší žádosti. Zkuste to prosím znovu. Kód chyby < kód >

K tomuto problému dochází kvůli přechodné chybě při změně konfigurace nebo převzetí služeb při selhání na back-endu.

Pokud chcete tento problém vyřešit, počkejte krátkou dobu a zkuste to znovu. Není vyžadován případ podpory, pokud problém přetrvává.

Jako osvědčený postup se ujistěte, že je nastavená logika opakování. Další informace o logice opakování naleznete v tématu [řešení přechodných chyb a chyb připojení k SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>Připojení bylo ukončeno kvůli omezením definovanému systémem.

### <a name="error-10928-resource-id-d"></a>Chyba 10928: ID prostředku:% d

**Podrobná chyba**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Pokud chcete tento problém obejít, zkuste jednu z následujících metod:

* Ověřte, zda existují dlouho běžící dotazy.

  > [!NOTE]
  > Jedná se o minimalist přístup, který nemusí problém vyřešit.

  1. Spusťte následující dotaz SQL pro kontrolu zobrazení [Sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , abyste viděli všechny blokující požadavky:

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. Určete **vstupní vyrovnávací paměť** pro blokování Headers.
  3. Nalaďte dotaz na blokování hlav.

    Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Pokud databáze konzistentně dosáhne svého limitu bez ohledu na blokující a dlouhotrvající dotazy, zvažte upgrade na jednu z nových verzí verze Preview (například [edice Standard nebo Premium](https://azure.microsoft.com/pricing/details/sql-database/)).

Další informace o možnostech cenového SQL Database najdete v tématu [Azure SQL Database ceny](https://azure.microsoft.com/pricing/details/sql-database/single/).

Další informace o zobrazeních dynamické správy najdete v tématu [zobrazení dynamické správy systému](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Další informace o této chybové zprávě najdete v tématu [SQL Database omezení prostředků pro Azure SQL Database Server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Chyba 10929: ID prostředku: 1

**Podrobná chyba**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Další informace o této chybě naleznete v tématu [chybové zprávy pro SQL Database klientské programy](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>Chyba 40501: služba je momentálně zaneprázdněná.

**Podrobná chyba**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Toto je chyba omezování modulu, což znamená, že se překročila omezení prostředků.

Další informace o omezeních prostředků najdete v tématu [omezení prostředků databázového serveru](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Chyba 40544: databáze dosáhla kvóty velikosti

**Podrobná chyba**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

K této chybě dochází, pokud databáze dosáhla kvóty velikosti.

Následující kroky vám pomůžou vyřešit problém nebo vám poskytnou další možnosti:

1. Ověřte aktuální velikost databáze pomocí řídicího panelu v Azure Portal.

   > [!NOTE]
   > Chcete-li zjistit, které tabulky spotřebovávají nejvíce místa a jsou proto potenciálními kandidáty pro vyčištění, spusťte následující dotaz SQL:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
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

**Podrobná chyba**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Při opakovaném výskytu této chyby zkuste problém vyřešit pomocí následujících kroků: 

1. Podívejte se na zobrazení sys. DM _exec_requests a zobrazte všechny otevřené relace, které mají pro sloupec total_elapsed_time vysokou hodnotu. Tuto kontrolu proveďte spuštěním následujícího skriptu SQL:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Určete vstupní vyrovnávací paměť pro dlouho běžící dotaz. 
3. Nalaďte dotaz.

Zvažte také dávkování dotazů. Informace o dávkovém zpracování najdete v tématu [použití dávkového zpracování ke zlepšení výkonu aplikace SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Chyba 40551: relace byla ukončena z důvodu nadměrného využití databáze TEMPDB

**Podrobná chyba**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Pokud chcete tento problém obejít, postupujte podle těchto kroků:

1. Změňte dotazy tak, aby se snížilo využití místa v dočasné tabulce. 
2. Vyřaďte dočasné objekty, které už nejsou potřeba. 
3. Zkraťte tabulky nebo odeberte nepoužité tabulky.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Chyba 40552: relace byla ukončena z důvodu nadměrného využití místa v protokolu transakcí.

**Podrobná chyba**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Při řešení tohoto problému zkuste použít následující metody:

* K tomuto problému může dojít z důvodu operací vložení, aktualizace nebo odstranění. Pokuste se snížit počet řádků, které jsou okamžitě provozovány implementací dávkování nebo rozdělení do několika menších transakcí.
* K tomuto problému může dojít kvůli operacím opětovného sestavení indexu. Pokud chcete tento problém obejít, ujistěte se, že počet řádků, které jsou v tabulce ovlivněny * (Průměrná velikost pole, které je aktualizované v bajtech + 80) < 2 gigabajty (GB).

  > [!NOTE]
  > V případě opětovného sestavení indexu by měla být průměrná velikost aktualizovaného pole nahrazena průměrnou velikostí indexu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Chyba 40553: relace byla ukončena z důvodu nadměrného využití paměti

**Podrobná chyba**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Pokud chcete tento problém obejít, zkuste optimalizovat dotaz.

Podrobný postup pro řešení potíží najdete v tématu [je můj dotaz běží v cloudu dobře?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Databázi "Master" požadovanou v rámci přihlášení nelze otevřít. Přihlášení se nezdařilo.

K tomuto problému dochází, protože účet nemá oprávnění pro přístup k hlavní databázi. Ve výchozím nastavení se ale SQL Server Management Studio (SSMS) pokusí připojit k hlavní databázi.

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Na přihlašovací obrazovce SSMS vyberte **Možnosti**a pak vyberte **Vlastnosti připojení**. 
2. V poli **připojit k databázi** zadejte výchozí název databáze uživatele jako výchozí přihlašovací databázi a pak vyberte **připojit**.

   ![Vlastnosti připojení](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Ověřte, jestli je chyba způsobená problémem připojení.

Chcete-li ověřit, zda je chyba způsobena problémem s připojením, zkontrolujte trasování zásobníku pro rámce, které ukazují volání pro otevření připojení, podobně jako následující (Poznamenejte si odkaz na třídu **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Pokud je výjimka aktivována při potížích s dotazem, všimnete si zásobníku volání, který je podobný následujícímu (Poznamenejte si odkaz na třídu **SqlCommand** ). V této situaci [budete ladit dotazy](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Další pokyny k vyladění výkonu najdete v následujících zdrojích informací:

* [Jak spravovat indexy a statistiky Azure SQL](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ruční ladění výkonu dotazů v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitorování Azure SQL Database výkonu pomocí zobrazení dynamické správy](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Provozování úložiště dotazů v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Postup řešení běžných potíží s připojením

1. Zajistěte, aby byl na aplikačním serveru povolený protokol TCP/IP jako klientský protokol. Další informace najdete v tématu [Konfigurace klientských protokolů](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na aplikačních serverech, na kterých nemáte nainstalované nástroje SQL Server, ověřte, že je povolený protokol TCP/IP, a to spuštěním **souboru Cliconfg. exe** (SQL Server nástroj klientské sítě). 
2. Zkontrolujte připojovací řetězec aplikace, abyste se ujistili, že je správně nakonfigurovaný. Ujistěte se například, že připojovací řetězec Určuje správný port (1433) a plně kvalifikovaný název serveru.
Viz [získat informace o připojení SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Zkuste zvýšit hodnotu časového limitu připojení. Doporučujeme použít časový limit připojení aspoň 30 sekund. 
4. Otestujte připojení mezi aplikačním serverem a databází SQL Azure pomocí [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), souboru UDL, příkazem příkazového testu nebo Telnet. Další informace najdete v tématu [Poradce při potížích s SQL Server problémy s připojením](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) a [diagnostikou pro problémy s připojením](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > V rámci kroků pro řešení potíží můžete také otestovat připojení na jiném klientském počítači.

5. Jako osvědčený postup se ujistěte, že je nastavená logika opakování. Další informace o logice opakování najdete v tématu [řešení přechodných chyb a chyb připojení k SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Pokud tyto kroky problém nevyřeší, zkuste shromáždit více dat a pak se obraťte na podporu. Pokud je vaše aplikace cloudová služba, povolte protokolování. Tento krok vrátí časové razítko UTC selhání. Kromě toho SQL Azure vrátí ID trasování. Tyto informace mohou využívat [služby zákaznické podpory společnosti Microsoft](http://azure.microsoft.com/support/options/) . 

Další informace o tom, jak povolit protokolování, najdete v tématu [Povolení protokolování diagnostiky pro aplikace v Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Související dokumenty**

* [Architektura připojení Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Azure SQL Database a řízení přístupu k síti v datovém skladu](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
