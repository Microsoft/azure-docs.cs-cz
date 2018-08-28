---
title: Vývoj U-SQL pomocí Pythonu, R a C# pro Azure Data Lake Analytics v aplikaci Visual Studio Code
description: Zjistěte, jak pomocí kódu Python, R a C# se odeslat úlohu ve službě Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 53859f5a81cf1d797ec93e83d75df5a329590dce
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051628"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Vývoj U-SQL pomocí Pythonu, R a C# pro Azure Data Lake Analytics v aplikaci Visual Studio Code
Zjistěte, jak používat Visual Studio Code (VSCode) pro zápis Python, R a C# kódu na pozadí pomocí U-SQL a odesílání úloh do služby Azure Data Lake. Další informace o Azure Data Lake Tools pro VSCode, naleznete v tématu [pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Před psaní vlastního kódu použití modelu code-behind, budete muset otevřít složku nebo pracovní prostor ve VSCode.


## <a name="prerequisites-for-python-and-r"></a>Požadavky na Pythonu a r.
Registrace Pythonu a r. sestavení rozšíření pro váš účet ADL. 
1. Otevřete svůj účet na portálu.
   - Vyberte **Přehled**. 
   - Klikněte na tlačítko **ukázkový skript**.
2. Klikněte na tlačítko **Další**.
3. Vyberte **nainstalovat rozšíření U-SQL**. 
4. Po instalaci rozšíření U-SQL, zobrazí se potvrzovací zpráva. 

  ![Nastavení prostředí pro python a r.](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > Pro nejlepší prostředí na službě jazyka Python nebo R Nainstalujte prosím VSCode Pythonu a r. rozšíření. 

## <a name="develop-python-file"></a>Vývoj soubor Pythonu
1. Klikněte na tlačítko **nový soubor** ve vašem pracovním prostoru.
2. Napište svůj kód v U-SQL. Následuje vzorový kód.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Klikněte pravým tlačítkem na soubor skriptu a pak vyberte **ADL: vygenerování kódu Pythonu za soubor**. 
4. **Xxx.usql.py** soubor je vygenerován ve své pracovní složce. Napište svůj kód v souboru Python. Následuje vzorový kód.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Klikněte pravým tlačítkem na **USQL** soubor, můžete kliknout na **kompilaci skriptu** nebo **odeslat úlohu** ke spuštění úlohy.

## <a name="develop-r-file"></a>Vývoj souboru R
1. Klikněte na tlačítko **nový soubor** ve vašem pracovním prostoru.
2. Napište svůj kód v souboru U-SQL. Následuje vzorový kód.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Klikněte pravým tlačítkem na **USQL** souboru a pak vyberte **ADL: generování kódu R za soubor**. 
4. **Xxx.usql.r** soubor je vygenerován ve své pracovní složce. Napište svůj kód v R souboru. Následuje vzorový kód.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klikněte pravým tlačítkem na **USQL** soubor, můžete kliknout na **kompilaci skriptu** nebo **odeslat úlohu** ke spuštění úlohy.

## <a name="develop-c-file"></a>Vývoj soubor jazyka C#
Použití modelu code-behind soubor je soubor jazyka C# přidružený jeden skript U-SQL. Můžete definovat skript vyhrazené pro UDO, UDA, UDT a systému souborů UDF v souboru kódu na pozadí. UDO, UDA, UDT a UDF lze použít přímo ve skriptu i bez registrace sestavení první. Použití modelu code-behind soubor je umístěn ve stejné složce jako jeho vytvoření partnerského vztahu soubor skriptu U-SQL. Pokud tento skript má název xxx.usql, je jako xxx.usql.cs název modelu code-behind. Pokud ručně odstraňte soubor kódu na pozadí, je zakázaná použití modelu code-behind pro jeho přidružené skript U-SQL. Další informace o psaní kódu zákazníka pro skript U-SQL najdete v tématu [psaní a použití vlastní kód v U-SQL: uživatelsky definovaných funkcí]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klikněte na tlačítko **nový soubor** ve vašem pracovním prostoru.
2. Napište svůj kód v souboru U-SQL. Následuje vzorový kód.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Klikněte pravým tlačítkem na **USQL** souboru a pak vyberte **ADL: vygenerovat CS za soubor s kódem**. 
4. **Xxx.usql.cs** soubor je vygenerován ve své pracovní složce. Napište svůj kód v souboru CS. Následuje vzorový kód.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Klikněte pravým tlačítkem na **USQL** soubor, můžete kliknout na **kompilaci skriptu** nebo **odeslat úlohu** ke spuštění úlohy.

## <a name="next-steps"></a>Další postup
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL místní spuštění a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Začínáme s Data Lake Analytics pomocí Powershellu](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí webu Azure portal](data-lake-analytics-get-started-portal.md)
* [Pomocí nástrojů Data Lake pro Visual Studio pro vývoj aplikací v U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
