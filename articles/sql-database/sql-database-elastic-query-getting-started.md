---
title: "Sestava napříč instancemi cloudu databází (vodorovné rozdělení do oddílů) | Microsoft Docs"
description: "Použijte dotazy na databázi křížové databáze do sestavy napříč více databází."
services: sql-database
manager: jhubbard
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/23/2016
ms.author: mlandzic
ms.openlocfilehash: 29f1291f5c5d6023ca7bcd47fc2c0d9b69fd39fa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Sestavy napříč instancemi cloudu databází (preview)
Můžete vytvořit sestavy z několika databází Azure SQL z bodu pomocí jednoho připojení [elastické dotazu](sql-database-elastic-query-overview.md). Databáze musí mít oddíly vodorovně (také označované jako "horizontálně dělené").

Pokud máte existující databázi, přečtěte si téma [migrace existujících databází do databází upraveným](sql-database-elastic-convert-to-use-elastic-tools.md).

Pochopit objektů SQL potřebné k dotazování, najdete v části [dotazu mezi databázemi vodorovně oddílů](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Požadavky
Stažení a spuštění [Začínáme s ukázkou nástroje elastické databáze](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvoření horizontálního oddílu mapy manager pomocí ukázkové aplikace
Zde vytvoříte mapu horizontálního oddílu manager spolu s několika horizontálních oddílů, za nímž následuje vložení dat do horizontálních oddílů. Pokud jste již má instalační program horizontálních oddílů s horizontálně dělená data v nich, můžete přeskočit následující kroky a přesunout k další části.

1. Sestavení a spuštění **Začínáme s nástroje elastické databáze** ukázkové aplikace. Postupujte podle pokynů až do kroku 7 v části [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na konci tohoto kroku 7 zobrazí se následující příkazový řádek:

    ![příkazový řádek][1]
2. V okně příkazového řádku zadejte "1" a stiskněte klávesu **Enter**. To vytvoří horizontálního oddílu správce mapy a přidá dva horizontálních oddílů server. Potom zadejte "3" a stiskněte klávesu **Enter**; čtyřikrát akci opakujte. Vloží řádky ukázková data ve vašem horizontálních oddílů.
3. [Portál Azure](https://portal.azure.com) by měl zobrazit tři nové databáze na serveru:

   ![Visual Studio potvrzení][2]

   Mezidatabázové dotazy v tomto okamžiku jsou podporovány prostřednictvím knihovny klienta elastické databáze. Například v příkazovém okně použijte možnost 4. Výsledky z dotazu víc horizontálních jsou vždy **UNION ALL** výsledků ze všech horizontálních oddílů.

   V další části jsme vytvořit koncový bod ukázkové databáze podporující bohatší dotazování dat napříč horizontálních oddílů.

## <a name="create-an-elastic-query-database"></a>Vytvoření dotazu elastické databáze
1. Otevřete [portál Azure](https://portal.azure.com) a přihlaste se.
2. Vytvořte novou databázi Azure SQL na stejném serveru jako vašeho nastavení horizontálního oddílu. Název databáze "ElasticDBQuery."

    ![Portál Azure a cenovou úroveň][3]

    > [!NOTE]
    > můžete použít existující databázi. Pokud můžete tak učinit, nesmí být jeden z horizontálních oddílů, které byste chtěli provést své dotazy. Tato databáze se použije pro vytváření objektů metadat pro dotaz elastické databáze.
    >

## <a name="create-database-objects"></a>Vytvoření databázových objektů
### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč databáze obor a přihlašovací údaje
Ty se používají k připojení k správce mapy horizontálního oddílu a horizontálních oddílů:

1. Spusťte aplikaci SQL Server Management Studio nebo SQL Server Data Tools v sadě Visual Studio.
2. Připojení k databázi ElasticDBQuery a spuštěním následujících příkazů T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" a "password" by měla být stejná jako informace o přihlášení se používají v kroku 6 v [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) v [Začínáme s nástroje elastické databáze](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Externí zdroje dat
Pokud chcete vytvořit externího zdroje dat, spusťte následující příkaz v databázi ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" je název horizontálního oddílu mapy, pokud jste vytvořili horizontálního oddílu mapy a správce mapy horizontálního oddílu pomocí Ukázka nástroje elastické databáze. Ale pokud jste použili vlastní instalace pro tuto ukázku, pak je nutné název mapy horizontálního oddílu, které jste zvolili v aplikaci.

### <a name="external-tables"></a>Externí tabulky
Vytvořte externí tabulku, která odpovídá tabulku zákazníků na horizontálních oddílů spuštěním následujícího příkazu na databázi ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL elastické databáze
Jakmile definujete zdroj externích dat a externí tabulky teď můžete použít úplnou T-SQL na externí tabulky.

Spusťte tento dotaz na databázi ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Si všimnete, že dotaz agreguje výsledky ze všech horizontálních oddílů a poskytuje následující výstup:

![Podrobnosti výstupu][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Import výsledků dotazu elastické databáze do aplikace Excel
 Můžete importovat výsledky z dotazu do souboru aplikace Excel.

1. Spusťte aplikaci Excel 2013.
2. Přejděte na **Data** pásu karet.
3. Klikněte na tlačítko **z jiných zdrojů** a klikněte na tlačítko **z SQL serveru**.

   ![Importu pro aplikaci Excel z jiných zdrojů][5]
4. V **Průvodce datovým připojením** zadejte název a přihlašovací údaje serveru. Pak klikněte na tlačítko **Další**.
5. V dialogovém okně **vyberte databáze, která obsahuje data, která chcete**, vyberte **ElasticDBQuery** databáze.
6. Vyberte **zákazníci** tabulky v zobrazení seznamu a klikněte na tlačítko **Další**. Pak klikněte na tlačítko **Dokončit**.
7. V **importovat Data** formuláři v části **vyberte, jak chcete zobrazit tato data v sešitu**, vyberte **tabulky** a klikněte na tlačítko **OK**.

Všechny řádky z **zákazníci** tabulky, uložené v různých horizontálních oddílů naplnit listu aplikace Excel.

Teď můžete použít funkce vizualizace výkonné dat v aplikaci Excel. Připojovací řetězec s názvem serveru, názvu databáze a pověření slouží k připojení k databázi elastické dotazu vaše integrace nástrojů BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro vaše nástroje. Může být elastické dotaz do databáze a externí tabulky stejně jako všechny ostatní databáze systému SQL Server a tabulek systému SQL Server, které by se připojit k vaší nástrojem.

### <a name="cost"></a>Náklady
Není k dispozici pro použití funkce elastické databáze dotazu bez dalších poplatků.

Informace o cenách najdete v části [podrobnosti o cenách na SQL databázi](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další postup

* Přehled elastické dotazů najdete v tématu [elastické dotazu přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurzu, najdete v části [Začínáme s mezidatabázové dotazu (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle oddílů data, najdete v části [dotazování svisle na oddíly dat)](sql-database-elastic-query-vertical-partitioning.md)
* Syntaxe a ukázkové dotazy pro horizontálně dělenou data, najdete v části [dotazování vodorovně rozdělena na oddíly dat)](sql-database-elastic-query-horizontal-partitioning.md)
* V tématu [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, který spouští příkaz jazyka Transact-SQL na jedné vzdálené databáze SQL Azure nebo sadu databází, které slouží jako horizontálních oddílů v vodorovné schéma rozdělení oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
