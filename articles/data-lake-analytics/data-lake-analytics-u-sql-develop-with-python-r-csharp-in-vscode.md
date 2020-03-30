---
title: Spouštění úloh U-SQL v Pythonu, R a C# – Azure Data Lake Analytics
description: Naučte se používat kód za sebou s Python, R a C# k odeslání úlohy v Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309713"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Vývoj U-SQL s Pythonem, R a C# pro Azure Data Lake Analytics v kódu Visual Studia
Naučte se používat Visual Studio Code (VSCode) k zápisu kódu Pythonu, R a C# s U-SQL a odeslání úloh do služby Azure Data Lake. Další informace o nástrojích Azure Data Lake Tools for VSCode najdete [v tématu Použití nástrojů datového jezera Azure pro kód Visual Studia](data-lake-analytics-data-lake-tools-for-vscode.md).

Před zápisem vlastního kódu na pozadí je třeba otevřít složku nebo pracovní prostor ve VSCode.


## <a name="prerequisites-for-python-and-r"></a>Předpoklady pro Python a R
Zaregistrujte Python a Rozšíření R sestavení pro váš účet ADL. 
1. Otevřete svůj účet na portálu.
   - Vyberte **Přehled**. 
   - Klepněte na **položku Ukázkový skript**.
2. Klikněte na **Další**.
3. Vyberte **možnost Instalovat rozšíření U-SQL**. 
4. Po instalaci rozšíření U-SQL se zobrazí potvrzovací zpráva. 

   ![Nastavení prostředí pro python a R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Pro nejlepší zkušenosti s jazykovou službou Python a R nainstalujte rozšíření VSCode Python a R. 

## <a name="develop-python-file"></a>Vývoj souboru Pythonu
1. Klikněte na **Nový soubor** v pracovním prostoru.
2. Napište kód v U-SQL. Následuje ukázka kódu.
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
    
3. Klepněte pravým tlačítkem myši na soubor skriptu a vyberte **příkaz ADL: Generovat kód Pythonu za souborem**. 
4. Soubor **xxx.usql.py** je generován v pracovní složce. Napište kód do souboru Pythonu. Následuje ukázka kódu.

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
5. Klikněte pravým tlačítkem myši do souboru **USQL,** můžete kliknout na **kompilovat skript** nebo **odeslat úlohu** do spuštěné úlohy.

## <a name="develop-r-file"></a>Vývoj souboru R
1. Klikněte na **Nový soubor** v pracovním prostoru.
2. Napište kód do souboru U-SQL. Následuje ukázka kódu.
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
3. Klepněte pravým tlačítkem myši do souboru **USQL** a vyberte **možnost ADL: Generovat kód R za souborem**. 
4. Soubor **xxx.usql.r** je generován ve vaší pracovní složce. Napište kód do souboru R. Následuje ukázka kódu.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klikněte pravým tlačítkem myši do souboru **USQL,** můžete kliknout na **kompilovat skript** nebo **odeslat úlohu** do spuštěné úlohy.

## <a name="develop-c-file"></a>Vývoj souboru C#
Soubor s kódem na pozadí je soubor C# přidružený k jednomu skriptu U-SQL. V souboru s kódem na pozadí můžete definovat skript vyhrazený pro UDO, UDA, UDT a UDF. UDO, UDA, UDT a UDF lze použít přímo ve skriptu bez registrace sestavení jako první. Soubor s kódem na pozadí je umístěn ve stejné složce jako jeho peering U-SQL skript soubor. Pokud je skript s názvem xxx.usql, kód na pozadí je pojmenován jako xxx.usql.cs. Pokud ručně odstraníte soubor s kódem na pozadí, funkce kódu na pozadí je zakázána pro jeho přidružené U-SQL skript. Další informace o psaní zákaznického kódu pro skript U-SQL naleznete [v tématu Zápis a použití vlastního kódu v u-SQL: Uživatelem definované funkce]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klikněte na **Nový soubor** v pracovním prostoru.
2. Napište kód do souboru U-SQL. Následuje ukázka kódu.
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
3. Klepněte pravým tlačítkem myši do souboru **USQL** a vyberte **možnost ADL: Generate CS Code Behind File**. 
4. Soubor **xxx.usql.cs** je generován v pracovní složce. Napište svůj kód do souboru CS. Následuje ukázka kódu.

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
5. Klikněte pravým tlačítkem myši do souboru **USQL,** můžete kliknout na **kompilovat skript** nebo **odeslat úlohu** do spuštěné úlohy.

## <a name="next-steps"></a>Další kroky
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Místní spuštění u-SQL a místní ladění s kódem sady Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Začínáme s Analýzou datových jezer pomocí PowerShellu](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Analýzou datového jezera pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Použití nástrojů data lake pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití katalogu Data Lake Analytics(U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
