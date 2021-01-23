---
title: Pochopení a řešení problémů s blokováním Azure SQL
titleSuffix: Azure SQL Database
description: Přehled témat specifických pro službu Azure SQL Database o blokování a odstraňování potíží.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 1/14/2020
ms.openlocfilehash: b73e72969a851428034499d447ecb162a61aa9ab
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725782"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Pochopení a řešení potíží s blokováním Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Cíl

Článek popisuje blokování v databázích SQL Azure a ukazuje, jak řešit problémy a řešit blokování. 

V tomto článku se termín připojení vztahuje na jednu přihlášenou relaci databáze. Každé připojení se zobrazí jako ID relace (SPID) nebo session_id v mnoha zobrazení dynamické správy. Každý z těchto SPIDs se často označuje jako proces, přestože to není samostatný kontext procesu v obvyklém smyslu. Místo toho se každé SPID skládá z prostředků serveru a datových struktur nezbytných pro obsluhu požadavků jednoho připojení z daného klienta. Jedna klientská aplikace může mít jedno nebo víc připojení. Z perspektivy Azure SQL Database neexistuje žádný rozdíl mezi několika připojeními z jedné klientské aplikace na jednom klientském počítači a více připojeními z více klientských aplikací nebo více klientských počítačů. jsou atomické. Jedno připojení může blokovat jiné připojení, bez ohledu na zdrojového klienta.

> [!NOTE]
> **Tento obsah je určený pro Azure SQL Database.** Azure SQL Database vychází z nejnovější stabilní verze databázového stroje Microsoft SQL Server, takže většina obsahu je podobná, i když se možnosti řešení potíží a nástroje můžou lišit. Další informace o blokování v SQL Server najdete v tématu [vysvětlení a řešení potíží se zablokováním SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Pochopení blokování 
 
Blokování je nenevyhnutelná a návrhová charakteristika systému pro správu relačních databází (RDBMS) s souběžnou korelací založenou na zamykání. Jak už bylo uvedeno výše, v SQL Server se blokování vyskytne, když jedna relace drží zámek na konkrétním prostředku a druhý identifikátor SPID se pokusí získat konfliktní typ zámku na stejném prostředku. Obvykle je časový rámec, pro který je u prvního identifikátoru SPID uzamčen prostředek malý. Když vlastnící relace uvolní zámek, druhé připojení se pak uvolní a získá svůj vlastní zámek na prostředku a pokračuje ve zpracování. Jedná se o běžné chování a v průběhu dne může probíhat mnohokrát a bez znatelného dopadu na výkon systému.

Doba trvání a transakční kontext dotazu určují, jak dlouho jsou zámky uchovávány, a tedy jejich vliv na jiné dotazy. Pokud dotaz není proveden v rámci transakce (a nejsou použity žádné pomocné parametry zámku), budou zámky pro příkazy SELECT uchovávány pouze v okamžiku, kdy je ve skutečnosti čtena, nikoli během dotazu. V případě příkazů INSERT, UPDATE a DELETE jsou zámky uchovávány během dotazu, jak pro konzistenci dat, tak i v případě potřeby i k tomu, aby byl dotaz vrácen zpět.

Pro dotazy spouštěné v rámci transakce je doba trvání, pro kterou jsou zámky uchovávány, určeny typem dotazu, úrovní izolace transakce a zda jsou v dotazu použity pomocné parametry zámku. Popis uzamykání, pomocných parametrů zámku a úrovní izolace transakcí najdete v následujících článcích:

* [Uzamykání v databázovém stroji](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Přizpůsobení uzamykání a správy verzí řádků](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Režimy zámku](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Kompatibilita zámku](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transakce](/sql/t-sql/language-elements/transactions-transact-sql)

Když blokování a blokování brání v bodě, kde dochází k škodlivému vlivu na výkon systému, je z některého z následujících důvodů:

* SPID uchovává v sadě prostředků po delší dobu zámky, než je uvolní. Tento typ blokujícího překladu se v průběhu času vyřeší, ale může způsobit snížení výkonu.

* Identifikátor SPID uchovává v sadě prostředků zámky a nikdy je neuvolňuje. Tento typ blokování se nevyřešil sám a brání v přístupu k ovlivněným prostředkům po neomezenou dobu.

V prvním scénáři může být tato situace velmi kapalinová, protože jiné SPIDs způsobí, že se v průběhu času blokují na různé prostředky a vytvoří se cíl přesunutí. Tyto situace se obtížně odstraňují při použití [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) k zúžení problému na jednotlivé dotazy. Naproti tomu druhá situace má za následek konzistentní stav, který je možné snadněji diagnostikovat.

## <a name="applications-and-blocking"></a>Aplikace a blokování

V případě blokujícího problému může být zaostření na vyladění a problémy platforem na straně serveru. Nicméně pozornost vyplácená pouze pro databázi nemusí vést k vyřešení a může zvýšit čas a spotřebu energie lépe směrované při kontrole klientské aplikace a dotazů, které odesílá. Bez ohledu na to, jakou úroveň viditelnosti aplikace zveřejňuje, je problém blokující často nutný pro kontrolu přesných příkazů SQL odeslaných aplikací a přesné chování aplikace ohledně zrušení dotazů, správy připojení, načítání všech řádků výsledků a tak dále. Pokud vývojový nástroj nepovoluje explicitní kontrolu nad správou připojení, zrušením dotazu, vypršením časového limitu dotazu, načtením výsledků a tak dále, nemusí být možné problémy s blokováním přeložit. Tento potenciál by měl být pečlivě vyšetřen před výběrem nástroje pro vývoj aplikací pro Azure SQL Database, zejména pro OLTP prostředí s citlivým výkonem. 

Věnujte pozornost výkonu databáze během fáze navrhování a konstrukce databáze a aplikace. Konkrétně by se měla vyhodnotit spotřeba prostředků, úroveň izolace a délka cesty transakce pro každý dotaz. Každý dotaz a transakce by měly být co nejjednodušší. Je nutné, aby se v souvislosti s řádným oborem správy připojení mohl jednat o přijatelný výkon s nízkým počtem uživatelů, ale výkon může být výrazně snížený, protože počet uživatelů se škáluje směrem nahoru. 

Díky správnému návrhu aplikací a dotazů je Azure SQL Database schopný podporovat mnoho tisíc současných uživatelů na jednom serveru, a to s malým blokováním.

> [!Note]
> Další pokyny k vývoji aplikací najdete v tématu [řešení potíží s připojením a dalších chyb pomocí Azure SQL Database a spravované instance Azure SQL](troubleshoot-common-errors-issues.md) a [zpracování přechodných chyb](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Řešení potíží s blokováním

Bez ohledu na to, která blokující situaci jsou v systému, je tato metodika pro uzamykání řešení potíží stejná. Tato logická oddělení představují zbývající část složení tohoto článku. Pojmem je vyhledání blokování hlav a určení toho, co tento dotaz dělá a proč se blokuje. Jakmile se zjistí problematický dotaz (to znamená, co zachovává zámky po dlouhou dobu), bude dalším krokem analýza a určení, proč k blokování dochází. Až porozumíme tomu, proč můžeme provést změny přenavrhováním dotazu a transakce.

Postup řešení potíží:

1. Identifikujte hlavní blokující relaci (blokování hlav).

2. Vyhledejte dotaz a transakci, které způsobují blokování (co je drží zámky pro prodloužené období).

3. Analyzovat/pochopit, proč dochází k prodlouženému blokování

4. Řešení blokujícího problému přenavrhováním dotazu a transakce

Teď se podrobně na diskuzi, jak určit hlavní blokující relaci s odpovídajícím zachycením dat.

## <a name="gather-blocking-information"></a>Shromáždit informace o blokování

Aby bylo možné řešit potíže s blokujícími problémy, může správce databáze používat skripty SQL, které neustále monitorují stav uzamykání a blokování v databázi SQL Azure. Pro shromáždění těchto dat existují v podstatě dvě metody. 

První je dotazování dynamické správy objektů (DMOs) a uložení výsledků pro porovnání v průběhu času. Některé objekty, na které se odkazuje v tomto článku, jsou zobrazení dynamické správy (zobrazení dynamické správy) a některé jsou funkce dynamické správy (DMFs). Druhou metodou je použití XEvents k zachycení toho, co je prováděno. 


## <a name="gather-information-from-dmvs"></a>Shromažďování informací z zobrazení dynamické správy

Odkazování na zobrazení dynamické správy na blokování řešení potíží má za cíl určení identifikátoru SPID (ID relace) na začátku blokujícího řetězu a příkazu SQL. Vyhledejte SPIDs oběti, které jsou zablokované. Pokud je jakékoli SPID zablokované jiným identifikátorem SPID, pak Prozkoumejte SPID vlastnící prostředek (blokující identifikátor SPID). Je toto SPID taky blokované? Můžete procházet řetěz a vyhledat blokování hlav a prozkoumat, proč zachovává jeho zámek.

Nezapomeňte spustit všechny tyto skripty v cílové službě Azure SQL Database.

* Příkazy sp_who a sp_who2 jsou starší příkazy pro zobrazení všech aktuálních relací. DMV sys.dm_exec_sessions vrací více dat v sadě výsledků dotazu, která je snazší dotazování a filtrování. Najdete sys.dm_exec_sessions v základních dalších dotazech. 

* Pokud již máte určitou konkrétní relaci identifikovanou, můžete použít `DBCC INPUTBUFFER(<session_id>)` k vyhledání posledního příkazu, který byl odeslán relací. Podobné výsledky mohou být vráceny pomocí funkce sys.dm_exec_input_buffer Dynamic Management (DMF) v sadě výsledků dotazu, která je snazší dotazovat a filtrovat, a poskytuje session_id a request_id. Například pro vrácení nejnovějšího dotazu odeslaného session_id 66 a request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Podívejte se na sys.dm_exec_requests a odkázat na sloupec blocking_session_id. Když blocking_session_id = 0, relace není blokovaná. I když sys.dm_exec_requests zobrazí jenom aktuálně prováděné požadavky, bude v sys.dm_exec_sessions uvedeno jakékoli připojení (aktivní nebo ne). Sestavujte na tomto společném spojení mezi sys.dm_exec_requests a sys.dm_exec_sessions v dalším dotazu.

* Spusťte tento ukázkový dotaz pro vyhledání aktivně spouštěných dotazů a jejich aktuálního textu v dávce SQL nebo vstupní vyrovnávací paměti pomocí [Sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) nebo [Sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) zobrazení dynamické správy. Pokud jsou data vrácená `text` polem sys.DM_EXEC_SQL_TEXT null, dotaz se aktuálně neprovádí. V takovém případě `event_info` bude pole sys.dm_exec_input_buffer obsahovat poslední řetězec příkazu předaný modulu SQL Engine. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Pro zachycení dlouhotrvajících nebo nepotvrzených transakcí použijte další sadu zobrazení dynamické správy pro zobrazení aktuálních otevřených transakcí, včetně [Sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [Sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [Sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)a sys.dm_exec_sql_text. K dispozici je několik zobrazení dynamické správy přidružených k sledovacím transakcím, další informace najdete v tématu další [zobrazení dynamické správy o transakcích](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) . 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Referenční [Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) , která je na vrstvě vlákna nebo úlohy SQL. Tím se vrátí informace o typu čekání SQL, na kterých se v tuto chvíli nachází požadavek. Stejně jako sys.dm_exec_requests jsou sys.dm_os_waiting_tasks vráceny pouze aktivní žádosti. 

> [!Note]
> Další informace o typech čekání, včetně agregovaných čekacích statistik v čase, najdete v [Sys.DM_DB_WAIT_STATS](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)DMV. Tato DMV vrací součty agregačních čekání pouze pro aktuální databázi.

* Podrobnější informace o tom, jaké zámky umístily do dotazů, najdete v [Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV. Tato DMV může vracet velké objemy dat v produkčním SQL Server a je užitečná pro diagnostiku, které zámky jsou aktuálně drženy. 

Kvůli VNITŘNÍmu spojení v sys.dm_os_waiting_tasks následující dotaz omezí výstup z sys.dm_tran_locks jenom na aktuálně Blokované požadavky, jejich stav čekání a jejich zámky:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* V případě zobrazení dynamické správy ukládání výsledků dotazu v průběhu času bude poskytovat datové body, které vám umožní zkontrolovat blokování v zadaném časovém intervalu, aby bylo možné identifikovat trvalé blokování nebo trendy. 

## <a name="gather-information-from-extended-events"></a>Shromažďování informací z rozšířených událostí

Kromě výše uvedených informací je často nutné zachytit trasování aktivit na serveru, aby bylo možné důkladně prozkoumat problém blokující Azure SQL Database. Například pokud relace spustí více příkazů v rámci transakce, bude zastoupen pouze poslední příkaz, který byl odeslán. Jeden z předchozích příkazů však může být v případě, že zámky jsou stále uchovávány. Trasování vám umožní zobrazit všechny příkazy spouštěné relací v rámci aktuální transakce.

Existují dva způsoby, jak zachytit trasování v SQL Server. Rozšířené události (XEvents) a trasování profileru. [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) je ale už Nepodporovaná technologie trasování, která se Azure SQL Database nepodporuje. [Rozšířené události](/sql/relational-databases/extended-events/extended-events) jsou novější technologie trasování, která umožňuje větší univerzálnost a menší dopad na zjištěný systém a jeho rozhraní je integrováno do SQL Server Management Studio (SSMS). 

Přečtěte si dokument, který vysvětluje, jak používat [Průvodce vytvořením nové relace rozšířených událostí](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) v SSMS. Pro databáze SQL Azure ale SSMS poskytuje podsložku rozšířené události v každé databázi v Průzkumník objektů. K zachycení těchto užitečných událostí použijte Průvodce relací rozšířených událostí: 

-   Chyby kategorií:
    -   Upozorňuje
    -   Error_reported  
    -   Execution_warning

-   Upozornění kategorie: 
    -   Missing_join_predicate

-   Spuštění kategorie:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Zamknout
    -   Lock_deadlock

-   Relace
    -   Existing_connection
    -   Přihlásit
    -   Odhlásit

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identifikace a řešení běžných blokujících scénářů

Prozkoumáním výše uvedených informací můžete určit příčinu většiny potíží s blokováním. Zbytek tohoto článku je diskuze o tom, jak tyto informace použít k identifikaci a řešení některých běžných scénářů blokování. Tato diskuze předpokládá, že jste použili blokující skripty (odkazované dříve) k zachycení informací o blokujících SPIDs a zachytili aktivitu aplikace pomocí relace XEvent.

## <a name="analyze-blocking-data"></a>Analyzovat blokující data 

* Zkontrolujte výstup sys.dm_exec_requests zobrazení dynamické správy a sys.dm_exec_sessions, abyste určili hlavice blokujících řetězců pomocí blocking_these a session_id. Tím se jasně identifikují Blokované požadavky a blokují se. Přečtěte si další podrobnosti o blokovaných a blokujících relacích. Existuje pro blokující řetězec společný nebo kořenový adresář? Budou nejspíš sdílet společnou tabulku a jedna nebo víc relací zapojených do blokujícího řetězce provádí operaci zápisu. 

* Prohlédněte si výstup sys.dm_exec_requests zobrazení dynamické správy a sys.dm_exec_sessions informace o SPIDs na začátku blokujícího řetězce. Vyhledejte následující pole: 

    -    `sys.dm_exec_requests.status`  
    V tomto sloupci se zobrazuje stav konkrétního požadavku. Stav spánku obvykle označuje, že bylo dokončeno provedení a čeká, než aplikace odešle jiný dotaz nebo dávku. Stav spustitelný nebo spuštěno znamená, že SPID aktuálně zpracovává dotaz. Následující tabulka poskytuje stručné vysvětlení různých hodnot stavu.

    | Status | Význam |
    |:-|:-|
    | Pozadí | SPID spouští úlohu na pozadí, jako je například zjišťování zablokování, zapisovač protokolů nebo kontrolní bod. |
    | Režimu spánku | Identifikátor SPID aktuálně není spuštěn. To obvykle znamená, že SPID očekává příkaz z aplikace. |
    | Spuštěno | Identifikátor SPID je aktuálně spuštěn v plánovači. |
    | Spustitelný | Identifikátor SPID je ve frontě spustitelný Scheduleru a čeká na získání času plánovače. |
    | Dočasně blokován. | SPID čeká na určitý prostředek, jako je třeba zámek nebo západ. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    V tomto poli se dozvíte, kolik otevřených transakcí v této relaci. Pokud je tato hodnota větší než 0, je identifikátor SPID v otevřené transakci a může být držen zámky získané jakýmkoli příkazem v rámci transakce.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Podobně v tomto poli se dozvíte, kolik otevřených transakcí v této žádosti. Pokud je tato hodnota větší než 0, je identifikátor SPID v otevřené transakci a může být držen zámky získané jakýmkoli příkazem v rámci transakce.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` a `last_wait_type`  
    Pokud  `sys.dm_exec_requests.wait_type`   je hodnota null, požadavek aktuálně nečeká na cokoli a  `last_wait_type`   hodnota označuje poslední  `wait_type`   nalezený požadavek. Další informace o  `sys.dm_os_wait_stats` a popis nejběžnějších typů čekání naleznete v tématu [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).  `wait_time`   Hodnotu lze použít k určení, zda požadavek probíhá. Pokud dotaz proti sys.dm_exec_requests tabulce vrátí hodnotu ve  `wait_time`   sloupci, který je menší než  `wait_time`   hodnota z předchozího dotazu sys.dm_exec_requests, znamená to, že předchozí zámek byl získán a vydán a nyní čeká na nové uzamčení (předpokládá se, že se nejedná o nulu `wait_time` ). To lze ověřit porovnáním `wait_resource`   výstupu sys.dm_exec_requests, který zobrazuje prostředek, pro který požadavek čeká.

    -   `sys.dm_exec_requests.wait_resource` Toto pole indikuje prostředek, na kterém čeká blokovaný požadavek. V následující tabulce jsou uvedeny běžné  `wait_resource`   formáty a jejich význam:

    | Prostředek | Formát | Příklad | Vysvětlení | 
    |:-|:-|:-|:-|
    | Tabulka | DatabaseID: ObjectID: IndexID | KARTA: 5:261575970:1 | V tomto případě je databáze s ID 5 ukázkovou databází pubs a ID objektu 261575970 je tabulka názvů a 1 je clusterovaný index. |
    | Stránka | DatabaseID: FileID: PageID | STRÁNKA: 5:1:104 | V tomto případě je databáze s ID 5 typu pubs, soubor s ID 1 je primárním datovým souborem a stránka 104 je stránka patřící do tabulky titles. Chcete-li identifikovat object_id, do které stránka patří, použijte funkci dynamické správy [Sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)a předejte DatabaseID, FileID, pageid z `wait_resource` . | 
    | Klíč | DatabaseID: Hobt_id (hodnota hash pro klíč indexu) | KLÍČ: 5:72057594044284928 (3300a4f361aa) | V tomto případě je databáze s ID 5 pubs, Hobt_ID 72057594044284928 odpovídá index_id 2 pro object_id 261575970 (tabulka tituly). Pomocí zobrazení katalogu sys. partitions přidružte hobt_id ke konkrétnímu index_id a object_id. Neexistuje žádný způsob, jak vyhodnotit hodnotu hash klíče indexu na hodnotu konkrétního klíče. |
    | Řádek | DatabaseID: FileID: PageID: slot (řádek) | IDENTIFIKÁTOR RID: 5:1:104:3 | V tomto případě je databáze s ID 5 typu pubs, soubor s ID 1 je primárním datovým souborem, stránka 104 je stránka patřící do tabulky titles a pozice 3 označuje pozici řádku na stránce. |
    | Sestavení  | DatabaseID: FileID: PageID: slot (řádek) | IDENTIFIKÁTOR RID: 5:1:104:3 | V tomto případě je databáze s ID 5 typu pubs, soubor s ID 1 je primárním datovým souborem, stránka 104 je stránka patřící do tabulky titles a pozice 3 označuje pozici řádku na stránce. |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV obsahuje data o otevřených transakcích, které lze připojit k jiným zobrazení dynamické správy pro úplný přehled transakcí čekajících na potvrzení nebo vrácení zpět. Pomocí následujícího dotazu můžete vracet informace o otevřených transakcích, které jsou připojené k ostatním zobrazení dynamické správy, včetně [Sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Vezměte v úvahu aktuální stav transakce, `transaction_begin_time` a další údaje o stavu, abyste vyhodnotili, zda se jedná o zdroj blokování.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Jiné sloupce

        Zbývající sloupce v [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) a [Sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) mohou poskytnout přehled o kořeni problému i. Jejich užitečnost se liší v závislosti na okolnostech problému. Například můžete určit, jestli problém při použití v sys.dm_exec_requests, a tak dále, v některých síťových knihovnách (net_library), pokud je poslední dávka odeslaná pomocí SPID `last_request_start_time` v sys.dm_exec_sessions, jak dlouho byla žádost spuštěná `start_time` v atd.


## <a name="common-blocking-scenarios"></a>Běžné scénáře blokování

Tabulka níže mapuje běžné příznaky na jejich pravděpodobné příčiny.  

`wait_type`Sloupce, `open_transaction_count` a `status` odkazují na informace vrácené [Sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), jiné sloupce mohou být vráceny [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). "Překládám?" sloupec označuje, zda se bude blokující rozlišení vyřešit sami, nebo zda má být relace ukončena `KILL` příkazem. Další informace naleznete v tématu [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scenario | Waittype | Open_Tran | Status | Klád? | Další příznaky |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NENÍ NULL | >= 0 | spustitelný | Ano, když se dotaz dokončí. | V sys.dm_exec_sessions se v průběhu času budou **číst**, **cpu_time** a **memory_usage** sloupce. Doba trvání dotazu bude vysoká, až se dokončí. |
| 2 | NULL | \>0,8 | režimu spánku | Ne, můžete ale ukončit SPID. | Oznámení se může zobrazit v relaci rozšířené události pro toto ID SPID, která indikuje časový limit dotazu nebo zrušení. |
| 3 | NULL | \>= 0 | spustitelný | Ne. Nebude vyřešen, dokud klient nenačte všechny řádky nebo ukončí připojení. SPID může být ukončeno, ale může trvat až 30 sekund. | Pokud open_transaction_count = 0 a SPID udržuje zámky, zatímco je úroveň izolace transakce výchozí (čtení COMMMITTED), je to Pravděpodobná příčina. |  
| 4 | Různé | \>= 0 | spustitelný | Ne. Nebude vyřešen, dokud klient zruší dotazy nebo ukončí připojení. SPIDs lze ukončit, ale může trvat až 30 sekund. | Sloupec **hostname** v sys.DM_EXEC_SESSIONS pro SPID na začátku řetězce blokování bude stejný jako jeden z identifikátoru SPID, který blokuje. |  
| 5 | NULL | \>0,8 | návrat | Ano. | Oznámení se může zobrazit v relaci rozšířených událostí pro toto ID SPID, která indikuje časový limit dotazu nebo zrušení, nebo byl vydán pouze příkaz ROLLBACK. |  
| 6 | NULL | \>0,8 | režimu spánku | Takže. Pokud systém Windows NT zjistí, že relace již není aktivní, připojení Azure SQL Database bude přerušeno. | `last_request_start_time`Hodnota v sys.dm_exec_sessions je mnohem starší než aktuální čas. |

Následující scénáře se v těchto scénářích rozšíří. 

1.  Blokování způsobené obvykle běžícím dotazem s dlouhou dobou spuštění

    **Řešení**: řešení pro tento typ blokování problému slouží k vyhledání způsobů optimalizace dotazu. Tato třída blokujícího problému ve skutečnosti může být pouze problémem s výkonem a vyžaduje, abyste ji provedli jako takovou. Informace o řešení potíží s konkrétním pomalým spuštěným dotazem najdete v tématu [jak řešit potíže s pomalým spuštěním dotazů na SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Další informace najdete v tématu [monitorování a optimalizace výkonu](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Sestavy z [úložiště dotazů](/sql/relational-databases/performance/best-practice-with-the-query-store) v SSMS jsou také vysoce doporučeným a cenným nástrojem pro identifikaci nejcennějších dotazů, které jsou optimální pro provádění. Také si přečtěte část [inteligentní výkon](intelligent-insights-overview.md) Azure Portal pro Azure SQL Database, včetně [Query Performance Insight](query-performance-insight-use.md).

    Pokud máte dlouhotrvající dotaz, který blokuje jiné uživatele a není možné ho optimalizovat, zvažte jeho přesunutí z prostředí OLTP do vyhrazeného systému generování sestav, což je [synchronní replika jen pro čtení databáze](read-scale-out.md).

1.  Blokování způsobené pozastaveným SPID, které má nepotvrzenou transakci

    Tento typ blokování lze často identifikovat pomocí identifikátoru SPID, který je v režimu spánku nebo čeká na příkaz, ale u jehož úrovně vnořování transakce ( `@@TRANCOUNT` , `open_transaction_count` od sys.dm_exec_requests) je větší než nula. K této chybě může dojít, pokud aplikace vyprší časový limit dotazu nebo dojde k zrušení bez nutnosti vystavení požadovaného počtu příkazů ROLLBACK a/nebo potvrzení. Když SPID obdrží časový limit dotazu nebo zruší, ukončí aktuální dotaz a dávku, ale nevrátí se do transakce automaticky ani nepotvrdí. Tato aplikace zodpovídá za to, protože Azure SQL Database nemůže předpokládat, že celá transakce se musí vrátit zpátky z důvodu zrušení jednoho dotazu. Časový limit dotazu nebo zrušení se zobrazí jako událost upozornění na upozornění pro identifikátor SPID v relaci rozšířené události.

    Chcete-li předvést nepotvrzenou explicitní transakci, vydejte následující dotaz:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Pak tento dotaz spusťte ve stejném okně:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    Výstup druhého dotazu indikuje, že úroveň vnořování transakce je jedna. Všechny zámky získané v transakci jsou stále uchovávány, dokud transakce nebyla potvrzena nebo vrácena zpět. Pokud aplikace explicitně otevřou a potvrdí transakce, může komunikace nebo jiná chyba opustit relaci a její transakci v otevřeném stavu. 

    Pomocí výše uvedeného skriptu na sys.dm_tran_active_transactions Identifikujte aktuálně nepotvrzené transakce.

    **Řešení**:

     -   Kromě toho může být tato třída blokování potíží také problémem s výkonem a vyžaduje, abyste ji provedete jako takovou. Pokud je možné čas spuštění dotazu zmenšit, neproběhne časový limit dotazu nebo zrušení. Je důležité, aby aplikace mohla zpracovat časový limit nebo zrušit scénáře, které by měly nastat, ale můžete také využít výhod zkoumání výkonu dotazu. 
     
     -    Aplikace musí správně spravovat úrovně vnořování transakce nebo mohou způsobit blokující problém následující po zrušení dotazu tímto způsobem. Zvažte použití těchto zdrojů:  

            *    V obslužné rutině chyby klientské aplikace proveďte `IF @@TRANCOUNT > 0 ROLLBACK TRAN` následující chybu, i když klientská aplikace nevěří, že transakce je otevřená. Je vyžadována kontrola otevřených transakcí, protože uložená procedura volaná během dávky mohla spustit transakci bez vědomí klientské aplikace. Některé podmínky, jako je zrušení dotazu, zabrání proceduře v provedení po aktuálním příkazu, takže i v případě, že procedura obsahuje logiku pro kontrolu `IF @@ERROR <> 0` a přerušení transakce, nebude tento kód vrácení zpět proveden v takových případech.  
            *    Pokud se sdružování připojení používá v aplikaci, která otevírá připojení a spouští malý počet dotazů před uvolněním připojení zpět do fondu, jako je například webová aplikace, dočasné zakázání sdružování připojení může přispět k vyřešení problému, dokud není změněna klientská aplikace tak, aby správně zpracovala chyby. Když zabráníte sdružování připojení, uvolněním připojení dojde k fyzickému odpojení Azure SQL Databaseho připojení. Výsledkem je, že server vrátí otevřené transakce.  
            *    Použijte `SET XACT_ABORT ON` pro připojení nebo ve všech uložených procedurách, které začínají transakce a nečistí se po chybě. V případě chyby za běhu zruší toto nastavení všechny otevřené transakce a vrátí řízení klientovi. Další informace najdete v [nastavení XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).
    > [!NOTE]
    > Připojení není obnovené, dokud se znovu nepoužije z fondu připojení, takže je možné, že uživatel může otevřít transakci a pak uvolnit připojení k fondu připojení, ale nemusí se znovu použít po dobu několika sekund, během které by transakce zůstala otevřená. Pokud se připojení znovu nepoužije, transakce se po vypršení časového limitu připojení a odebrání z fondu připojení zruší. Proto je optimální, aby klientská aplikace přerušila transakce v obslužné rutině chyb nebo používala `SET XACT_ABORT ON` k tomu, aby se předešlo této možné prodlevě.

    > [!CAUTION]
    > Následuje příkaz `SET XACT_ABORT ON` T-SQL za příkazem, který způsobí, že se chyba neprovede. To může mít vliv na zamýšlený tok stávajícího kódu.

1.  Blokování způsobené identifikátorem SPID, jehož odpovídající klientská aplikace nenačítal všechny řádky výsledků k dokončení

    Po odeslání dotazu na server musí všechny aplikace okamžitě načíst všechny řádky výsledků, které mají být dokončeny. Pokud aplikace nenačte všechny řádky výsledků, můžou být zámky ponechány na tabulkách a blokují ostatní uživatele. Pokud používáte aplikaci, která transparentně odesílá příkazy SQL na server, aplikace musí načíst všechny řádky výsledků. Pokud není (a nemůžete ho nakonfigurovat tak, aby to provedl), možná nebudete schopni vyřešit problém s blokováním. Chcete-li se tomuto problému vyhnout, můžete omezit nedostatečně chovatcí aplikace na generování sestav nebo databáze podpory rozhodování.
    
    > [!NOTE]
    > Přečtěte si [pokyny pro logiku opakování](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) pro aplikace, které se připojují k Azure SQL Database. 
    
    **Řešení**: aplikace musí být přepsána, aby se načetly všechny řádky výsledku do dokončení. Nejedná se o použití [posunu a načtení v klauzuli ORDER by](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) dotazu k provedení stránkování na straně serveru.

1.  Blokování způsobené identifikátorem SPID, který je ve stavu vrácení zpět

    Dotaz na úpravu dat, který je ukončený nebo zrušený mimo uživatelsky definovanou transakci, se vrátí zpět. Tato situace může nastat i v případě, že se odpojuje relace klientské sítě nebo když je požadavek vybraný jako oběť zablokování. To lze často identifikovat pozorováním výstupu sys.dm_exec_requests, který může indikovat **příkaz** vrácení zpět a **Percent_complete sloupec** může zobrazit průběh. 

    Díky [funkci urychleného obnovení databáze](../accelerated-database-recovery.md) zavedenou v 2019 musí být zdlouhavé vrácení zpět nepravděpodobné.
    
    **Řešení**: Počkejte, než se identifikátor SPID dokončí, vraťte změny, které byly provedeny. 

    Aby k této situaci nedocházelo, během zaneprázdněných hodin v systémech OLTP provádějte rozsáhlé dávkové operace zápisu ani operace vytvoření indexu nebo operace údržby. Pokud je to možné, proveďte tyto operace během období s nízkou aktivitou.

1.  Blokování způsobené osamoceným připojením

    Pokud klientská aplikace prochází chyby nebo je restartována klientská pracovní stanice, síťová relace na serveru se nemusí za určitých podmínek hned zrušit. Z Azure SQL Database perspektivy se klient stále zobrazuje a jakékoli zámky, které jste získali, můžou být pořád zachované. Další informace najdete v tématu [řešení potíží s osamocenými připojeními v SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Řešení**: Pokud se klientská aplikace odpojila bez vhodného vyčištění prostředků, můžete identifikátor SPID ukončit pomocí `KILL` příkazu. `KILL`Příkaz jako vstup převezme hodnotu SPID. Pokud například chcete odstranit SPID 99, vydejte následující příkaz:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Viz také

* [Monitorování a ladění výkonu ve službách Azure SQL Database a Azure SQL Managed Instance](/monitor-tune-overview.md)
* [Monitorování výkonu pomocí úložiště dotazů](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Průvodce uzamykáním transakcí a správou verzí řádků](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [NASTAVIT ÚROVEŇ IZOLACE TRANSAKCE](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Rychlý start: Rozšířené události na SQL Serveru](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights používání AI k monitorování a řešení potíží s výkonem databáze](intelligent-insights-overview.md)

## <a name="learn-more"></a>Další informace

* [Azure SQL Database: vylepšení ladění výkonu pomocí automatického ladění](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Zvýšení výkonu Azure SQL Database pomocí automatického ladění](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Poskytování konzistentního výkonu pomocí Azure SQL](/learn/modules/azure-sql-performance/)
* [Řešení potíží s připojením a dalších chyb pomocí Azure SQL Database a spravované instance Azure SQL](troubleshoot-common-errors-issues.md)
* [Zpracování přechodných chyb](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)