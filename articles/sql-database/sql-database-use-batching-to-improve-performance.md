---
title: Použití dávkování pro zvýšení výkonu aplikací Azure SQL Database
description: Téma poskytuje důkazy této dávkování databázové operace značně imroves rychlost a škálovatelnost aplikací Azure SQL Database. I když tyto dávkování techniky fungují pro libovolnou databázi systému SQL Server, zaměřuje článku se na Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: f347543bbea11329cf4bb7c03dac6ccf7f04ac77
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455384"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Použití dávkování pro zvýšení výkonu aplikací SQL Database

Dávkování operací do služby Azure SQL Database výrazně zlepšuje výkon a škálovatelnost aplikací. Chcete-li pochopit výhody, první část tohoto článku popisuje některé výsledky testů ukázky, které porovnávají sekvenční a dávkové žádosti do služby SQL Database. Zbytek tohoto článku ukazuje techniky, scénáře a aspekty, které umožňují použití dávkování úspěšně v aplikacích Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Proč je dávkování důležité pro službu SQL Database

Dávkové zpracování volání vzdálené služby je dobře známé strategie pro zvýšení výkonu a škálovatelnosti. Jsou pevně dané náklady zpracování, které všechny interakce s vzdálené služby, jako je například serializace, přenos v síti a deserializace. Balení mnoho samostatné transakce do jedné dávkové minimalizuje tyto náklady.

V tomto dokumentu budeme chtít různých SQL Database dávkování strategie a scénáře. I když tyto strategie jsou také důležité pro místní aplikace, které používají SQL Server, existuje několik důvodů pro zvýraznění použití dávkování pro službu SQL Database:

* Je potenciálně větší latence sítě v přístupu k SQL Database, zejména v případě, že při přístupu k SQL Database z oblasti mimo stejné datové centrum Microsoft Azure.
* Vlastnosti víceklientské služby SQL Database znamená, že efektivitu koreluje vrstvu přístupu dat pro celkovou škálovatelnost databáze. SQL Database musí jediný tenant nebo uživatel zabránit přivlastňuje databázových prostředků na úkor jiných tenantů. SQL Database v reakci na využívání služeb nad rámec předdefinované kvóty, můžete snížit propustnosti nebo odpoví omezování výjimky. Efektivitu, jako jsou dávkové zpracování, umožňují více práce na SQL Database před překročení těchto omezení. 
* Dávkování platí také pro architektury, které používají více databází (sharding). Efektivity vaší interakce se každá jednotka databáze stále je ale klíčovým faktorem celkovou škálovatelnost. 

Jednou z výhod použití SQL Database je, že není nutné ke správě serverů, které hostují databáze. Tato spravovaná infrastruktura ale také znamená, že budete muset uvažovat jinak o optimalizace databáze. Můžete už zvažte vylepšení infrastruktury hardwarem nebo síťovými databáze. Microsoft Azure řídí těchto prostředích. Hlavní oblasti, které lze řízeně je, jak aplikace komunikuje se službou SQL Database. Dávkování je jedním z těchto optimalizacích. 

První část papíru prozkoumá různých technik vytváření dávkování pro aplikace .NET, které používají SQL Database. Poslední dvě části se věnují dávkování pokyny a scénáře.

## <a name="batching-strategies"></a>Strategie dávkování

### <a name="note-about-timing-results-in-this-article"></a>Mějte na paměti o časování výsledky v tomto článku

> [!NOTE]
> Výsledky nejsou srovnávací testy, ale jsou určené k zobrazení **relativní výkon**. Časování jsou vycházet z průměru minimálně 10 testovacích běhů. Operace se vloží do prázdné tabulky. Tyto testy se měřené pre-V12 a jejich nemusí odpovídat nutně propustnost, které můžete zaznamenat do databáze V12 pomocí nového [jednotek DTU úrovně služeb](sql-database-service-tiers-dtu.md) nebo [– vCore úrovně služeb](sql-database-service-tiers-vcore.md). Relativní výhody dávkování postup by měl vypadat přibližně.

### <a name="transactions"></a>Transakce

Vypadá to strangeová začít kontrolu dávkování podle diskuze o transakce. Ale použití transakcí na straně klienta má jemný dávkování efekt na straně serveru, který zlepšuje výkon. A transakce jde přidat jenom pár řádků kódu, takže poskytují rychlý způsob, jak zvýšit výkon při sekvenčních operací.

Vezměte v úvahu následující kód jazyka C#, která obsahuje posloupnost vložení a aktualizujte operace s jednoduchou tabulku.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Následující kód ADO.NET postupně provede tyto operace.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Nejlepší způsob, jak optimalizovat tento kód je implementace nějaké formy dávkování na straně klienta z těchto volání. Ale neexistuje jednoduchý způsob, jak zvýšit výkon tento kód jednoduše obalením sekvence volání v transakci. Zde je stejný kód, který používá transakce.

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

V obou těchto příkladech se používají ve skutečnosti transakce. V prvním příkladu je každého jednotlivého volání implicitní transakce. V druhém příkladu zabalí explicitní transakce všech volání. Za dokumentaci [dávky zápisu transakční protokol](https://msdn.microsoft.com/library/ms186259.aspx), záznamy protokolu jsou zapsány na disk při potvrzení transakce. Pokud uvedete více volání v transakci, můžete tak zápis do transakčního protokolu zpoždění, dokud je transakce potvrzena. V důsledku toho jsou povolení dávkování pro zápis do protokolu transakcí serveru.

V následující tabulce jsou uvedeny některé výsledky testování ad hoc. Testy provést stejnou sekvenční vložení a nemusíte transakce. Pro další perspektivy první sadu testů spustila vzdáleně z přenosného počítače do databáze v Microsoft Azure. Druhá sada testů spustili z cloudové služby a databáze i nacházejí ve stejném datacentru Microsoft Azure (USA – západ). Následující tabulka uvádí doba trvání v MS sekvenční operace vložení a nemusíte transakce.

**On-Premises do Azure**:

| Operace | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure do Azure (stejném datovém centru)**:

| Operace | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).

Podle předchozí výsledky testů, ve skutečnosti obtékání jediná operace v rámci transakce snižuje výkon. Ale zvyšuje počet operací v rámci jedné transakce stane více označena zlepšení výkonu. Rozdíly ve výkonnosti se snadněji postřehnutelné také dojde-li všechny operace v rámci datového centra Microsoft Azure. Zvýší latence používat SQL Database z oblasti mimo datové centrum Microsoft Azure ruší výkonový zisk plynoucí z použití transakcí.

I když využívání transakce může zvýšit výkon, nadále [dodržovat osvědčené postupy pro transakce a připojení](https://msdn.microsoft.com/library/ms187484.aspx). Zachovat transakce co nejkratší a ukončete připojení k databázi po dokončení práce. Používání příkaz v předchozím příkladu zaručuje, že se připojení uzavře, po dokončení bloku následující kód.

Předchozí příklad ukazuje, že můžete přidat místní transakce k veškerému kódu ADO.NET se dvěma řádky. Transakce nabízejí rychlý způsob, jak zlepšit výkon kód, který provádí sekvenční vložení, aktualizace a odstranění operace. Ale nejrychlejší výkon, zvažte možnost změnit kód dál využívat dávkování na straně klienta, jako jsou parametry s hodnotou tabulky.

Další informace o transakce v rozhraní ADO.NET, naleznete v tématu [místní transakce v rozhraní ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry Table-valued

Parametry Table-valued podporu uživatelem definovaná Tabulka typů jako parametrů příkazů jazyka Transact-SQL, uložených procedur a funkcí. Tato technika dávkování na straně klienta umožňuje odeslat více řádků v rámci parametr s hodnotou tabulky. Použití parametrů table-valued, nejprve definujte typ tabulky. Následující příkaz jazyka Transact-SQL umožňuje vytvořit typ tabulky s názvem **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


V kódu, můžete vytvořit **DataTable** s přesně stejné názvy a typy typu tabulky. Předá **DataTable** v parametru text dotazu nebo uložené procedury volání. Následující příklad ukazuje tento postup:

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

V předchozím příkladu **SqlCommand** objekt vloží řádků z parametru s hodnotou tabulky, **@TestTvp**. Dříve vytvořený **DataTable** je přiřazen objekt pro tento parametr se **SqlCommand.Parameters.Add** metoda. Dávkování vložení v jednom volání výrazně zvyšuje výkon přes sekvenční operace vložení.

Aby se zvýšil z předchozího příkladu dále, použití uložené procedury místo příkazu založený na textu. Následující příkaz jazyka Transact-SQL vytvoří uloženou proceduru, která přebírá **SimpleTestTableType** parametr s hodnotou tabulky.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Změňte **SqlCommand** objektu deklarace v předchozím příkladu následující kód.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Ve většině případů parametrů table-valued mají ekvivalentní, nebo lepší výkon než jiné postupy dávkování. Parametry Table-valued jsou často vhodnější, protože jsou flexibilnější, než jiné možnosti. Jiné techniky, jako je například hromadná kopie SQL, například povolit pouze vkládání nových řádků. Ale s parametry s hodnotou tabulky, můžete použít logiku v uložené proceduře k určení, které řádky jsou aktualizace a které jsou vloží. Typ tabulky můžete také upravit tak, aby obsahovala sloupec "Operace", která určuje, zda zadaný řádek by měl být vložen, aktualizoval nebo odstranil.

V následující tabulce jsou uvedeny ad-hoc výsledky testu pro použití parametrů table-valued v milisekundách.

| Operace | On-Premises do Azure (ms) | Stejné datové centrum Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Výkonový zisk plynoucí z dávkování je okamžitě zřejmý. V předchozím testu sekvenčních 1000 operace trvalo 129 sekund mimo datové centrum a 21 sekund z v rámci datového centra. Ale s parametry table-valued 1000 operace trvat pouze 2.6 sekund mimo datové centrum a 0.4 sekund v rámci datového centra.

Další informace o parametry table-valued najdete v tématu [Table-Valued parametry](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Hromadná kopie SQL

Hromadná kopie SQL je další způsob, jak vložit velkých objemů dat do cílové databáze. Aplikace .NET mohou použít **SqlBulkCopy** pro provádění hromadných operací vložení. **SqlBulkCopy** je podobné funkce jako nástroj příkazového řádku **Bcp.exe**, nebo pomocí příkazu jazyka Transact-SQL **BULK INSERT**. Následující příklad kódu ukazuje, jak hromadně kopírovat řádků ve zdroji **DataTable**, tabulky do cílové tabulky v SQL serveru, tabulka.

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

Existují případy, kdy hromadné kopírování je upřednostňované nad parametry s hodnotou tabulky. Zobrazit srovnávací tabulka z parametrů Table-Valued oproti operace HROMADNÉHO vložení v článku [Table-Valued parametry](https://msdn.microsoft.com/library/bb510489.aspx).

Výkon dávkování se zobrazit následující výsledky testů ad-hoc **SqlBulkCopy** v milisekundách.

| Operace | On-Premises do Azure (ms) | Stejné datové centrum Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

V menší velikosti dávky, použijte parametry table-valued překonal **SqlBulkCopy** třídy. Ale **SqlBulkCopy** provést 31. 12 % rychlejší než parametrů table-valued testů 1 000 a 10 000 řádků. Jako parametry table-valued **SqlBulkCopy** je dobrou volbou pro dávkové operace vložení, zejména v případě, že ve srovnání s výkon operací dávce.

Další informace o hromadné kopírování v ADO.NET, naleznete v tématu [operace hromadného kopírování na SQL serveru](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Příkazy s parametry vložit více řádků

Jeden alternativu pro malé dávky je pro sestavení velkých parametrizovaného příkazu INSERT, který vloží několik řádků. Následující příklad kódu ukazuje tento postup.

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


V tomto příkladu slouží k zobrazení základní princip. Vyzkoušet realističtější scénář by projít požadované entity, které chcete současně vytvořit řetězec dotazu a parametry příkazu. Jste omezeni na celkový počet parametrů dotazu 2100, toto nastavení omezuje celkový počet řádků, které mohou být zpracovány tímto způsobem.

Následující výsledky testů ad-hoc zobrazení výkonu tento typ příkazu insert v milisekundách.

| Operace | Parametry Table-valued (ms) | Jedním příkazem INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Tento přístup může být mírně rychlejší pro listy, které jsou kratší než 100 řádků. Sice malé zlepšení, tato technika je další možností, které může fungovat dobře ve vašem scénáři konkrétní aplikaci.

### <a name="dataadapter"></a>DataAdapter

**DataAdapter** třída umožňuje změnit **datovou sadu** objekt a potom odešlete změny jako operace INSERT, UPDATE a DELETE. Pokud používáte **DataAdapter** tímto způsobem, je důležité si uvědomit, že samostatné volání pro každou operaci distinct. Chcete-li zvýšit výkon, použijte **UpdateBatchSize** na počet operací, které by měl být dávce najednou. Další informace najdete v tématu [provádění dávky operací pomocí adaptérů dat](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Rozhraní Entity framework

Entity Framework nepodporuje aktuálně dávkování. Různými vývojáři v komunitě jste se pokusili ukazují alternativní řešení, jako je například přepsání **SaveChanges** metody. Ale řešení jsou obvykle složitý a vlastní aplikace a data modelu. Entity Framework projektu codeplex aktuálně obsahuje stránku diskuze na žádost o tuto funkci. Tato diskuse, naleznete v tématu [poznámky ze schůzky návrhu - 2. srpna 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

Pro úplnost domníváme, že je důležité mluvit o XML jako strategie dávkování. Použití XML má však žádné výhody oproti jiné metody a několik nevýhody. Tento přístup je podobný parametrů table-valued ale souboru XML nebo řetězec je předaný uložené proceduře místo uživatelem definovaná tabulka. Uložená procedura analyzuje příkazy v uložené proceduře.

Existuje několik nevýhod tohoto přístupu:

* Práce s XML může být náročné a náchylné k chybám.
* Při parsování kódu XML v databázi, může být náročné na CPU.
* Ve většině případů je pomalejší než parametrů table-valued této metody.

Z těchto důvodů použití XML pro dávkové dotazy, se nedoporučuje.

## <a name="batching-considerations"></a>Dávkování důležité informace

Následující části obsahují další pokyny k použití dávkování aplikace SQL Database.

### <a name="tradeoffs"></a>Kompromisy

V závislosti na vaší architektuře dávkování může zahrnovat kompromis mezi výkonem a odolnost proti chybám. Zvažte například scénář, kde vaše role se neočekávaně ocitne mimo provoz. Pokud ztratíte jeden řádek dat, dopad je menší než dopad ztráty dávky velké neodeslané řádků. Existuje větší riziko při buffer řádky před jejich odesláním do databáze v zadaném časovém období.

Kvůli kompromis Vyhodnoťte typ operace této služby batch můžete. Batch agresivnější (větších dávek a delší časová okna) data, která jsou méně důležité.

### <a name="batch-size"></a>Velikost dávky

V testech obvykle došlo k rozdělení velké dávky do menších bloků žádnou jinou výhodu. Ve skutečnosti tento pododdíl často za následek pomalejší výkon než jedné dávkové velké. Zvažte například scénář, ve které chcete vložit 1 000 řádků. Následující tabulka ukazuje, jak dlouho trvá pomocí parametrů table-valued vložte 1000 řádků při rozdělit do menších dávek.

| Velikost dávky | Iterace | Parametry Table-valued (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Uvidíte, že nejlepší výkon pro 1 000 řádků je odešlete je všechny najednou. V jiných testech (není tady zobrazené) bylo malé výkonnější batch 10000 řádek rozdělit na dvě dávky 5000. Ale schéma tabulky pro tyto testy je poměrně jednoduché, takže by měl provádět testy na určitá data a velikosti dávky k ověření tyto poznatky.

Dalším faktorem vzít v úvahu je, že pokud celkový počet batch stane příliš velký, SQL Database může omezit a odmítnout potvrdit dávku. Nejlepších výsledků dosáhnete Otestujte váš konkrétní scénář k určení, zda je velikost ideální dávky. Velikost dávky konfigurovatelné za běhu, abyste povolit rychlé úpravy na základě výkonu nebo chyby.

Nakonec pomocí rizika spojená s dávkování vyrovnat velikost dávky. Pokud nejsou přechodné chyby nebo roli nezdaří, vezměte v úvahu důsledky opakováním operace nebo ztráty dat. v dávce.

### <a name="parallel-processing"></a>Paralelní zpracování

Co když postavili snížit velikost dávky, ale používá více vláken k provedení práce? Znovu naše testy jsme si ukázali, že několik menších s více vlákny dávky zpravidla dělá horší, než v jedné dávce větší. Následující test se pokusí vložit 1000 řádků v jedné nebo více paralelních dávky. Tento test ukazuje, jak více souběžných dávky ve skutečnosti snížení výkonu.

| Velikost dávky [iterace] | Dvě vlákna (ms) | Čtyři vláken (ms) | Šest vláken (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Výsledky nejsou srovnávací testy. Zobrazit [Poznámka o časování výsledky v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Pro snížení výkonu z důvodu paralelismu několik možných příčin:

* Existuje několik souběžných síťových volání místo jednoho.
* Více operací s jednu tabulku může způsobit konflikty a blokování.
* Existují režie spojené s multithreading.
* Prostředky na otevření více připojení převažuje získané výhody paralelní zpracování.

Pokud je cílem různých tabulek nebo databází, je možné si zobrazit, získáte pomocí této strategie výkonu. Horizontální dělení databází nebo federace by scénář pro tento přístup. Horizontální dělení používá několik databází a směruje jiná data pro každou databázi. Je-li každé malé dávky k jiné databázi, potom paralelní provádění operací může být efektivnější. Zvýšení výkonu však není natolik závažné, chcete použít jako základ pro rozhodnutí a používat horizontální dělení databází ve vašem řešení.

V některé návrhy můžete po menších dávkách pro paralelní zpracování způsobit lepší propustnost žádostí o systém pod zátěží. V takovém případě i když je rychlejší jedné větší dávkové zpracování, zpracování několika dávek současně může být efektivnější.

Pokud používáte paralelního spouštění, vezměte v úvahu řízení maximální počet pracovních vláken. Menší číslo může znamenat nižší výskyt kolizí a rychlejší dobu spuštění. Zvažte také další zátěž, která to umístí na cílové databázi v připojení a transakce.

### <a name="related-performance-factors"></a>Faktory související výkonu

Typické doprovodné materiály k výkonu databáze má vliv také dávkování. Například vložit pro tabulky, které mají velké primární klíč, nebo více neclusterovaných indexů je snížení výkonu.

Pokud parametry table-valued pomocí uložené procedury, můžete použít příkaz **SET NOCOUNT na** na začátku postupu. Tento příkaz potlačí vrátit počet ovlivněných řádků v postupu. Nicméně v testech, použití **SET NOCOUNT na** nemělo žádný vliv nebo snížení výkonu. Test uložené procedury bylo jednoduché pomocí jediného **vložit** z parametru s hodnotou tabulky. Je možné, že složitější uložené procedury, budou těžit z tohoto prohlášení. Ale Nepředpokládejte, že přidání **SET NOCOUNT na** uložené procedury automaticky zvyšuje výkon. K pochopení dopadu testu uložená procedura a nemusíte **SET NOCOUNT na** příkazu.

## <a name="batching-scenarios"></a>Dávkování scénáře

Následující části popisují způsob použití parametrů table-valued ve třech scénářích aplikací. První scénář popisuje, jak ukládání do vyrovnávací paměti a dávek vzájemně spolupracují. Druhý scénář zvyšuje výkon tím, že provádí hlavní podrobnosti operace v rámci volání jedné uložené procedury. Poslední scénář popisuje, jak používat parametry table-valued do činnosti "UPSERT".

### <a name="buffering"></a>ukládání do vyrovnávací paměti

I když jsou některé scénáře, které jsou zřejmí kandidáti pro dávkové zpracování, existuje mnoho scénářů, které může využívat dávkové zpracování pomocí zpožděného zpracování. Zpožděné zpracování, ale taky mají větší riziko, že data nejsou ztracena v případě neočekávaného selhání. Je důležité porozumět toto riziko a zvážit důsledky.

Představte si třeba webovou aplikaci, která sleduje historii navigace jednotlivých uživatelů. S každým požadavkem stránky může aplikace provádět volání zaznamenat zobrazení stránky uživatele databáze. Ale vyšší výkon a škálovatelnost lze dosáhnout tak, že ukládání do vyrovnávací paměti navigace aktivit uživatelů a potom odešlete data do databáze v dávkách. Můžete aktivovat aktualizaci databáze zabralo nebo velikost vyrovnávací paměti. Pravidlo například může určit, že služby batch by měl být zpracován až po 20 sekund nebo když vyrovnávací paměti dosáhne 1000 položek.

Následující příklad kódu používá [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) ke zpracování ve vyrovnávací paměti události vyvolané službou třídu monitorování. Když vyplní vyrovnávací paměti nebo je dosaženo časového limitu, batch uživatelská data se odesílají do databáze s parametr s hodnotou tabulky.

Následující třídy NavHistoryData modely navigace podrobností o uživateli. Obsahuje základní informace, jako je identifikátor uživatele, adresu URL získat přístup a čas přístupu.

```c#
    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }
```

Třída NavHistoryDataMonitor zodpovídá za ukládání do vyrovnávací paměti navigační data uživatele do databáze. Obsahuje metody, RecordUserNavigationEntry, které reaguje vyvoláním **OnAdded** událostí. Následující kód ukazuje logiku konstruktoru, který používá Rx k vytvoření pozorovatelných kolekce založené na události. Pak přihlásí se k této pozorovatelných kolekce s metodou vyrovnávací paměti. Přetížení Určuje, že vyrovnávací paměti by měly být odeslány každých 20 sekund nebo 1 000 položek.

```c#
    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }
```

Obslužná rutina převede všechny položky ve vyrovnávací paměti na typ s hodnotou tabulky a pak předá tento typ uloženou proceduru, která zpracovává dávka. Následující kód ukazuje úplnou definici NavHistoryDataEventArgs a NavHistoryDataMonitor třídy.

```c#
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
```

Pokud chcete použít tuto třídu vyrovnávací paměti, aplikace vytvoří objekt statické NavHistoryDataMonitor. Pokaždé, když uživatel přistupuje k na stránce aplikace volá metodu NavHistoryDataMonitor.RecordUserNavigationEntry. Vyrovnávací paměti logiky pokračuje postará o odesílání těchto položek do databáze v dávkách.

### <a name="master-detail"></a>Hlavní podrobností

Parametry Table-valued jsou užitečné pro jednoduché scénáře vložit. Však může být náročnější operace vložení služby batch, které se týkají více než jednou tabulkou. "Záznamů master/detail" scénář je typický příklad. Hlavní tabulka obsahuje primární entity. Jeden nebo více tabulka a tabulka podrobností uložit víc dat o entitě. V tomto scénáři cizího klíče vynucují vztah podrobnosti na jedinečné entity hlavní. Vezměte v úvahu zjednodušenou verzi PurchaseOrder tabulku a její přidružené OrderDetail. Následující příkaz jazyka Transact-SQL vytvoří tabulku PurchaseOrder se čtyřmi sloupci: OrderID, OrderDate, CustomerID a stav.

```sql
    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Jednotlivé objednávky obsahuje jeden nebo více nákupů produktu. Tyto informace jsou zachyceny PurchaseOrderDetail tabulky. Následující příkaz jazyka Transact-SQL vytvoří PurchaseOrderDetail tabulku s 5 sloupci: OrderID, OrderDetailID, ProductID, UnitPrice a OrderQty.

```sql
    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))
```

Sloupce OrderID v tabulce PurchaseOrderDetail musí odkazovat na pořadí z tabulky PurchaseOrder. Následující definice cizího klíče vynucuje toto omezení.

```sql
    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Pokud chcete používat parametry table-valued, musí mít jeden typ uživatelem definovaná tabulka pro každou cílovou tabulku.

```sql
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
```

Potom definujte uloženou proceduru, která přijímá tabulek z těchto typů. Tento postup umožňuje aplikaci místně batch sadu objednávky a podrobnosti objednávky v jednom volání. Následující příkaz jazyka Transact-SQL poskytuje kompletní uloženou proceduru deklarace pro tento příklad pořadí nákupu.

```sql
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
```

V tomto příkladu místně definované @IdentityLink tabulka ukládá skutečnými hodnotami OrderID z nově vloženou řádků. Tyto identifikátory pořadí se liší od dočasné OrderID hodnot v @orders a @details parametry s hodnotou tabulky. Z tohoto důvodu @IdentityLink pak připojí OrderID hodnot z tabulky @orders parametr do skutečné hodnoty OrderID pro nové řádky v tabulce PurchaseOrder. Po provedení tohoto kroku @IdentityLink tabulka může usnadnit vkládání OrderDetails s skutečné OrderID, která splňuje omezení cizího klíče.

Tuto uloženou proceduru lze z kódu nebo z jiných volání příkazů jazyka Transact-SQL. V části parametrů table-valued tohoto dokumentu příklad kódu. Následující příkaz jazyka Transact-SQL ukazuje, jak volat sp_InsertOrdersBatch.

```sql
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
```

Toto řešení umožňuje každé dávky použít sady hodnot OrderID, které začínají hodnotou 1. Tyto dočasné hodnoty OrderID popisu vztahů v dávce, ale skutečné hodnoty OrderID jsou určeny v době operace insert. Můžete spustit stejný příkaz v předchozím příkladu opakovaně a generovat jedinečný objednávky v databázi. Z tohoto důvodu zvažte možnost přidat další kód nebo databáze logiku, která brání duplicitní objednávky při použití této funkce dávkování technika.

Tento příklad ukazuje, že ještě složitější databázových operací, jako jsou hlavní podrobnosti operace, můžete sjednotit pomocí parametrů table-valued.

### <a name="upsert"></a>UPSERTOVAT

Dávkování jiný scénář zahrnuje současně aktualizaci existující řádky a vkládání nových řádků. Tato operace je někdy označovány jako "UPSERT" (aktualizace + insert) operaci. Místo samostatných volání pro vložení a aktualizace, je nejvhodnější pro tuto úlohu příkazu MERGE. Příkazu MERGE můžete provést i insert a operace v jednom volání aktualizace.

Parametry Table-valued lze pomocí příkazu MERGE k provádění aktualizací a vložení. Představte si třeba zjednodušené tabulky zaměstnanců, která obsahuje následující sloupce: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
```

V tomto příkladu můžete použít skutečnost, že je SocialSecurityNumber jedinečný provést SLOUČENÍ více zaměstnanců. Nejprve vytvořte typ uživatelem definovaná tabulka:

```sql
    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO
```

V dalším kroku vytvořte uloženou proceduru nebo napsat kód, který se používá k provedení aktualizace a vložit příkazu MERGE. Následující příklad pomocí příkazu MERGE na parametr s hodnotou tabulky @employees, typu EmployeeTableType. Obsah @employees tabulky se tady nezobrazují.

```sql
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
```

Další informace najdete v dokumentaci a příklady příkazu MERGE. I když nejde provést stejnou práci v kroku více uložené volání procedury s samostatné INSERT a operace aktualizace, příkazu MERGE je mnohem efektivnější. Kód databáze můžete také sestavit volání příkazů jazyka Transact-SQL, která pomocí příkazu MERGE přímo bez nutnosti dvě volání databáze pro INSERT a UPDATE.

## <a name="recommendation-summary"></a>Souhrn doporučení

Následující seznam obsahuje souhrn dávkování doporučení popsaných v tomto článku:

* Můžete zvýšit výkon a škálovatelnost aplikací SQL Database pomocí ukládání do vyrovnávací paměti a dávek.
* Seznamte se s kompromisy mezi dávkové zpracování a ukládání do vyrovnávací paměti a odolnost proti chybám. Během selhání role riziko ztráty nezpracované dávku důležitých podnikových dat vyváží výkon výhodou dávkování.
* Pokus zachovat všechna volání do databáze v jednom datacentru snížit latenci.
* Pokud se rozhodnete jednoho technika dávkování, parametry table-valued nabízí nejlepší výkon a flexibilitu.
* Nejrychlejší výkon vložit postupujte podle následujících obecných pokynů, ale test váš scénář:
  * < 100 řádků pomocí jediného parametrizovaného příkazu INSERT.
  * < 1 000 řádků použijte parametry s hodnotou tabulky.
  * Pro > = 1 000 řádků, použijte SqlBulkCopy.
* Pro aktualizace a operace odstranění, použijte parametry table-valued pomocí uložené procedury logiku, která určuje správnou funkci na každý řádek v tabulce parametru.
* Pokyny pro velikost dávky:
  * Použijte největší velikosti služby batch, které dávají smysl pro vaše aplikace a obchodní požadavky.
  * Zajistit rovnováhu mezi zvýšení výkonu velkých dávek rizik dočasné nebo katastrofální chyby. Co je důsledkem opakování nebo ke ztrátě dat v dávce? 
  * Otestujte největší velikost dávky k ověření, že databáze SQL není ho odmítnout.
  * Vytvořte nastavení konfigurace tohoto ovládacího prvku dávkové zpracování, jako je například velikost dávky nebo vyrovnávací paměti časový interval. Tato nastavení poskytují flexibilitu. Chování dávkového zpracování v produkčním prostředí můžete změnit bez opětovného nasazení cloudové služby.
* Nepoužívejte paralelní spouštění dávek, které pracují na jedné tabulce v jedné databázi. Pokud budete chtít rozdělit v jedné dávce napříč několika pracovních vláken, spuštění testů, abyste zjistili ideální počet vláken. Po neurčené prahovou hodnotu další vlákna se snížit výkon spíše než ji zvětšete.
* Zvažte možnost ukládání do vyrovnávací paměti o velikosti a čas jako způsob implementace dávkování pro další scénáře.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na způsob návrhu databáze a kódování postupy související s dávkování může zlepšit škálovatelnosti a výkonu aplikace. Ale to je pouze jediný faktor do celkové strategie. Další způsoby, jak vylepšit výkon a škálovatelnost, naleznete v tématu [Průvodce výkonem Azure SQL Database pro izolované databáze](sql-database-performance-guidance.md) a [cenové a výkonové požadavky fondu elastické databáze](sql-database-elastic-pool-guidance.md).

