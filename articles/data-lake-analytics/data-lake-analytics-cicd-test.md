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
ms.openlocfilehash: fa5c113541452a93c25adc7c14bdaa6994434c71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365856"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testování kódu Azure Data Lake Analytics

Azure Data Lake nabízí jazyka U-SQL, který spojuje deklarativní SQL pomocí imperativního jazyka C# pro zpracování dat v libovolném měřítku. V tomto dokumentu se dozvíte, jak vytvořit testovací případy pro U-SQL a rozšířeného kódu C# UDO (uživatelem definovaný operátor).

## <a name="test-u-sql-scripts"></a>Testování skriptů U-SQL

Skript U-SQL je zkompilován a optimalizovaná pro spustitelný kód pro spuštění na počítačích v cloudu nebo na místním počítači. Proces kompilace a optimalizace zpracovává celý skript U-SQL jako celek. Nelze provést tradiční "unit test" pro každý příkaz. Ale testování pomocí U-SQL SDK a místní spuštění sady SDK, můžete provést testy úroveň skriptu.

### <a name="create-test-cases-for-u-sql-script"></a>Vytvoření testovacích případů pro skript U-SQL

Azure Data Lake Tools pro Visual Studio umožňuje vytvářet testovací případy skript U-SQL.

1.  Klikněte pravým tlačítkem na skript U-SQL v Průzkumníku řešení a pak vyberte **vytvoření testu jednotek**.
2.  Vytvořit nový testovací projekt nebo vložit testovací případ do existujícího projektu testů.

    ![Data Lake Tools pro Visual Studio – vytvořte testovací projekt U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools pro Visual Studio – vytvořit konfigurace projektu testů U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Správa zdroje dat testu

Při testování skriptů U-SQL, potřebujete testovací vstupních souborů. Testovací data můžete spravovat pomocí konfigurace **zdroj dat otestovat** vlastnosti projektu v U-SQL. 

Při volání `Initialize()` rozhraní v testu U-SQL SDK, kořenové složky místní dočasných dat je vytvořené v rámci pracovního adresáře projektu testu a všechny soubory a podsložky (a soubory v podsložkách) ve zdrojové složce testovací data se zkopírují do dočasný místní data kořenová složka předtím, než spustíte testovací případy skript U-SQL. Můžete přidat další složky zdroje dat testu rozdělením test cestu ke složce data oddělte středníkem.

![Nástroje data Lake pro Visual Studio – konfigurace zdroje dat projektu testu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Správa databáze prostředí pro testování

Případně skripty U-SQL pomocí dotazu s objekty databáze U-SQL (například při volání uložené procedury) je nutné pro inicializaci prostředí databáze před spuštěním U-SQL testovací případy. `Initialize()` Rozhraní v testu U-SQL SDK vám pomůže nasadit všechny databáze, které se odkazuje projekt U-SQL ke kořenové složce dočasných dat místní v pracovním adresáři testovacího projektu. 

Další informace o [Správa odkazů projektu databáze U-SQL pro projekt v U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Zkontrolujte výsledky testu

`Run()` Rozhraní vrátí výsledek provedení úlohy. 0 znamená úspěch a 1 znamená selhání. Vyhodnocení funkcí jazyka C# můžete také ověřit výstupy. 

### <a name="run-test-cases-in-visual-studio"></a>Spouštění testovacích případů v sadě Visual Studio

Projekt testů skript U-SQL je nástavbou C# testovacího rozhraní jednotky. Po sestavení projektu, můžete si projít všechny testovací případy **Průzkumníka testů > seznam stop**. Alternativně klikněte pravým tlačítkem na soubor .cs a pak vyberte **spustit testy**.

## <a name="test-c-udos"></a>Test C# UDO

### <a name="create-test-cases-for-c-udos"></a>Vytvoření testovacích případů pro C# UDO

C# rámce jednotkových testů můžete použít k testování vašich C# UDO (operátory definované uživatelem). Při testování UDO, je nutné připravit odpovídající **IRowset** objekty jako vstupy.

Existují dva způsoby, jak vytvořit objekt IRowset:

- Načtení dat ze souboru k vytvoření IRowset:

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

- Data ze sběru dat použijte k vytvoření IRowset:

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

Po zavolání funkce UDO můžete ověřit výsledky prostřednictvím schématu a sady řádků hodnota ověření pomocí vyhodnocení funkce jazyka C#. Můžete použít ukázkový kód v projektu vzorku testů jednotek U-SQL C# UDO prostřednictvím **soubor > Nový > projekt** v sadě Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Spouštění testovacích případů v sadě Visual Studio

Po vytvoření testovacího projektu můžete spustit všechny Testové případy ale **Průzkumníka testů > seznam stop**, nebo klikněte pravým tlačítkem na soubor .cs a zvolte **spustit testy**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Spouštění testovacích případů v aplikaci Visual Studio Team Service

Obě **projektů testování skriptů U-SQL** a **testovacích projektů C# UDO** dědit projektů testů jednotek C#. [Úlohy test sady Visual Studio](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) v sadě Visual Studio Team Services, můžete spustit tyto testovací případy. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Spouštění testovacích případů U-SQL ve službě Visual Studio Team Service

Pro testování U-SQL, ujistěte se, že načtete `CPPSDK` na sestavovacím počítači a pak je předat `CPPSDK` cestu k USqlScriptTestRunner (cppSdkFolderFullPath: @"").

**Co je CPPSDK?**

CPPSDK je balíček, který zahrnuje Microsoft Visual C++ 14 a sada Windows SDK 10.0.10240.0. Toto je prostředí, který je nezbytný modulem runtime U-SQL. Můžete získat tento balíček v Azure Data Lake Tools pro Visual Studio Instalační složka:

- Pro sadu Visual Studio 2015 je v části `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Pro Visual Studio 2017 je v části `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Příprava CPPSDK v agentovi sestavení Visual Studio Team Services**

Nejběžnější způsob přípravy CPPSDK závislostí ve Visual Studio Team Service je následujícím způsobem:

1.  Složky, která obsahuje knihovny CPPSDK ZIP.
2.  Zkontrolujte v souboru .zip do systému správy zdrojů. (Soubor .zip zajišťuje změnami všechny knihovny ve složce CPPSDK tak, že některé soubory nejsou ignoruje ".gitignore".)   
3.  Rozbalte soubor ZIP v kanálu sestavení.
4.  Bod `USqlScriptTestRunner` do této složky rozbaleny v počítači sestavení.

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>Spouštění jazyka C# UDO testovacích případů v sadě Visual Studio Team Services

Pro C# UDO test Ujistěte se, že chcete-li odkazovat na následující sestavení, které jsou potřeba pro UDO. Pokud se budete odkazovat na ně prostřednictvím [balíček Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), ujistěte se, že ve vašem kanálu sestavení přidáte úlohu obnovení NuGet.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Další postup

- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Spusťte skript U-SQL na místním počítači](data-lake-analytics-data-lake-tools-local-run.md)
- [Vývoj databáze U-SQL pomocí databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

