---
title: Použít projekt databáze U-SQL pro vývoj databáze U-SQL pro Azure Data Lake | Dokumentace Microsoftu
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
ms.openlocfilehash: a8099a8c9bcec8ed5a7bb480dae58d2c077b8fe0
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889512"
---
# <a name="use-u-sql-database-project-to-develop-u-sql-database-for-azure-data-lake"></a>Pomocí projekt U-SQL database můžete vyvíjet databáze U-SQL pro Azure Data Lake

U-SQL database poskytuje strukturovaných zobrazení Nestrukturovaná data, spravovat strukturovaná data v tabulkách a poskytuje systém katalogu obecné metadat pro uspořádání vašich strukturovaných dat a vlastní kód. Databáze je koncept, která seskupuje tyto související objekty.

Další informace o [databáze U-SQL a jazyka DDL (Data Definition)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql). 

Projekt databáze U-SQL je typu projektu v sadě Visual Studio, který pomáhá vývojářům vyvíjet, spravovat a nasazovat své databáze U-SQL, rychlé a snadné.

## <a name="create-a-u-sql-database-project"></a>Vytvořte projekt databáze U-SQL

Azure Data Lake Tools pro Visual Studio přidá nová šablona projektu, volá se po verzi 2.3.3000.0 databázový projekt U-SQL. Chcete-li vytvořit projekt v U-SQL, projděte si **soubor > Nový > projekt**, databázový projekt U-SQL najdete v části **Azure Data Lake > U-SQL uzel**.

![Data Lake Tools pro Visual Studio vytvořte projekt v u-sql database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-using-database-project"></a>Vývoj objekty databáze U-SQL pomocí databázového projektu

Klikněte pravým tlačítkem na projekt databáze U-SQL, klikněte na tlačítko **Přidat > Nová položka**, všechny podporované typy objektů najdete v průvodci Přidat novou položku. 

1.  Pro objekt bez sestavení například funkce vracející tabulku nový skript U-SQL je vytvořen po přidání nové položky. Můžete začít vyvíjet příkaz DDL pro daný objekt v editoru.
2.  Nástroj pro sestavení objektu, poskytuje uživatelsky přívětivé Editoru uživatelského rozhraní, která vám pomůže zaregistrovat sestavení a nasazení .dll a další soubory. Postupujte podle následujících kroků pro přidání objektu definici sestavení na databázový projekt U-SQL:

1.  Přidání odkazů projektu C#, která zahrnuje UDO/UDAG/UDF na databázový projekt U-SQL.

    ![Data Lake Tools pro Visual Studio přidejte odkaz na projekt u-sql database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pro Visual Studio přidejte odkaz na projekt u-sql database](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  V návrhovém zobrazení sestavení, zvolte odkazované sestavení z **vytvořit sestavení z reference** rozevíracího seznamu.

    ![Data Lake Tools pro Visual Studio vytvořit sestavení z reference](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Přidat **spravované závislosti** a **další soubory** případné. Při přidávání dalších souborů, nástroj bude používat relativní cesty zajistit, aby že ho později vyhledat sestavení na svém místním počítači a počítač sestavení. @_DeployTempDirectory je předem definovaná proměnná, která se odkazuje nástroj k výstupní složce sestavení. V části výstupu sestavení všechna sestavení měla v podsložce s názvem, protože název sestavení, všechny knihovny DLL a další soubory můžete najít existuje v podsložce. 
 
## <a name="build-u-sql-database-project"></a>Sestavit projekt U-SQL database

Výstup pro databázový projekt U-SQL je nasazení balíček U-SQL database, s příponou s názvem sestavení `.usqldbpack`. `.usqldbpack` Balíček je zazipovaný soubor obsahuje všechny příkazy DDL v jeden skript U-SQL v **DDL** složky a všechny knihovny DLL a dalších souborů sestavení v **Temp** složky.

Další informace o [jak vytvořit databázový projekt U-SQL pomocí příkazového řádku MSBuild a Visual Studio Team Service úloha sestavení](data-lake-analytics-cicd-overview.md#build-u-sql-database-project).

## <a name="deploy-u-sql-database"></a>Nasazení databáze U-SQL

Je možné nasadit balíček .usqldbpack místní účet nebo účet Azure Data Lake Analytics pomocí sady Visual Studio nebo nasazení sady SDK. 

### <a name="deploy-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Můžete nasadit databázi U-SQL pomocí databázový projekt U-SQL nebo .usqldbpack balíčku v sadě Visual Studio.

#### <a name="deploy-through-u-sql-database-project"></a>Nasazení přes databázový projekt U-SQL

1.  Klikněte pravým tlačítkem na projekt U-SQL database a zvolte **nasadit**.
2.  Průvodce nasazením databáze v U-SQL, vyberte **i účet ADLA** byste chtěli nasadit databázi. Podporují se (místního) účtu i účet ADLA.
3.  **Databáze zdrojového** se vyplní automaticky přejdete na balíček .usqldbpack ve výstupní složka sestavení projektu
4.  Zadejte **název_databáze** pro vytvoření databáze. Pokud existuje databáze se stejnou existující v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez nutnosti opětovného vytvoření databáze.
5.  Klikněte na tlačítko **odeslat** nasazení databáze U-SQL. Všechny prostředky (sestavení a další soubory) se nahrají a úlohu U-SQL obsahuje všechny příkazy DDL se odešlou.

    ![Data Lake Tools pro Visual Studio nasadit projekt databáze u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![U-sql databáze projektu Průvodce nasazením nástroje data Lake pro Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-u-sql-database-deployment-package"></a>Nasadit prostřednictvím balíčku pro nasazení databáze U-SQL

1.  Otevřít **Průzkumníka serveru**, rozbalte **účtu Azure Data Lake Analytics** byste chtěli nasadit databázi.
2.  Klikněte pravým tlačítkem na databáze U-SQL a zvolte **nasadit databázi**.
3.  Nastavte **databázového zdroje** pro balíček (soubor .usqldbpack) nasazení databáze U-SQL.
4.  Zadejte **název_databáze** pro vytvoření databáze. Pokud existuje databáze se stejnou existující v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez nutnosti opětovného vytvoření databáze.

    ![Data Lake Tools pro Visual Studio nasazení balíčku databáze u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools pro Visual Studio nasadit Průvodce vytvořením balíčku databáze u-sql](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-using-sdk"></a>Nasazení databáze U-SQL pomocí sady SDK

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají k nasazení databáze U-SQL. Je součástí sady SDK [balíček Nuget sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), vyhledáním v `build/runtime/PackageDeploymentTool.exe`.

[Další informace o sadě SDK a jak vytvořit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service).

## <a name="reference-a-u-sql-database-project"></a>Odkazovat na databázový projekt U-SQL

Projekt v U-SQL můžete odkazovat na databázový projekt U-SQL. Odkaz má vliv na dvě úlohy:

- Sestavení projektu: odkazovaná databáze prostředí jsou nastaveny před sestavením skriptů U-SQL. 
- Místní spuštění proti účet (Local-project): prostředí odkazovaná databáze jsou nasazené na účet (Local-project) před spuštěním skriptu U-SQL. [Další informace o místní spuštění a rozdíl mezi (místní počítač) a účet (Local projektu) i tady](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-u-sql-database-reference"></a>Jak přidat odkaz na databázi U-SQL

1. Klikněte pravým tlačítkem na projekt U-SQL v **Průzkumníka řešení**a zvolte **přidat odkaz na databázi U-SQL...** .

    ![Data Lake Tools pro Visual Studio přidejte odkaz na projekt databáze](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Nakonfigurujte databázový odkaz z projektu U-SQL database v aktuálním řešení nebo soubor balíčku databáze U-SQL.
3. Zadejte název databáze.

    ![Data Lake Tools pro Visual Studio přidejte databáze projektu odkaz na Průvodce](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
