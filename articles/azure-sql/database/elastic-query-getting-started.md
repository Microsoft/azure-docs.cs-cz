---
title: Sestava napříč cloudových databází s horizontálním škálováním
description: Dotazy databázové databáze pro různé databáze můžete používat k sestavování napříč více databázemi.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92781222"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Sestava napříč cloudových databází s horizontálním škálováním (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Můžete vytvářet sestavy z více databází z jednoho spojovacího bodu pomocí [elastického dotazu](elastic-query-overview.md). Databáze musí být horizontálně rozdělené (označuje se také jako "horizontálně dělené").

Pokud máte existující databázi, přečtěte si téma [migrace existujících databází do databází s horizontálním](elastic-convert-to-use-elastic-tools.md)navýšení kapacity.

Pro pochopení objektů SQL potřebných pro dotazování si přečtěte téma [dotazování napříč horizontálně rozdělenými databázemi](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Požadavky

Stáhněte si a spusťte [ukázku Začínáme s nástroji pro elastic Database](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Vytvoření správce map horizontálních oddílů pomocí ukázkové aplikace
Tady vytvoříte správce map horizontálních oddílů spolu s několika horizontálních oddílůy, za kterými následuje vložení dat do horizontálních oddílů. Pokud už máte horizontálních oddílů nastavení s horizontálně dělené daty, můžete přeskočit následující kroky a přejít k další části.

1. Sestavte a spusťte ukázkovou aplikaci **nástroje Začínáme s elastic Database** , a to pomocí kroků v části článek [Stažení a spuštění ukázkové aplikace](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Po dokončení všech kroků se zobrazí následující příkazový řádek:

    ![příkazový řádek][1]
2. V příkazovém okně zadejte "1" a stiskněte klávesu **ENTER**. Tím se vytvoří správce map horizontálních oddílů a na server se přidá dva horizontálních oddílů. Pak zadejte "3" a stiskněte **ENTER**; Opakujte akci čtyřikrát. Tím se vloží vzorové datové řádky do horizontálních oddílů.
3. [Azure Portal](https://portal.azure.com) by se měly na vašem serveru zobrazovat tři nové databáze:

   ![Potvrzení sady Visual Studio][2]

   V tomto okamžiku jsou mezidatabázové dotazy podporovány prostřednictvím klientských knihoven Elastic Database. Použijte například možnost 4 v příkazovém okně. Výsledky z dotazu multi-horizontálních oddílů jsou vždycky **sjednocením všech** výsledků ze všech horizontálních oddílů.

   V další části vytvoříme ukázkový koncový bod databáze, který podporuje bohatší dotaz na data napříč horizontálních oddílů.

## <a name="create-an-elastic-query-database"></a>Vytvoření elastické databáze dotazů

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se.
2. Vytvořte novou databázi v Azure SQL Database na stejném serveru jako instalační program horizontálních oddílů. Pojmenujte databázi "ElasticDBQuery".

    ![Azure Portal a cenová úroveň][3]

    > [!NOTE]
    > můžete použít existující databázi. Pokud to uděláte, nesmí to být jedna z horizontálních oddílů, na kterou byste chtěli spustit dotazy. Tato databáze se použije k vytvoření objektů metadat pro dotaz elastické databáze.
    >

## <a name="create-database-objects"></a>Vytváření databázových objektů
### <a name="database-scoped-master-key-and-credentials"></a>Hlavní klíč a přihlašovací údaje v rámci databáze
Používají se k připojení ke Správci map horizontálních oddílů a k horizontálních oddílů:

1. Otevřete SQL Server Management Studio nebo SQL Server Data Tools v aplikaci Visual Studio.
2. Připojte se k databázi ElasticDBQuery a spusťte následující příkazy T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    uživatelské jméno a heslo by se mělo shodovat s přihlašovacími údaji použitými v kroku 3 oddílu [Stažení a spuštění ukázkové aplikace](elastic-scale-get-started.md#download-and-run-the-sample-app) v článku **Začínáme s elastic Database Tools** .

### <a name="external-data-sources"></a>Externí zdroje dat
Pokud chcete vytvořit externí zdroj dat, spusťte v databázi ElasticDBQuery tento příkaz:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" je název mapy horizontálních oddílů, pokud jste vytvořili mapu horizontálních oddílů a správce mapování horizontálních oddílů pomocí ukázky nástrojů elastické databáze. Pokud jste však pro tuto ukázku použili vlastní instalaci, měl by se jednat o název mapy horizontálních oddílů, který jste zvolili v aplikaci.

### <a name="external-tables"></a>Externí tabulky
Vytvořte externí tabulku, která odpovídá tabulce Customers na horizontálních oddílů spuštěním následujícího příkazu v databázi ElasticDBQuery:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Spuštění ukázkového dotazu T-SQL pro elastickou databázi
Po definování externího zdroje dat a externích tabulek teď můžete použít plný T-SQL přes vaše externí tabulky.

Spustit tento dotaz v databázi ElasticDBQuery:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Všimněte si, že dotaz agreguje výsledky ze všech horizontálních oddílů a poskytne následující výstup:

![Podrobnosti výstupu][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Import výsledků dotazu elastické databáze do aplikace Excel
 Výsledky dotazu můžete importovat do excelového souboru.

1. Spusťte Excel 2013.
2. Přejděte na pás karet **data** .
3. Klikněte na **z jiných zdrojů** a klikněte na **z SQL Server**.

   ![Import z jiných zdrojů z Excelu][5]
4. V **Průvodci datovým připojením** zadejte název serveru a přihlašovací údaje. Potom klikněte na **Další**.
5. V dialogovém okně **Vyberte databázi** obsahující požadovaná data a vyberte databázi **ElasticDBQuery** .
6. V zobrazení seznamu vyberte tabulku **zákazníci** a klikněte na **Další**. Klikněte na **Dokončit**.
7. Ve formuláři **importovat data** v části **Vyberte, jakým způsobem chcete zobrazit tato data v sešitu** vyberte **tabulka** a klikněte na tlačítko **OK**.

Všechny řádky z tabulky **Customers** uložené v různých horizontálních oddílů naplní excelový list.

Nyní můžete používat výkonné funkce pro vizualizaci dat v Excelu. Připojovací řetězec s názvem vašeho serveru, názvem databáze a přihlašovacími údaji můžete použít k propojení nástrojů pro integraci BI a dat s databází elastického dotazu. Ujistěte se, že je pro nástroj SQL Server podporována jako zdroj dat. Na databázi elastických dotazů a externích tabulkách můžete odkazovat stejně jako na jakékoli jiné SQL Server databáze a SQL Server tabulky, ke kterým se připojíte pomocí nástroje.

### <a name="cost"></a>Náklady
Pro použití funkce dotazu Elastic Database se neúčtují žádné další poplatky.

Informace o cenách najdete v článku informace o [cenách SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Další kroky

* Přehled elastického dotazu najdete v tématu [Přehled elastického dotazu](elastic-query-overview.md).
* Kurz pro vertikální dělení najdete v tématu [Začínáme s mezidatabázovým dotazem (vertikální dělení)](elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro vertikálně dělená data najdete v tématu [dotazování na vertikálně dělená data](elastic-query-vertical-partitioning.md) .
* Syntaxe a ukázkové dotazy pro horizontálně rozdělená data najdete v tématu [dotazování na horizontálně dělená data](elastic-query-horizontal-partitioning.md) .
* V tématu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) pro uloženou proceduru, která provádí příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadě databází, která slouží jako horizontálních oddílů ve vodorovném schématu dělení.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->