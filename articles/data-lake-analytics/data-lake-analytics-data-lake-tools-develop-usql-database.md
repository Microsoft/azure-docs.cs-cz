---
title: Vývoj projektu U-SQL Database – Azure Data Lake
description: Naučte se vyvíjet databázi U-SQL pomocí Nástroje Azure Data Lake pro Visual Studio.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: ee35385b88bf4fbd5f899fde032b11b99a20d050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87130031"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Použití databázového projektu U-SQL pro vývoj databáze U-SQL pro Azure Data Lake

Databáze U-SQL poskytuje strukturovaná zobrazení nad nestrukturovanými daty a spravovanými strukturovanými daty v tabulkách. Poskytuje také obecný systém katalogu metadat pro uspořádání strukturovaných dat a vlastního kódu. Databáze je koncept, který seskupí tyto související objekty dohromady.

Přečtěte si další informace o [databázi U-SQL a jazyku DDL (Data Definition Language)](/u-sql/data-definition-language-ddl-statements). 

Projekt databáze U-SQL je typ projektu v aplikaci Visual Studio, který pomáhá vývojářům rychle a snadno vyvíjet, spravovat a nasazovat databáze U-SQL.

## <a name="create-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Nástroje Azure Data Lake pro Visual Studio přidat novou šablonu projektu s názvem projekt databáze U-SQL po verzi 2.3.3000.0. Chcete-li vytvořit projekt U-SQL, vyberte **soubor > nový > projekt**. Projekt U-SQL Database lze nalézt v části **Azure Data Lake > uzlu u-SQL**.

![Data Lake Tools for Visual Studio – vytvoření databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Vývoj objektů databáze U-SQL pomocí databázového projektu

Klikněte pravým tlačítkem na projekt databáze U-SQL. Vyberte **přidat > novou položku**. Všechny nové podporované typy objektů můžete najít v průvodci **přidáním nové položky** . 

Pro objekt, který není typu Assembly (například funkce vracející tabulku), je vytvořen nový skript U-SQL po přidání nové položky. Můžete začít vyvíjet příkaz DDL pro daný objekt v editoru.

V případě objektu sestavení poskytuje nástroj uživatelsky přívětivý Editor uživatelského rozhraní, který vám pomůže zaregistrovat sestavení a nasadit soubory DLL a další další soubory. Následující kroky ukazují, jak přidat definici objektu sestavení do projektu databáze U-SQL:

1.  Přidejte odkazy na projekt C#, který zahrnuje UDO/UDAG/UDF pro projekt databáze U-SQL.

    ![Data Lake Tools for Visual Studio – přidat odkaz na projekt databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio – přidat odkaz na projekt databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  V zobrazení návrhu sestavení vyberte odkazovaná sestavení z rozevírací nabídky **vytvořit sestavení z odkazu** .

    ![Data Lake Tools for Visual Studio – vytvoření sestavení z reference](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Přidejte **spravované závislosti** a **Další soubory** , pokud existují. Když přidáte další soubory, nástroj použije relativní cestu k tomu, aby bylo zajištěno, že bude moci najít sestavení v místním počítači i v počítači sestavení později. 

@_DeployTempDirectory je předdefinovaná proměnná, která ukazuje nástroj na výstupní složku sestavení. V rámci výstupní složky sestavení má každé sestavení podsložku s názvem sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce. 
 
## <a name="build-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Výstup sestavení pro projekt databáze U-SQL je balíček pro nasazení U-SQL Database s názvem s příponou `.usqldbpack` . `.usqldbpack`Balíček je soubor. zip, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce **DDL** a všechny knihovny DLL a další soubory pro sestavení ve složce **TEMP** .

Přečtěte si další informace o [tom, jak vytvořit projekt databáze U-SQL pomocí příkazového řádku MSBuild a úlohy Azure DevOps Services sestavení](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

Balíček. usqldbpack se dá nasadit na místní účet nebo účet Azure Data Lake Analytics pomocí sady Visual Studio nebo sady Deployment SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v aplikaci Visual Studio

Databázi U-SQL můžete nasadit prostřednictvím databázového projektu U-SQL nebo balíčku. usqldbpack v aplikaci Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Nasazení prostřednictvím databázového projektu U-SQL

1.  Klikněte pravým tlačítkem na projekt databáze U-SQL a pak vyberte **nasadit**.
2.  V **Průvodci nasazením U-SQL Database**vyberte **účet ADLA** , do kterého chcete nasadit databázi. Podporují se místní účty i účty ADLA.
3.  **Zdroj databáze** je vyplněn automaticky a odkazuje na balíček. usqldbpack ve výstupní složce sestavení projektu.
4.  Chcete-li vytvořit databázi, zadejte název v **názvu databáze** . Pokud databáze s tímto názvem již v cílovém Azure Data Lake Analytics účtu existuje, jsou všechny objekty, které jsou definovány v projektu databáze, vytvořeny bez opětovného vytvoření databáze.
5.  Chcete-li nasadit databázi U-SQL, vyberte **Odeslat**. Všechny prostředky (sestavení a další soubory) se nahrají a úloha U-SQL, která obsahuje všechny příkazy DDL, se odešle.

    ![Data Lake Tools for Visual Studio – nasazení databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio – Průvodce nasazením databázového projektu U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Nasazení prostřednictvím balíčku pro nasazení U-SQL Database

1.  Otevřete **Průzkumník serveru**. Pak rozbalte **účet Azure Data Lake Analytics** , do kterého chcete nasadit databázi.
2.  Klikněte pravým tlačítkem na **databáze U-SQL**a pak zvolte **nasadit databázi**.
3.  Nastavte **zdroj databáze** na cestu k balíčku nasazení U-SQL Database (soubor. usqldbpack).
4.  Chcete-li vytvořit databázi, zadejte **název databáze** . Pokud existuje databáze se stejným názvem, která již existuje v cílovém Azure Data Lake Analyticsm účtu, všechny objekty, které jsou definovány v projektu databáze, jsou vytvořeny bez opětovného vytvoření databáze.

    ![Data Lake Tools for Visual Studio – nasazení balíčku databáze U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Data Lake Tools for Visual Studio – Průvodce nasazením databázového balíčku U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Nasazení databáze U-SQL pomocí sady SDK

`PackageDeploymentTool.exe` poskytuje rozhraní pro programování a příkazový řádek, která usnadňují nasazení databází U-SQL. Sada SDK je součástí [balíčku NuGet U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který se nachází na adrese `build/runtime/PackageDeploymentTool.exe` .

[Přečtěte si další informace o sadě SDK a nastavení kanálu CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Odkazování na projekt databáze U-SQL

Projekt U-SQL může odkazovat na projekt databáze U-SQL. Odkaz má vliv na dvě úlohy:

- *Sestavení projektu*: před vytvořením skriptů U-SQL nastavte odkazovaná databázová prostředí. 
- *Místní spuštění proti (místní projekt)*: odkazované databázové prostředí se před provedením skriptu U-SQL nasadí na účet (místní projekt). [Přečtěte si další informace o místních spuštěních a o rozdílech mezi účty (místní počítač) a (místní projekt)](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Postup přidání referenčních informací o databázi U-SQL

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt U-SQL a pak zvolte **Přidat odkaz u-SQL Database..**.

    ![Data Lake Tools for Visual Studio – přidat odkaz na projekt databáze](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Nakonfigurujte odkaz na databázi z databázového projektu U-SQL v aktuálním řešení nebo v souboru balíčku pro databázi U-SQL.
3. Zadejte název databáze.

    ![Průvodce odkazem na databázový projekt pro Data Lake nástroje pro Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Postup testování kódu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spuštění skriptu U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
