---
title: Kódy chyb SQL – Chyba připojení databáze | Dokumentace Microsoftu
description: 'Další informace o kódy chyb SQL pro klientské aplikace SQL Database, jako je například běžných chyb připojení databáze, databáze kopírování problémy a obecné chyby. '
keywords: Kód chyby SQL, přístup k sql, Chyba připojení databáze, kódy chyb sql
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sstein
ms.openlocfilehash: d97ec2cc67da7c4bc1479c55a9a7c35c0c754532
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092524"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kódy chyb SQL pro klientské aplikace SQL Database: chyby připojení a dalších problémů databáze

Tento článek uvádí kódy chyb SQL pro klientské aplikace SQL Database, včetně chyb připojení databáze, přechodné chyby (také nazývané přechodné chyby), chyby zásad správného řízení prostředků, problémů kopie databáze, elastický fond a další chyby. Většina kategorií jsou konkrétní ke službě Azure SQL Database a se nevztahují na serveru Microsoft SQL Server. Viz také [chybové zprávy systému](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Chyby připojení databáze, přechodné chyby a jiné dočasné chyby
Následující tabulka popisuje kódy chyb SQL pro chyby připojení ke ztrátě a jiné přechodné chyby, které se můžete setkat, když se aplikace pokusí o přístup k databázi SQL. Získávání začít kurzy o tom, jak se připojit ke službě Azure SQL Database, najdete v části [připojení ke službě Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Většina běžných chyb připojení databáze a přechodných chyb
Infrastrukturu Azure dokáže dynamicky rekonfigurovat servery, když dojde k vysokému zatížení ve službě SQL Database.  Toto dynamické chování může způsobit, že váš klientský program ztratí své připojení ke službě SQL Database. Tento druh chybový stav se nazývá *přechodných chyb*.

Důrazně doporučujeme, že váš klientský program tak, aby ho může znovu vytvořit připojení na zóny umožněte čas přechodná chyba opraví sama, má logika opakovaných pokusů.  Doporučujeme, abyste čekat po dobu 5 sekund před prvním opakováním. Opakování po prodlevě kratší než 5 sekund rizika zahlcení cloudovou službu. Pro každým dalším pokusem exponenciálně růst zpoždění až 60 sekund.

Přechodná chyba chyby se obvykle manifestu jako jeden z následujících chybových zpráv z klientských programů:

* Databáze &lt;%{db_name/&gt; na serveru &lt;Azure_instance&gt; není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud se problém nevyřeší, obraťte se na zákaznickou podporu a poskytněte ID trasování relace &lt;session_id&gt;
* Databáze &lt;%{db_name/&gt; na serveru &lt;Azure_instance&gt; není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud se problém nevyřeší, obraťte se na zákaznickou podporu a poskytněte ID trasování relace &lt;session_id&gt;. (Microsoft SQL Server, chyba: 40613)
* Stávající připojení vynuceně zavřel vzdálený hostitel.
* System.Data.Entity.Core.EntityCommandExecutionException: Při provádění definice příkazu došlo k chybě. Naleznete informacích o vnitřní výjimce. ---> System.Data.SqlClient.SqlException: úrovni přenosu došlo k chybě při příjmu výsledků ze serveru. (poskytovatel: Zprostředkovatel relací, chyba: 19 - fyzické připojení není použitelné)
* Pokus o připojení k sekundární databázi se nezdařila, protože je právě Rekonfigurace databáze a je zaneprázdněný, použití nových stránek při uprostřed aktivní transakce u primární databáze. 

Příklady kódu logiku opakování naleznete v tématu:

* [Připojení knihoven pro službu SQL Database a SQL Server](sql-database-libraries.md) 
* [Akce k vyřešení chyb připojení a přechodné chyby ve službě SQL Database](sql-database-connectivity-issues.md)

Diskuze nad aspekty *blokování období* pro klienty, kteří používají ADO.NET je k dispozici v [SQL sdružování připojení serveru (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kódy chyb přechodných chyb
Tyto chyby jsou přechodné a je třeba opakovat v aplikaci logiky: 

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 4060 |16 |Databázi nelze otevřít "%.&#x2a;ls" požadovaný v přihlášení. Přihlášení se nezdařilo. |
| 40197 |17 |Služby došlo k chybě při zpracování vaší žádosti. Zkuste to prosím znovu. Kód chyby: %d.<br/><br/>Tato chyba se zobrazí, až bude služba mimo provoz kvůli softwaru nebo upgradem hardwaru, selhání hardwaru nebo jiné problémy převzetí služeb při selhání. Kód chyby: (%d) vloženým do zprávu o chybě 40197 poskytuje další informace o typu selhání nebo převzetí služeb při selhání došlo k chybě. Některé příklady, které kódy jsou vložené v rámci zprávu o chybě 40197 chyby jsou 40020, 40143, 40166 a 40540.<br/><br/>Opětovné připojení k vašemu serveru služby SQL Database automaticky vás připojí k kopii databáze v dobrém stavu. Vaše aplikace musí zachytit 40197, protokol chyb kód vložený chyby (%d) v rámci zpráva pro řešení potíží a opakovat pokus o připojení ke službě SQL Database, dokud jsou prostředky k dispozici a je připojení znovu navázáno. |
| 40501 |20 |Služba je aktuálně zaneprázdněna. Zkuste požadavek zopakovat za 10 sekund. ID incidentu: %ls. Kód: %d.<br/><br/>Další informace naleznete v tématu:<br/>• [Limity prostředků azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40613 |17 |Databáze '%.&#x2a;ls' na serveru '%.&#x2a;ls' není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte ID trasování relace '%.&#x2a;ls'. |
| 49918 |16 |Požadavek nelze zpracovat. Není dostatek prostředků ke zpracování požadavku.<br/><br/>Služba je aktuálně zaneprázdněna. Zkuste prosím požadavek později. |
| 49919 |16 |Proces nejde vytvořit nebo aktualizovat požadavku. Příliš mnoho operací vytvoření nebo aktualizace v průběhu pro předplatné "% ld".<br/><br/>Služba je zaneprázdněna zpracování více vytvořit nebo aktualizovat požadavky pro vaše předplatné nebo serveru. Požadavky jsou aktuálně zablokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro čekající operace. Počkejte, dokud čekající vytváření nebo aktualizace žádosti o dokončení nebo odstraňte jednu z probíhajících žádostí a zkuste odeslat žádost později. |
| 49920 |16 |Požadavek nelze zpracovat. Příliš mnoho operací v průběhu pro předplatné "% ld".<br/><br/>Tato služba je zaneprázdněná zpracováním více požadavků pro toto předplatné. Požadavky jsou aktuálně zablokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) na provozní stav. Počkejte, dokud se čekající žádosti se dokončí nebo odstraňte jednu z probíhajících žádostí a zkuste odeslat žádost později. |
| 4221 |16 |Přihlášení ke čtení na sekundární se nezdařilo z důvodu dlouhého čekání na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika není k dispozici pro přihlášení, protože chybí verze řádků pro transakce, které byly vydávaných za pochodu, pokud se replika byl recyklován. Problém můžete vyřešit vrácení zpět nebo potvrzením aktivních transakcí na primární replice. Výskyty tuto podmínku můžete minimalizovat vyhýbat dlouhým transakcím zápisu na primární. |

## <a name="database-copy-errors"></a>Chyby kopírování databáze
Při kopírování databáze Azure SQL Database můžete došlo k následujícím chybám. Další informace najdete v tématu [Kopírování databáze služby Azure SQL Database](sql-database-copy.md).

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 40635 |16 |Klient s IP adresou '%.&#x2a;ls' je dočasně zakázána. |
| 40637 |16 |Vytvoření kopie databáze je aktuálně zakázaný. |
| 40561 |16 |Kopírování databáze se nezdařilo. Buď zdrojová nebo cílová databáze neexistuje. |
| 40562 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze byla vyřazena. |
| 40563 |16 |Kopírování databáze se nezdařilo. Cílová databáze byla vyřazena. |
| 40564 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu. |
| 40565 |16 |Kopírování databáze se nezdařilo. Víc než 1 souběžné databáze kopírování ze stejného zdroje bude povolené. Vyřaďte cílovou databázi a zkuste to znovu později. |
| 40566 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu. |
| 40567 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu. |
| 40568 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze má k dispozici. Vyřaďte cílovou databázi a zkuste to znovu. |
| 40569 |16 |Kopírování databáze se nezdařilo. Cílová databáze má k dispozici. Vyřaďte cílovou databázi a zkuste to znovu. |
| 40570 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu později. |
| 40571 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu později. |

## <a name="resource-governance-errors"></a>Chyby zásad správného řízení prostředků
Tyto chyby jsou způsobeny nadměrného využití prostředků při práci s Azure SQL Database. Příklad:

* Transakce byl otevřený příliš dlouho.
* Transakce je příliš mnoho zámky.
* Aplikace spotřebovává příliš mnoho paměti.
* Aplikace spotřebovává příliš mnoho `TempDb` místa.

Související témata:

* Podrobnější informace jsou k dispozici zde: [limity prostředků Azure SQL Database](sql-database-service-tiers-dtu.md).

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 10928 |20 |ID prostředku: %d. Limit %s pro databázi je %d a bylo ho dosaženo. Další informace najdete na adrese [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>ID prostředku, které určuje prostředek, který byl dosažen limit. Pro pracovní vlákna, ID zdroje = 1. Pro relace, ID zdroje = 2.<br/><br/>Další informace o této chybě a způsobu jeho řešení najdete v tématu:<br/>• [Limity prostředků azure SQL Database](sql-database-service-tiers-dtu.md). |
| 10929 |20 |ID prostředku: %d. Minimální záruka %s je %d, maximální limit je %d, a aktuální využití databáze je %d. Server je však aktuálně zaneprázdněna větší než %d žádosti o podporu pro tuto databázi. Další informace najdete na adrese [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Jinak zkuste to prosím znovu později.<br/><br/>ID prostředku, které určuje prostředek, který byl dosažen limit. Pro pracovní vlákna, ID zdroje = 1. Pro relace, ID zdroje = 2.<br/><br/>Další informace o této chybě a způsobu jeho řešení najdete v tématu:<br/>• [Limity prostředků azure SQL Database](sql-database-service-tiers-dtu.md). |
| 40544 |20 |Databáze dosáhla své kvóty velikosti. Oddíl nebo odstranění dat, případně odstraňte indexy najdete v dokumentaci k možná řešení. |
| 40549 |16 |Relace je ukončena, protože máte dlouhotrvající transakci. Zkuste transakci zkrátit. |
| 40550 |16 |Relace byla ukončena, protože získala příliš mnoho zámků. Zkuste problém se čtením nebo upravit menší počet řádků v rámci jedné transakce. |
| 40551 |16 |Relace byla ukončena z důvodu nadměrného `TEMPDB` využití. Zkuste upravit dotaz a snížit využití místa na dočasnou tabulku.<br/><br/>Pokud používáte dočasné objekty, uchovejte prostor na disku v `TEMPDB` databáze přetažením dočasné objekty po už nejsou potřeba relací. |
| 40552 |16 |Relace byla ukončena z důvodu nadměrného transakce využití místa protokolu. Zkuste upravit menší počet řádků v rámci jedné transakce.<br/><br/>Provést hromadné vloží pomocí `bcp.exe` nástroj nebo `System.Data.SqlClient.SqlBulkCopy` třídy, zkuste použít `-b batchsize` nebo `BatchSize` možnosti, jak omezit počet řádků zkopírovali na server v každé transakci. Pokud bude probíhat opětovné sestavení indexu s `ALTER INDEX` prohlášení, zkuste použít `REBUILD WITH ONLINE = ON` možnost. |
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz a zpracovával méně řádků.<br/><br/>Snížení počtu `ORDER BY` a `GROUP BY` operace ve vašem kódu jazyka Transact-SQL snižuje požadavky na paměť vašeho dotazu. |

## <a name="elastic-pool-errors"></a>Chyby elastického fondu
Tyto chyby se vztahují k vytváření a používání elastických fondů:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |Elastický fond dosáhl svého limitu úložiště. Využití úložiště pro elastický fond nemůže být delší než (%d) MB. |Elastický fond maximální místo v MB. |Došlo k pokusu o zápis dat do databáze, když byl dosažen limit úložiště elastického fondu. |Zvažte možnost zvýšit počet jednotek Dtu a/nebo přidání úložiště do elastického fondu pokud je to možné za účelem zvýšení limitu úložiště, snížit využití strany jednotlivých databází v elastickém fondu úložiště nebo odebrat databáze z elastického fondu. |
| 10929 |EX_USER |Minimální záruka %s je %d, maximální limit je %d, a aktuální využití databáze je %d. Server je však aktuálně zaneprázdněna větší než %d žádosti o podporu pro tuto databázi. Zobrazit [ http://go.microsoft.com/fwlink/?LinkId=267637 ](http://go.microsoft.com/fwlink/?LinkId=267637) žádostí o pomoc. Jinak zkuste to prosím znovu později. |DTU nebo minimální počet virtuálních jader na databázi. DTU nebo na databázi maximální počet virtuálních jader |Celkový počet souběžných pracovních procesů (požadavků) napříč všemi databázemi v elastickém fondu došlo k pokusu o překročení limitu fondu. |Zvažte zvýšení počtu jednotek Dtu nebo virtuálních jader, pokud je to možné elastického fondu za účelem zvýšení limitu pracovního procesu, nebo odeberte databáze z elastického fondu. |
| 40844 |EX_USER |Databáze: %ls"na serveru"%ls"je"%ls"edice databáze v elastickém fondu a nemůže mít vztah průběžného kopírování. |Název databáze, edice databáze, název serveru |StartDatabaseCopy příkazu je vydané pro neprémiové databáze v elastickém fondu. |Již brzy |
| 40857 |EX_USER |Elastický fond pro server se nepodařilo najít: "%ls", název elastického fondu: "%ls". |název serveru. Název elastického fondu |Zadaný elastického fondu v zadaný server neexistuje. |Zadejte název platné elastického fondu. |
| 40858 |EX_USER |Elastický fond "%ls" již existuje v serveru: "%ls. |Název elastického fondu, název serveru |Zadaný elastického fondu v zadané logické serveru již existuje. |Zadejte nový název elastického fondu. |
| 40859 |EX_USER |Elastický fond nepodporuje úroveň služby "%ls". |úrovně služby elastického fondu |Zadaná služba úroveň se nepodporuje pro zřizování elastického fondu. |Zadejte správnou verzi nebo nechte prázdné, použije se výchozí úroveň služby úrovně služby. |
| 40860 |EX_USER |Kombinace elastického fondu. %ls"a služby cíl"%ls"je neplatná. |Název elastického fondu. Název cílové úrovně služby |Elastického fondu a služba cíle lze zadat současně pouze v případě, že je zadaný cíl služby jako "ElasticPool". |Zadejte správné kombinace elastického fondu a cíle služby. |
| 40861 |EX_USER |Edice databáze ' %. *ls se nemůže lišit od úrovně služby elastického fondu, což je ' %.* ls. |edice databáze úrovně služby elastického fondu |Edice databáze se liší od úrovně služby elastického fondu. |Nezadávejte edici databáze, které se liší od úrovně služby elastického fondu.  Všimněte si, že není nutné nastavit edici databáze. |
| 40862 |EX_USER |Název elastického fondu musí být zadán, pokud je zadaný cíl služby elastického fondu. |Žádný |Cíl služby elastického fondu jednoznačně neidentifikuje elastického fondu. |Zadejte název elastického fondu, pokud používáte cíl služby elastického fondu. |
| 40864 |EX_USER |Počet jednotek Dtu pro elastický fond musí být minimálně (%d) Dtu, pro úroveň služby "%. * ls. |Počet jednotek Dtu pro elastický fond; úrovně služby elastického fondu. |Došlo k pokusu o nastavení počet jednotek Dtu pro elastický fond nižší než minimální limit. |Opakovat nastavení počet jednotek Dtu elastického fondu alespoň minimální limit. |
| 40865 |EX_USER |Počet jednotek Dtu pro elastický fond nemůže být delší než (%d) Dtu, pro úroveň služby "%. * ls. |Počet jednotek Dtu pro elastický fond; úrovně služby elastického fondu. |Došlo k pokusu o nastavení počet jednotek Dtu pro elastický fond přesahuje maximální limit. |Nastavení jednotky Dtu pro elastický fond na menší než maximální limit opakování. |
| 40867 |EX_USER |Musí být na databázi maximální počet jednotek DTU na nejnižší (%d) pro úroveň služby "%. * ls. |Maximální počet jednotek DTU na databázi. úrovně služby elastického fondu |Došlo k pokusu o nastavení maximální počet jednotek DTU na databázi níže podporovaný limit. | Vezměte v úvahu při použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40868 |EX_USER |Každou databázi maximální počet jednotek DTU nesmí přesáhnout (%d) pro úroveň služby "%. * ls. |Maximální počet jednotek DTU na databázi. úrovně služby elastického fondu. |Došlo k pokusu o nastavení maximální počet jednotek DTU na databázi mimo podporovaný limit. | Vezměte v úvahu při použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40870 |EX_USER |Minimální počet jednotek DTU na databázi nesmí přesáhnout (%d) pro úroveň služby "%. * ls. |Minimální počet jednotek DTU na databázi. úrovně služby elastického fondu. |Došlo k pokusu o nastavení minimální počet jednotek DTU na databázi mimo podporovaný limit. | Vezměte v úvahu při použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40873 |EX_USER |Počet databází (%d) a minimální počet jednotek DTU na databázi (%d) nemůže překročit počet jednotek Dtu elastického fondu (%d). |Počet databází v elastickém fondu; Minimální počet jednotek DTU na databázi. Počet jednotek Dtu elastického fondu. |Pokus zadat minimální počet jednotek DTU pro databáze v elastickém fondu, která překračuje počet jednotek Dtu elastického fondu. | Vezměte v úvahu zvýšit počet jednotek Dtu elastického fondu, nebo snižte minimální počet jednotek DTU na databázi nebo snížit počet databází v elastickém fondu. |
| 40877 |EX_USER |Elastický fond nejde odstranit, pokud neobsahuje žádné databáze. |Žádný |Elastický fond obsahuje jednu nebo více databází a proto nejde odstranit. |Chcete-li odstranit ji odeberte databáze z elastického fondu. |
| 40881 |EX_USER |Elastický fond "%. * ls dosáhla svého limitu počtu databází.  Omezení počtu databází pro elastický fond nesmí přesáhnout (%d) pro elastický fond s (%d) Dtu. |Název elastického fondu omezení počtu databáze z elastického fondu počet jednotek Edtu pro fond zdrojů. |Pokus o vytvoření nebo přidání databáze do elastického fondu, pokud bylo dosaženo limitu počtu databáze z elastického fondu. | Zvažte možnost zvýšit počet jednotek Dtu elastického fondu pokud je to možné za účelem zvýšení limitu jeho databáze, nebo odebrat databáze z elastického fondu. |
| 40889 |EX_USER |Počet jednotek Dtu nebo limit úložiště elastického fondu. %. * ls není možné snížit, protože pro jeho databáze, která neposkytuje dostatečné volné místo. |Název elastického fondu. |Probíhá pokus o snížit limit úložiště elastického fondu pod jeho využití úložiště. | Zvažte snížení využití úložiště jednotlivých databází v elastickém fondu nebo odeberte databáze z fondu za účelem snížení jeho počtu jednotek Dtu nebo limit úložiště. |
| 40891 |EX_USER |Minimální počet jednotek DTU na databázi (%d) nemůže být delší než maximální počet jednotek DTU na databázi (%d). |Minimální počet jednotek DTU na databázi. Maximální počet jednotek DTU na databázi. |Došlo k pokusu o nastavení minimální počet jednotek DTU na databázi vyšší než maximální počet jednotek DTU na databázi. |Zajistěte, aby že minimální počet jednotek DTU na databáze není delší než maximální počet jednotek DTU na databázi. |
| Bude doplněno |EX_USER |Velikost úložiště pro jednotlivé databáze v elastickém fondu nemůže být delší než maximální velikost povolenou "%. * ls služby elastického fondu úrovně. |úrovně služby elastického fondu |Maximální velikost databáze přesahuje maximální velikost povolenou úrovně služby elastického fondu. |Nastavte maximální velikost databáze v rámci maximální velikost povolenou úrovně služby elastického fondu. |

Související témata:

* [Vytvoření elastického fondu (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Správa elastického fondu (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Vytvoření elastického fondu (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Monitorování a správa elastického fondu (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Obecné chyby
Tyto chyby nespadají do všech předchozích kategorií.

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) není platný název, protože obsahuje neplatné znaky. |
| 18452 |14 |Přihlášení se nezdařilo. Přihlášení proběhlo z nedůvěryhodné domény a nelze použít s Windows authentication.%.&#x2a;ls (přihlášení systému Windows není podporováno v této verzi systému SQL Server). |
| 18456 |14 |Přihlášení uživatele se nezdařilo. '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(přihlášení se nezdařilo pro uživatele "%.&#x2a;ls". Změna hesla se nezdařila. Změna hesla při přihlášení se nepodporuje v této verzi systému SQL Server.) |
| 18470 |14 |Přihlášení se nezdařilo pro uživatele "%.&#x2a;ls". Důvod: Účet je disabled.%.&#x2a;ls |
| 40014 |16 |Více databází nelze použít v rámci jedné transakce. |
| 40054 |16 |Tabulky bez clusterovaného indexu nejsou podporovány v této verzi systému SQL Server. Vytvořit clusterovaný index a zkuste to znovu. |
| 40133 |15 |Tato operace není podporována v této verzi systému SQL Server. |
| 40506 |16 |Zadaný kód SID je neplatný pro tuto verzi systému SQL Server. |
| 40507 |16 |%.&#x2a;ls ls se nedá vyvolat, s parametry v této verzi systému SQL Server. |
| 40508 |16 |Příkaz USE není podporován pro přepínání databází. Pro připojení k jiné databázi, použijte nové připojení. |
| 40510 |16 |Příkaz '%.&#x2a;ls' není podporován v této verzi systému SQL Server |
| 40511 |16 |Integrovaná funkce '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40512 |16 |Zastaralé funkce "%ls" není v této verzi systému SQL Server podporována. |
| 40513 |16 |Server proměnné '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40514 |16 |"%ls" není podporován v této verzi systému SQL Server. |
| 40515 |16 |Odkaz na název databáze nebo serveru v '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40516 |16 |Globální dočasné objekty nejsou v této verzi systému SQL Server podporovány. |
| 40517 |16 |Možnost klíčového slova nebo příkazu '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40518 |16 |Příkaz DBCC '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40520 |16 |Zabezpečitelná Třída '% S_MSG' není podporováno v této verzi systému SQL Server. |
| 40521 |16 |Zabezpečitelná Třída '% S_MSG' není podporován v oboru serveru v této verzi systému SQL Server. |
| 40522 |16 |Typ objektu "%.&#x2a;ls" databáze není podporován v této verzi systému SQL Server. |
| 40523 |16 |Vytvoření implicitního uživatele '%.&#x2a;ls' není podporován v této verzi systému SQL Server. Explicitně vytvořte uživatele před jeho použitím. |
| 40524 |16 |Datový typ '%.&#x2a;ls' není v této verzi systému SQL Server podporována. |
| 40525 |16 |S "%.ls" se nepodporuje v této verzi systému SQL Server. |
| 40526 |16 |%.&#x2a;ls zprostředkovatele sady řádků ls' v této verzi systému SQL Server není podporována. |
| 40527 |16 |Odkazované servery nejsou v této verzi systému SQL Server podporovány. |
| 40528 |16 |Nelze mapovat uživatele na certifikáty, asymetrické klíče ani na přihlášení Windows v této verzi systému SQL Server. |
| 40529 |16 |Integrovaná funkce "%.&#x2a;ls" v zosobnění kontextu není podporováno v této verzi systému SQL Server. |
| 40532 |11 |Nelze otevřít serveru "%.&#x2a;ls" požadovaný v přihlášení. Přihlášení se nezdařilo. |
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz a zpracovával méně řádků.<br/><br/> Snížení počtu `ORDER BY` a `GROUP BY` operace ve vašem kódu jazyka Transact-SQL pomáhá snížit požadavky na paměť vašeho dotazu. |
| 40604 |16 |Není příkaz CREATE/ALTER DATABASE se nepovedlo, protože by došlo k překročení kvóty serveru. |
| 40606 |16 |Připojení databáze se nepodporuje v této verzi systému SQL Server. |
| 40607 |16 |Přihlašovací údaje Windows nejsou v této verzi systému SQL Server podporovány. |
| 40611 |16 |Servery můžete mít definované maximálně 128 pravidel brány firewall. |
| 40614 |16 |Počáteční IP adresa pravidla brány firewall nesmí přesahovat koncovou IP adresu. |
| 40615 |16 |Nejde otevřít server "{0}' požadovaný v přihlášení. Klient s IP adresou{1}' nemá povolený přístup k serveru.<br /><br />Povolit přístup, použijte portál databáze SQL nebo spusťte sp\_nastavit\_brány firewall\_pravidlo v hlavní databázi vytvořte pravidlo brány firewall pro tuto IP adresu nebo rozsah adres. Může trvat až pět minut, než tato změna projevila. |
| 40617 |16 |Název pravidla firewallu, který se spustí s (název pravidla) je příliš dlouhý. Maximální délka je 128. |
| 40618 |16 |Název pravidla firewallu nemůže být prázdný. |
| 40620 |16 |Přihlášení se nezdařilo pro uživatele "%.&#x2a;ls". Změna hesla se nezdařila. V této verzi systému SQL Server nepodporuje změnu hesla při přihlášení. |
| 40627 |20 |Operace na serveru "{0}"a databáze"{1}" probíhá. Počkejte prosím několik minut, než to zkusíte znovu. |
| 40630 |16 |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože je příliš krátké. |
| 40631 |16 |Zadané heslo je příliš dlouhý. Heslo musí mít maximálně 128 znaků. |
| 40632 |16 |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože není dostatečně komplexní. |
| 40636 |16 |Nemůžete použít název vyhrazené databáze "%.&#x2a;ls" v této operaci. |
| 40638 |16 |Neplatné id odběru (id předplatného). Odběr neexistuje. |
| 40639 |16 |Požadavek neodpovídá schématu: (Chyba schématu). |
| 40640 |20 |Na serveru došlo k neočekávané výjimce. |
| 40641 |16 |Zadané umístění je neplatné. |
| 40642 |17 |Server je aktuálně zaneprázdněna. Zkuste to prosím znova později. |
| 40643 |16 |Hodnota zadaná hlavička x-ms-version je neplatná. |
| 40644 |14 |Nepodařilo se autorizovat přístup k zadanému odběru. |
| 40645 |16 |Servername (servername) nemůže být prázdný nebo mít hodnotu null. To lze provést pouze malých písmen "a"-"z", číslice 0 – 9 a spojovník. Nesmí pomlčkou začínat ani název. |
| 40646 |16 |ID předplatného nemůže být prázdný. |
| 40647 |16 |Předplatné (id předplatného) nemá server (servername). |
| 40648 |17 |Provedly se příliš mnoho požadavků. Zkuste to prosím znovu později. |
| 40649 |16 |Je zadaný neplatný typ obsahu. Je podporován pouze application/xml. |
| 40650 |16 |Předplatné (id předplatného) neexistuje nebo není připraven provést operaci. |
| 40651 |16 |Nepovedlo se vytvořit server, protože předplatné (id předplatného) je zakázané. |
| 40652 |16 |Nelze přesunout nebo vytvořit server. Předplatné (id předplatného) překročí kvótu serveru. |
| 40671 |17 |Selhání komunikace mezi bránou a službu správy. Zkuste to prosím znovu později. |
| 40852 |16 |Nejde otevřít databázi ' %. \*ls na serveru "%. \*ls požadovaný v přihlášení. Přístup k databázi je povolený jenom pomocí povoleno zabezpečení připojovacího řetězce. Chcete-li přistupovat k databázi, upravte připojovací řetězce obsahující zabezpečení na serveru plně kvalifikovaný název domény –.database.windows "Název_serveru".net by měla upravit tak, aby .database "Název_serveru". `secure`. windows.net. |
| 40914 | 16 | Nejde otevřít server "*[název_serveru]*' požadovaný v přihlášení. Klient není povolen přístup k serveru.<br /><br />Pokud chcete vyřešit, zvažte přidání [pravidlo virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |SQL Azure systém pod zátěží a je uvedení horní limit na souběžné operace DB CRUD pro jeden server (např. vytvoření databáze). Server uvedený v chybové zprávě překročil maximální počet souběžných připojení. Zkuste to znovu později. |
| 45169 |16 |Systém SQL azure pod zátěží a je uvedení horní limit počtu souběžných server operace CRUD pro v rámci jednoho předplatného (např. vytvoření serveru). Předplatné zadané v chybové zprávě překročil maximální počet souběžných připojení, a žádost byla zamítnuta. Zkuste to znovu později. |

## <a name="next-steps"></a>Další postup
* Přečtěte si informace o [funkce služby Azure SQL Database](sql-database-features.md).
* Přečtěte si informace o [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
* Přečtěte si informace o [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

