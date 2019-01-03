---
title: Sestavy napříč databázemi s horizontálním navýšením kapacity (horizontální dělení) | Dokumentace Microsoftu
description: Použijte databázové dotazy napříč databázemi do sestavy ve více databázích.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: da876152b55ee626025b6192a5004648a29545dc
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604180"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Sestavy napříč databázemi s horizontálním navýšením kapacity (preview)
Můžete vytvořit sestavy z několika databázemi Azure SQL z jednoho připojení bodu pomocí [elastický dotaz](sql-database-elastic-query-overview.md). Databáze musí být horizontálně dělené do oddílů (označované také jako "horizontálně dělené").

Pokud máte existující databázi, naleznete v tématu [migrace existujících databází do databází s horizontálním navýšením kapacity](sql-database-elastic-convert-to-use-elastic-tools.md).

Vysvětlení SQL objektů potřebných k dotazování, najdete v tématu [dotazování napříč databázemi s horizontálně dělené](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Požadavky
Stáhněte a spusťte [Začínáme s ukázkou nástrojů Elastic Database](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvořte ukázkovou aplikaci pomocí Správce mapování horizontálního oddílu
Tady vytvoříte mapy horizontálních oddílů manager společně s několika horizontálními oddíly, za nímž následuje vložení dat do horizontální oddíly. Pokud jste už nastavení horizontálními oddíly s horizontálně dělená data v nich, můžete přeskočit následující kroky a přejít k další části.

1. Sestavit a spustit **Začínáme s nástroji Elastic Database** ukázkovou aplikaci. Postupujte podle pokynů až do kroku 7 v části [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). Na konci kroku 7 zobrazí se následující příkazový řádek:

    ![Příkazový řádek][1]
2. V příkazovém okně zadejte "1" a stiskněte klávesu **Enter**. To vytvoří správce mapování horizontálního oddílu a přidá dvě horizontálních oddílů k serveru. Potom zadejte "3" a stiskněte klávesu **Enter**; čtyřikrát opakujte akci. To vloží ukázková data řádků vaše horizontálních oddílů.
3. [Webu Azure portal](https://portal.azure.com) by se zobrazit tři nové databáze na serveru:

   ![Visual Studio potvrzení][2]

   Mezidatabázové dotazy v tomto okamžiku jsou podporovány pomocí klientské knihovny Elastic Database. Možnost 4 například použijte v příkazovém okně. Výsledky z více horizontálních oddílů dotazu jsou vždy **UNION ALL** výsledků ze všech horizontálních oddílů.

   V další části vytvoříme koncový bod ukázkové databáze, která podporuje bohatší dotazování data napříč horizontálními oddíly.

## <a name="create-an-elastic-query-database"></a>Vytvořte dotaz na elastic database
1. Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se.
2. Vytvořte novou databázi Azure SQL na stejném serveru jako nastavení horizontálními oddíly. Název databáze "ElasticDBQuery."

    ![Cenová úroveň a webu Azure portal][3]

    > [!NOTE]
    > můžete použít existující databázi. Pokud vám pomůžou, proto to nesmí být jeden z horizontálních oddílů, které byste chtěli provést vaše dotazy. Tato databáze se použije pro vytváření objektů metadat pro dotaz na elastickou databázi.
    >

## <a name="create-database-objects"></a>Vytváření databázových objektů
### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč s rozsahem databáze a přihlašovacích údajů
Ty se používají k připojení k správce mapování horizontálních oddílů a horizontální oddíly:

1. Otevřete SQL Server Management Studio nebo SQL Server Data Tools v sadě Visual Studio.
2. Připojení k databázi ElasticDBQuery a spusťte následující příkazy T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" a "password" by měla být stejná jako přihlašovací údaje použité v kroku 6 postupu [stažení a spuštění ukázkové aplikace](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) v [Začínáme s nástroji elastic database](sql-database-elastic-scale-get-started.md).

### <a name="external-data-sources"></a>Externí zdroje dat
K vytvoření externího zdroje dat, spusťte následující příkaz v databázi ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" je název mapy horizontálních oddílů, pokud jste vytvořili mapy horizontálních oddílů a ukázka nástroje pro elastické databáze správce mapování horizontálních oddílů. Nicméně pokud jste použili vlastní nastavení pro tuto ukázku, pak je třeba název mapy horizontálních oddílů, kterou jste zvolili v aplikaci.

### <a name="external-tables"></a>Externí tabulky
Vytvoření externí tabulky, která odpovídá spuštěním následujícího příkazu na databázi ElasticDBQuery tabulky Zákazníci na horizontální oddíly:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL elastické databáze
Pokud jste definovali v externím zdroji dat. a externích tabulek teď můžete úplné T-SQL na externí tabulky.

Spusťte tento dotaz na databázi ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Uvidíte, že dotaz agreguje výsledky ze všech horizontálních oddílů a poskytuje následující výstup:

![Podrobnosti výstupu][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importovat výsledky dotazu elastické databáze do Excelu
 Můžete importovat z výsledků dotazu do Excelového souboru.

1. Spuštění Excelu 2013.
2. Přejděte **Data** pásu karet.
3. Klikněte na tlačítko **z jiných zdrojů** a klikněte na tlačítko **z SQL serveru**.

   ![Import z aplikace Excel z jiných zdrojů][5]
4. V **Průvodce datovým připojením** zadejte název a přihlašovací údaje serveru. Pak klikněte na tlačítko **Další**.
5. V dialogovém okně **vyberte databázi, která obsahuje data, která chcete**, vyberte **ElasticDBQuery** databáze.
6. Vyberte **zákazníkům** tabulky v zobrazení seznamu a klikněte na tlačítko **Další**. Pak klikněte na tlačítko **Dokončit**.
7. V **Import dat** formuláře, v části **vyberte požadovaný způsob zobrazení dat v sešitu**vyberte **tabulky** a klikněte na tlačítko **OK**.

Všechny řádky z **zákazníkům** tabulky, uložené v různých horizontálních oddílech naplnit do Excelového souboru.

Teď můžete použít funkce v Excelu výkonné datové vizualizace. Můžete použít připojovací řetězec s názvem serveru, název databáze a přihlašovací údaje pro připojení k databázi elastický dotaz integrace nástroje pro BI a data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro nástroj. Mohou odkazovat na dotaz na elastic database a externí tabulky, stejně jako jakékoli jiné databáze systému SQL Server a tabulek systému SQL Server, které by se připojit s nástrojem.

### <a name="cost"></a>Náklady
Neexistuje žádné další poplatky za využívání funkci dotaz na Elastic Database.

Informace o cenách najdete v části [podrobnosti o cenách na SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další postup

* Přehled elastický dotaz, naleznete v tématu [elastický dotaz přehled](sql-database-elastic-query-overview.md).
* Vertikální dělení kurz najdete v tématu [Začínáme s mezidatabázovými dotazy (vertikální oddíly)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy na vertikálně dělená data, najdete v části [dotazování na vertikálně dělené data)](sql-database-elastic-query-vertical-partitioning.md)
* Syntaxe a ukázkové dotazy pro horizontálně dělená data, najdete v části [dotazování na horizontálně dělené data)](sql-database-elastic-query-horizontal-partitioning.md)
* Naleznete v tématu [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provádí příkaz jazyka Transact-SQL na jeden vzdálený Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly takovým vodorovné schéma vytváření oddílů.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
