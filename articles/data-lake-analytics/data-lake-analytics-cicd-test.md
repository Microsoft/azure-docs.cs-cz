---
title: Postup testování kódu Azure Data Lake Analytics
description: Naučte se, jak přidat testovací případy pro jazyk U-SQL a Rozšířený kód C# pro Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 7310c67ef20a4134d4f613ea969c96802958bf62
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219203"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testování kódu Azure Data Lake Analytics

Azure Data Lake poskytuje jazyk [U-SQL](data-lake-analytics-u-sql-get-started.md) . U-SQL kombinuje deklarativní SQL s imperativním jazykem C# pro zpracování dat v libovolném měřítku. V tomto dokumentu se dozvíte, jak vytvořit testovací případy pro kód U-SQL a rozšířeného uživatelem definovaného operátoru (UDO) jazyka C#.

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Skript U-SQL je zkompilován a optimalizován pro spuštění spustitelného kódu v Azure nebo na místním počítači. Proces kompilace a optimalizace zpracovává celý skript U-SQL jako celek. Pro každý příkaz nelze provést tradiční test jednotek. Pomocí sady testů U-SQL test SDK a místního běhu sady SDK ale můžete provádět testy na úrovni skriptů.

### <a name="create-test-cases-for-u-sql-script"></a>Vytvoření testovacích případů pro skript U-SQL

Nástroje Azure Data Lake pro Visual Studio umožňuje vytvořit testovací případy skriptu U-SQL.

1. V Průzkumník řešení klikněte pravým tlačítkem na skript U-SQL a pak vyberte **vytvořit test jednotek**.

1. Vytvořte nový testovací projekt nebo vložte testovací případ do existujícího testovacího projektu.

   ![Data Lake Tools for Visual Studio – vytvoření konfigurace testovacího projektu U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Správa zdroje testovacích dat

Když testujete skripty U-SQL, budete potřebovat testovací vstupní soubory. Chcete-li spravovat testovací data, v **Průzkumník řešení**klikněte pravým tlačítkem na projekt U-SQL a vyberte **vlastnosti**. Do **zdroje testovacích dat**můžete zadat zdroj.

![Data Lake Tools for Visual Studio – konfigurace zdroje testovacích dat projektu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Při volání `Initialize()` rozhraní v sadě SDK testu U-SQL se vytvoří dočasná kořenová složka místních dat v pracovním adresáři testovacího projektu. Všechny soubory a složky ve složce zdroje testovacích dat se zkopírují do dočasné kořenové složky místních dat před spuštěním testovacích případů skriptu U-SQL. Můžete přidat další zdroje testovacích dat tak, že rozdělujete cestu ke složce testovacích dat středníkem.

### <a name="manage-the-database-environment-for-testing"></a>Správa databázového prostředí pro testování

Pokud vaše skripty U-SQL používají nebo se dotazují s objekty databáze U-SQL, je nutné před spuštěním testovacích případů U-SQL inicializovat prostředí databáze. Tento přístup může být nutný při volání uložených procedur. `Initialize()`Rozhraní v sadě SDK testu u-SQL vám pomůže nasadit všechny databáze, na které se odkazuje v projektu u-SQL, do dočasné místní složky místních dat v pracovním adresáři testovacího projektu.

Další informace o tom, jak spravovat odkazy na projekt U-SQL Database pro projekt U-SQL, najdete v tématu [Referenční dokumentace k databázovému projektu u](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)-SQL.

### <a name="verify-test-results"></a>Ověřit výsledky testu

`Run()`Rozhraní vrátí výsledek provedení úlohy. *0* znamená úspěch a *1* znamená selhání. K ověření výstupů můžete použít také funkce kontrolního výrazu jazyka C#.

### <a name="run-test-cases-in-visual-studio"></a>Spuštění testovacích případů v aplikaci Visual Studio

Projekt testů skriptu U-SQL je postaven nad rámec testovacího rozhraní jednotek jazyka C#. Po sestavení projektu vyberte **test**  >  **Windows**  >  **Test Explorer**. Testovací případy lze spustit z **Průzkumníka testů**. Případně klikněte pravým tlačítkem myši na soubor. cs v testu jednotek a vyberte **Spustit testy**.

## <a name="test-c-udos"></a>Test C# Udo

### <a name="create-test-cases-for-c-udos"></a>Vytvoření testovacích případů pro C# Udo

K otestování uživatelem definovaných operátorů jazyka C# (Udo) můžete použít rozhraní pro testování částí v jazyce C#. Při testování Udo je třeba připravit odpovídající objekty **IRowset** jako vstupy.

Existují dva způsoby, jak vytvořit objekt **IRowset** :

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

- K vytvoření **IRowset**použijte data z kolekce dat:

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

### <a name="verify-test-results"></a>Ověřit výsledky testu

Po volání funkcí UDO můžete ověřit výsledky prostřednictvím schématu a ověření hodnoty sady řádků pomocí funkcí kontrolního výrazu jazyka C#. Do svého řešení můžete přidat **projekt testu jednotek U-SQL C# Udo** . Provedete to tak, že vyberete **soubor > nový > projekt** v aplikaci Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Spuštění testovacích případů v aplikaci Visual Studio

Po sestavení projektu vyberte **test**  >  **Windows**  >  **Test Explorer**. Testovací případy lze spustit z **Průzkumníka testů**. Případně klikněte pravým tlačítkem myši na soubor. cs v testu jednotek a vyberte **Spustit testy**.

## <a name="run-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů v Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Projekty testů **skriptu U-SQL** a **projekty Udo v jazyce c#** dědí projekty testování částí v jazyce c#. [Úkol testu sady Visual Studio](/azure/devops/pipelines/test/getting-started-with-continuous-testing) v Azure Pipelines může spustit tyto testovací případy.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů U-SQL v Azure Pipelines

U testu U-SQL Zajistěte, aby se načetly do `CPPSDK` počítače sestavení, a pak předejte `CPPSDK` cestu k `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` .

#### <a name="what-is-cppsdk"></a>Co je CPPSDK?

CPPSDK je balíček, který obsahuje Microsoft Visual C++ 14 a Windows SDK 10.0.10240.0. Tento balíček zahrnuje prostředí, které je potřeba pro modul runtime U-SQL. Tento balíček můžete získat v instalační složce Nástroje Azure Data Lake pro Visual Studio:

- V případě sady Visual Studio 2015 je `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- V případě sady Visual Studio 2017 je `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- V případě sady Visual Studio 2019 je `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Příprava CPPSDK v agentovi sestavení Azure Pipelines

Nejběžnější způsob, jak připravit závislost CPPSDK v Azure Pipelines, je následující:

1. PSČ složky, která obsahuje knihovny CPPSDK.

1. Soubor. zip vraťte do systému správy zdrojového kódu. Soubor. zip zajišťuje kontrolu všech knihoven ve složce CPPSDK, aby se soubory neignorovaly kvůli `.gitignore` souboru.

1. Rozbalte soubor. zip v kanálu sestavení.

1. Najeďte `USqlScriptTestRunner` na tuto složku unzip v počítači sestavení.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Spuštění testovacích případů C# UDO v Azure Pipelines

Pro test UDO v C# nezapomeňte odkazovat na následující sestavení, která jsou potřebná pro Udo.

- Microsoft. Analytics. Interfaces
- Microsoft. Analytics. Types
- Microsoft. Analytics. UnitTest

Pokud na ně odkazujete pomocí [balíčku NuGet Microsoft. Azure. datalake. USQL. Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), ujistěte se, že jste do kanálu sestavení přidali úlohu obnovení NuGet.

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Spuštění skriptu U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
- [Použití projektu U-SQL Database pro vývoj databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)