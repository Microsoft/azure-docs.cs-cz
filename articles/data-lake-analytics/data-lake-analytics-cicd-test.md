---
title: Tom, jak testovat kód Azure Data Lake Analytics | Dokumentace Microsoftu
description: Zjistěte, jak přidat testovací případy pro U-SQL a rozšířené kód jazyka C# pro Azure Data Lake Analytics.
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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37889509"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Tom, jak testovat kód Azure Data Lake Analytics

Azure Data Lake U-SQL poskytuje jako jazyk, který spojuje deklarativní SQL pomocí imperativního jazyka C# pro zpracování dat v libovolném měřítku. V tomto dokumentu se dozvíte, jak vytvořit testovací případy pro U-SQL a rozšířeného kódu C# UDO (operátor User-defined).

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Skript U-SQL je zkompilován a optimalizovaná tak, aby spustitelný kód a spustit v počítačích v cloudu nebo místním počítači. Proces kompilace a optimalizace zpracovává celý skript U-SQL jako celek ke spuštění. Nelze provést tradiční "unit test" pro každý příkaz. Pomocí U-SQL testovací sady SDK a místní spuštění sady SDK, ale můžete provést skript úrovně testy.

### <a name="create-test-cases-for-u-sql-script"></a>Vytvoření testovacích případů pro skript U-SQL

Azure Data Lake Tools pro Visual Studio nabízí kvalitní prostředí, které vám pomohou vytvořit testovací případy skript U-SQL.

1.  Klikněte pravým tlačítkem na skript U-SQL v Průzkumníku řešení a zvolte **vytvoření testu jednotek**.
2.  Nakonfigurujte tak, aby vytvořit nový testovací projekt nebo vložit testovacího případu k existujícímu projektu testů.

    ![Data Lake Tools pro Visual Studio vytvoří testovací projekt v u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools pro Visual Studio vytvořit konfigurace projektu testů u-sql](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Správa zdroje dat testu

Při testování skriptů U-SQL, jsou potřeba testovací vstupní soubory. Můžete spravovat tyto testovací data tím, že nakonfigurujete **zdroj dat otestovat** vlastnosti projektu v U-SQL. Při volání `Initialize()` rozhraní v testu U-SQL SDK, do dočasné složky kořen dat místního je vytvořené v rámci pracovního adresáře projektu testu a všechny soubory a podsložky (a soubory v podsložkách) ve zdrojové složce testovací data se zkopírují do dočasného kořenové složky místní data před spuštěním testovacích případů skript U-SQL. Je-li přidat další složky zdroje dat testu, plivání cestu ke složce data testu středníkem.

![Data Lake Tools pro Visual Studio konfigurace zdroje dat projektu testu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Správa databáze prostředí pro testování

Pokud používáte skripty U-SQL nebo dotazování pomocí U-SQL databázové objekty, například volání uložených procedur, je nutné inicializovat databázi prostředí před spuštěním U-SQL testovací případy. `Initialize()` Rozhraní v testu U-SQL SDK vám pomůže nasadit všechny databáze, které se odkazuje projekt U-SQL do dočasné složky místního kořen dat v pracovním adresáři testovací projekt. 

Další informace o [projekty odkazy na projekt v U-SQL, jak můžete spravovat databáze U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Zkontrolujte výsledky testu

`Run()` Rozhraní vrátí výsledek spuštění úlohy, 0 znamená úspěšné a 1 znamená, že se nezdařilo. Vyhodnocení funkcí jazyka C# můžete také ověřit výstupy. 

### <a name="execute-test-cases-in-visual-studio"></a>Spouštět testovací případy v sadě Visual Studio

Projekt testů skript U-SQL je postavený na rozhraní testování částí jazyka C#. Po sestavení projektu, můžete si projít všechny testovací případy **Průzkumníka testů > seznam stop**, nebo klikněte pravým tlačítkem na soubor .cs a zvolte **spustit testy**.

## <a name="test-c-udos"></a>Test C# UDO

### <a name="create-test-cases-for-c-udos"></a>Vytvoření testovacích případů pro C# UDO

C# rámce jednotkových testů můžete použít k testování UDOs(User-Defined Operator) C#. Při testování UDO, je nutné připravit odpovídající **IRowset** objektu jako vstupy.

Existují dva způsoby, jak vytvořit IRowset:

1.  Načtení dat ze souboru k vytvoření IRowset

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

2.  Data ze sběru dat použít k vytvoření IRowset

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

### <a name="verify-test-results"></a>Zkontrolujte výsledky testu

Po volání funkce UDO, můžete ověřit výsledek prostřednictvím schématu a sady řádků hodnota ověření pomocí jazyka C# vyhodnocení funkce. Vzorový kód může být v U-SQL C# UDO jednotky testů ukázkový projekt prostřednictvím **soubor > Nový > projekt** v sadě Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Spouštět testovací případy v sadě Visual Studio

Po sestavení testovacího projektu, můžete spustit všechny Testové případy ale **Průzkumníka testů > seznam stop**, nebo klikněte pravým tlačítkem na soubor .cs a zvolte **spustit testy**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Spouštění testovacích případů v aplikaci Visual Studio Team Service

Obě **testovací projekt U-SQL skriptů** a **testovacího projektu C# UDO** dědit C# projekt testu jednotek. [Úloha Visual Studio Test](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) ve Visual Studio Team Service můžete spustit tyto testovací případy. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Spouštění testovacích případů U-SQL ve službě Visual Studio Team Service

Pro testování U-SQL, ujistěte se, že načtete `CPPSDK` na sestavujícím počítači a předejte `CPPSDK` cestu k USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Co je CPPSDK?**

CPPSDK je, že obsahuje balíček Microsoft Visual C++ 14 a sady Windows SDK 10.0.10240.0, což je prostředí vyžaduje modul runtime U-SQL. Můžete získat tento balíček v Azure Data Lake Tools pro Visual Studio Instalační složka:

- Pro sadu Visual Studio 2015 je v části `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Pro Visual Studio 2017 je v části `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Jak připravit CPPSDK v agentu sestavení Visual Studio Team Service**

Je běžným způsobem pro přípravu tuto závislost CPPSDK ve Visual Studio Team Service:

1.  Složku ZIP obsahuje CPPSDK knihovny.
2.  Soubor zip do vašeho systému správy zdrojového kódu se změnami. (Soubor zip může Ujistěte se, že vrátíte se změnami všechny knihovny CPPSDK ve složce nebo některé soubory se bude ignorovat ".gitignore".)
3.  Rozbalte soubor zip v kanálu sestavení.
4.  Bod `USqlScriptTestRunner` do této složky rozbaleny v počítači sestavení.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Spouštění jazyka C# UDO testovacích případů v aplikaci Visual Studio Team Service

Pro C# UDO test Ujistěte se, že chcete odkazovat na následující sestavení, které jsou potřebné pro UDO. Pokud se budete odkazovat na ně prostřednictvím [balíček Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), ujistěte se, že ve vašem kanálu sestavení přidáte úlohu obnovení NuGet.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
- [Vývoj databáze U-SQL pomocí databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

