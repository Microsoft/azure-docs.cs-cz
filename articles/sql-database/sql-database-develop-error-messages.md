 ---
Title: kódy chyb SQL - Chyba připojení databáze | Popis Microsoft Docs: "Další informace o kódy chyb SQL pro klientské aplikace SQL Database, například běžných chyb připojení databáze, problémů kopie databáze a obecné chyby. "klíčová slova: Chyba připojení, kód chyby sql, přístup sql, databáze služby kódy chyby systému sql: documentationcenter databáze sql:" Autor: stevestein manager: jhubbard editor: "

ms.assetid: 2a23e4ca-ea93-4990-855a-1f9f05548202 ms.service: sql-database ms.custom: develop apps ms.workload: "Active" ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 09/28/2017 ms.author: sstein

---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Kódy chyb SQL pro klientské aplikace SQL Database: Databáze chyb připojení a další problémy

V tomto článku jsou uvedeny kódy chyb SQL pro klientské aplikace SQL Database, včetně chyb připojení databáze, přechodné chyby (také nazývané přechodných), prostředků zásad správného řízení chyb, problémů kopie databáze, elastický fond a dalších chyb. Většina kategorie jsou konkrétní do Azure SQL Database a nevztahují na Microsoft SQL Server. Viz také [systému chybové zprávy](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>K chybám připojení databáze, přechodné chyby a dalších dočasné chyb
Následující tabulka obsahuje kódy chyb SQL pro chyby ztrátě připojení a jiné přechodné chyby, které se můžete setkat, když se aplikace pokusí o přístup k databázi SQL. Získání Začínáme kurzy o tom, jak připojit k databázi SQL Azure, najdete v části [připojení k databázi SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Většina běžných chyb připojení databáze a přechodná chyba chyb
Infrastruktura Azure má schopnost dynamicky znovu nakonfigurovat servery, když nastat náročných úloh ve službě SQL Database.  Toto chování dynamické může způsobit, že váš klientský program ztratit připojení k databázi SQL. Tento druh chybový stav nazývá *přechodná chyba*.

Důrazně doporučujeme, aby váš klientský program má logika opakovaných pokusů, takže ji může znovu vytvořit připojení po poskytnutí času přechodná chyba sám opravit.  Doporučujeme, abyste zpoždění pro 5 sekund před první opakování. Opakování po prodlevě kratší než 5 sekund rizika zahltí cloudové služby. Pro každý další pokusy exponenciálnímu, růst zpoždění až do maximálního počtu 60 sekund.

Chyby přechodná chyba se obvykle manifest jako jeden z následujících chybových zpráv z klientských programů:

* Databáze &lt;%{db_name/&gt; na serveru &lt;Azure_instance&gt; není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte ID trasování relace &lt;session_id&gt;
* Databáze &lt;%{db_name/&gt; na serveru &lt;Azure_instance&gt; není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte ID trasování relace &lt;session_id&gt;. (Microsoft SQL Server, Error: 40613)
* Existující připojení bylo vzdáleným hostitelem nuceně uzavřeno.
* System.Data.Entity.Core.EntityCommandExecutionException: Při provádění definice příkazu došlo k chybě. Viz vnitřní výjimka podrobnosti. ---> System.Data.SqlClient.SqlException: úrovni přenosu došlo k chybě při příjmu výsledků ze serveru. (Zprostředkovatel: zprostředkovatele relací, chyba: 19 – fyzické připojení není použitelná)
* Pokus o připojení k sekundární databázi se nezdařila, protože databáze je právě reconfguration a je zaneprázdněný, použití nové stránky při uprostřed aktivní transakce na primární databáze. 

Příklady kódu logika opakovaných pokusů najdete v části:

* [Knihovny připojení pro SQL Database a SQL Server](sql-database-libraries.md) 
* [Akce pro opravu chyb připojení a přechodné chyby v databázi SQL](sql-database-connectivity-issues.md)

Popis *blokování období* pro klienty, kteří používají ADO.NET je k dispozici v [SQL sdružování připojení serveru (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Kódy chyb přechodná chyba
Tyto chyby jsou přechodné a v logiku aplikace je třeba opakovat: 

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 4060 |16 |Databázi nelze otevřít "%.&#x2a;ls" požadovaný v přihlášení. Přihlášení se nezdařilo. |
| 40197 |17 |Služby došlo k chybě při zpracování vaší žádosti. Zkuste to prosím znovu. Kód chyby: %d.<br/><br/>Tato chyba se zobrazí, pokud je služba dolů z důvodu softwaru nebo hardwaru upgrady, selhání hardwaru nebo jiných problémů převzetí služeb při selhání. Kód chyby (%d) vloženým do zprávy chyby 40197 poskytuje další informace o druh selhání nebo převzetí služeb při selhání došlo k chybě. Některé příklady, které jsou ve zprávě chyby 40197 vložených kódy chyby jsou 40020, 40143, 40166 a 40540.<br/><br/>Připojení k vaší databázi SQL serveru automaticky připojí k kopii databáze v pořádku. Aplikace musí catch protokolu chyb 40197, kód chyby embedded (%d) ve zprávě pro řešení potíží s a opakujte pokus o připojení k databázi SQL, dokud prostředky jsou k dispozici a je znovu navázat připojení. |
| 40501 |20 |Služba je aktuálně zaneprázdněna. Opakujte požadavek za 10 sekund. ID incidentu: %ls. Kód: %d.<br/><br/>Další informace naleznete v tématu:<br/>• [Limitů prostředků azure SQL Database](sql-database-service-tiers.md). |
| 40613 |17 |Databáze '%.&#x2a;ls' na serveru '%.&#x2a;ls' není aktuálně k dispozici. Opakujte pokus o připojení později. Pokud potíže potrvají, obraťte se na zákaznickou podporu a poskytněte ID trasování relace '%.&#x2a;ls'. |
| 49918 |16 |Žádost nelze zpracovat. Nedostatek prostředků pro zpracování požadavku.<br/><br/>Služba je aktuálně zaneprázdněna. Opakujte požadavek později. |
| 49919 |16 |Proces nelze vytvořit nebo aktualizovat požadavku. Příliš mnoho vytvořit nebo aktualizovat probíhající operace předplatného "% ld".<br/><br/>Služba je zaneprázdněná zpracování více vytvořit nebo aktualizovat požadavky pro vaše předplatné nebo server. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro čekajících operací. Počkejte, dokud čeká na vytvoření nebo aktualizace žádosti o dokončení nebo odstraňte jednu z vaší žádosti čekající na vyřízení a opakujte žádost později. |
| 49920 |16 |Žádost nelze zpracovat. Příliš mnoho operací v průběhu pro předplatné "% ld".<br/><br/>Služba je zaneprázdněná zpracování více požadavků pro tento odběr. Požadavky jsou aktuálně blokovány pro optimalizaci prostředků. Dotaz [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) pro stav operace. Počkejte, dokud nevyřízené žádosti o dokončení nebo odstraňte jednu z vaší žádosti čekající na vyřízení a opakujte žádost později. |
| 4221 |16 |Přihlášení k sekundární pro čtení se nezdařilo z důvodu dlouhého čekání na 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. Replika není k dispozici pro přihlášení, protože chybí řádek verze pro transakce, které byly během letu, pokud byla replika recykluje. Problém můžete vyřešit vrácení zpět nebo potvrzení aktivních transakcí v primární replice. Zamezení dlouho zápisu transakce na primárním můžete minimalizovat výskyty tuto podmínku. |

## <a name="database-copy-errors"></a>Chyby kopie databáze
Při kopírování databáze Azure SQL Database může být zjištěny následující chyby. Další informace najdete v tématu [Kopírování databáze služby Azure SQL Database](sql-database-copy.md).

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 40635 |16 |Klient s IP adresou '%. & #x2a; ls' je dočasně zakázána. |
| 40637 |16 |Vytvoření kopie databáze je aktuálně zakázáno. |
| 40561 |16 |Kopírování databáze se nezdařilo. Buď zdrojová nebo cílová databáze neexistuje. |
| 40562 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze byla vyřazena. |
| 40563 |16 |Kopírování databáze se nezdařilo. Cílová databáze byla vyřazena. |
| 40564 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a akci opakujte. |
| 40565 |16 |Kopírování databáze se nezdařilo. Je povoleno více než 1 kopie souběžných databáze z jednoho zdroje. Vyřaďte cílovou databázi a zkuste to znovu později. |
| 40566 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a akci opakujte. |
| 40567 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a akci opakujte. |
| 40568 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze již není dostupná. Vyřaďte cílovou databázi a akci opakujte. |
| 40569 |16 |Kopírování databáze se nezdařilo. Cílová databáze již není dostupná. Vyřaďte cílovou databázi a akci opakujte. |
| 40570 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu později. |
| 40571 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte cílovou databázi a zkuste to znovu později. |

## <a name="resource-governance-errors"></a>Chyby zásad správného řízení zdrojů
Tyto chyby jsou způsobeny nadměrnému využití prostředků při práci s Azure SQL Database. Příklad:

* Transakce byla otevřena příliš dlouho.
* Transakce je příliš mnoho zámky.
* Aplikace využívala příliš mnoho paměti.
* Aplikace využívala příliš mnoho `TempDb` místa.

Související témata:

* Podrobnější informace jsou k dispozici zde: [limitů prostředků Azure SQL Database](sql-database-service-tiers.md).

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 10928 |20 |ID zdroje: %d. Limit %s pro databázi je %d a byl dosažen. Další informace najdete v tématu [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>ID prostředku označuje prostředek, který byl dosažen limit. Pro pracovních vláken, ID zdroje = 1. Pro relace, ID zdroje = 2.<br/><br/>Další informace o této chybě a jak ho vyřešit najdete v části:<br/>• [Limitů prostředků azure SQL Database](sql-database-service-tiers.md). |
| 10929 |20 |ID zdroje: %d. Minimální záruka %s je %d, maximální limit je %d a aktuální využití databáze je %d. Server je však aktuálně zaneprázdněn pro podporu požadavků, které jsou větší než %d pro tuto databázi. Další informace najdete v tématu [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Jinak zkuste to prosím znovu později.<br/><br/>ID prostředku označuje prostředek, který byl dosažen limit. Pro pracovních vláken, ID zdroje = 1. Pro relace, ID zdroje = 2.<br/><br/>Další informace o této chybě a jak ho vyřešit najdete v části:<br/>• [Limitů prostředků azure SQL Database](sql-database-service-tiers.md). |
| 40544 |20 |Databáze dosáhla své kvóty velikosti. Oddíl nebo odstranění dat, případně odstraňte indexy najdete v dokumentaci o možných řešeních. |
| 40549 |16 |Relace je ukončena, protože máte dlouhotrvající transakci. Zkuste transakci zkrátit. |
| 40550 |16 |Relace byla ukončena, protože získala příliš mnoho zámků. Zkuste čtení nebo upravit menší počet řádků v rámci jedné transakce. |
| 40551 |16 |Relace byla ukončena z důvodu nadměrného `TEMPDB` využití. Zkuste upravit dotaz ke snížení využití místa dočasné tabulky.<br/><br/>Pokud používáte dočasné objekty, šetří místo v `TEMPDB` databáze umístěním dočasné objekty po relace již nepotřebujete. |
| 40552 |16 |Relace byla ukončena z důvodu nadměrného transakce využití místa protokolu. Zkuste upravit menší počet řádků v rámci jedné transakce.<br/><br/>Pokud provádíte hromadné vložení pomocí `bcp.exe` nástroj nebo `System.Data.SqlClient.SqlBulkCopy` třídy, zkuste použít `-b batchsize` nebo `BatchSize` možnosti omezit počet řádků zkopírovány na server v každé transakci. Pokud jsou nové sestavení indexu s `ALTER INDEX` příkaz, zkuste použít `REBUILD WITH ONLINE = ON` možnost. |
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz zpracovat méně řádků.<br/><br/>Omezení počtu `ORDER BY` a `GROUP BY` operace v kódu jazyka Transact-SQL snižuje požadavky na paměť tohoto dotazu. |

## <a name="elastic-pool-errors"></a>Chyby elastického fondu
K vytváření a používání elastické fondy se vztahují k následujícím chybám:

| Argument číslo chyby | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |Elastický fond bylo dosaženo limitu úložiště. Využití úložiště pro elastický fond nesmí překročit (%d) MB. |Omezení prostoru na elastického fondu v MB. |Probíhá pokus o zápisu dat do databáze, pokud bylo dosaženo limitu úložiště elastického fondu. |Zvažte zvýšení Chcete-li zvýšit limit úložiště, snížit úložiště používané jednotlivých databází v rámci fondu elastické jednotky Dtu elastického fondu pokud je to možné, nebo odebrat z fondu elastické databáze. |
| 10929 |EX_USER |Minimální záruka %s je %d, maximální limit je %d a aktuální využití databáze je %d. Server je však aktuálně zaneprázdněn pro podporu požadavků, které jsou větší než %d pro tuto databázi. V tématu [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) o pomoc. Jinak zkuste to prosím znovu později. |Minimální počet jednotek DTU na databázi; Maximální počet jednotek DTU na databázi |Celkový počet souběžných pracovních procesů (počet požadavků) mezi všechny databáze v elastickém fondu se pokusil překračují limit fondu. |Zvažte zvýšení limitu pracovní zvýšit počet jednotek Dtu elastického fondu pokud je to možné, nebo odebrat z fondu elastické databáze. |
| 40844 |EX_USER |Databáze: %ls"na serveru '%ls' je databáze edice"%ls"v elastickém fondu a nemůže mít relaci průběžná kopie. |Název databáze, edice databáze, název serveru |Příkaz StartDatabaseCopy je vydán pro jiný premium databáze v elastickém fondu. |Již brzy |
| 40857 |EX_USER |Elastický fond pro server nebyl nalezen: '%ls', název elastického fondu: '%ls'. |název serveru. Název elastického fondu |Zadaný elastického fondu v zadaný server neexistuje. |Zadejte název platné elastického fondu. |
| 40858 |EX_USER |Elastický fond '%ls' již existuje v serveru: '%ls' |Název elastického fondu, název serveru |Zadaný fond elastické již existuje v zadané logického serveru. |Zadejte nový název elastického fondu. |
| 40859 |EX_USER |Elastický fond nepodporuje úroveň služby "%ls". |úrovně služby elastického fondu |Zadaná služba úroveň se nepodporuje pro zřizování elastického fondu. |Zadejte správnou verzi nebo ponechat prázdné použití vrstvy služby výchozí úroveň služby. |
| 40860 |EX_USER |Kombinace elastického fondu. %ls"a služby cíl '%ls' je neplatný. |Název elastického fondu; Název cílové úrovně služby |Elastický fond a služby cíle lze zadat společně pouze v případě, že je zadaný cíl služby jako 'ElasticPool'. |Zadejte správné kombinace elastického fondu a cíl služby. |
| 40861 |EX_USER |Edice databáze ' %. *ls se nemůže lišit od úrovně služby elastického fondu, což je ' %.* ls'. |edice databáze, úrovně služby elastického fondu |Edice databáze se liší od úrovně služby elastického fondu. |Nezadávejte edici databáze, které se liší od úrovně služby elastického fondu.  Všimněte si, že není potřeba zadat edice databáze. |
| 40862 |EX_USER |Název elastického fondu musí být zadán, pokud je zadaný cíl služby elastického fondu. |Žádné |Cíl služby elastického fondu nepředstavuje jedinečnou identifikaci fondu elastické databáze. |Zadejte název elastického fondu, pokud používáte cíl služby elastického fondu. |
| 40864 |EX_USER |Počet jednotek Dtu pro elastický fond musí být minimálně (%d) Dtu, pro úroveň služby "%. * ls'. |Jednotky Dtu pro elastický fond; úrovně služby elastického fondu. |Došlo k pokusu o nastavení Dtu pro elastický fond pod minimální limit. |Nastavení jednotky Dtu pro elastického fondu alespoň minimální limit opakování. |
| 40865 |EX_USER |Počet jednotek Dtu pro elastický fond nesmí být delší než (%d) Dtu, pro úroveň služby "%. * ls'. |Jednotky Dtu pro elastický fond; úrovně služby elastického fondu. |Došlo k pokusu o nastavení Dtu pro elastický fond nad maximální limit. |Opakujte pokus o nastavení Dtu pro elastický fond na větší než maximální limit. |
| 40867 |EX_USER |Musí být na databázi maximální počet jednotek DTU na nejnižší (%d) pro úroveň služby "%. * ls'. |Maximální počet jednotek DTU na databázi; úrovně služby elastického fondu |Došlo k pokusu o nastavení maximální počet jednotek DTU na databázi níže podporovaný limit. | onsider pomocí úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40868 |EX_USER |Na databázi maximální počet jednotek DTU nesmí překročit (%d) pro úroveň služby "%. * ls'. |Maximální počet jednotek DTU na databázi; úrovně služby elastického fondu. |Došlo k pokusu o nastavení maximální počet jednotek DTU na databázi překračuje podporovaný limit. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40870 |EX_USER |Minimální počet jednotek DTU na databázi nesmí překročit (%d) pro úroveň služby "%. * ls'. |Minimální počet jednotek DTU na databázi; úrovně služby elastického fondu. |Došlo k pokusu o nastavení minimální počet jednotek DTU na databázi překračuje podporovaný limit. | Zvažte použití úrovně služby elastického fondu, který podporuje požadované nastavení. |
| 40873 |EX_USER |Počet databází (%d) a minimální počet jednotek DTU na databázi (%d) nemůže překročit počet jednotek Dtu elastického fondu (%d). |Počet databází v elastickém fondu; Minimální počet jednotek DTU na databázi; Počet jednotek Dtu elastického fondu. |Probíhá pokus o zadejte minimální počet jednotek DTU pro databáze v elastickém fondu, která překračuje počet jednotek Dtu elastického fondu. | Zvažte zvýšení počet jednotek Dtu elastického fondu nebo snížit minimální počet jednotek DTU na databázi nebo snížení počtu databází v elastickém fondu. |
| 40877 |EX_USER |Fondu elastické databáze nelze odstranit, pokud neobsahuje žádné databáze. |Žádné |Elastický fond obsahuje jednu nebo více databází a proto nelze odstranit. |Chcete-li odstranit ho odeberte databáze z elastického fondu. |
| 40881 |EX_USER |Elastický fond se %. * ls' bylo dosaženo limitu počtu databáze.  Limit počtu databáze pro elastický fond nesmí překročit (%d) pro elastický fond se (%d) Dtu. |Název elastického fondu; limit počtu databázi elastického fondu; Edtu pro fond zdrojů. |Probíhá pokus o vytvoření nebo přidat databáze do pružného fondu, pokud byl dosažen limit počtu databázi elastického fondu. | Zvažte zvýšení limitu databáze zvýšit počet jednotek Dtu elastického fondu pokud je to možné, nebo odebrat z fondu elastické databáze. |
| 40889 |EX_USER |Počet jednotek Dtu nebo limit úložiště elastického fondu. %. * ls' nelze zmenšit, vzhledem k tomu, které by poskytují dostatek úložného prostoru pro jeho databáze. |Název elastického fondu. |Probíhá pokus o snížit limit úložiště elastického fondu pod jeho využití úložiště. | Zvažte snížení využití úložiště jednotlivých databází v elastickém fondu nebo odebrat databáze z fondu, abyste mohli snížit jeho Dtu nebo limit úložiště. |
| 40891 |EX_USER |Minimální počet jednotek DTU na databázi (%d) nemůže být delší než maximální počet jednotek DTU na databázi (%d). |Minimální počet jednotek DTU na databázi; Maximální počet jednotek DTU na databázi. |Došlo k pokusu o nastavení minimální počet jednotek DTU na databázi vyšší než maximální počet jednotek DTU na databázi. |Zajistěte, aby že minimální počet jednotek DTU na databáze není delší než maximální počet jednotek DTU na databázi. |
| Bude doplněno |EX_USER |Velikost úložiště pro může jedna databáze v elastickém fondu nesmí přesahovat maximální velikost povolenou ' %. * ls' služby elastického fondu vrstvy. |úrovně služby elastického fondu |Maximální velikost databáze přesahuje maximální velikost povolenou úrovně služby elastického fondu. |Nastavte maximální velikost databáze v rámci maximální velikost povolenou úrovně služby elastického fondu. |

Související témata:

* [Vytvoření fondu elastické databáze (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Správa elastického fondu (C#)](sql-database-elastic-pool-manage-csharp.md). 
* [Vytvoření fondu elastické databáze (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Sledování a správě fondu elastické databáze (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Obecné chyby
Následující chyby nespadají do všech předchozích kategorií.

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) není platný název, protože obsahuje neplatné znaky. |
| 18452 |14 |Přihlášení se nezdařilo. Přihlášení proběhlo z nedůvěryhodné domény a nelze použít s Windows authentication.%. & #x2a; ls (přihlášení systému Windows není podporováno v této verzi systému SQL Server). |
| 18456 |14 |Přihlášení uživatele se nezdařilo. %. & #x2a;ls'.%. & #x2a; ls %. & #x2a; ls (přihlášení se nezdařilo pro uživatele "%. & #x2a; ls". Změna hesla se nezdařila. Změna hesla při přihlašování není podporováno v této verzi systému SQL Server.) |
| 18470 |14 |Přihlášení se nezdařilo pro uživatele "%. & #x2a; ls". Důvod: Účet je disabled.%. & #x2a; ls |
| 40014 |16 |Více databází nelze použít ve stejné transakci. |
| 40054 |16 |Tabulky bez clusterovaného indexu nejsou podporovány v této verzi systému SQL Server. Vytvořit clusterovaný index a zkuste to znovu. |
| 40133 |15 |Tato operace není podporována v této verzi systému SQL Server. |
| 40506 |16 |Zadaný kód SID je neplatný pro tuto verzi systému SQL Server. |
| 40507 |16 |' %. & #x2a; ls se nedá vyvolat, s parametry v této verzi systému SQL Server. |
| 40508 |16 |Příkaz USE není podporovaný pro přepínání databází. Pro připojení k jiné databázi, použijte nové připojení. |
| 40510 |16 |Příkaz '%. & #x2a; ls' není podporován v této verzi systému SQL Server |
| 40511 |16 |Integrovaná funkce '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40512 |16 |Zastaralé funkce "%ls" v této verzi systému SQL Server nepodporuje. |
| 40513 |16 |Server proměnné '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40514 |16 |v této verzi systému SQL Server nepodporuje '%ls'. |
| 40515 |16 |Odkaz na název databáze nebo serveru v '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40516 |16 |Globální dočasné objekty nejsou v této verzi systému SQL Server podporovány. |
| 40517 |16 |Možnost klíčového slova nebo příkazu '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40518 |16 |Příkaz DBCC '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40520 |16 |Zabezpečitelná Třída '% S_MSG' není v této verzi systému SQL Server podporována. |
| 40521 |16 |Zabezpečitelná Třída '% S_MSG' není v oboru serveru v této verzi systému SQL Server podporována. |
| 40522 |16 |Typ objektu "%. & #x2a; ls" databáze není podporován v této verzi systému SQL Server. |
| 40523 |16 |Vytvoření implicitního uživatele '%. & #x2a; ls' není podporován v této verzi systému SQL Server. Před jeho použitím explicitně vytvořte uživateli. |
| 40524 |16 |Datový typ '%. & #x2a; ls' není v této verzi systému SQL Server podporována. |
| 40525 |16 |S '%.ls' není podporován v této verzi systému SQL Server. |
| 40526 |16 |' %. & #x2a; zprostředkovatele sady řádků ls' v této verzi systému SQL Server není podporována. |
| 40527 |16 |Odkazované servery nejsou v této verzi systému SQL Server podporovány. |
| 40528 |16 |Nelze mapovat uživatele na certifikáty, asymetrické klíče ani na přihlášení systému Windows v této verzi systému SQL Server. |
| 40529 |16 |Integrovaná funkce "%. & #x2a; ls" v zosobnění kontextu není podporováno v této verzi systému SQL Server. |
| 40532 |11 |Nelze otevřít serveru "%. & #x2a; ls" požadovaný v přihlášení. Přihlášení se nezdařilo. |
| 40553 |16 |Relace byla ukončena z důvodu nadměrného využití paměti. Zkuste upravit dotaz zpracovat méně řádků.<br/><br/> Omezení počtu `ORDER BY` a `GROUP BY` operace v kódu jazyka Transact-SQL pomáhá snížit požadavky na paměť tohoto dotazu. |
| 40604 |16 |Může není CREATE/ALTER DATABASE, protože byste překročili kvótu serveru. |
| 40606 |16 |V této verzi systému SQL Server nepodporuje připojení databáze. |
| 40607 |16 |Přihlášení systému Windows nejsou v této verzi systému SQL Server podporovány. |
| 40611 |16 |Servery můžete mít definovaný maximálně 128 pravidel brány firewall. |
| 40614 |16 |Počáteční IP adresa pravidla brány firewall nesmí přesahovat koncovou IP adresu. |
| 40615 |16 |Server {0}' požadovaný v přihlášení nelze otevřít. Klient s IP adresou objekt {1}' nemá povolen přístup k serveru.<br /><br />Pokud chcete povolit přístup, použijte portál databáze SQL nebo spusťte sp\_nastavit\_brány firewall\_pravidlo v hlavní databázi, a vytvořte tak pravidlo brány firewall pro tuto IP adresu nebo rozsah adres. To může trvat až pět minut, než se změna projeví. |
| 40617 |16 |Název pravidla brány firewall začínající s (název pravidla) je příliš dlouhý. Maximální délka je 128. |
| 40618 |16 |Název pravidla brány firewall nemůže být prázdný. |
| 40620 |16 |Přihlášení se nezdařilo pro uživatele "%. & #x2a; ls". Změna hesla se nezdařila. Změna hesla při přihlašování není podporována v této verzi systému SQL Server. |
| 40627 |20 |Probíhá operace na serveru: {0} a databáze objekt {1}. Počkejte prosím několik minut, než to zkusíte znovu. |
| 40630 |16 |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože je příliš krátké. |
| 40631 |16 |Heslo, které jste zadali, je příliš dlouhý. Heslo musí mít víc než 128 znaků. |
| 40632 |16 |Ověření hesla se nezdařilo. Heslo nesplňuje požadavky zásad, protože není dostatečně komplexní. |
| 40636 |16 |Nemůžete použít název vyhrazené databáze "%. & #x2a; ls" v této operaci. |
| 40638 |16 |Neplatné id odběru (id předplatného). Odběr neexistuje. |
| 40639 |16 |Požadavek neodpovídá schématu: (Chyba schématu). |
| 40640 |20 |Serveru došlo k neočekávané výjimce. |
| 40641 |16 |Zadané umístění je neplatné. |
| 40642 |17 |Server je aktuálně zaneprázdněna. Zkuste to prosím znova později. |
| 40643 |16 |Hodnota zadaná hlavička x-ms-version je neplatná. |
| 40644 |14 |Autorizace přístupu k zadanému odběru se nezdařila. |
| 40645 |16 |Servername (servername) nemůže být prázdný nebo mít hodnotu null. Ho může obsahovat jenom malá písmena 'a'-'z', číslice 0 – 9 a spojovník. Spojovník nesmí začínat ani končit název. |
| 40646 |16 |ID odběru nemůže být prázdný. |
| 40647 |16 |Předplatné (id předplatného) nemá serveru (servername). |
| 40648 |17 |Příliš mnoho požadavků prováděly. Zkuste to prosím znovu později. |
| 40649 |16 |Je zadán neplatný typ obsahu. Je podporován pouze application/xml. |
| 40650 |16 |Předplatné (id předplatného) neexistuje nebo není připraven k použití. |
| 40651 |16 |Nepodařilo se vytvořit server, protože předplatné (id předplatného) je zakázané. |
| 40652 |16 |Nelze přesunout nebo vytvořit server. Předplatné (id předplatného) způsobí překročení kvóty serveru. |
| 40671 |17 |Selhání komunikace mezi bránou rozhraní a služba správy. Zkuste to prosím znovu později. |
| 40852 |16 |Databázi nelze otevřít ' %. \*ls na serveru ' %. \*ls' požadovaný v přihlášení. Přístup k databázi je povolena pouze pomocí řetězce připojení s povoleným zabezpečením. Chcete-li přistupovat k databázi, změňte připojovací řetězce tak, aby obsahovala zabezpečení na serveru plně kvalifikovaný název domény -.database.windows "název serveru, rozhraní .net by měl být upraven na .database"název serveru". `secure`. windows.net. |
| 40914 | 16 | Nelze otevřít serveru '*[název serveru]*' požadovaný v přihlášení. Klient nemá povolen přístup k serveru.<br /><br />Pokud chcete vyřešit, zvažte přidání [pravidlo virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |V systému SQL Azure je zatížen a umísťuje horní limit na souběžných operací DB CRUD pro jeden server (například vytvořit databázi). Server určený v chybové zprávě byla překročena maximální počet souběžných připojení. Zkuste to znovu později. |
| 45169 |16 |V systému SQL azure je zatížení a je umístění horní limit počtu souběžných serveru operace CRUD pro v rámci jednoho předplatného (například vytvořit serveru). Předplatné zadané v chybové zprávě byla překročena maximální počet souběžných připojení a žádost byla odepřena. Zkuste to znovu později. |

## <a name="next-steps"></a>Další postup
* Přečtěte si informace o [funkce databáze Azure SQL](sql-database-features.md).
* Přečtěte si informace o [úrovních služeb](sql-database-service-tiers.md).

