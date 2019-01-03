---
title: Doporučené postupy pro správu sestavení U-SQL v kanálu CI/CD pro službu Azure Data Lake
description: Podívejte se na osvědčené postupy pro správu U-SQL C# sestavení v kanálu CI/CD s Azure DevOps.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0d9192e5ca4dba202ca5287481072bb0f8ae5621
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598515"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Doporučené postupy pro správu sestavení U-SQL v kanálu CI/CD

V tomto článku se dozvíte, jak spravovat zdrojový kód sestavení U-SQL s nově zavedenými databázový projekt U-SQL. Také se dozvíte, jak nastavit průběžnou integraci a nasazování (CI/CD) kanálů pro registraci sestavení s použitím Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Použití databázového projektu U-SQL ke správě zdrojového kódu sestavení

[Databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) je typu projektu v sadě Visual Studio, který pomáhá vývojářům vyvíjet, spravovat a nasazovat své databáze U-SQL, rychlé a snadné. Můžete spravovat všechny objekty U-SQL database (s výjimkou přihlašovací údaje) s databázový projekt U-SQL. 

Ke správě C# zdrojový kód sestavení a sestavení registrace DDL U-SQL skriptů, použijte:

* Databázový projekt U-SQL ke správě sestavení registrace U-SQL skriptů.
* (Pro U-SQL aplikace) ke správě knihovna tříd C# zdrojový kód a závislosti pro operátory definované uživatelem, funkce a agregátorů (UDO, funkcí UDF a UDAGs).
* Databázový projekt U-SQL k odkazování projekt knihovny tříd. 

Databázový projekt U-SQL můžete odkazovat na projekt knihovny tříd (pro aplikace U-SQL). Můžete vytvořit sestavení zaregistrována v databázi U-SQL s použitím odkazuje C# zdrojový kód z tohoto projektu knihovny tříd (pro aplikace U-SQL).

Postupujte podle těchto kroků k vytvoření projektů a přidávat odkazy.
1. Vytvořte projekt knihovny tříd (pro aplikace U-SQL) tak, že vyberete **souboru** > **nový** > **projektu**. Projekt je pod **Azure Data Lake > U-SQL** uzlu.

   ![Nástroje data Lake pro Visual Studio – vytvořit C# projekt knihovny tříd](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Přidat váš uživatelský C# kód v projektu knihovny tříd (pro aplikace U-SQL).

1. Vytvořte projekt U-SQL tak, že vyberete **souboru** > **nový** > **projektu**. Projekt je pod **Azure Data Lake** > **U-SQL** uzlu.

   ![Data Lake Tools pro Visual Studio - databázový projekt vytvořit U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Přidejte odkaz na C# projekt knihovny tříd pro databázový projekt U-SQL.

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools pro Visual Studio – přidání U-SQL databáze odkaz na projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Vytvořit skript sestavení v projektu databáze U-SQL tak, že kliknete pravým tlačítkem projekt a vyberete **přidat novou položku**.

   ![Nástroje data Lake pro Visual Studio – přidat skript sestavení](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otevřete skript sestavení v návrhovém zobrazení sestavení. Vyberte z odkazovaných sestavení **vytvořit sestavení z reference** rozevírací nabídky.

    ![Data Lake Tools pro Visual Studio – vytvořit sestavení z reference](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Přidat **spravované závislosti** a **další soubory**, pokud tam nějaké jsou. Když přidáte další soubory, nástroj používá relativní cestu k Ujistěte se, že ho můžete najít sestavení na svém místním počítači a v počítači sestavení později. 

**@_DeployTempDirectory** v editoru v dolní části okna je předdefinovaná proměnná, která se odkazuje nástroj k výstupní složce sestavení. Ve výstupní složce sestavení každé sestavení má podsložku s názvem název sestavení. Všechny knihovny DLL a další soubory jsou v této podsložce. 

## <a name="build-a-u-sql-database-project"></a>Sestavit projekt U-SQL database

Výstup sestavení pro projekt U-SQL database je balíček pro nasazení databáze U-SQL. Je název s příponou `.usqldbpack`. `.usqldbpack` Balíček je soubor ZIP, který zahrnuje všechny příkazy DDL v jeden skript U-SQL ve složce DDL. Všechny soubory sestavené .dll a dalších souborů sestavení se ve složce Temp.

## <a name="deploy-a-u-sql-database"></a>Nasazení databáze U-SQL

`.usqldbpack` Balíček je možné nasadit na místní účet nebo účet Azure Data Lake Analytics. Pomocí sady Visual Studio nebo nasazení sady SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Nasazení databáze U-SQL v sadě Visual Studio

Můžete nasadit databázi U-SQL pomocí databázový projekt U-SQL nebo `.usqldbpack` balíčku v sadě Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Nasazení pomocí databázový projekt U-SQL

1.  Klikněte pravým tlačítkem na projekt U-SQL databáze a pak vyberte **nasadit**.
2.  V **nasazení databáze U-SQL** průvodce, vyberte **i účet ADLA** do které chcete nasadit databázi. Místní účty a účty ADLA jsou podporovány.
3.  **Databáze zdrojového** se vyplní automaticky. Odkazuje na balíček .usqldbpack ve výstupní složka sestavení projektu.
4.  Zadejte název do **název_databáze** k vytvoření databáze. Pokud databáze s tímto stejným názvem již existuje v cílovém účtu Azure Data Lake Analytics, všechny objekty, které jsou definovány v databázi projektu jsou vytvořeny bez opětovné vytvoření databáze.
5.  Pokud chcete nasadit databázi U-SQL, vyberte **odeslat**. Všechny prostředky, jako jsou sestavení a další soubory jsou odeslány. Odeslání úlohy U-SQL, který zahrnuje všechny příkazy jazyka DDL.

    ![Data Lake Tools pro Visual Studio - databázový projekt nasazení U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools pro Visual Studio – Průvodce projektem nasazení U-SQL database](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Nasazení databáze U-SQL v Azure DevOps

`PackageDeploymentTool.exe` poskytuje programování a rozhraní příkazového řádku, které pomáhají k nasazení databáze U-SQL. Je součástí sady SDK [balíček Nuget sady SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), který je umístěn na `build/runtime/PackageDeploymentTool.exe`.

V Azure DevOps můžete úlohu příkazového řádku a tuto sadu SDK k nastavení kanálu služby automation pro obnovení databáze U-SQL. [Další informace o sadě SDK a jak vytvořit kanál CI/CD pro nasazení databáze U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Další postup

* [Nastavení kanálu CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Testování kódu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
* [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
