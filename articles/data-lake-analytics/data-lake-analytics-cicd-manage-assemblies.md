---
title: Správa sestavení U-SQL v kanálu CI/CD-Azure Data Lake
description: Seznamte se s osvědčenými postupy pro správu sestavení U-SQL C# v kanálu CI/CD pomocí Azure DevOps.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015277"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Osvědčené postupy pro správu sestavení U-SQL v kanálu CI/CD

V tomto článku se dozvíte, jak spravovat zdrojový kód sestavení U-SQL pomocí nově zavedeného databázového projektu U-SQL. Naučíte se také, jak nastavit kanál průběžné integrace a nasazování (CI/CD) pro registraci sestavení pomocí Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Použití databázového projektu U-SQL ke správě zdrojového kódu sestavení

[Projekt databáze u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) je typ projektu v aplikaci Visual Studio, který pomáhá vývojářům rychle a snadno vyvíjet, spravovat a nasazovat databáze u-SQL. U databázového projektu U-SQL můžete spravovat všechny objekty databáze U-SQL (kromě přihlašovacích údajů).

Pro správu zdrojového kódu sestavení C# a skriptů DDL U-SQL registrace sestavení použijte:

- Projekt databáze u-SQL pro správu registrace sestavení skripty U-SQL.
- Knihovna tříd (pro aplikaci U-SQL) pro správu zdrojového kódu a závislostí v jazyce C# pro uživatelsky definované operátory, funkce a agregátory (Udo, UDF a UDAGs).
- Projekt databáze U-SQL pro odkazování na projekt knihovny tříd.

Projekt databáze U-SQL může odkazovat na knihovnu tříd (pro aplikaci U-SQL). Sestavení registrovaná v databázi U-SQL můžete vytvořit pomocí odkazovaného zdrojového kódu C# z této knihovny tříd (pro aplikace U-SQL).

Pomocí těchto kroků můžete vytvořit projekty a přidat odkazy.

1. Vytvořte projekt knihovny tříd (pro aplikaci u-SQL) tak, že vyberete **soubor**  >  **Nový**  >  **projekt**. Projekt je pod uzlem **Azure Data Lake > U-SQL** .

   ![Data Lake Tools for Visual Studio – vytvoření projektu knihovny tříd C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Přidejte uživatelem definovaný kód C# do knihovny tříd (pro aplikaci U-SQL).

1. Vytvořte projekt U-SQL tak, že vyberete **soubor**  >  **Nový**  >  **projekt**. Projekt je pod uzlem **Azure Data Lake**  >  **U-SQL** .

   ![Data Lake Tools for Visual Studio – vytvoření databázového projektu U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Přidejte odkaz na projekt knihovny tříd jazyka C# pro projekt databáze U-SQL.

   ![Data Lake Tools for Visual Studio – přidat odkaz](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio – přidat odkaz na projekt databáze U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Vytvořte skript sestavení v projektu databáze U-SQL tak, že kliknete pravým tlačítkem na projekt a vyberete **Přidat novou položku**.

   ![Data Lake Tools for Visual Studio – přidání skriptu sestavení](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otevřete skript sestavení v zobrazení návrhu sestavení. Vyberte odkazované sestavení z rozevírací nabídky **vytvořit sestavení z odkazu** .

   ![Data Lake Tools for Visual Studio – vytvoření sestavení z reference](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Přidejte **spravované závislosti** a **Další soubory**, pokud existují. Když přidáte další soubory, nástroj použije relativní cestu k tomu, aby bylo zajištěno, že bude moci najít sestavení v místním počítači a v počítači sestavení později.

**\@ _DeployTempDirectory** v okně editoru v dolní části je předdefinovaná proměnná, která tento nástroj odkazuje na výstupní složku sestavení. V rámci výstupní složky sestavení má každé sestavení podsložku s názvem sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce.

## <a name="build-a-u-sql-database-project"></a>Vytvoření databázového projektu U-SQL

Výstup sestavení pro projekt databáze U-SQL je balíček pro nasazení U-SQL Database. Má název s příponou `.usqldbpack` . `.usqldbpack`Balíček je soubor. zip, který obsahuje všechny příkazy DDL v jednom skriptu U-SQL ve složce DDL. Všechny sestavené soubory DLL a další soubory pro sestavení jsou v dočasné složce.

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

`.usqldbpack`Balíček se dá nasadit buď do místního účtu, nebo do účtu Azure Data Lake Analytics. Použijte sadu Visual Studio nebo sadu SDK nasazení.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v aplikaci Visual Studio

Databázi U-SQL můžete nasadit pomocí databázového projektu U-SQL nebo `.usqldbpack` balíčku v aplikaci Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Nasazení pomocí databázového projektu U-SQL

1. Klikněte pravým tlačítkem na projekt databáze U-SQL a pak vyberte **nasadit**.

1. V průvodci **nasazením U-SQL Database** vyberte **účet ADLA** , do kterého chcete nasadit databázi. Podporují se místní účty i účty ADLA.

1. **Zdroj databáze** se vyplní automaticky. Odkazuje na balíček. usqldbpack ve výstupní složce sestavení projektu.

1. Chcete-li vytvořit databázi, zadejte název v **názvu databáze** . Pokud databáze s tímto názvem již v cílovém Azure Data Lake Analytics účtu existuje, jsou všechny objekty, které jsou definovány v projektu databáze, vytvořeny bez opětovného vytvoření databáze.

1. Chcete-li nasadit databázi U-SQL, vyberte **Odeslat**. Budou nahrány všechny prostředky, jako jsou sestavení a další soubory. Úloha U-SQL, která zahrnuje všechny příkazy DDL, je odeslána.

   ![Data Lake Tools for Visual Studio – nasazení databázového projektu U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools for Visual Studio – Průvodce nasazením databázového projektu U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Nasazení databáze U-SQL v Azure DevOps

`PackageDeploymentTool.exe` poskytuje rozhraní pro programování a příkazový řádek, která usnadňují nasazení databází U-SQL. Sada SDK je součástí [balíčku NuGet U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který se nachází na adrese `build/runtime/PackageDeploymentTool.exe` .

V Azure DevOps můžete k nastavení kanálu automatizace pro obnovení databáze U-SQL použít úlohu příkazového řádku a tuto sadu SDK. [Přečtěte si další informace o sadě SDK a o tom, jak vytvořit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Další kroky

- [Nastavení kanálu CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testování kódu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Spuštění skriptu U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
