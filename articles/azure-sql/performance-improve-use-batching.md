---
title: Použití dávkového zpracování ke zlepšení výkonu aplikace
description: Téma poskytuje legitimaci, že dávkování databázových operací značně vylepšuje rychlost a škálovatelnost vašich Azure SQL Database a aplikací spravované instance Azure SQL. I když tyto techniky dávkování fungují pro jakékoli databáze SQL, zaměřuje se na Azure na Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 487b668d9a3d934220fecf5c0896f7ef492c6775
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840485"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Jak používat dávkování ke zlepšování Azure SQL Database a výkonu aplikací spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Dávkové operace s Azure SQL Database a Azure SQL Managed instance významně zvyšují výkon a škálovatelnost vašich aplikací. Aby bylo možné pochopit výhody, první část tohoto článku se zabývá některými ukázkovými výsledky testů, které porovnávají sekvenční a dávkové požadavky na databázi ve službě Azure SQL Database nebo Azure SQL Managed instance. Zbývající část článku ukazuje techniky, scénáře a požadavky, které vám pomůžou úspěšně používat dávkování v aplikacích Azure.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Proč je dávkování důležité pro Azure SQL Database a Azure SQL Managed instance?

Dávkování volání vzdálené služby je dobře známá strategie pro zvýšení výkonu a škálovatelnosti. Existují pevné náklady na zpracování jakýchkoli interakcí se vzdálenou službou, jako je serializace, přenos v síti a deserializace. Balení mnoha samostatných transakcí do jedné dávky minimalizuje tyto náklady.

V tomto článku chceme prozkoumávat různé strategie a scénáře dávkování. I když jsou tyto strategie také důležité pro místní aplikace, které používají SQL Server, je k dispozici několik důvodů pro zdůraznění použití dávkování pro Azure SQL Database a Azure SQL Managed instance:

* Při přístupu k Azure SQL Database a spravované instanci Azure SQL se může zvýšit latence sítě, obzvláště pokud přistupujete Azure SQL Database nebo spravované instanci SQL Azure mimo stejné Microsoft Azure datacentrum.
* Víceklientské charakteristiky Azure SQL Database a Azure SQL Managed instance znamenají, že efektivita vrstvy přístupu k datům se koreluje s celkovou škálovatelností databáze. V reakci na použití přesahující předdefinované kvóty může Azure SQL Database a Azure SQL Managed instance snížit propustnost nebo reagovat s výjimkami omezování. Efektivita, jako je například dávkování, vám umožní provádět více práce, než dosáhnou těchto omezení.
* Dávkování je také platné pro architektury, které používají více databází (horizontálního dělení). Efektivita interakce s každou databázovou jednotkou je stále klíčovým faktorem v celkové škálovatelnosti.

Jednou z výhod používání Azure SQL Database nebo spravované instance Azure SQL je to, že nemusíte spravovat servery, které hostují databázi. Tato spravovaná infrastruktura ale také znamená, že je nutné se rozmyslet odlišně na optimalizaci databáze. Už nebudete mít přehled o tom, jak se má zlepšit hardware databáze nebo síťová infrastruktura. Microsoft Azure řídí tato prostředí. Hlavní oblast, kterou můžete ovládat, je způsob, jakým vaše aplikace komunikuje s Azure SQL Database a službou Azure SQL Managed instance. Dávkování je jedna z těchto optimalizací.

První část tohoto článku prověřuje různé techniky dávkování pro aplikace .NET, které používají Azure SQL Database nebo Azure SQL Managed instance. Poslední dva oddíly obsahují pokyny a scénáře dávkování.

## <a name="batching-strategies"></a>Strategie dávkování

### <a name="note-about-timing-results-in-this-article"></a>Poznámka o výsledcích časování v tomto článku

> [!NOTE]
> Výsledky nejsou srovnávacími testy, ale mají za následek zobrazení **relativního výkonu**. Časování jsou založena na průměru alespoň 10 testovacích běhů. Operace jsou vloženy do prázdné tabulky. Tyto testy byly měřeny před V12 a nemusí nutně odpovídat propustnosti, ke které může dojít v databázi V12 pomocí nových [úrovní služeb DTU](database/service-tiers-dtu.md) nebo [úrovní služeb Vcore](database/service-tiers-vcore.md). Relativní výhoda techniky dávkování by měla být podobná.

### <a name="transactions"></a>Transakce

Zdá se, že se nezvyklí zahájit kontrolu dávkování diskuzí za transakce. Ale použití transakcí na straně klienta má malý efekt dávkování na straně serveru, který zvyšuje výkon. A transakce lze přidat pouze s několika řádky kódu, takže poskytují rychlý způsob, jak vylepšit výkon sekvenčních operací.

Vezměte v úvahu následující kód jazyka C#, který obsahuje posloupnost operací vložení a aktualizace v jednoduché tabulce.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

Následující ADO.NET kód provede postupně tyto operace.

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

Nejlepším způsobem, jak tento kód optimalizovat, je implementovat nějakou formu dávkování těchto volání na straně klienta. Nicméně existuje jednoduchý způsob, jak zvýšit výkon tohoto kódu pouhým zabalením sekvence volání v transakci. Zde je stejný kód, který používá transakci.

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

Transakce jsou ve skutečnosti používány v obou těchto příkladech. V prvním příkladu je každé jednotlivá volání implicitní transakce. V druhém příkladu explicitní transakce balí všechna volání. Na dokumentaci pro [protokol transakcí zápisu na úrovni](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)služby jsou záznamy protokolu po potvrzení transakce vyprázdněny na disk. Takže v transakci může zápis do transakčního protokolu zpozdit až do potvrzení transakce. V důsledku toho povolíte dávkování pro zápisy do transakčního protokolu serveru.

Následující tabulka uvádí některé výsledky ad hoc testování. Testy prováděly stejné sekvenční vložení s transakcemi a bez nich. Pro další perspektivu se první sada testů vzdáleně spustila z přenosného počítače do databáze v Microsoft Azure. Druhá sada testů běžela z cloudové služby a databáze, která se nachází v rámci stejného Microsoft Azure datacentra (Západní USA). Následující tabulka ukazuje dobu v milisekundách sekvenčních vkládání s transakcemi a bez nich.

**Z místního prostředí do Azure**:

| Operace | Bez transakce (MS) | Transakce (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1 226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

Z **Azure do Azure (stejné datacentrum)**:

| Operace | Bez transakce (MS) | Transakce (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

Na základě předchozích výsledků testů zabalení jediné operace v transakci ve skutečnosti snižuje výkon. Ale při zvyšování počtu operací v rámci jedné transakce se zvýšení výkonu bude označeno příznakem. Rozdíl mezi výkonem je také více patrné, pokud se všechny operace vyskytují v rámci Microsoft Azure datacentra. Zvýšení latence při použití Azure SQL Database nebo spravované instance Azure SQL z vnějšku služby Microsoft Azure Datacenter má za to, že se zvyšuje výkon při použití transakcí.

I když použití transakcí může zvýšit výkon, i nadále [sledovat osvědčené postupy pro transakce a připojení](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105)). Udržujte transakce co nejkratší a po dokončení práce zavřete připojení k databázi. Příkaz using v předchozím příkladu zajišťuje, aby bylo připojení uzavřeno po dokončení následného bloku kódu.

Předchozí příklad ukazuje, že můžete přidat místní transakci do libovolného ADO.NET kódu pomocí dvou řádků. Transakce nabízí rychlý způsob, jak vylepšit výkon kódu, který provádí operace sekvenčního vkládání, aktualizace a odstranění. Pro nejrychlejší výkon ale zvažte možnost změny kódu dále, aby bylo možné využít dávkování na straně klienta, jako jsou například parametry s hodnotou tabulky.

Další informace o transakcích v ADO.NET naleznete v tématu [místní transakce v ADO.NET](/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry s hodnotou tabulky

Parametry s hodnotou tabulky podporují uživatelsky definované typy tabulek jako parametry v příkazech jazyka Transact-SQL, uložených procedurách a funkcích. Tato metoda dávkování na straně klienta umožňuje odeslat více řádků dat v rámci parametru s hodnotou tabulky. Chcete-li použít parametry s hodnotou tabulky, nejprve Definujte typ tabulky. Následující příkaz Transact-SQL vytvoří typ tabulky s názvem **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

V kódu vytvoříte **objekt DataTable** s přesnými názvy a typy typu tabulky. Předejte tento **objekt DataTable** do parametru v textovém dotazu nebo v volání uložené procedury. Následující příklad ukazuje tuto techniku:

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

V předchozím příkladu objekt **SqlCommand** vloží řádky z parametru s hodnotou tabulky ** \@ TestTvp**. Dříve vytvořený objekt **DataTable** je přiřazen tomuto parametru pomocí metody **SqlCommand. Parameters. Add** . Dávkování vkládání v jednom volání významně zvyšuje výkon při sekvenčních vkládáních.

Pro zlepšení předchozího příkladu použijte namísto textového příkazu uloženou proceduru. Následující příkaz Transact-SQL vytvoří uloženou proceduru, která převezme parametr s hodnotou tabulky **SimpleTestTableType** .

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

Pak změňte deklaraci objektu **SqlCommand** v předchozím příkladu kódu na následující.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Ve většině případů mají parametry s hodnotou tabulky stejný nebo lepší výkon než ostatní techniky dávkování. Parametry s hodnotou tabulky jsou často vhodnější, protože jsou flexibilnější než jiné možnosti. Například jiné techniky, jako je například hromadné kopírování SQL, povolují pouze vkládání nových řádků. S parametry s hodnotou tabulky můžete ale pomocí logiky v uložené proceduře určit, které řádky jsou aktualizace a které jsou vloženy. Typ tabulky lze také upravit tak, aby obsahoval sloupec "Operation", který označuje, zda má být zadaný řádek vložen, aktualizován nebo odstraněn.

V následující tabulce jsou uvedeny výsledky ad hoc testů pro použití parametrů s hodnotou tabulky v milisekundách.

| Operace | Z místního prostředí do Azure (MS) | Stejné datacentrum Azure (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

Zvýšení výkonu z dávky je okamžitě zřejmé. V předchozím sekvenčním testu trvaly operace 1000 129 sekund mimo datacentrum a 21 sekund v rámci datového centra. S parametry s hodnotou tabulky 1000 ale operace trvá jenom 2,6 sekund mimo datacentrum a 0,4 sekund v rámci datového centra.

Další informace o parametrech s hodnotou tabulky najdete v tématu [parametry s hodnotou tabulky](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>Hromadné kopírování SQL

Hromadné kopírování SQL je dalším způsobem, jak vložit velké objemy dat do cílové databáze. Aplikace .NET můžou k provádění hromadných operací vložení použít třídu **SqlBulkCopy** . **SqlBulkCopy** je obdobou funkcí nástroje příkazového řádku, **Bcp.exe**nebo příkazu jazyka Transact-SQL **Bulk INSERT**. Následující příklad kódu ukazuje, jak hromadně zkopírovat řádky ve zdrojovém **objektu DataTable**, tabulce, do cílové tabulky myTable.

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

V některých případech je vhodnější hromadné kopírování přes parametry s hodnotou tabulky. Viz srovnávací tabulka parametrů Table-Valued a BULK INSERT operace v článku [parametry s hodnotou tabulky](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

Následující výsledky ad hoc testu ukazují výkon dávkování s **SqlBulkCopy** v milisekundách.

| Operace | Z místního prostředí do Azure (MS) | Stejné datacentrum Azure (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

V menších velikostech dávek vykonává použití parametrů s hodnotou tabulky **SqlBulkCopy** třídu. **SqlBulkCopy** však u testů 1 000 a 10 000 řádků 12-31% rychlejší než parametry tabulky. Podobně jako u parametrů s hodnotou tabulky je **SqlBulkCopy** vhodnou možností pro dávková vkládání, zejména v porovnání s výkonem nedávkových operací.

Další informace o hromadném kopírování v ADO.NET najdete v tématu [operace hromadného kopírování](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>Parametrizované příkazy INSERT s více řádky

Jednou alternativou pro malé dávky je vytvoření velkého parametrizovaného příkazu INSERT, který vkládá více řádků. Následující příklad kódu ukazuje tuto techniku.

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

Tento příklad je určen k zobrazení konceptu Basic. Realističtější scénář by měl projít pomocí požadovaných entit k vytvoření řetězce dotazu a parametrů příkazu současně. Omezíte na celkový počet parametrů dotazu 2100, takže toto omezení celkový počet řádků, které lze tímto způsobem zpracovat.

Následující výsledky ad hoc testu ukazují výkon tohoto typu příkazu INSERT v milisekundách.

| Operace | Parametry s hodnotou tabulky (MS) | Vložení jednoho příkazu (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

Tento přístup může být trochu rychlejší pro dávky, které jsou menší než 100 řádků. I když je vylepšení malé, tato technika je další možnost, která může dobře fungovat ve scénáři konkrétní aplikace.

### <a name="dataadapter"></a>Modul

Třída **DataAdapter** umožňuje upravit objekt **DataSet** a následně odeslat změny jako operace vložení, aktualizace a odstranění. Používáte-li objekt **DataAdapter** tímto způsobem, je důležité si uvědomit, že pro každou operaci DISTINCT jsou provedeny samostatné volání. Chcete-li zvýšit výkon, použijte vlastnost **UpdateBatchSize** na počet operací, které by měly být v dávce. Další informace najdete v tématu [provádění dávkových operací pomocí Dataadapterů](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters).

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](https://docs.microsoft.com/ef/efcore-and-ef6/#saving-data) podporuje dávkování.

### <a name="xml"></a>XML

V případě úplnosti se domníváme, že je důležité mluvit o XML jako strategii pro dávkování. Použití jazyka XML však nemá žádné výhody nad jinými metodami a několika nevýhodami. Přístup je podobný jako u parametrů s hodnotou tabulky, ale soubor XML nebo řetězec se předává do uložené procedury místo do uživatelsky definované tabulky. Uložená procedura analyzuje příkazy v uložené proceduře.

Tento přístup má několik nevýhody:

* Práce s XML může být náročná a náchylná k chybám.
* Analýza XML v databázi může být náročná na výkon procesoru.
* Ve většině případů je tato metoda pomalejší než parametry s hodnotou tabulky.

Z těchto důvodů se použití XML pro dávkové dotazy nedoporučuje.

## <a name="batching-considerations"></a>Předpoklady pro dávkování

V následujících částech najdete další pokyny pro použití dávkování v aplikacích Azure SQL Database a Azure SQL Managed instance.

### <a name="tradeoffs"></a>Kompromisy

V závislosti na vaší architektuře může dávkování zahrnovat kompromisy mezi výkonem a odolností. Představte si třeba situaci, kdy se vaše role neočekávaně neukončí. Pokud ztratíte jeden řádek dat, je dopad menší než dopad ztráty velké dávky neodeslaných řádků. Při ukládání řádků do mezipaměti v určeném časovém intervalu je větší riziko.

Z důvodu těchto kompromisů vyhodnoťte typ operací, které jste dávkují. Dávkujte efektivněji (větší dávky a delší časová období) s daty, která jsou méně kritická.

### <a name="batch-size"></a>Velikost dávky

V našich testech neexistovala obvykle žádná výhoda pro rozdělení velkých dávek do menších bloků dat. Ve skutečnosti toto rozdělení často vedlo k nižšímu výkonu než odeslání jedné velké dávky. Představte si třeba situaci, kdy chcete vložit 1000 řádků. Následující tabulka ukazuje, jak dlouho trvá použití parametrů s hodnotou tabulky k vložení 1000 řádků při rozdělení do menších dávek.

| Velikost dávky | Iterací | Parametry s hodnotou tabulky (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

Můžete vidět, že nejlepší výkon pro 1000 řádků je odeslat najednou. V ostatních testech (zde nejsou uvedeny) bylo zvýšení výkonu pro přerušení dávky 10000 řádků na dvě dávky 5000. Schéma tabulky pro tyto testy je poměrně jednoduché, takže byste měli provádět testy na konkrétní data a velikosti dávek, abyste ověřili tyto závěry.

Dalším faktorem, který je třeba zvážit, je to, že pokud celková dávka přestala být příliš velká, Azure SQL Database nebo spravovaná instance Azure SQL může omezit a odmítat zápis dávky. Nejlepších výsledků dosáhnete, když otestujete konkrétní scénář a určíte, jestli je k dispozici ideální velikost dávky. Nastavit velikost dávky za běhu za účelem umožnění rychlých úprav na základě výkonu nebo chyb.

Nakonec vyvážit velikost dávky s riziky spojenými s dávkou. Pokud dojde k přechodným chybám nebo dojde k chybě role, zvažte důsledky opakování operace nebo ztráty dat v dávce.

### <a name="parallel-processing"></a>Paralelní zpracování

Co kdybyste měli přístup k omezení velikosti dávky, ale pro provedení práce bylo použito více vláken? Naše testy znovu ukázaly, že několik menších vláken s více vlákny se obvykle provádí horší než jedna větší dávka. Následující test se pokusí vložit 1000 řádků do jedné nebo více paralelních dávek. Tento test ukazuje, jak více současných dávek ve skutečnosti snížil výkon.

| Velikost dávky [iterace] | Dvě vlákna (MS) | Čtyři vlákna (MS) | Šest vláken (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Výsledky nejsou srovnávacími testy. Podívejte se na [poznámku o výsledcích časování v tomto článku](#note-about-timing-results-in-this-article).

Může dojít k několika potenciálním důvodům pro snížení výkonu v důsledku paralelismu:

* K dispozici je několik současných síťových volání namísto jednoho.
* Více operací na jedné tabulce může mít za následek kolizí a blokování.
* Existují režijní náklady spojené s více vlákny.
* Náklady na otevření více připojení převažují z výhody paralelního zpracování.

Pokud cílíte na různé tabulky nebo databáze, je možné, že se pomocí této strategie zobrazí nějaký nárůst výkonu. Tento přístup by představovala databáze horizontálního dělení nebo datafederace. Horizontálního dělení používá více databází a směruje různá data do každé databáze. Pokud se každá malá dávka přesměruje na jinou databázi, může být provádění operací paralelně efektivnější. Zvýšení výkonu však není dostatečně důležité, aby ho bylo možné použít jako základ pro rozhodnutí o použití horizontálního dělení databáze ve vašem řešení.

V některých návrzích může paralelní spouštění menších dávek způsobit lepší propustnost požadavků v systému v rámci zátěže. V takovém případě, i když je rychlejší zpracovat jedinou větší dávku, může být zpracování více dávkových procesů efektivnější.

Pokud používáte paralelní provádění, zvažte řízení maximálního počtu pracovních vláken. Menší číslo může mít za následek méně kolizí a kratší dobu provádění. Zvažte také dodatečné zatížení, které tato operace v cílové databázi umístí jak v připojeních, tak v transakcích.

### <a name="related-performance-factors"></a>Související faktory výkonu

Typické pokyny k výkonu databáze ovlivňují také dávkování. Například pro tabulky, které mají velký primární klíč nebo mnoho neclusterovaných indexů, je třeba vložit výkon.

Pokud parametry s hodnotou tabulky používají uloženou proceduru, můžete použít **sadu** příkazů na začátku postupu. Tento příkaz potlačuje Návrat počtu ovlivněných řádků v proceduře. V našich testech však použití **set-Count na** žádném z nich nijak neovlivňují nebo snížilo výkon. Uložená procedura testu byla v parametru s hodnotou tabulky jednoduchá a jediným příkazem **INSERT** . Je možné, že tento příkaz by měl těžit z složitějších uložených procedur. Ale nemusíte předpokládat, že přidání **Nastavení nepočítat** k uložené proceduře automaticky zlepšuje výkon. Pro pochopení tohoto efektu otestujte uloženou proceduru pomocí příkazu **set-Count on** a bez něj.

## <a name="batching-scenarios"></a>Scénáře dávkování

Následující části popisují, jak používat parametry s hodnotou tabulky ve třech scénářích aplikací. První scénář ukazuje, jak může fungovat ukládání do vyrovnávací paměti a dávkování. Druhý scénář zvyšuje výkon provedením operací hlavní-podrobnosti v jednom volání uložené procedury. V posledním scénáři se dozvíte, jak používat v operaci "UPSERT" parametry s hodnotou tabulky.

### <a name="buffering"></a>Do vyrovnávací paměti

I když existují některé scénáře, které jsou pro dávkování zjevné kandidáty, existuje mnoho scénářů, které by mohly využít dávkování opožděným zpracováním. Opožděné zpracování ale také přináší větší riziko, že data jsou ztracena v případě neočekávaného selhání. Je důležité pochopit toto riziko a vzít v úvahu důsledky.

Představte si třeba webovou aplikaci, která sleduje historii navigace jednotlivých uživatelů. Na každý požadavek na stránku může aplikace provést volání databáze pro záznam zobrazení stránky uživatele. Vyšší výkon a škálovatelnost se ale dají dosáhnout ukládáním do vyrovnávací paměti pro navigační aktivity uživatelů a odesláním těchto dat do databáze v dávkách. Aktualizaci databáze můžete aktivovat podle uplynulého času nebo velikosti vyrovnávací paměti. Pravidlo může například určovat, že by měla být dávka zpracována po 20 sekundách nebo když vyrovnávací paměť dosáhne 1000 položek.

Následující příklad kódu používá [reaktivní rozšíření – RX](https://docs.microsoft.com/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) pro zpracování událostí vyrovnávací paměti vyvolaných třídou monitorování. Při vyplňování vyrovnávací paměti nebo vypršení časového limitu se dávka uživatelských dat pošle do databáze s parametrem s hodnotou tabulky.

Následující třídy NavHistoryData modelují podrobnosti o navigaci uživatele. Obsahuje základní informace, jako je identifikátor uživatele, přístup k adrese URL a doba přístupu.

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

Třída NavHistoryDataMonitor zodpovídá za ukládání dat uživatelských navigace do databáze do vyrovnávací paměti. Obsahuje metodu RecordUserNavigationEntry, která reaguje zvýšením **přidané** události. Následující kód ukazuje logiku konstruktoru, která používá RX k vytvoření pozorovatelované kolekce na základě události. Pak se přihlásí k odběru této pozorovatelované kolekce metodou vyrovnávací paměti. Přetížení určuje, že se má vyrovnávací paměť odeslat každých 20 sekund nebo 1000 záznamů.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Obslužná rutina převede všechny položky vyrovnávací paměti na typ s hodnotou tabulky a poté předá tento typ uložené proceduře, která zpracovává dávku. Následující kód ukazuje kompletní definici pro třídy NavHistoryDataEventArgs i NavHistoryDataMonitor.

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

Obslužná rutina převede všechny položky vyrovnávací paměti na typ s hodnotou tabulky a poté předá tento typ uložené proceduře, která zpracovává dávku. Následující kód ukazuje kompletní definici pro třídy NavHistoryDataEventArgs i NavHistoryDataMonitor.

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

Chcete-li použít tuto třídu ukládání do vyrovnávací paměti, aplikace vytvoří statický objekt NavHistoryDataMonitor. Pokaždé, když uživatel přistupuje k stránce, aplikace zavolá metodu NavHistoryDataMonitor. RecordUserNavigationEntry. Logika ukládání do vyrovnávací paměti pokračuje v posílání těchto záznamů do databáze v dávkách.

### <a name="master-detail"></a>Hlavní podrobnosti

Parametry s hodnotou tabulky jsou užitečné při jednoduchých scénářích vkládání. Může však být náročnější na dávkové vkládání, které zahrnuje více než jednu tabulku. Dobrým příkladem je scénář "hlavní/podrobnosti". Hlavní tabulka identifikuje primární entitu. Jedna nebo více tabulek podrobností ukládá více dat o entitě. V tomto scénáři relace cizího klíče vynutily vztah podrobností k jedinečné hlavní entitě. Vezměte v úvahu zjednodušenou verzi tabulky PurchaseOrder a její přidruženou tabulku OrderDetail. Následující příkaz Transact-SQL vytvoří tabulku PurchaseOrder se čtyřmi sloupci: ČísloObjednávky, DatumObjednávky, CustomerID a status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Každá objednávka obsahuje jeden nebo více nákupů produktů. Tyto informace jsou zachyceny v tabulce PurchaseOrderDetail. Následující příkaz Transact-SQL vytvoří tabulku PurchaseOrderDetail s pěti sloupci: ČísloObjednávky, OrderDetailID, ProductID, UnitPrice a OrderQty.

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

Sloupec ČísloObjednávky v tabulce PurchaseOrderDetail musí odkazovat na pořadí z tabulky PurchaseOrder. Toto omezení vynutila následující definice cizího klíče.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Aby bylo možné použít parametry s hodnotou tabulky, je nutné mít jeden uživatelem definovaný typ tabulky pro každou cílovou tabulku.

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

Pak definujte uloženou proceduru, která akceptuje tabulky těchto typů. Tento postup umožňuje aplikaci místně dávkovat sadu objednávek a podrobnosti objednávky v rámci jednoho volání. Následující příkaz Transact-SQL poskytuje kompletní deklaraci uložené procedury pro tento příklad nákupní objednávky.

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

V tomto příkladu lokálně definovaná @IdentityLink tabulka ukládá skutečné hodnoty ČísloObjednávky z nově vložených řádků. Tyto identifikátory objednávky se liší od dočasných hodnot KódObjednávky v @orders parametrech a a hodnotách @details tabulky. Z tohoto důvodu @IdentityLink tabulka pak propojí hodnoty ČísloObjednávky z @orders parametru s hodnotami reálné hodnoty ČísloObjednávky pro nové řádky v tabulce PurchaseOrder. Po provedení tohoto kroku @IdentityLink může tabulka zjednodušit vkládání podrobností objednávky se skutečnou ČísloObjednávky, která splňuje omezení cizího klíče.

Tuto uloženou proceduru lze použít z kódu nebo z jiných volání jazyka Transact-SQL. Příklad kódu naleznete v části s parametry s hodnotou tabulky v tomto dokumentu. Následující příkaz Transact-SQL ukazuje, jak volat sp_InsertOrdersBatch.

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

Toto řešení umožňuje, aby každá dávka používala sadu hodnot ČísloObjednávky, které začínají na 1. Tyto dočasné hodnoty ČísloObjednávky popisují relace v dávce, ale skutečné hodnoty ČísloObjednávky se určují v době operace vložení. Můžete opakovaně spouštět stejné příkazy v předchozím příkladu a generovat jedinečné objednávky v databázi. Z tohoto důvodu zvažte přidání dalšího kódu nebo databázové logiky, které brání duplicitním objednávkám při použití této techniky dávkování.

Tento příklad ukazuje, že ještě složitější databázové operace, například operace hlavní-podrobnosti, mohou být dávkově použity pomocí parametrů s hodnotou tabulky.

### <a name="upsert"></a>UPSERT

Další scénář dávkování zahrnuje současné aktualizace stávajících řádků a vkládání nových řádků. Tato operace se někdy označuje jako operace "UPSERT" (Update + Insert). Místo provádění samostatných volání pro vložení a aktualizaci je příkaz MERGE nejvhodnější pro tuto úlohu. Příkaz MERGE může provádět operace vložení i aktualizace v rámci jednoho volání.

Parametry s hodnotou tabulky lze použít spolu s příkazem MERGE k provedení aktualizací a vložení. Zvažte například zjednodušenou tabulku zaměstnanců, která obsahuje následující sloupce: ČísloZaměstnance, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

V tomto příkladu můžete použít fakt, že SocialSecurityNumber je jedinečný k provedení sloučení více zaměstnanců. Nejdřív vytvořte uživatelsky definovaný typ tabulky:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Dále vytvořte uloženou proceduru nebo zadejte kód, který používá příkaz MERGE k provedení aktualizace a vložení. Následující příklad používá příkaz MERGE u parametru s hodnotou tabulky, @employees typu EmployeeTableType. Obsah @employees tabulky tady není zobrazený.

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

Další informace naleznete v dokumentaci a příkladech příkazu MERGE. I když stejnou práci lze provést ve volání uložené procedury s vícenásobným krokem s oddělenými operacemi INSERT a UPDATE, příkaz MERGE je efektivnější. Databázový kód může také vytvořit volání Transact-SQL, která přímo používají příkaz MERGE bez vyžadování dvou volání databáze pro funkce INSERT a UPDATE.

## <a name="recommendation-summary"></a>Souhrn doporučení

Následující seznam poskytuje souhrn doporučení pro dávkování popsaných v tomto článku:

* Využijte vyrovnávací paměti a dávkování, abyste zvýšili výkon a škálovatelnost Azure SQL Database a aplikací spravované instance Azure SQL.
* Pochopení kompromisů mezi dávkami, ukládáním do vyrovnávací paměti a odolností Při selhání role může být riziko ztráty nezpracované dávky důležitých podnikových dat převáženo přínosem pro výkon dávkování.
* Pokuste se zachovat všechna volání do databáze v rámci jednoho datového centra, aby se snížila latence.
* Pokud zvolíte jednu techniku dávkování, parametry s hodnotou tabulky nabízejí nejlepší výkon a flexibilitu.
* Pro nejrychlejší vložení výkonu postupujte podle těchto obecných pokynů, ale otestujte svůj scénář:
  * Pro řádky < 100 použijte jeden parametrizovaný příkaz INSERT.
  * Pro řádky < 1000 použijte parametry s hodnotou tabulky.
  * Pro >= 1000 řádků použijte SqlBulkCopy.
* Pro operace Update a DELETE použijte parametry s hodnotou tabulky s logikou uložené procedury, která určuje správnou operaci na každém řádku parametru Table.
* Pokyny pro velikost dávky:
  * Využijte největší velikosti dávek, které jsou vhodné pro vaše aplikace a obchodní požadavky.
  * Vyvážení nárůstu výkonu velkých dávek s riziky dočasných nebo závažných chyb. Jaké jsou důsledky opakování nebo ztráty dat v dávce?
  * Otestujte největší velikost dávky, abyste ověřili, že Azure SQL Database nebo spravovaná instance Azure SQL ji neodmítl.
  * Vytvořte nastavení konfigurace, které řídí dávkování, jako je například velikost dávky nebo časový interval ukládání do vyrovnávací paměti. Tato nastavení poskytují flexibilitu. Chování dávkování v produkčním prostředí můžete změnit, aniž byste museli znovu nasazovat cloudovou službu.
* Vyhněte se paralelnímu spouštění dávek, které pracují s jednou tabulkou v jedné databázi. Pokud se rozhodnete rozdělit jednu dávku napříč více pracovními vlákny, spusťte testy pro určení ideálního počtu vláken. Po nespecifikované prahové hodnotě bude více vláken snižovat výkon a nebude je zvyšovat.
* Zvažte ukládání do vyrovnávací paměti podle velikosti a času jako způsob implementace dávkování pro více scénářů.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na to, jak techniky navrhování a kódování databází, které souvisejí s dávkou, můžou zlepšit výkon a škálovatelnost aplikace. Toto je ale v celkové strategii jenom jeden faktor. Další způsoby, jak zvýšit výkon a škálovatelnost, najdete v tématu doprovodné materiály k [výkonu databáze](database/performance-guidance.md) a [cenové a výkonové požadavky pro elastický fond](database/elastic-pool-overview.md).
