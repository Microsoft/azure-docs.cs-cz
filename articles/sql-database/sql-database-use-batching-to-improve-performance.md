---
title: Jak používat dávkování ke zlepšení výkonu aplikace
description: Toto téma poskytuje důkazy o tom, že dávkování databázových operací výrazně zvyšuje rychlost a škálovatelnost aplikací Azure SQL Database. Přestože tyto techniky dávkování pracovat pro všechny databáze SQL Server, zaměření článku je na Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545020"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Zlepšení výkonu aplikace SQL Database pomocí dávkování

Dávkové operace do Azure SQL Database výrazně zlepšuje výkon a škálovatelnost vašich aplikací. Chcete-li porozumět výhodám, první část tohoto článku pokrývá některé ukázkové výsledky testů, které porovnávají sekvenční a dávkové požadavky do databáze SQL. Zbývající část článku ukazuje techniky, scénáře a důležité informace, které vám pomohou úspěšně používat dávkování ve vašich aplikacích Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Proč je dávkování důležité pro databázi SQL

Dávkování volání vzdálené služby je dobře známá strategie pro zvýšení výkonu a škálovatelnosti. Existují pevné náklady na zpracování všech interakcí se vzdálenou službou, jako je serializace, síťový přenos a deserializace. Balení mnoha samostatných transakcí do jedné dávky minimalizuje tyto náklady.

V tomto článku chceme prozkoumat různé strategie a scénáře dávkování databáze SQL. Přestože tyto strategie jsou také důležité pro místní aplikace, které používají SQL Server, existuje několik důvodů pro zvýraznění použití dávkování pro databázi SQL:

* V přístupu k databázi SQL je potenciálně větší latence sítě, zejména pokud přistupujete k databázi SQL mimo stejné datové centrum Microsoft Azure.
* Víceklientské charakteristiky databáze SQL znamená, že účinnost vrstvy přístupu k datům koreluje s celkovou škálovatelnost databáze. SQL Database musí zabránit každému jedinému klientovi nebo uživateli v monopolizaci databázových prostředků na úkor jiných klientů. V reakci na použití nad rámec předdefinovaných kvót sql database může snížit propustnost nebo reagovat s omezení výjimky. Efektivita, jako je dávkování, vám umožní provádět další práci v databázi SQL před dosažením těchto limitů. 
* Dávkování je také efektivní pro architektury, které používají více databází (sharding). Efektivita vaší interakce s každou databázovou jednotkou je stále klíčovým faktorem celkové škálovatelnosti. 

Jednou z výhod použití databáze SQL je, že není třeba spravovat servery, které jsou hostitelem databáze. Tato spravovaná infrastruktura však také znamená, že musíte přemýšlet o optimalizaci databáze odlišně. Již nelze hledat vylepšení hardwaru databáze nebo síťové infrastruktury. Microsoft Azure řídí tato prostředí. Hlavní oblast, kterou můžete řídit, je způsob interakce aplikace s databází SQL. Dávkování je jednou z těchto optimalizací. 

První část papíru zkoumá různé techniky dávkování pro aplikace .NET, které používají databázi SQL. Poslední dvě části popisují pokyny a scénáře dávkování.

## <a name="batching-strategies"></a>Strategie dávkování

### <a name="note-about-timing-results-in-this-article"></a>Poznámka k výsledkům časování v tomto článku

> [!NOTE]
> Výsledky nejsou měřítka, ale jsou určeny k prokázání **relativní výkonnosti**. Časování jsou založeny v průměru alespoň 10 zkušebních jízd. Operace jsou vloženy do prázdné tabulky. Tyto testy byly měřeny před V12 a nemusí nutně odpovídat propustnost, které může dojít v databázi V12 pomocí nové [úrovně služeb DTU](sql-database-service-tiers-dtu.md) nebo [virtuálních jader úrovně služby](sql-database-service-tiers-vcore.md). Relativní přínos dávkovací techniky by měl být podobný.

### <a name="transactions"></a>Transakce

Zdá se divné začít přezkum dávkování tím, že diskutuje transakce. Ale použití transakcí na straně klienta má subtilní dávky na straně serveru efekt, který zlepšuje výkon. A transakce lze přidat pouze s několika řádky kódu, takže poskytují rychlý způsob, jak zlepšit výkon sekvenčních operací.

Zvažte následující kód Jazyka C#, který obsahuje posloupnost operací vložení a aktualizace v jednoduché tabulce.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
Následující ADO.NET kód postupně provádí tyto operace.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Nejlepší způsob, jak optimalizovat tento kód je implementovat nějakou formu dávkování na straně klienta těchto volání. Existuje však jednoduchý způsob, jak zvýšit výkon tohoto kódu jednoduchým zabalením posloupnosti volání v transakci. Zde je stejný kód, který používá transakci.

```csharp
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
```

Transakce jsou skutečně používány v obou těchto příkladech. V prvním příkladu každé jednotlivé volání je implicitní transakce. V druhém příkladu explicitní transakce zalomí všechna volání. Podle dokumentace pro [transakční protokol s napředkem pro zápis](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)jsou záznamy protokolu při potvrzení transakce vyprázdněny na disk. Takže zahrnutím více volání v transakci, zápis do transakční protokol může zpoždění, dokud transakce je potvrzena. Ve skutečnosti povolujete dávkování pro zápisy do transakčního protokolu serveru.

V následující tabulce jsou uvedeny některé výsledky testování ad hoc. Testy provedly stejné sekvenční vložení s transakcemi i bez transakce. Z větší perspektivy první sada testů běžel vzdáleně z přenosného počítače do databáze v Microsoft Azure. Druhá sada testů byla spuštěna z cloudové služby a databáze, které byly umístěny v rámci stejného datového centra Microsoft Azure (– –USA– západ). V následující tabulce je uvedena doba trvání sekvenčních vložení s transakcemi a bez transakcí v milisekundách.

**Místní do Azure:**

| Provoz | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1 226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure to Azure (stejné datové centrum):**

| Provoz | Žádná transakce (ms) | Transakce (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).

Na základě předchozích výsledků testů obtékání jedné operace v transakci ve skutečnosti snižuje výkon. Ale jak zvýšit počet operací v rámci jedné transakce, zlepšení výkonu se stává výraznější. Rozdíl ve výkonu je také výraznější, když dojde ke všem operacím v rámci datového centra Microsoft Azure. Zvýšená latence používání sql database z mimo datové centrum Microsoft Azure zastiňuje zvýšení výkonu pomocí transakcí.

Přestože použití transakcí může zvýšit výkon, nadále [dodržovat osvědčené postupy pro transakce a připojení](https://msdn.microsoft.com/library/ms187484.aspx). Udržujte transakci co nejkratší a po dokončení práce zavřete připojení k databázi. Příkaz using v předchozím příkladu zajišťuje, že připojení je ukončeno po dokončení následného bloku kódu.

Předchozí příklad ukazuje, že můžete přidat místní transakce do libovolného kódu ADO.NET se dvěma řádky. Transakce nabízejí rychlý způsob, jak zlepšit výkon kódu, který umožňuje postupné vkládání, aktualizace a odstraňování operací. Pro nejrychlejší výkon však zvažte další změnu kódu, abyste využili výhod dávkování na straně klienta, jako jsou parametry s hodnotou tabulky.

Další informace o transakcích v ADO.NET naleznete v [tématu Místní transakce v ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry s hodnotou tabulky

Parametry s hodnotou tabulky podporují uživatelem definované typy tabulek jako parametry v příkazech Transact-SQL, uložených procedurách a funkcích. Tato technika dávkování na straně klienta umožňuje odesílat více řádků dat v rámci parametru s hodnotou tabulky. Chcete-li použít parametry s hodnotou tabulky, nejprve definujte typ tabulky. Následující příkaz Transact-SQL vytvoří typ tabulky s názvem **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

V kódu vytvoříte **DataTable** s přesně stejnými názvy a typy typu tabulky. Předejte tuto **datatable** v parametru v textovém dotazu nebo volání uložené procedury. Následující příklad ukazuje tuto techniku:

```csharp
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
```

V předchozím příkladu vloží objekt **SqlCommand** řádky z parametru s hodnotou tabulky ** \@TestTvp**. Dříve vytvořený objekt **DataTable** je tomuto parametru přiřazen metodou **SqlCommand.Parameters.Add.** Dávkování vloží v jednom volání výrazně zvyšuje výkon přes sekvenční vloží.

Chcete-li dále zlepšit předchozí příklad, použijte uloženou proceduru namísto textového příkazu. Následující příkaz Transact-SQL vytvoří uloženou proceduru, která přebírá parametr s hodnotou tabulky **SimpleTestTableType.**

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Potom změňte deklaraci objektu **SqlCommand** v předchozím příkladu kódu na následující.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Ve většině případů mají parametry s hodnotou tabulky ekvivalentní nebo lepší výkon než jiné techniky dávkování. Parametry s hodnotou tabulky jsou často vhodnější, protože jsou flexibilnější než jiné možnosti. Například jiné techniky, jako je například hromadné kopírování SQL, povolit pouze vkládání nových řádků. Ale s parametry s hodnotou tabulky, můžete použít logiku v uložené proceduře k určení, které řádky jsou aktualizace a které jsou vloží. Typ tabulky lze také upravit tak, aby obsahoval sloupec Operace, který označuje, zda má být zadaný řádek vložen, aktualizován nebo odstraněn.

V následující tabulce jsou uvedeny výsledky testů ad hoc pro použití parametrů s hodnotou tabulky v milisekundách.

| Provoz | Místní do Azure (ms) | Azure stejné datové centrum (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Zvýšení výkonu z dávkování je okamžitě zřejmé. V předchozím sekvenčním testu trvalo 1000 operací 129 sekund mimo datové centrum a 21 sekund z datového centra. Ale s parametry s hodnotou tabulky 1000 operace trvat pouze 2,6 sekundy mimo datové centrum a 0,4 sekundy v rámci datového centra.

Další informace o parametrech s hodnotou tabulky naleznete v [tématu Parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Hromadná kopie SQL

Hromadné kopírování SQL je další způsob, jak vložit velké množství dat do cílové databáze. Aplikace .NET mohou použít třídu **SqlBulkCopy** k provádění operací hromadného vkládání. **SqlBulkCopy** je podobný ve funkci nástroj příkazového řádku, **Bcp.exe**nebo Transact-SQL příkaz, **BULK INSERT**. Následující příklad kódu ukazuje, jak hromadně zkopírovat řádky ve zdrojové **tabulce DataTable**, tabulka, do cílové tabulky v SQL Server, MyTable.

```csharp
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
```

Existují některé případy, kdy je hromadné kopírování upřednostňováno před parametry s hodnotou tabulky. Podívejte se na srovnávací tabulku parametrů s hodnotou tabulky versus operace BULK INSERT v článku [Parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb510489.aspx).

Následující výsledky testu ad hoc ukazují výkon dávkování s **SqlBulkCopy** v milisekundách.

| Provoz | Místní do Azure (ms) | Azure stejné datové centrum (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

V menších velikostech dávek parametry s hodnotou tabulky překonaly třídu **SqlBulkCopy.** **SqlBulkCopy** však provádí 12-31% rychlejší než parametry s hodnotou tabulky pro testy 1 000 a 10 000 řádků. Stejně jako parametry s hodnotou tabulky, **SqlBulkCopy** je dobrou volbou pro dávkové vložení, zejména ve srovnání s výkonem nedávkových operací.

Další informace o hromadné kopírování v ADO.NET naleznete [v tématu Operace hromadného kopírování na serveru SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Příkazy Parametrizované INSERT s více řádky

Jednou z alternativ pro malé dávky je vytvořit velký parametrizovaný příkaz INSERT, který vloží více řádků. Následující příklad kódu ukazuje tuto techniku.

```csharp
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
```

Tento příklad má ukázat základní koncept. Realističtější scénář by smyčkou prostřednictvím požadovaných entit k vytvoření řetězce dotazu a parametry příkazu současně. Jste omezeni na celkem 2100 parametrů dotazu, takže to omezuje celkový počet řádků, které mohou být zpracovány tímto způsobem.

Následující výsledky testu ad hoc ukazují výkon tohoto typu příkazu insert v milisekundách.

| Provoz | Parametry s hodnotou tabulky (ms) | Jednopříkazová funkce INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Tento přístup může být o něco rychlejší pro dávky, které jsou menší než 100 řádků. Přestože zlepšení je malý, tato technika je další možnost, která může fungovat dobře ve vašem scénáři konkrétní aplikace.

### <a name="dataadapter"></a>Dataadapter

Třída **DataAdapter** umožňuje upravit objekt **DataSet** a potom odeslat změny jako operace VLOŽENÍ, AKTUALIZACE a ODSTRANĚNÍ. Pokud používáte **DataAdapter** tímto způsobem, je důležité si uvědomit, že samostatná volání jsou prováděny pro každou jinou operaci. Chcete-li zvýšit výkon, použijte **UpdateBatchSize** vlastnost k počtu operací, které by měly být dávkové najednou. Další informace naleznete [v tématu Provádění dávkových operací pomocí datových adaptérů](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Účetní rámec

Entity Framework aktuálně nepodporuje dávkování. Různí vývojáři v komunitě se pokusili demonstrovat zástupná řešení, jako je například přepsat **SaveChanges** metoda. Ale řešení jsou obvykle složité a přizpůsobené aplikaci a datovému modelu. Projekt codeplex entity Framework má aktuálně diskusní stránku o této žádosti o funkci. Chcete-li zobrazit tuto diskusi, viz [Návrh Meeting Notes - 2.](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012)

### <a name="xml"></a>XML

Pro úplnost, cítíme, že je důležité mluvit o XML jako dávkování strategie. Použití xml však nemá žádné výhody oproti jiným metodám a několika nevýhodám. Přístup je podobný parametrům s hodnotou tabulky, ale soubor NEBO řetězec XML je předán uložené proceduře namísto uživatelem definované tabulky. Uložená procedura analyzuje příkazy v uložené proceduře.

Tento přístup má několik nevýhod:

* Práce s XML může být těžkopádná a náchylná k chybám.
* Analýza XML v databázi může být náročná na procesor.
* Ve většině případů je tato metoda pomalejší než parametry s hodnotou tabulky.

Z těchto důvodů se použití jazyka XML pro dávkové dotazy nedoporučuje.

## <a name="batching-considerations"></a>Důležité informace o dávkování

Následující části poskytují další pokyny pro použití dávkování v aplikacích databáze SQL.

### <a name="tradeoffs"></a>Kompromisy

V závislosti na vaší architektuře může dávkování zahrnovat kompromis mezi výkonem a odolností proti chybám. Zvažte například scénář, kde vaše role neočekávaně přejde dolů. Pokud ztratíte jeden řádek dat, dopad je menší než dopad ztráty velké dávky neodeslaných řádků. Existuje větší riziko při ukládání řádků do vyrovnávací paměti před jejich odesláním do databáze v zadaném časovém okně.

Z důvodu tohoto kompromisu vyhodnoťte typ operací, které dávkově. Dávka agresivněji (větší dávky a delší čas ová okna) s daty, která jsou méně kritická.

### <a name="batch-size"></a>Velikost dávky

V našich testech obvykle neexistovala žádná výhoda rozdělení velkých dávek na menší bloky. Ve skutečnosti toto dělení často vedlo k pomalejšímu výkonu než odeslání jedné velké dávky. Zvažte například scénář, ve kterém chcete vložit 1000 řádků. V následující tabulce je uvedeno, jak dlouho trvá použití parametrů s hodnotou tabulky, chcete-li vložit 1000 řádků, pokud je rozděleno do menších dávek.

| Velikost dávky | Iterací | Parametry s hodnotou tabulky (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Můžete vidět, že nejlepší výkon pro 1000 řádků je odeslat všechny najednou. V jiných testech (není zde uvedeno) došlo k malému zvýšení výkonu, který rozdělil dávku řádku 10000 na dvě dávky 5000. Schéma tabulky pro tyto testy je však poměrně jednoduché, takže byste měli provést testy na konkrétní data a velikosti dávek k ověření těchto zjištění.

Dalším faktorem, který je třeba zvážit, je, že pokud se celková dávka stane příliš velkou, sql database může omezit a odmítnout potvrzení dávky. Nejlepších výsledků dosáhnete, vyzkoušejte konkrétní scénář a zjistěte, zda existuje ideální velikost dávky. Umožňuje konfigurovat velikost dávky za běhu, aby bylo možné provádět rychlé úpravy na základě výkonu nebo chyb.

Nakonec vyvažte velikost dávky s riziky spojenými s dávkováním. Pokud jsou přechodné chyby nebo role selže, zvažte důsledky opakování operace nebo ztráty dat v dávce.

### <a name="parallel-processing"></a>Paralelní zpracování

Co kdybyste využili přístup ke snížení velikosti dávky, ale k provedení práce použili více vláken? Opět naše testy ukázaly, že několik menších vícevláknových dávek obvykle provádí horší než jeden větší dávka. Následující test se pokusí vložit 1000 řádků v jedné nebo více paralelních dávkách. Tento test ukazuje, jak více souběžných dávek skutečně snížilo výkon.

| Velikost dávky [Iterace] | Dvě vlákna (ms) | Čtyři vlákna (ms) | Šest nití (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Výsledky nejsou měřítkem. Podívejte se na [poznámku o výsledky časování v tomto článku](#note-about-timing-results-in-this-article).
> 
> 

Existuje několik možných důvodů pro zhoršení výkonu v důsledku paralelismu:

* Existuje více souběžných síťových volání namísto jednoho.
* Více operací proti jedné tabulce může mít za následek kolize a blokování.
* Existují režijní náklady spojené s multithreading.
* Náklady na otevření více připojení převažuje nad výhodou paralelního zpracování.

Pokud cílíte na různé tabulky nebo databáze, je možné zobrazit určitý nárůst výkonu s touto strategií. Databáze syrový a federací by scénář pro tento přístup. Sharding používá více databází a trasy různých dat do každé databáze. Pokud každá malá dávka bude do jiné databáze, pak provádění operací paralelně může být efektivnější. Zvýšení výkonu však není dostatečně významné použít jako základ pro rozhodnutí o použití databáze synicí ve vašem řešení.

V některých návrzích může paralelní provádění menších dávek vést ke zlepšení propustnosti požadavků v systému při zatížení. V tomto případě i v případě, že je rychlejší zpracovat jednu větší dávku, zpracování více dávek paralelně může být efektivnější.

Pokud používáte paralelní spuštění, zvažte řízení maximálního počtu pracovních podprocesů. Menší počet může mít za následek méně kolize a rychlejší spuštění. Zvažte také další zatížení, které to umístí na cílové databáze v připojení a transakce.

### <a name="related-performance-factors"></a>Související faktory výkonu

Typické pokyny pro výkon databáze také ovlivňuje dávkování. Například vložit výkon je snížena pro tabulky, které mají velký primární klíč nebo mnoho indexů bez clusteru.

Pokud parametry s hodnotou tabulky používají uloženou proceduru, můžete použít příkaz **SET NOCOUNT ON** na začátku procedury. Tento příkaz potlačí vrácení počtu ovlivněných řádků v postupu. Však v našich testech použití **SET NOCOUNT ON** buď neměl žádný vliv nebo snížení výkonu. Test uložená procedura byla jednoduchá s jedním příkazem **INSERT** z parametru s hodnotou tabulky. Je možné, že složitější uložené procedury by těžit z tohoto prohlášení. Ale nepředpokládejte, že přidání **SET NOCOUNT na** uložené procedury automaticky zlepšuje výkon. Chcete-li pochopit efekt, otestujte uloženou proceduru s příkazem **SET NOCOUNT ON** a bez něj.

## <a name="batching-scenarios"></a>Scénáře dávkování

Následující části popisují, jak používat parametry s hodnotou tabulky ve třech scénářích aplikace. První scénář ukazuje, jak ukládání do vyrovnávací paměti a dávkování může spolupracovat. Druhý scénář zlepšuje výkon provedením operací hlavního detailu v jednom volání uložené procedury. Konečný scénář ukazuje, jak používat parametry s hodnotou tabulky v operaci "UPSERT".

### <a name="buffering"></a>Vyrovnávací paměti

I když existují některé scénáře, které jsou zřejmé, kandidát pro dávkování, existuje mnoho scénářů, které by mohly využít dávkování zpožděné zpracování. Zpožděné zpracování však také nese větší riziko, že data jsou ztracena v případě neočekávané selhání. Je důležité pochopit toto riziko a zvážit důsledky.

Zvažte například webovou aplikaci, která sleduje historii navigace každého uživatele. Při každém požadavku na stránku může aplikace provést volání databáze pro záznam zobrazení stránky uživatele. Vyššívýkon a škálovatelnost lze však dosáhnout ukládáním do vyrovnávací paměti navigačních aktivit uživatelů a následným odesláním těchto dat do databáze v dávkách. Aktualizaci databáze můžete spustit podle uplynulého času a velikosti vyrovnávací paměti. Pravidlo může například určit, že dávka by měla být zpracována po 20 sekundách nebo když vyrovnávací paměť dosáhne 1000 položek.

Následující příklad kódu používá [reaktivní rozšíření - Rx](https://msdn.microsoft.com/data/gg577609) ke zpracování událostí ve vyrovnávací paměti vyvolaných třídou monitorování. Po dosažení vyrovnávací paměti výplně nebo časového opojení je dávka uživatelských dat odeslána do databáze s parametrem s hodnotou tabulky.

Následující třída NavHistoryData modeluje podrobnosti navigace uživatele. Obsahuje základní informace, jako je identifikátor uživatele, přístupová adresa URL a doba přístupu.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Třída NavHistoryDataMonitor je zodpovědná za ukládání dat navigace uživatele do databáze. Obsahuje metodu RecordUserNavigationEntry, která reaguje vyvoláním události **OnAdded.** Následující kód ukazuje logiku konstruktoru, který používá Rx k vytvoření pozorovatelné kolekce založené na události. Potom se přihlásí k této pozorovatelné kolekce s Buffer metoda. Přetížení určuje, že vyrovnávací paměť by měla být odeslána každých 20 sekund nebo 1000 položek.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Obslužná rutina převede všechny položky ve vyrovnávací paměti na typ s hodnotou tabulky a poté předá tento typ uložené proceduře, která zpracuje dávku. Následující kód zobrazuje úplnou definici pro třídy NavHistoryDataEventArgs a NavHistoryDataMonitor.

```csharp
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
```

Obslužná rutina převede všechny položky ve vyrovnávací paměti na typ s hodnotou tabulky a poté předá tento typ uložené proceduře, která zpracuje dávku. Následující kód zobrazuje úplnou definici pro třídy NavHistoryDataEventArgs a NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
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

Chcete-li použít tuto třídu ukládání do vyrovnávací paměti, aplikace vytvoří statický objekt NavHistoryDataMonitor. Pokaždé, když uživatel přistupuje ke stránce, aplikace volá Metodu NavHistoryDataMonitor.RecordUserNavigationEntry. Ukládání do vyrovnávací paměti logiky pokračuje postarat o odesílání těchto položek do databáze v dávkách.

### <a name="master-detail"></a>Hlavní detail

Parametry s hodnotou tabulky jsou užitečné pro jednoduché scénáře INSERT. Však může být náročnější dávkové vložky, které zahrnují více než jednu tabulku. Dobrým příkladem je scénář "master/detail". Hlavní tabulka identifikuje primární entitu. Jedna nebo více tabulek podrobností ukládá více dat o entitě. V tomto scénáři vztahy cizího klíče vynucují vztah podrobností k jedinečné hlavní entitě. Zvažte zjednodušenou verzi tabulky Nákup a její přidružené tabulky OrderDetail. Následující Transact-SQL vytvoří tabulku PurchaseOrder se čtyřmi sloupci: OrderID, OrderDate, CustomerID a Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Každá objednávka obsahuje jeden nebo více nákupů produktů. Tyto informace jsou zachyceny v tabulce PurchaseOrderDetail. Následující Transact-SQL vytvoří purchaseorderdetail tabulku s pěti sloupci: OrderID, OrderDetailID, ProductID, UnitPrice a OrderQty.

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

Sloupec OrderID v tabulce PurchaseOrderDetail musí odkazovat na objednávku z tabulky Nákupob. Následující definice cizího klíče vynucuje toto omezení.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Chcete-li použít parametry s hodnotou tabulky, musíte mít pro každou cílovou tabulku jeden uživatelem definovaný typ tabulky.

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

Pak definujte uloženou proceduru, která přijímá tabulky těchto typů. Tento postup umožňuje aplikaci místně dávkovat sadu objednávek a podrobnosti objednávky v jednom volání. Následující Transact-SQL poskytuje kompletní uložené procedury prohlášení pro tento příklad nákupní objednávky.

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

V tomto příkladu místně @IdentityLink definované tabulky ukládá skutečné hodnoty OrderID z nově vložených řádků. Tyto identifikátory pořadí se liší od dočasných @orders @details hodnot OrderID v parametrech s hodnotou tabulky a v hodnotě tabulky. Z tohoto důvodu @IdentityLink tabulka pak připojí hodnoty OrderID z parametru @orders do skutečné hodnoty OrderID pro nové řádky v tabulce Nákup. Po tomto kroku @IdentityLink tabulka může usnadnit vkládání podrobností objednávky s aktuální OrderID, který splňuje omezení cizího klíče.

Tato uložená procedura lze použít z kódu nebo z jiných volání Transact-SQL. Příklad kódu naleznete v části parametry v hodnotě tabulky v tomto článku. Následující Transact-SQL ukazuje, jak volat sp_InsertOrdersBatch.

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

Toto řešení umožňuje každé dávce použít sadu hodnot OrderID, které začínají na 1. Tyto dočasné OrderID hodnoty popisují vztahy v dávce, ale skutečné OrderID hodnoty jsou určeny v době operace vložení. Můžete spustit stejné příkazy v předchozím příkladu opakovaně a generovat jedinečné objednávky v databázi. Z tohoto důvodu zvažte přidání další kód nebo databázové logiky, která zabraňuje duplicitní objednávky při použití této techniky dávkování.

Tento příklad ukazuje, že ještě složitější databázové operace, jako jsou operace hlavního detailu, mohou být dávkovány pomocí parametrů s hodnotou tabulky.

### <a name="upsert"></a>UPSERT (UPSERT)

Další scénář dávkování zahrnuje současně aktualizaci existujících řádků a vkládání nových řádků. Tato operace se někdy označuje jako operace "UPSERT" (aktualizace + vložení). Místo samostatnévolání INSERT a UPDATE, merge prohlášení je nejvhodnější pro tento úkol. Příkaz MERGE můžete provádět operace vložení a aktualizace v jednom volání.

Parametry s hodnotou tabulky lze použít s příkazem MERGE k provádění aktualizací a vložení. Zvažte například zjednodušenou tabulku zaměstnanců, která obsahuje následující sloupce: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

V tomto příkladu můžete použít skutečnost, že SocialSecurityNumber je jedinečný k provedení sloučení více zaměstnanců. Nejprve vytvořte uživatelem definovaný typ tabulky:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Dále vytvořte uloženou proceduru nebo napište kód, který používá příkaz MERGE k provedení aktualizace a vložení. Následující příklad používá příkaz MERGE na parametr @employeess hodnotou tabulky , typu EmployeeTableType. Obsah @employees tabulky zde není zobrazen.

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

Další informace naleznete v dokumentaci a příklady příkazu MERGE. Přestože stejnou práci lze provést v volání uložené procedury s více krok s samostatné insert a update operace, merge příkaz je efektivnější. Databázový kód můžete také vytvořit Transact-SQL volání, které používají příkaz MERGE přímo bez nutnosti dvě volání databáze pro INSERT a UPDATE.

## <a name="recommendation-summary"></a>Shrnutí doporučení

Následující seznam obsahuje souhrn doporučení dávkování popsané v tomto článku:

* Pomocí ukládání do vyrovnávací paměti a dávkování zvýšit výkon a škálovatelnost aplikací databáze SQL.
* Seznamte se s kompromisy mezi dávkováním/ukládáním do vyrovnávací paměti a odolností proti chybám. Během selhání role může riziko ztráty nezpracované dávky důležitých obchodních dat převážit nad výhodami dávkování výkonu.
* Pokuste se zachovat všechna volání do databáze v rámci jednoho datového centra ke snížení latence.
* Pokud zvolíte jednu techniku dávkování, parametry s hodnotou tabulky nabízejí nejlepší výkon a flexibilitu.
* Chcete-li dosáhnout nejrychlejšího výkonu vložení, postupujte podle těchto obecných pokynů, ale vyzkoušejte svůj scénář:
  * Pro < 100 řádků použijte jeden parametrizovaný příkaz INSERT.
  * Pro < 1000 řádků použijte parametry s hodnotou tabulky.
  * Pro >= 1000 řádků použijte SqlBulkCopy.
* Pro operace aktualizace a odstranění použijte parametry s hodnotou tabulky s logikou uložené procedury, která určuje správnou operaci na každém řádku v parametru tabulky.
* Pokyny pro velikost dávky:
  * Použijte největší velikosti dávek, které dávají smysl pro vaše požadavky na aplikaci a obchodní účely.
  * Vyvažte zvýšení výkonu velkých dávek s riziky dočasných nebo katastrofických selhání. Jaký je důsledek opakování nebo ztráty dat v dávce? 
  * Otestujte největší velikost dávky a ověřte, zda ji databáze SQL neodmítne.
  * Vytvořte nastavení konfigurace, která řídí dávkování, například velikost dávky nebo časové okno ukládání do vyrovnávací paměti. Tato nastavení poskytují flexibilitu. Můžete změnit dávkování chování v produkčním prostředí bez opětovného nasazení cloudové služby.
* Vyhněte se paralelnímu provádění dávek, které pracují na jedné tabulce v jedné databázi. Pokud se rozhodnete rozdělit jednu dávku mezi více pracovních vláken, spusťte testy k určení ideálního počtu podprocesů. Po nespecifikované prahové hodnoty více podprocesů sníží výkon, nikoli zvýší.
* Zvažte ukládání do vyrovnávací paměti na velikost a čas jako způsob implementace dávkování pro další scénáře.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na to, jak techniky návrhu databáze a kódování související s dávkováním mohou zlepšit výkon a škálovatelnost vaší aplikace. Ale to je jen jeden faktor ve vaší celkové strategii. Další způsoby, jak zlepšit výkon a škálovatelnost, najdete v článku [Azure SQL Database pokyny k výkonu pro jednotlivé databáze](sql-database-performance-guidance.md) a ceny a výkon [aspekty pro elastický fond](sql-database-elastic-pool-guidance.md).

