---
title: Jak používat dávkování pro zvýšení výkonu aplikací Azure SQL Database
description: V tématu poskytuje důkazy této dávkování databázových operací výrazně imroves rychlosti a škálovatelnost aplikací Azure SQL Database. I když tyto dávkování techniky fungovat pro libovolnou databázi systému SQL Server, je zaměřená článek v Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d534e138af7a22b32fbf64e2200016091beac62f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Jak používat dávkování pro zvýšení výkonu aplikací databáze SQL
Dávkování operací do Azure SQL Database výrazně zvyšuje výkon a škálovatelnost aplikací. Chcete-li pochopit výhody, první část tohoto článku popisuje některé ukázkové výsledky testů, porovnávající postupného a dávkové požadavky na databázi SQL. Zbývající část článek ukazuje techniky, scénáře a požadavky umožňují použít dávkování úspěšně v aplikacích Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Proč je dávkování důležité pro databázi SQL?
Dávkování volání vzdálené služby je dobře známé strategie pro zvýšení výkonu a škálovatelnosti. Jsou pevně dané zpracování náklady na všechny interakce s vzdálené služby, jako je například serializace, přenos v síti a deserializace. Balení mnoho samostatné transakcí do jedné dávkové minimalizuje tyto náklady.

V tomto dokumentu jsme chcete prověřit různé SQL Database dávkování strategie a scénáře. I když tyto strategie jsou také důležité pro místní aplikace, které používají systém SQL Server, tady je několik důvodů pro zvýraznění dávkování pro databázi SQL. použití:

* Je potenciálně vyšší latence sítě přístup k databázi SQL, zejména pokud v přístupu k databázi SQL z mimo stejného datového centra Microsoft Azure.
* Víceklientské vlastnosti SQL Database znamená, že efektivitu na data přístup vrstvy odpovídá celkové škálovatelnost databáze. Databáze SQL zabránit přivlastňuje databázových prostředků na úkor ostatních klientů žádné jednoho klienta nebo uživatele. Databáze SQL v reakci na využití překračující předdefinované kvóty, můžete snížit propustnost nebo odpovědět s omezení výjimky. Efektivitu své činnosti, jako je například dávkování, umožňují provést u databáze SQL dříve, než dorazila těchto mezních hodnot. 
* Dávkování je také efektivní pro architektury, které používají více databází (horizontálního dělení). Efektivitu interakce se jednotlivých jednotek databáze je stále klíčovým faktorem vaší celkovou škálovatelnost. 

Jednou z výhod používání databáze SQL je, že nemáte ke správě serverů, které jsou hostiteli databáze. Však této spravované infrastruktury také znamená, že můžete mít jinak myslet optimalizace databáze. Už můžete zobrazit ke zlepšení databáze hardware nebo síť infrastruktury. Microsoft Azure řídí těchto prostředích. Hlavní oblasti, která můžete řídit je, jak vaše aplikace komunikuje s databází SQL. Dávkování je jedním z těchto optimalizace. 

První část papíru prověří různé dávkování techniky pro aplikace .NET, které používají SQL Database. Poslední dvě části se věnují dávkování pokyny a scénáře.

## <a name="batching-strategies"></a>Dávkování strategie
### <a name="note-about-timing-results-in-this-article"></a>Všimněte si o výsledcích časování v tomto článku
> [!NOTE]
> Výsledky nejsou srovnávacích testů, ale jsou určené k zobrazení **relativní výkon**. Časování jsou založené na v průměru minimálně 10 test spustí. Operace se vloží do prázdná tabulka. Tyto testy byly měřená pre-V12 a jejich nemusí odpovídat nutně propustnosti, kterou může docházet v databázi V12 pomocí nové [DTU úrovně služeb](sql-database-service-tiers-dtu.md) nebo [úrovně služeb vCore](sql-database-service-tiers-vcore.md). Relativní výhodou dávkování technika by mělo být podobné.
> 
> 

### <a name="transactions"></a>Transakce
Podle všeho neobvyklé zahájíte o dávkování podle hovoříte o transakce. Ale použití transakce na straně klienta má vliv dávkování jemně straně serveru, který zlepšuje výkon. A transakce lze přidat pouze zadání několika řádků kódu, takže poskytují rychlý způsob, jak zvýšit výkon při sekvenčních operací.

Vezměte v úvahu následující C# kód, který obsahuje posloupnost vložení a aktualizace operací na jednoduché tabulky.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Následující kód ADO.NET postupně provádí tyto operace.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Nejlepší způsob, jak optimalizovat tento kód je implementace určitou formu klienta dávkování těchto volání. Ale je jednoduchý způsob, jak zvýšit výkon tento kód jednoduše zabalení pořadí volání v transakci. Zde je stejný kód, který používá transakce.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();

        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }

        transaction.Commit();
    }

Transakce se používá ve skutečnosti v obou těchto příkladech. V prvním příkladu každý jednotlivých volání je implicitní transakci. V druhém příkladu explicitní transakce zabalí všechny volání. Za v dokumentaci [předběžné transakčního protokolu](https://msdn.microsoft.com/library/ms186259.aspx), záznamy protokolu jsou vyprazdňuje na disk, když transakce potvrzena. Další volání uvedete v transakci, můžete tak zápis transakčního protokolu počkat, až je transakce potvrzena. V důsledku toho jsou povolení dávkování při zápisu do protokolu transakcí serveru.

V následující tabulce jsou uvedeny některé výsledky testování ad hoc. Testy provést stejné sekvenční vložení a bez transakce. Pro další perspektivy první sada testů spustili vzdáleně z přenosného počítače do databáze v Microsoft Azure. Druhá sada testů spustili z cloudové služby a databáze oba nacházejí ve stejném datovém centru Microsoft Azure (západní USA). Následující tabulka uvádí dobu v milisekundách sekvenční operace INSERT a bez transakce.

**Místním nasazením a Azure**:

| Operace | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure do Azure (stejného datového centra)**:

| Operace | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

Na základě předchozího výsledků testu zabalení jedné operace v transakci ve skutečnosti sníží výkon. Ale můžete zvýšit počet operací v rámci jedné transakce, zlepšování výkonu stane více označen. Rozdíl výkonu je také více patrné, dojde-li všechny operace v rámci datového centra Microsoft Azure. Zvýší latence používání databáze SQL z oblasti mimo datové centrum Microsoft Azure ruší výkonnější použití transakcí.

Přestože použití transakcí může zvýšit výkon, i nadále [sledovat osvědčené postupy pro připojení a transakce](https://msdn.microsoft.com/library/ms187484.aspx). Zachovat transakce co nejkratší a zavřete připojení k databázi po dokončení práce. Pomocí příkazu v předchozím příkladu zaručuje, že připojení je ukončeno po dokončení následných kód bloku.

Předchozí příklad ukazuje, můžete přidat místní transakce pro jakýkoli kód ADO.NET se dvěma řádky. Transakce nabízejí rychlý způsob, jak zlepšit výkon kód, který umožňuje sekvenční vložit, aktualizovat a odstraňovat operace. Ale nejrychlejší výkonu, zvažte změnu kód dále využít výhody dávkování na straně klienta, jako jsou parametry s hodnotou tabulky.

Další informace o transakcích v ADO.NET naleznete v tématu [místní transakce v ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry s hodnotou tabulky
Parametry s hodnotou tabulky podporují uživatelem definovaná tabulka typy jako parametry příkazy jazyka Transact-SQL, uložené procedury a funkce. Tato technika dávkování na straně klienta, umožní vám odesílat více řádků dat v rámci parametr s hodnotou tabulky. Pokud chcete používat parametry s hodnotou tabulky, nejdříve definujte typ tabulky. Následující příkaz Transact-SQL vytvoří typ tabulky s názvem **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


V kódu, můžete vytvořit **DataTable** s přesnou stejné názvy a typy typu tabulky. To předat **DataTable** parametr v textu dotazu nebo uložené proceduře volání. Následující příklad ukazuje, tento postup:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }

        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);

        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });

        cmd.ExecuteNonQuery();
    }

V předchozím příkladu **SqlCommand** objekt vloží řádky z parametr s hodnotou tabulky **@TestTvp**. Dříve vytvořenou **DataTable** objektu je přiřazen tento parametr se **SqlCommand.Parameters.Add** metoda. Dávkování vloží jedno volání výrazně zvyšuje výkon přes sekvenční vložení.

Chcete-li zlepšit další předchozí příklad, použijte uložené procedury místo příkaz založený na textu. Následující příkaz Transact-SQL vytvoří uložené procedury, která přebírá **SimpleTestTableType** parametr s hodnotou tabulky.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Změňte **SqlCommand** deklarace v předchozím příkladu kódu pro následující objekt.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Ve většině případů parametry s hodnotou tabulky mít ekvivalentní, nebo lepší výkon než jinými technikami, dávkování. Parametry s hodnotou tabulky jsou často vhodnější, protože jsou flexibilnější než jiné možnosti. Jinými technikami, jako je například hromadného kopírování SQL, například povolit jenom vkládání nových řádků. Ale s parametry s hodnotou tabulky, můžete použít logiku v uložené proceduře k určení, které řádky jsou aktualizace a který se vloží. Typ tabulky můžete také upravit tak, aby obsahovala sloupec "Operace", která určuje, zda zadaný řádek by měl být vložit, aktualizovat nebo odstranit.

V následující tabulce jsou uvedeny ad-hoc výsledků testů pro použití s hodnotou tabulky parametrů v milisekundách.

| Operace | Místním nasazením a Azure (ms) | Azure stejného datového centra (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

Zvýšení výkonu z dávkování je okamžitě zřejmá. V předchozím testu sekvenčních 1000 operace trvalo 129 sekund mimo datové centrum a 21 sekund z v rámci datového centra. Ale s parametry s hodnotou tabulky 1000 operace trvat jenom 2.6 sekund mimo datové centrum a 0,4 sekundy v rámci datového centra.

Další informace o parametry s hodnotou tabulky najdete v tématu [zavolat parametry](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Hromadné kopírování SQL
Hromadné kopírování SQL je další způsob vložení velkých objemů dat do cílové databáze. Aplikace .NET mohou použít **SqlBulkCopy** třída provést hromadné operace vložení. **SqlBulkCopy** je podobně jako u nástroj příkazového řádku **Bcp.exe**, nebo pomocí příkazu Transact-SQL **BULK INSERT**. Následující příklad kódu ukazuje, jak hromadně kopírovat řádky ve zdroji **DataTable**, tabulky do cílové tabulky v systému SQL Server MyTable.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Existují případy, kdy je hromadné kopírování upřednostňované nad parametry s hodnotou tabulky. Podívejte se na tabulku porovnání s hodnotou tabulky parametrů versus BULK INSERT operace v článku [zavolat parametry](https://msdn.microsoft.com/library/bb510489.aspx).

Zobrazit následující výsledky testů ad-hoc výkon dávkování s **SqlBulkCopy** v milisekundách.

| Operace | Místním nasazením a Azure (ms) | Azure stejného datového centra (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

V menší velikosti dávky, použijte parametry s hodnotou tabulky outperformed **SqlBulkCopy** třídy. Ale **SqlBulkCopy** provést 12-31 % rychlejší než parametry s hodnotou tabulky pro testy 1 000 a 10 000 řádků. Jako parametry s hodnotou tabulky **SqlBulkCopy** je vhodný pro dávkové vložení, zejména v případě, že ve srovnání s výkon operací jiný-zpracovat v dávce.

Další informace o hromadné kopírování v ADO.NET naleznete v tématu [operace hromadného kopírování v systému SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Příkazy s parametry vložit více řádků
Jeden alternativou k malé dávky je vytvořit velké parametrizované příkaz INSERT, který se vloží více řádků. Následující příklad kódu ukazuje tento postup.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();

        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

        SqlCommand cmd = new SqlCommand(insertCommand, connection);

        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }

        cmd.ExecuteNonQuery();
    }


Tento příklad slouží k zobrazení základní koncept. Realističtější scénář by projít požadované entity současně vytvořit řetězec dotazu a parametry příkazu. Jste omezený na celkem parametry dotazu 2100, toto nastavení omezuje celkový počet řádků, které lze zpracovat tímto způsobem.

Následující výsledky testů ad-hoc zobrazit výkon tento typ příkazu insert v milisekundách.

| Operace | Parametry s hodnotou tabulky (ms) | Vložení jedním příkazem (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

Tento přístup může být mírně rychlejší pro balíků, které jsou menší než 100 řádků. I když zlepšení je malý, tento postup je další možností, které může fungovat i ve vašem scénáři konkrétní aplikaci.

### <a name="dataadapter"></a>DataAdapter
**DataAdapter** třída umožňuje upravovat **datovou sadu** objektu a pak odeslat provedené změny jako operace INSERT, UPDATE a DELETE. Pokud používáte **DataAdapter** tímto způsobem je důležité si uvědomit, že samostatné volání jsou vytvářeny pro každou operaci distinct. Chcete-li zvýšit výkon, použijte **UpdateBatchSize** vlastnost počet operací, které by měl zpracovat v dávce najednou. Další informace najdete v tématu [provádění dávkové operace pomocí DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Rozhraní Entity framework
Rozhraní Entity Framework aktuálně nepodporuje dávkování. Různé vývojáři v komunitě Pokusili jste se ukazují alternativní řešení, například přepsání **SaveChanges** metoda. Ale řešení jsou obvykle komplexní a přizpůsobit v aplikaci a datového modelu. Rozhraní Entity Framework projektu webu codeplex má v současnosti diskusní stránky na žádost o této funkce. Tato diskuse najdete v tématu [poznámky ze schůzky návrhu - 2 srpen 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Pro úplnost se domníváme, že je potřeba mluvit o XML jako strategie dávkování. Použití XML má však žádné výhody přes jiné metody a několik nevýhody. Přístup je podobná parametry s hodnotou tabulky, ale souboru XML nebo řetězec předaný uložené proceduře místo uživatelem definovaná tabulka. Uložená procedura analyzuje příkazy v uložené proceduře.

Existuje několik nevýhod tohoto přístupu:

* Práce s XML může být náročná a chyba náchylné k chybám.
* Analýza kódu XML v databázi, může být náročná na prostředky procesoru.
* Ve většině případů je tato metoda pomalejší než parametry s hodnotou tabulky.

Z těchto důvodů se nedoporučuje použití XML pro dotazy batch.

## <a name="batching-considerations"></a>Dávkování aspekty
Následující části obsahují další pokyny pro použití dávkování v aplikacích databáze SQL.

### <a name="tradeoffs"></a>Kompromisy
V závislosti na vaší architektury dávkování může zahrnovat kompromis mezi výkon a odolnost. Zvažte například scénář, kde vaše role neočekávaně ocitne mimo provoz. Pokud ztratíte jeden řádek dat, dopad je menší než dopad ztráty velké dávku neodeslané řádků. Existuje větší riziko, pokud vyrovnávací paměť řádky před jejich odesláním do databáze v zadaném časovém období.

Z důvodu této kompromis Vyhodnoťte typ operací, že batch můžete. Batch důkladnějšímu (větší dávky a delší dobu windows) s daty, která je méně kritický.

### <a name="batch-size"></a>Velikost dávky
V našich testech se obvykle žádnou výhodu nejnovější velké dávky na menší bloky. Ve skutečnosti tento pododdíl často za následek nižší výkon než jeden velký dávky. Zvažte například scénář, kam chcete vložit 1 000 řádků. Následující tabulka ukazuje, jak dlouho Pokud chcete používat parametry s hodnotou tabulky vložit 1 000 řádků při rozdělit do menších dávek.

| Velikost dávky | Iterace | Parametry s hodnotou tabulky (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

Uvidíte, že je všechny najednou odeslat je nejlepší výkon pro 1 000 řádků. Jiné testy (není tady zobrazené) bylo malé výkonnější dávce 10000 řádek rozdělit na dvě dávky 5000. Ale schématu tabulky pro tyto testy je poměrně jednoduché, měli byste provést testy na konkrétních dat a velikosti dávky k ověření těchto zjištění.

Dalším faktorem vzít v úvahu je, že pokud celkový počet batch příliš velká, SQL Database může omezení a odmítnout potvrzení dávky. Nejlepších výsledků dosáhnete otestujte konkrétní scénář k určení, jestli je velikost dávky ideální. Velikost dávky konfigurovatelné za běhu, aby povolit rychlé úpravy na základě výkonu nebo chyby.

Nakonec vyrovnávat velikost dávky s riziky spojenými s dávkování. Pokud nejsou přechodné chyby nebo roli selže, vezměte v úvahu důsledky opakováním operace nebo ke ztrátě dat v dávce.

### <a name="parallel-processing"></a>Paralelní zpracování
Co když trvalo přístup snižuje velikost dávky, ale používá více vláken k provedení práce? Naše testy znovu, ukázalo, že několik menších vícevláknové dávek zpravidla dělá horší, než jeden větší batch. Následující test se pokusí vložit řádky 1000 do jedné nebo více paralelních dávek. Tento test ukazuje, jak více souběžných dávky ve skutečnosti snížení výkonu.

| Velikost dávky [iterací] | Dva vláken (ms) | Čtyři vláken (ms) | Šest vláken (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Výsledky nejsou srovnávacích testů. Najdete v článku [Poznámka o výsledcích časování v tomto tématu](#note-about-timing-results-in-this-topic).
> 
> 

Existuje několik možných důvodů pro snížení výkonu z důvodu paralelismus:

* Existuje více souběžných sítě volání místo jeden.
* Více operací pro jedinou tabulku může způsobit konflikty a blokování.
* Existují režijní náklady spojené s více vláken.
* Náklady otevření více připojení převáží výhodou paralelní zpracování.

Pokud cílíte různých tabulek nebo databází, je možné zobrazit některé výkonu získáte pomocí této strategie. Scénář pro tento postup by horizontálního dělení databáze nebo federace. Horizontálního dělení používá více databází a směruje různých datových na každou databázi. Pokud každé malé dávky k jiné databázi, může být efektivnější pak paralelní provádění operací. Zvýšení výkonu však není dostatečně významné chcete použít jako základ pro rozhodnutí a použít horizontálního dělení databáze ve vašem řešení.

V některé návrhy můžete paralelní provádění menší dávek způsobit lepší propustnost požadavky v rámci systému zatížení. V takovém případě i když je rychlejší zpracovat jeden větší batch, více listů paralelní zpracování může být efektivnější.

Pokud používáte paralelní provádění, vezměte v úvahu řízení maximální počet pracovních vláken. Zmenšete počet může být výsledkem nižší výskyt kolizí a rychlejší dobu provádění. Zvažte také další zátěže, které to umístí na cílovou databázi v připojení a transakce.

### <a name="related-performance-factors"></a>Faktory související výkonu
Dávkování ovlivní také typické pokyny na výkon databáze. Můžete například vložit pro tabulky, které mají velký primární klíč, nebo mnoho neclusterované indexy je snížit výkon.

Pokud parametry s hodnotou tabulky pomocí uložené procedury, můžete použít příkaz **SET NOCOUNT ON** na začátku procesu. Tento příkaz potlačí návrat počet ovlivněných řádků v postupu. Ale v našich testech se použití **SET NOCOUNT ON** nemělo žádný vliv nebo snížení výkonu. Test uložené procedury bylo jednoduché s jedním **vložit** příkazu z parametru s hodnotou tabulky. Je možné, že by složitější uložené procedury těžit z tohoto prohlášení. Ale Nepředpokládejte, že přidání **SET NOCOUNT ON** uložené procedury automaticky zvyšuje výkon. Chcete-li pochopení dopadu, otestovat vaše uložené procedury s i bez **SET NOCOUNT ON** příkaz.

## <a name="batching-scenarios"></a>Dávkování scénáře
Následující části popisují, jak používat parametry s hodnotou tabulky v tři scénáře aplikací. První scénář popisuje, jak ukládání do vyrovnávací paměti a dávkování vzájemně spolupracují. Druhý scénář zlepšuje výkon provádění operací s podrobnostmi v jedné uložené procedury volání. Poslední scénář popisuje, jak používat parametry s hodnotou tabulky v operace "UPSERT".

### <a name="buffering"></a>Ukládání do vyrovnávací paměti
I když je několik scénářů, které jsou zřejmé kandidáta pro dávkování, existuje mnoho scénářů, které může využívat výhod dávkování zpožděné zpracování. Zpožděné zpracování však představuje větší riziko, že data jsou v případě neočekávaného selhání ztraceny. Je důležité pochopit toto riziko a zvážit důsledky.

Představte si třeba webové aplikace, která sleduje navigační historii jednotlivých uživatelů. S každým požadavkem stránky může aplikace Změna databáze volání k zaznamenání zobrazení stránky uživatele. Ale vyšší výkon a škálovatelnost lze dosáhnout ukládání do vyrovnávací paměti navigační aktivity uživatelů a pak odešle tato data do databáze v dávkách. Můžete aktivovat aktualizaci databáze uplynulý čas nebo velikost vyrovnávací paměti. Pravidlo může například určit, že by měl po 20 sekund nebo pokud vyrovnávací paměť dosáhne 1000 položek zpracování dávky.

Následující příklad kódu používá [reaktivní rozšíření - Rx](https://msdn.microsoft.com/data/gg577609) ke zpracování ve vyrovnávací paměti události vyvolané službou třída monitorování. Když vyrovnávací vyplní celé nebo dosaženo časového limitu, je odeslána dávky dat uživatele do databáze s parametr s hodnotou tabulky.

Následující třídy NavHistoryData modelů navigační podrobností o uživateli. Obsahuje základní informace, jako je identifikátor uživatele, adresu URL přístup a doba přístupu k.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

Třída NavHistoryDataMonitor je zodpovědná za ukládání do vyrovnávací paměti navigační data uživatele do databáze. Obsahuje metody, RecordUserNavigationEntry, který odpovídá zobrazením **OnAdded** událostí. Následující kód ukazuje konstruktor logiky, která používá Rx můžete vytvořit kolekci pozorovatelné založené na události. Pak přihlásí se k této kolekci pozorovatelné s metodou vyrovnávací paměti. Přetížení Určuje, že vyrovnávací paměti by měly být odeslány každých 20 sekund nebo 1 000 položek.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Obslužná rutina převede všechny položky ve vyrovnávací paměti typu s hodnotou tabulky a pak předá tento typ uložené procedury, která zpracovává dávky. Následující kód ukazuje dokončení definice pro NavHistoryDataEventArgs i NavHistoryDataMonitor třídy.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }

    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;

        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }

        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }

        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }

            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();

                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;

                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });

                cmd.ExecuteNonQuery();
            }
        }
    }

Pokud chcete používat tuto třídu vyrovnávací paměti, aplikace vytvoří objekt statické NavHistoryDataMonitor. Pokaždé, když uživatel přistupuje k na stránce aplikace volá metodu NavHistoryDataMonitor.RecordUserNavigationEntry. Abyste dbali odesláním těchto položek do databáze v dávkách pokračuje logice vyrovnávací paměti.

### <a name="master-detail"></a>Hlavní podrobností
Parametry s hodnotou tabulky jsou užitečné pro jednoduché scénáře INSERT. Však může být více náročné dávkového vložení, které zahrnují více než jedna tabulka. Scénář "hlavního a podrobného" je dobrým příkladem. Hlavní tabulka obsahuje primární entity. Minimálně jedna tabulka podrobností uložit víc dat o entitě. V tomto scénáři vynutit relace cizích klíčů relace podrobnosti jedinečný hlavní entity. Vezměte v úvahu zjednodušenou verzi PurchaseOrder tabulka a její přidružené OrderDetail tabulkou. Následující příkaz Transact-SQL vytvoří tabulku PurchaseOrder s čtyři sloupce: OrderID, OrderDate, CustomerID a stav.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Každý pořadí obsahuje jeden nebo více nákupy produktu. Tyto informace se zaznamená v tabulce PurchaseOrderDetail. Následující příkaz Transact-SQL vytvoří tabulku PurchaseOrderDetail s pěti sloupce: OrderID, OrderDetailID, ProductID, UnitPrice a OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

Sloupce OrderID v tabulce PurchaseOrderDetail musíte odkázat pořadí z tabulky PurchaseOrder. Následující definice cizího klíče vynucuje toto omezení.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Aby bylo možné používat parametry s hodnotou tabulky, musí mít jeden typ uživatelem definovaná tabulka pro každou cílovou tabulku.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO

    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Poté definujte uložené procedury, která přijímá tabulky z těchto typů. Tento postup umožňuje aplikaci místně dávky sadu objednávek a podrobnosti o pořadí v jediném volání. Následující příkaz Transact-SQL poskytuje deklaraci dokončení uložené procedury v tomto příkladu pořadí nákupu.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;

    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );

          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

V tomto příkladu místně definované @IdentityLink ukládá skutečnými hodnotami OrderID z nově vložené řádky tabulky. Tyto identifikátory pořadí se liší od dočasné hodnoty OrderID @orders a @details parametry s hodnotou tabulky. Z tohoto důvodu @IdentityLink pak připojí OrderID hodnoty z tabulky @orders parametr skutečné hodnoty OrderID pro nové řádky v tabulce PurchaseOrder. Po provedení tohoto kroku @IdentityLink tabulky můžete usnadnit vkládání podrobnosti pořadí s skutečné OrderID, který splňuje omezení cizího klíče.

Tuto uloženou proceduru lze z kódu nebo jiná volání jazyka Transact-SQL. Parametry s hodnotou tabulky části tohoto dokumentu příklad kódu. Následující příkaz Transact-SQL ukazuje způsob volání sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType

    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')

    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)

    exec sp_InsertOrdersBatch @orders, @details

Toto řešení umožňuje každé dávky používat sadu OrderID hodnoty, které začínají znakem 1. Tyto hodnoty dočasné OrderID popisu relací v dávce, ale skutečný OrderID hodnoty jsou určeny v době operace insert. Můžete spustit stejné příkazy v předchozím příkladu opakovaně a generovat jedinečný objednávky v databázi. Z tohoto důvodu je vhodné přidat další kód nebo databáze logiku, která brání duplicitní objednávky při použití tohoto dávkování techniku.

Tento příklad ukazuje, že i složitější databázových operací, jako je například seznam podrobnosti operace, může zpracovat v dávce pomocí parametry s hodnotou tabulky.

### <a name="upsert"></a>UPSERT
Jiné dávkování scénář zahrnuje současně aktualizaci existujících řádků a vkládání nových řádků. Tato operace se někdy označuje jako "UPSERT" (aktualizace + insert) operaci. Místo samostatné volání k vložení a aktualizace, je nejvhodnější pro tuto úlohu příkazu MERGE. Příkaz MERGE můžete provést i insert a operace v jednom volání aktualizace.

Parametry s hodnotou tabulky můžete použít s příkazem MERGE k provádění aktualizací a vložení. Představte si třeba zjednodušené tabulky zaměstnanců, která obsahuje následující sloupce: EmployeeID, FirstName, LastName, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

V tomto příkladu můžete skutečnost, že je SocialSecurityNumber jedinečný ke sloučení více zaměstnanců. Nejprve vytvořte typ uživatelem definovaná tabulka:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Dále vytvořte uloženou proceduru nebo napsat kód, který používá příkaz MERGE k provádění aktualizace a vložit. Následující příklad používá příkazu MERGE na parametr s hodnotou tabulky @employees, typu EmployeeTableType. Obsah @employees tabulky nejsou zobrazeny zde.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Další informace najdete v dokumentaci a příklady příkazu MERGE. I když můžete provést stejný pracovní v kroku více uložené volání procedury s oddělené INSERT a operace aktualizace, příkazu MERGE je efektivnější. Kód databáze můžete také vytvořit volání jazyka Transact-SQL, která pomocí příkazu MERGE přímo bez nutnosti dvě volání databáze pro příkaz INSERT a UPDATE.

## <a name="recommendation-summary"></a>Souhrnná doporučení
Následující seznam obsahuje souhrn dávkování doporučení popsaných v tomto článku:

* Pokud chcete zvýšit výkon a škálovatelnost databáze SQL aplikace pomocí ukládání do vyrovnávací paměti a dávkování.
* Pochopení kompromisy mezi dávkování nebo ukládání do vyrovnávací paměti a odolnost. Při selhání role riziko ztráty nezpracované dávku důležitých podnikových dat vyváží výkon výhodou dávkování.
* Pokus zachovat všechna volání do databáze v rámci jednoho datového centra ke snížení latence.
* Pokud si zvolíte jednu dávkování techniku, parametry s hodnotou tabulky nabízí nejlepší výkon a flexibilitu.
* Nejrychlejší vložení výkonu postupujte podle následujících obecných pokynů ale otestovat váš scénář:
  * < 100 řádků pomocí jedné parametrizovaného příkaz INSERT.
  * < 1 000 řádků použijte parametry s hodnotou tabulky.
  * Pro > = 1 000 řádků, použijte SqlBulkCopy.
* Pro aktualizaci a operace odstranění, použijte parametry s hodnotou tabulky s logiky uložené procedury, která určuje správné operaci na každý řádek v tabulce parametru.
* Pokyny pro velikost dávky:
  * Použijte největší velikosti dávky, které dávají smysl pro vaše aplikace a podnikových požadavků.
  * Vyrovnávat zvýšení výkonu velkých dávek s rizika dočasné nebo závažné selhání. Co je důsledkem opakování nebo ztrátě dat v dávce? 
  * Otestujte největší velikost dávky k ověření, že databáze SQL není odmítnout ho.
  * Vytvořte nastavení konfigurace tohoto ovládacího prvku dávkování, jako je například velikost dávky nebo vyrovnávací paměti časový interval. Tato nastavení poskytují flexibilitu. Dávkování chování v produkčním prostředí můžete změnit bez opětovného nasazení cloudové služby.
* Vyhněte se paralelní zpracování dávek, které působí na jednotlivé tabulky v jedné databáze. Pokud si zvolíte jedné dávkové rozdělit mezi několik pracovních vláken, spusťte testy můžete určit ideální počet vláken. Po neurčené prahová hodnota další podprocesy bude snížit výkon, a nikoli zvýšit ji.
* Vezměte v úvahu ukládání do vyrovnávací paměti na velikost a čas jako způsob implementace dávkování pro více scénářů.

## <a name="next-steps"></a>Další postup
Tento článek zaměřuje na jak návrhu databáze a kódování techniky související s dávkování může zlepšit výkon aplikace a škálovatelnost. Ale toto je pouze jediný faktor v vaše celková strategie. Další způsoby, jak zvýšit výkon a škálovatelnost, najdete v části [Azure SQL Database – Průvodce výkonem pro izolované databáze](sql-database-performance-guidance.md) a [cenové a výkonové požadavky fondu elastické databáze](sql-database-elastic-pool-guidance.md).

