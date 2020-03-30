---
title: Sestavy napříč škálovatenou cloudovou databází
description: Pomocí dotazů databáze křížových databází můžete vykazovat sestavy ve více databázích.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823781"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Sestavy napříč škálovatenatými cloudovými databázemi (preview)

Sestavy můžete vytvářet z více databází Azure SQL z jednoho spojovacího bodu pomocí [elastického dotazu](sql-database-elastic-query-overview.md). Databáze musí být horizontálně rozděleny (také označované jako "horizontálně dělené").

Pokud máte existující databázi, [přečtěte si informace o migraci existujících databází do databází s horizontálním navýšením kapacity](sql-database-elastic-convert-to-use-elastic-tools.md).

Chcete-li porozumět objektům SQL potřebným k dotazování, přečtěte si informace [o dotazu napříč horizontálně rozdělenými databázemi](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Požadavky

Stáhněte a spusťte [ukázku nástroje Začínáme s nástrojem elastické databáze](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvoření správce mapy skřípků pomocí ukázkové aplikace
Zde vytvoříte správce mapy střepů spolu s několika úlomky, následovaný vložením dat do štřepů. Pokud jste se stalo, že již mají nastavení štrůdličky s rozdělená data v nich, můžete přeskočit následující kroky a přejít na další část.

1. Vytvořte a spusťte ukázkovou aplikaci **Nástroje elastické databáze** podle kroků v části článku [Stáhněte a spusťte ukázkovou aplikaci](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1). Po dokončení všech kroků se zobrazí následující příkazový řádek:

    ![Příkazového řádku][1]
2. V příkazovém okně zadejte "1" a stiskněte **Enter**. Tím se vytvoří správce mapy střepů a přidá dva oddíly šibenic na server. Poté zadejte "3" a stiskněte **Enter**; opakujte akci čtyřikrát. To vloží řádky ukázkových dat do štřepů.
3. Na [webu Azure portal](https://portal.azure.com) by se měly zobrazit tři nové databáze na serveru:

   ![Potvrzení sady Visual Studio][2]

   V tomto okamžiku jsou dotazy mezi databázemi podporovány prostřednictvím klientských knihoven elastické databáze. V příkazovém okně například použijte možnost 4. Výsledky z dotazu s více oddíly jsou vždy **UNION VŠECHNY** výsledky ze všech šdružů.

   V další části vytvoříme koncový bod ukázkové databáze, který podporuje bohatší dotazování dat napříč oddíly.

## <a name="create-an-elastic-query-database"></a>Vytvoření databáze elastických dotazů
1. Otevřete [portál Azure](https://portal.azure.com) a přihlaste se.
2. Vytvořte novou databázi Azure SQL na stejném serveru jako vaše nastavení oddílu. Pojmenujte databázi ElasticDBQuery.

    ![Portál Azure a cenová úroveň][3]

    > [!NOTE]
    > můžete použít existující databázi. Pokud to můžete udělat, nesmí to být jeden z úlomků, na kterých chcete provést dotazy. Tato databáze se použije pro vytváření objektů metadat pro elastický databázový dotaz.
    >

## <a name="create-database-objects"></a>Vytváření databázových objektů
### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a pověření s rozsahem databáze
Ty se používají k připojení ke správci mapy střepů a úlomky:

1. Otevřete sql server management studio nebo SQL Server datové nástroje v sadě Visual Studio.
2. Připojte se k databázi ElasticDBQuery a spusťte následující příkazy T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "uživatelské jméno" a "heslo" by měly být stejné jako přihlašovací údaje použité v kroku 3 části [Stáhnout a spustit ukázkovou aplikaci](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) v článku **Začínáme s nástroji elastické databáze.**

### <a name="external-data-sources"></a>Externí zdroje dat
Chcete-li vytvořit externí zdroj dat, spusťte následující příkaz v databázi ElasticDBQuery:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" je název mapy střepů, pokud jste vytvořili mapu střepů a správce mapy střepů pomocí ukázky nástrojů elastické databáze. Pokud jste však pro tuto ukázku použili vlastní nastavení, měl by to být název mapy svižnosti, který jste zvolili v aplikaci.

### <a name="external-tables"></a>Externí tabulky
Vytvořte externí tabulku, která odpovídá tabulce Zákazníci na šiřidlích provedením následujícího příkazu v databázi ElasticDBQuery:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového elastického dotazu T-SQL
Jakmile definujete externí zdroj dat a externí tabulky, můžete nyní použít celý T-SQL přes externí tabulky.

Spusťte tento dotaz v databázi ElasticDBQuery:

    select count(CustomerId) from [dbo].[Customers]

Všimněte si, že dotaz agreguje výsledky ze všech střepů a poskytuje následující výstup:

![Podrobnosti o výstupu][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Import výsledků elastického databázového dotazu do Excelu
 Výsledky dotazu můžete importovat do souboru aplikace Excel.

1. Spusťte Excel 2013.
2. Přejděte na **pás karet dat.**
3. Klepněte na **položku Z jiných zdrojů** a klepněte na **položku Ze serveru SQL Server**.

   ![Import excelu z jiných zdrojů][5]
4. V **Průvodci datovým připojením** zadejte název serveru a přihlašovací údaje. Pak klikněte na **Další**.
5. V dialogovém **okně: Vyberte databázi obsahující požadovaná data**, vyberte databázi **ElasticDBQuery.**
6. V zobrazení seznamu vyberte tabulku **Zákazníci** a klepněte na tlačítko **Další**. Klikněte na **Dokončit**.
7. Ve formuláři **Importovat data** vyberte v části **Vyberte, jak chcete tato data zobrazit v sešitu**, vyberte **Tabulka** a klepněte na **OK**.

Všechny řádky z tabulky **Zákazníci** uložené v různých šiřících dřících naplní list aplikace Excel.

Nyní můžete používat výkonné funkce vizualizace dat aplikace Excel. Pomocí připojovacího řetězce s názvem serveru, názvem databáze a přihlašovacími údaji můžete připojit nástroje BI a data integrace k databázi elastických dotazů. Ujistěte se, že SQL Server je podporován jako zdroj dat pro váš nástroj. Můžete odkazovat na databázi elastických dotazů a externí tabulky, stejně jako všechny ostatní sql server databáze a SQL Server tabulky, které byste se připojit pomocí nástroje.

### <a name="cost"></a>Náklady
Za použití funkce Elastic Database Query se neplatí žádné další poplatky.

Informace o cenách naleznete v [tématu PODROBNOSTI o cenách databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v [tématu Přehled elastického dotazu](sql-database-elastic-query-overview.md).
* Kurz vertikálního dělení naleznete v [tématu Začínáme s dotazem mezi databázemi (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle dělená data, najdete [v tématu Dotazování svisle dělených dat)](sql-database-elastic-query-vertical-partitioning.md)
* Syntaxe a ukázkové dotazy pro horizontálně dělená data naleznete v [tématu Dotazování horizontálně dělených dat)](sql-database-elastic-query-horizontal-partitioning.md)
* Viz [\_sp \_spustit vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provede příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly v horizontálním dělení schéma.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
