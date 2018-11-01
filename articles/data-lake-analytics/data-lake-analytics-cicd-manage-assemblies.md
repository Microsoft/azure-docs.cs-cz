---
title: Osvědčený postup správy sestavení U-SQL v kanálu CI/CD pro službu Azure Data Lake
description: Přečtěte si doporučené postupy správy U-SQL C# kanál sestavení v CI/CD s Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633882"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>Osvědčený postup správy sestavení U-SQL v kanálu CI/CD

V tomto článku se dozvíte, jak spravovat zdrojový kód sestavení U-SQL s použitím nového zavedení projektu databáze U-SQL. Můžete se také dozvíte, jak nastavit průběžnou integraci a nasazování (CI/CD) kanálů pro registraci sestavení pomocí Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Použití databázového projektu U-SQL ke správě zdrojového kódu sestavení

[Databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) je typu projektu v sadě Visual Studio, který pomáhá vývojářům vyvíjet, spravovat a nasazovat své databáze U-SQL, rychlé a snadné. Všechny objekt databáze U-SQL (s výjimkou přihlašovací údaje) můžete spravovat pomocí databázový projekt U-SQL. 

Doporučeným způsobem správy C# sestavení zdrojového kódu a sestavení registrace DDL skriptů U-SQL je:

* Použití **projekt v U-SQL databáze** ke správě sestavení registrace U-SQL skriptů.
* Použití **knihovna tříd (pro aplikace U-SQL)** ke správě C# zdrojový kód a závislosti pro operátory definované uživatelem, funkce a agregátorů (UDO/UDF/UDAGs).
* Databázový projekt U-SQL můžete odkazovat na projekt knihovny tříd. 

Databázový projekt U-SQL můžete odkazovat na projekt knihovny tříd (pro aplikace U-SQL). Sestavení zaregistrována v databázi U-SQL můžete vytvořit pomocí odkazuje C# zdrojový kód z tohoto projektu knihovny tříd (pro aplikace U-SQL).

Můžete postupovat podle následujících kroků vytváření projektů a přidávání odkazů na:
1. Vytvořte projekt knihovny tříd (pro aplikace U-SQL) prostřednictvím **soubor > Nový > projekt**. Projekt je pod **Azure Data Lake > U-SQL** uzlu.
   ![Nástroje data Lake pro Visual Studio – vytvořit C# projekt knihovny tříd](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. Přidat váš uživatelský C# kód v projektu knihovny tříd (pro aplikace U-SQL). 
3. Vytvořte projekt U-SQL pomocí **soubor > Nový > projekt**. Projekt je pod **Azure Data Lake > U-SQL** uzlu.
   ![Data Lake Tools pro Visual Studio - databázový projekt vytvořit U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. Přidat odkaz C# projekt knihovny tříd pro databázový projekt U-SQL.

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. Vytvořit skript sestavení v projektu U-SQL database prostřednictvím **klikněte pravým tlačítkem na projekt > Přidat novou položku**.
   ![Nástroje data Lake pro Visual Studio – přidat skript sestavení](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. Otevřete skript sestavení v návrhovém zobrazení sestavení, klikněte na položku odkazované sestavení z **vytvořit sestavení z reference** rozevírací nabídky.

    ![Data Lake Tools pro Visual Studio – vytvořit sestavení z reference](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Přidat **spravované závislosti** a **další soubory** Pokud tam nějaké jsou. Když přidáte další soubory, nástroj používá relativní cestu k Ujistěte se, že ho můžete najít sestavení na svém místním počítači a v počítači sestavení později. 

**@_DeployTempDirectory** v editoru v dolní části okna je předdefinovaná proměnná, která se odkazuje nástroj k výstupní složce sestavení. Ve výstupní složce sestavení každé sestavení má podsložku s názvem název sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce. 

## <a name="build-a-u-sql-database-project"></a>Sestavit projekt U-SQL database

Výstup pro databázový projekt U-SQL je U-SQL database balíček pro nasazení s názvem s příponou sestavení `.usqldbpack`. `.usqldbpack` Balíček je soubor .zip, který zahrnuje všechny příkazy jazyka DDL v jeden skript U-SQL v **DDL** složky a všechny sestavené knihovny DLL a dalších souborů sestavení v **Temp** složky.

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

`.usqldbpack` Balíčku může být nasazena na místní účet nebo účet Azure Data Lake Analytics pomocí sady Visual Studio nebo nasazení sady SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Můžete nasadit databázi U-SQL pomocí databázový projekt U-SQL nebo `.usqldbpack` balíčku v sadě Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Nasazení přes databázový projekt U-SQL

1.  Klikněte pravým tlačítkem na projekt U-SQL databáze a pak vyberte **nasadit**.
2.  V **Průvodce nasazením databáze U-SQL**, vyberte **i účet ADLA** do které chcete nasadit databázi. Místní účty a účty ADLA jsou podporovány.
3.  **Databáze zdrojového** se vyplní automaticky, a výstupní složce sestavení body do balíčku .usqldbpack v projektu.
4.  Zadejte název do **název_databáze** k vytvoření databáze. Pokud databáze s tímto stejným názvem již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez nutnosti opětovného vytvoření databáze.
5.  Pokud chcete nasadit databázi U-SQL, vyberte **odeslat**. Všechny prostředky (sestavení a další soubory) se nahrají a odeslání úlohy U-SQL, který zahrnuje všechny příkazy jazyka DDL.

    ![Data Lake Tools pro Visual Studio - databázový projekt nasazení U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools pro Visual Studio – Průvodce projektem nasazení U-SQL database](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>Nasazení databáze U-SQL v Azure DevOps

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají k nasazení databáze U-SQL. Je součástí sady SDK [balíček Nuget sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který je umístěn na `build/runtime/PackageDeploymentTool.exe`.

V Azure DevOps můžete použít příkazový řádek úkolu a tuto sadu SDK k nastavení kanálu pro automatizaci pro obnovení databáze U-SQL. [Další informace o sadě SDK a jak vytvořit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Další postup

* [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
