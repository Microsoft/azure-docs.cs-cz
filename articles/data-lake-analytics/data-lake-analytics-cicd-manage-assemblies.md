---
title: Správa sestavení U-SQL v kanálu CI/CD – Azure Data Lake
description: Seznamte se s osvědčenými postupy pro správu sestavení U-SQL C# v kanálu CI/CD pomocí Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315850"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Doporučené postupy pro správu sestavení U-SQL v kanálu CI/CD

V tomto článku se dozvíte, jak spravovat zdrojový kód sestavení U-SQL s nově zavedeným projektem databáze U-SQL. Také se dozvíte, jak nastavit kanál průběžné integrace a nasazení (CI/CD) pro registraci sestavení pomocí Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Správa zdrojového kódu sestavení pomocí databázového projektu U-SQL

[Projekt databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) je typ projektu v sadě Visual Studio, který vývojářům pomáhá rychle a snadno vyvíjet, spravovat a nasazovat své databáze U-SQL. Můžete spravovat všechny databázové objekty U-SQL (s výjimkou pověření) s u-SQL databázového projektu. 

Chcete-li spravovat zdrojový kód sestavení jazyka C# a registraci sestavení DDL U-SQL skripty, použijte:

* U-SQL databázový projekt pro správu registrace sestavení U-SQL skripty.
* Knihovna tříd (pro aplikaci U-SQL) pro správu zdrojového kódu jazyka C# a závislostí pro uživatelem definované operátory, funkce a agregátory (UDO, UDf a UDAGs).
* U-SQL databázový projekt odkazovat na projekt knihovny tříd. 

Databázový projekt U-SQL může odkazovat na projekt knihovny tříd (pro aplikaci U-SQL). Můžete vytvořit sestavení registrovaná v databázi U-SQL pomocí odkazovaný zdrojový kód Jazyka C# z tohoto projektu knihovny tříd (pro aplikaci U-SQL).

Chcete-li vytvořit projekty a přidat odkazy, postupujte takto.
1. Vytvořte projekt knihovny tříd (pro aplikaci U-SQL) výběrem **souboru** > **nový** > **projekt**. Projekt je pod **Azure Data Lake > U-SQL** uzlu.

   ![Nástroje datového jezera pro Visual Studio– vytvoření projektu knihovny tříd jazyka C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Přidejte uživatelem definovaný kód Jazyka C v projektu Knihovna tříd (pro aplikaci U-SQL).

1. Vytvořte projekt U-SQL výběrem **možnosti Nový** > **New** > **projekt souboru**. Projekt je pod uzu**U-SQL** **Azure Data Lake.** > 

   ![Nástroje datového jezera pro Visual Studio – vytvoření databázového projektu U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Přidejte odkaz na projekt knihovny tříd jazyka C# pro databázový projekt U-SQL.

    ![Nástroje datového jezera pro Visual Studio – odkaz na projekt databáze U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Nástroje datového jezera pro Visual Studio – odkaz na projekt databáze U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Vytvořte skript sestavení v databázovém projektu U-SQL klepnutím pravým tlačítkem myši na projekt a výběrem **možnosti Přidat novou položku**.

   ![Nástroje datového jezera pro Visual Studio – přidat skript sestavení](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otevřete skript sestavy v návrhovém zobrazení sestavy. Vyberte odkazované sestavení z rozevírací nabídky **Vytvořit.**

    ![Nástroje datového jezera pro Visual Studio – vytvořit sestavení z odkazu](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Přidejte **spravované závislosti** a **další soubory**, pokud existují. Když přidáte další soubory, nástroj použije relativní cestu k ujistěte se, že může najít sestavení v místním počítači a na sestavení počítače později.

_DeployTempDirectory v okně editoru v dolní části je předdefinovaná proměnná, která odkazuje nástroj na výstupní složku sestavení. ** \@** Ve výstupní složce sestavení má každé sestavení podsložku s názvem název sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce.

## <a name="build-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Výstup sestavení pro databázový projekt U-SQL je balíček nasazení databáze U-SQL. Je pojmenována s příponou `.usqldbpack`. Balíček `.usqldbpack` je soubor ZIP, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce DDL. Všechny vytvořené soubory DLL a další soubory pro sestavení jsou ve složce Temp.

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

Balíček `.usqldbpack` lze nasadit buď do místního účtu, nebo do účtu Azure Data Lake Analytics. Použijte Visual Studio nebo sada SDK nasazení. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Databázi U-SQL můžete nasadit pomocí databázového projektu `.usqldbpack` U-SQL nebo balíčku v sadě Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Nasazení pomocí databázového projektu U-SQL

1.  Klepněte pravým tlačítkem myši na databázový projekt U-SQL a vyberte příkaz **Nasadit**.
2.  V průvodci **Nasazením databáze U-SQL** vyberte **účet ADLA,** do kterého chcete databázi nasadit. Místní účty i účty ADLA jsou podporovány.
3.  **Zdroj databáze** je vyplněn automaticky. Odkazuje na balíček .usqldbpack ve výstupní složce sestavení projektu.
4.  Chcete-li vytvořit databázi, zadejte název do pole **Název databáze.** Pokud databáze se stejným názvem již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázovém projektu, jsou vytvořeny bez opětovného vytvoření databáze.
5.  Chcete-li nasadit databázi U-SQL, vyberte **odeslat**. Všechny prostředky, jako jsou sestavení a další soubory, jsou odeslány. Je odeslána úloha U-SQL, která obsahuje všechny příkazy DDL.

    ![Nástroje datového jezera pro Visual Studio – nasazení databázového projektu U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Nástroje datového jezera pro Visual Studio – průvodce nasazením databázového projektu U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Nasazení databáze U-SQL v Azure DevOps

`PackageDeploymentTool.exe`poskytuje rozhraní pro programování a příkazový řádek, která pomáhají nasadit databáze U-SQL. Sada SDK je součástí [balíčku U-SQL SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který se nachází na adrese `build/runtime/PackageDeploymentTool.exe`.

V Azure DevOps můžete použít úlohu příkazového řádku a tuto sadu SDK k nastavení kanálu automatizace pro aktualizaci databáze U-SQL. [Přečtěte si další informace o sadě SDK a o tom, jak nastavit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Další kroky

* [Nastavení kanálu CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Otestujte kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Spuštění skriptu U-SQL v místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
