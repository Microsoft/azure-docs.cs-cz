---
title: Vývoj databázového projektu U-SQL – Azure Data Lake
description: Zjistěte, jak vytvořit databázi U-SQL pomocí nástrojů Azure Data Lake Tools pro Visual Studio.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309929"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Použití databázového projektu U-SQL k vývoji databáze U-SQL pro Azure Data Lake

U-SQL databáze poskytuje strukturovaná zobrazení přes nestrukturovaná data a spravovaná strukturovaná data v tabulkách. Poskytuje také obecný systém katalogu metadat pro uspořádání strukturovaných dat a vlastního kódu. Databáze je koncept, který seskupuje tyto související objekty dohromady.

Další informace o [databázi U-SQL a jazyce definice dat (DDL)](/u-sql/data-definition-language-ddl-statements). 

Projekt databáze U-SQL je typ projektu v sadě Visual Studio, který vývojářům pomáhá rychle a snadno vyvíjet, spravovat a nasazovat své databáze U-SQL.

## <a name="create-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Nástroje datového jezera Azure pro Visual Studio přidaly novou šablonu projektu s názvem Databázový projekt U-SQL po verzi 2.3.3000.0. Chcete-li vytvořit projekt U-SQL, vyberte **možnost Soubor > Nový > Project**. U-SQL databázový projekt najdete v azure **datového jezera > uzla U-SQL**.

![Nástroje datového jezera pro Visual Studio – vytvoření databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Vývoj databázových objektů U-SQL pomocí databázového projektu

Klikněte pravým tlačítkem myši na databázový projekt U-SQL. Vyberte **Přidat > novou položku**. Všechny nové podporované typy objektů najdete v Průvodci **přidáním nové položky.** 

Pro objekt bez sestavení (například funkce s hodnotou tabulky) je po přidání nové položky vytvořen nový skript U-SQL. Můžete začít vyvíjet ddl příkaz pro tento objekt v editoru.

Pro objekt sestavení nástroj poskytuje uživatelsky přívětivý editor uživatelského rozhraní, který vám pomůže zaregistrovat sestavení a nasadit soubory DLL a další další soubory. Následující kroky ukazují, jak přidat definici objektu sestavení do projektu databáze U-SQL:

1.  Přidejte odkazy na projekt Jazyka C#, které obsahují UDO/UDAG/UDF pro databázový projekt U-SQL.

    ![Nástroje datového jezera pro Visual Studio – odkaz na projekt databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Nástroje datového jezera pro Visual Studio – odkaz na projekt databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  V návrhovém zobrazení sestavy zvolte odkazovanou sestavu z **rozevírací nabídky Vytvořit sestavu.**

    ![Nástroje datového jezera pro Visual Studio – vytvořit sestavení z odkazu](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Přidejte **spravované závislosti** a **další soubory,** pokud existují. Když přidáte další soubory, nástroj použije relativní cestu, aby se ujistil, že může najít sestavení jak v místním počítači, tak v počítači sestavení později. 

@_DeployTempDirectoryje předdefinovaná proměnná, která odkazuje nástroj na výstupní složku sestavení. Ve výstupní složce sestavení má každé sestavení podsložku s názvem název sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce. 
 
## <a name="build-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Výstup sestavení pro databázový projekt U-SQL je balíček nasazení databáze U-SQL s názvem s příponou `.usqldbpack`. Balíček `.usqldbpack` je soubor ZIP, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce **DDL** a všechny knihovny DLL a další soubory pro sestavení ve složce **Temp.**

Další informace o [tom, jak vytvořit databázový projekt U-SQL pomocí příkazového řádku MSBuild a úlohy sestavení služby Azure DevOps](data-lake-analytics-cicd-overview.md)Services .

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

Balíček .usqldbpack lze nasadit na místní účet nebo účet Azure Data Lake Analytics pomocí Sady Visual Studio nebo sady SDK nasazení. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Databázi U-SQL můžete nasadit prostřednictvím databázového projektu U-SQL nebo balíčku .usqldbpack v sadě Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Nasazení prostřednictvím databázového projektu U-SQL

1.  Klepněte pravým tlačítkem myši na databázový projekt U-SQL a vyberte příkaz **Nasadit**.
2.  V **Průvodci nasazením databáze U-SQL**vyberte **účet ADLA,** do kterého chcete databázi nasadit. Místní účty i účty ADLA jsou podporovány.
3.  **Zdroj databáze** je vyplněn automaticky a odkazuje na balíček .usqldbpack ve výstupní složce sestavení projektu.
4.  Chcete-li vytvořit databázi, zadejte název do pole **Název databáze.** Pokud databáze se stejným názvem již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázovém projektu, jsou vytvořeny bez opětovného vytvoření databáze.
5.  Chcete-li nasadit databázi U-SQL, vyberte **odeslat**. Všechny prostředky (sestavení a další soubory) jsou odeslány a u-SQL úloha, která obsahuje všechny příkazy DDL je odeslána.

    ![Nástroje datového jezera pro Visual Studio – nasazení databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Nástroje datového jezera pro Visual Studio – průvodce nasazením databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Nasazení prostřednictvím balíčku nasazení databáze U-SQL

1.  Sem **Průzkumník serveru**. Pak rozbalte **účet Azure Data Lake Analytics,** do kterého chcete databázi nasadit.
2.  Klepněte pravým tlačítkem myši na **položku U-SQL Databases**a pak zvolte **Nasadit databázi**.
3.  Nastavte **zdroj databáze** na cestu pro nasazení databáze U-SQL (.usqldbpack file).
4.  Chcete-li vytvořit databázi, zadejte **název databáze.** Pokud existuje databáze se stejným názvem, který již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázovém projektu jsou vytvořeny bez opětovného vytvoření databáze.

    ![Nástroje datového jezera pro Visual Studio – balíček databáze Nasazení U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Nástroje datového jezera pro Visual Studio – průvodce nasazením balíčku databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Nasazení databáze U-SQL pomocí sady SDK

`PackageDeploymentTool.exe`poskytuje rozhraní pro programování a příkazový řádek, která pomáhají nasadit databáze U-SQL. Sada SDK je součástí [balíčku U-SQL SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který se nachází na adrese `build/runtime/PackageDeploymentTool.exe`.

[Další informace o sadě SDK a o tom, jak nastavit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Odkaz na databázový projekt U-SQL

Projekt U-SQL může odkazovat na databázový projekt U-SQL. Odkaz ovlivňuje dvě úlohy:

- *Sestavení projektu*: Před sestavením skriptů U-SQL nastavte referenční databázová prostředí. 
- *Místní spuštění proti účtu (místní projekt):* Odkazovaná databázová prostředí jsou nasazena na účet (místní projekt) před spuštěním skriptu U-SQL. [Další informace o místních spuštěních a rozdílu mezi účtem (místní počítač) a (místní projekt) naleznete zde](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Jak přidat odkaz na databázi U-SQL

1. Klepněte pravým tlačítkem myši na projekt U-SQL v **Průzkumníku řešení**a pak zvolte **Přidat odkaz na databázi U-SQL...**.

    ![Nástroje datového jezera pro Visual Studio – přidání odkazu na databázový projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Nakonfigurujte odkaz na databázi z databázového projektu U-SQL v aktuálním řešení nebo v souboru balíčku databáze U-SQL.
3. Zadejte název databáze.

    ![Nástroje datového jezera pro visual studio přidat průvodce odkazem na projekt databáze](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Jak otestovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spuštění skriptu U-SQL v místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
