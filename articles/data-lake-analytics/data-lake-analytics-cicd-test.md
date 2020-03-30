---
title: Jak otestovat kód Azure Data Lake Analytics
description: Zjistěte, jak přidat testovací případy pro U-SQL a rozšířený kód C# pro Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913962"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Otestujte kód Azure Data Lake Analytics

Azure Data Lake poskytuje jazyk [U-SQL.](data-lake-analytics-u-sql-get-started.md) U-SQL kombinuje deklarativní SQL s imperativní C# pro zpracování dat v libovolném měřítku. V tomto dokumentu se dozvíte, jak vytvořit testovacích případů pro U-SQL a rozšířený kód uživatelem definovaného operátoru Jazyka C# (UDO).

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Skript U-SQL je zkompilován a optimalizován pro spuštění spustitelného kódu v Azure nebo v místním počítači. Proces kompilace a optimalizace zpracovává celý U-SQL skript jako celek. Nelze provést tradiční testování částí pro každý příkaz. Však pomocí U-SQL test SDK a místní spuštění sady SDK, můžete provést testy na úrovni skriptu.

### <a name="create-test-cases-for-u-sql-script"></a>Vytvoření testovacích případů pro skript U-SQL

Nástroje datového jezera Azure pro Visual Studio umožňují vytvářet testovací případy skriptů U-SQL.

1. Klepněte pravým tlačítkem myši na skript U-SQL v Průzkumníku řešení a pak vyberte **příkaz Vytvořit test částí**.

1. Vytvořte nový testovací projekt nebo vložte testovací případ do existujícího testovacího projektu.

   ![Nástroje datového jezera pro Visual Studio – vytvoření konfigurace testovacího projektu U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Správa testovacího zdroje dat

Při testování skriptů U-SQL potřebujete testovat vstupní soubory. Chcete-li spravovat testovací data, klepněte v **Průzkumníku řešení**pravým tlačítkem myši na projekt U-SQL a vyberte příkaz **Vlastnosti**. Zdroj můžete zadat do **testovacího zdroje dat**.

![Nástroje datového jezera pro Visual Studio – konfigurace zdroje dat testovacího projektu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Při volání `Initialize()` rozhraní v u-SQL test SDK, dočasné kořenové složky místních dat je vytvořen pod pracovní adresář testovacího projektu. Všechny soubory a složky ve složce zdroje testovacích dat jsou před spuštěním testovacích případů skriptu U-SQL zkopírovány do dočasné místní kořenové složky dat. Můžete přidat další složky testovacího zdroje dat rozdělením cesty testovací složky dat s středníkem.

### <a name="manage-the-database-environment-for-testing"></a>Správa databázového prostředí pro testování

Pokud vaše u-SQL skripty použít nebo dotaz s u-SQL databázové objekty, je třeba inicializovat prostředí databáze před spuštěním u-SQL testovacích případů. Tento přístup může být nezbytné při volání uložené procedury. Rozhraní `Initialize()` v sada SDK testu U-SQL vám pomůže nasadit všechny databáze, na které projekt U-SQL odkazuje, do kořenové složky dočasných místních dat v pracovním adresáři testovacího projektu.

Další informace o správě odkazů na projekt databáze U-SQL pro projekt U-SQL naleznete v [tématu Reference a U-SQL database project](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Ověření výsledků testů

Rozhraní `Run()` vrátí výsledek spuštění úlohy. *0* znamená úspěch a *1* znamená neúspěch. Můžete také použít C# assert funkce k ověření výstupů.

### <a name="run-test-cases-in-visual-studio"></a>Spuštění testovacích případů v sadě Visual Studio

Testovací projekt skriptu U-SQL je postaven na rámci testování částí C#. Po sestavení projektu vyberte **možnost Testovat** > **Průzkumníka testů systému****Windows** > . Testovací případy můžete spustit z **Průzkumníka testů**. Případně klepněte pravým tlačítkem myši na soubor CS v testu částí a vyberte spustit **testy**.

## <a name="test-c-udos"></a>Testování udos a c#

### <a name="create-test-cases-for-c-udos"></a>Vytvořit testovací případy pro udos c#

Můžete použít rozhraní c# testování částí k testování vašich c# uživatelem definované operátory (UDO). Při testování UDO, je třeba připravit odpovídající **IRowset** objekty jako vstupy.

Objekt **IRowset** lze vytvořit dvěma způsoby:

- Načtení dat ze souboru pro vytvoření **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Použití dat ze sběru dat k vytvoření **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Ověření výsledků testů

Po volání funkcí UDO můžete ověřit výsledky prostřednictvím ověření hodnoty schématu a sady řádků pomocí funkcí jazyka C# assert. Do vašeho řešení můžete přidat **projekt testování částí U-SQL C# UDO.** Chcete-li tak učinit, vyberte **soubor > nový > project** v sadě Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Spuštění testovacích případů v sadě Visual Studio

Po sestavení projektu vyberte **možnost Testovat** > **Průzkumníka testů systému****Windows** > . Testovací případy můžete spustit z **Průzkumníka testů**. Případně klepněte pravým tlačítkem myši na soubor CS v testu částí a vyberte spustit **testy**.

## <a name="run-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů v Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Oba **u-SQL skript testovací projekty** a **C# UDO testovací projekty** dědí C# projekty testování částí. [Testovací úloha Visual Studia](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) v Azure Pipelines můžete spustit tyto testovací případy.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů U-SQL v Azure Pipelines

V případě testu U-SQL se `CPPSDK` ujistěte, že načtete počítač sestavení, a pak předejte `CPPSDK` cestu k aplikaci `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`.

#### <a name="what-is-cppsdk"></a>Co je CPPSDK?

CPPSDK je balíček, který obsahuje Microsoft Visual C++ 14 a Windows SDK 10.0.10240.0. Tento balíček obsahuje prostředí, které je potřeba runtime U-SQL. Tento balíček můžete získat ve složce instalace Azure Data Lake Tools for Visual Studio:

- Pro Visual Studio 2015 je`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Pro Visual Studio 2017 je pod`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Pro Visual Studio 2019 je`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Příprava sady CPPSDK v agentovi sestavení Azure Pipelines

Nejběžnější způsob, jak připravit závislost CPPSDK v Azure Pipelines je následující:

1. Zip složku, která obsahuje knihovny CPPSDK.

1. Soubor ZIP se může zobrazit v systému správy zdrojového kódu. Soubor ZIP zajišťuje vrácení všech knihoven se změnami ve složce CPPSDK, aby `.gitignore` soubory nebyly ignorovány z důvodu souboru.

1. Rozbalte soubor ZIP v kanálu sestavení.

1. Přejděte `USqlScriptTestRunner` na tuto rozbalenou složku v počítači sestavení.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů UDO jazyka C# v Azure Pipelines

Pro c# UDO test, ujistěte se, že odkaz na následující sestavení, které jsou potřebné pro UDO.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Typy
- Microsoft.Analytics.UnitTest

Pokud na ně odkazujete prostřednictvím [balíčku Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), nezapomeňte přidat úlohu NuGet Restore v kanálu sestavení.

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Spuštění skriptu U-SQL v místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
- [Použití databázového projektu U-SQL k vývoji databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
