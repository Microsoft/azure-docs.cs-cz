---
title: Pomocí projekt U-SQL database můžete vyvíjet databáze U-SQL pro Azure Data Lake | Dokumentace Microsoftu
description: Další informace jak vyvíjet databáze U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 1fee28ad185629b0cb26abb54cc2e196fca791e5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364045"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Pomocí projekt U-SQL database můžete vyvíjet databáze U-SQL pro Azure Data Lake

U-SQL database nabízí v porovnání s Nestrukturovaná data a spravované strukturovaných dat v tabulkách strukturovaných zobrazení. Také poskytuje systém katalogu obecné metadat pro uspořádání vašich strukturovaných dat a vlastní kód. Databáze je koncept, která seskupuje tyto související objekty.

Další informace o [databáze U-SQL a jazyka DDL (Data Definition)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Databázový projekt U-SQL je typu projektu v sadě Visual Studio, který pomáhá vývojářům vyvíjet, spravovat a nasazovat své databáze U-SQL, rychlé a snadné.

## <a name="create-a-u-sql-database-project"></a>Vytvořte projekt databáze U-SQL

Azure Data Lake Tools pro Visual Studio přidá nová šablona projektu, volá se po verzi 2.3.3000.0 databázový projekt U-SQL. Chcete-li vytvořit projekt v U-SQL, vyberte **soubor > Nový > projekt**. Databázový projekt U-SQL najdete v části **Azure Data Lake > U-SQL uzel**.

![Data Lake Tools pro Visual Studio – vytvořte projekt databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Vývoj objekty databáze U-SQL pomocí databázového projektu

Klikněte pravým tlačítkem na projekt U-SQL database. Vyberte **Přidat > Nová položka**. Můžete najít všechny nové typy podporovaných objektů v **přidat novou položku** průvodce. 

Pro objekt bez sestavení (třeba funkce vracející tabulku) se vytvoří nový skript U-SQL, po přidání nové položky. Můžete začít vyvíjet příkaz DDL pro daný objekt v editoru.

Nástroj pro sestavení objektu, poskytuje uživatelsky přívětivé Editoru uživatelského rozhraní, která vám pomůže zaregistrovat sestavení a nasazení souborů knihovny DLL a další další soubory. Následující kroky ukazují, jak přidat objektu definici sestavení na databázový projekt U-SQL:

1.  Přidání odkazů do projektu C#, které zahrnují UDO/UDAG/UDF pro databázový projekt U-SQL.

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  V návrhovém zobrazení sestavení, zvolte odkazované sestavení z **vytvořit sestavení z reference** rozevírací nabídky.

    ![Data Lake Tools pro Visual Studio – vytvořit sestavení z reference](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Přidat **spravované závislosti** a **další soubory** Pokud tam nějaké jsou. Když přidáte další soubory, nástroj používá relativní cestu k Ujistěte se, že ho můžete najít sestavení na svém místním počítači a v počítači sestavení později. 

@_DeployTempDirectory je předdefinovaná proměnná, která se odkazuje nástroj k výstupní složce sestavení. Ve výstupní složce sestavení každé sestavení má podsložku s názvem název sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce. 
 
## <a name="build-a-u-sql-database-project"></a>Sestavit projekt U-SQL database

Výstup pro databázový projekt U-SQL je U-SQL database balíček pro nasazení s názvem s příponou sestavení `.usqldbpack`. `.usqldbpack` Balíček je soubor .zip, který zahrnuje všechny příkazy jazyka DDL v jeden skript U-SQL v **DDL** složky a všechny knihovny DLL a dalších souborů sestavení v **Temp** složky.

Další informace o [způsob, jakým se sestavit projekt U-SQL database s MSBuild příkazového řádku a Visual Studio Team Services vytvářet úlohy](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

Balíček .usqldbpack je nasadit na místní účet nebo účet Azure Data Lake Analytics pomocí sady Visual Studio nebo nasazení sady SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Můžete nasadit databázi U-SQL pomocí databázový projekt U-SQL nebo .usqldbpack balíčku v sadě Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Nasazení přes databázový projekt U-SQL

1.  Klikněte pravým tlačítkem na projekt U-SQL databáze a pak vyberte **nasadit**.
2.  V **Průvodce nasazením databáze U-SQL**, vyberte **i účet ADLA** do které chcete nasadit databázi. Místní účty a účty ADLA jsou podporovány.
3.  **Databáze zdrojového** se vyplní automaticky, a výstupní složce sestavení body do balíčku .usqldbpack v projektu.
4.  Zadejte název do **název_databáze** k vytvoření databáze. Pokud databáze s tímto stejným názvem již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez nutnosti opětovného vytvoření databáze.
5.  Pokud chcete nasadit databázi U-SQL, vyberte **odeslat**. Všechny prostředky (sestavení a další soubory) se nahrají a odeslání úlohy U-SQL, který zahrnuje všechny příkazy jazyka DDL.

    ![Data Lake Tools pro Visual Studio - databázový projekt nasazení U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools pro Visual Studio – Průvodce projektem nasazení U-SQL database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Nasadit prostřednictvím balíčku pro nasazení databáze U-SQL

1.  Otevřít **Průzkumníka serveru**. Pak rozbalte **účtu Azure Data Lake Analytics** do které chcete nasadit databázi.
2.  Klikněte pravým tlačítkem myši **databáze U-SQL**a klikněte na tlačítko **nasadit databázi**.
3.  Nastavte **databázového zdroje** pro balíček (soubor .usqldbpack) nasazení databáze U-SQL.
4.  Zadejte **název_databáze** k vytvoření databáze. Pokud existuje databáze se stejným názvem, který již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez nutnosti opětovného vytvoření databáze.

    ![Data Lake Tools pro Visual Studio – balíček nasazení U-SQL database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools pro Visual Studio – průvodce balíčku nasazení U-SQL database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Nasazení databáze U-SQL pomocí sady SDK

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají k nasazení databáze U-SQL. Je součástí sady SDK [balíček Nuget sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který je umístěn na `build/runtime/PackageDeploymentTool.exe`.

[Další informace o sadě SDK a jak vytvořit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Odkazovat na databázový projekt U-SQL

Projekt v U-SQL můžete odkazovat na databázový projekt U-SQL. Odkaz má vliv na dvě úlohy:

- *Sestavení projektu*: nastavení prostředí odkazovaná databáze před sestavením skriptů U-SQL. 
- *Místní spuštění proti (local projektu) účet*: prostředí odkazovaná databáze jsou nasazené do (místní projektu) účtu před spuštěním skriptu U-SQL. [Další informace o místní spuštění a rozdíl mezi (místní počítače) (místní projekt) a účet tady](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Jak přidat odkaz na databázi U-SQL

1. Klikněte pravým tlačítkem na projekt U-SQL v **Průzkumníka řešení**a klikněte na tlačítko **přidat odkaz na databázi U-SQL...** .

    ![Data Lake Tools pro Visual Studio – přidat odkaz na projekt databáze](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurovat odkaz na databázi z databázový projekt U-SQL v aktuálním řešení nebo soubor balíčku databáze U-SQL.
3. Zadejte název databáze.

    ![Data Lake Tools pro Visual Studio přidejte databáze projektu odkaz na Průvodce](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Další postup

- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
