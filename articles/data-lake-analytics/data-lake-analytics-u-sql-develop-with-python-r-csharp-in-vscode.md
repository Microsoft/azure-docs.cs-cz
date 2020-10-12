---
title: Spouštění úloh U-SQL v Pythonu, R a C# – Azure Data Lake Analytics
description: Naučte se používat kód za použití Pythonu, R a C# k odeslání úlohy v Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: 673c8db0b7c6d3d8658d2d3b6cfd7e00f3d7d986
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876202"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Vývoj U-SQL pomocí Pythonu, R a C# pro Azure Data Lake Analytics v Visual Studio Code
Naučte se používat Visual Studio Code (VSCode) k zápisu kódu Pythonu, R a C# za použití U-SQL a odesílání úloh do služby Azure Data Lake. Další informace o Azure Data Lakech nástrojích pro VSCode najdete v tématu [použití nástroje Azure Data Lake pro Visual Studio kódu](data-lake-analytics-data-lake-tools-for-vscode.md).

Před psaním vlastního kódu na pozadí musíte v VSCode otevřít složku nebo pracovní prostor.


## <a name="prerequisites-for-python-and-r"></a>Předpoklady pro Python a R
Zaregistrujte sestavení rozšíření Python a R pro váš účet ADL. 
1. Otevřete účet na portálu.
   - Vyberte **Přehled**. 
   - Klikněte na **ukázkový skript**.
2. Klikněte na **Další**.
3. Vyberte **instalovat rozšíření U-SQL**. 
4. Po instalaci rozšíření U-SQL se zobrazí potvrzovací zpráva. 

   ![Nastavení prostředí pro Python a R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Pro lepší zkušenosti se službou Python a jazyka R prosím nainstalujte VSCode Python a R Extension. 

## <a name="develop-python-file"></a>Vývoj souboru Pythonu
1. Klikněte na **nový soubor** v pracovním prostoru.
2. Napište svůj kód do U-SQL. Následuje ukázka kódu.
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
    
3. Klikněte pravým tlačítkem na soubor skriptu a pak vyberte **ADL: vygenerujte soubor kódu Pythonu na pozadí**. 
4. Soubor **xxx.usql.py** se vygeneruje v pracovní složce. Napište svůj kód do souboru Python. Následuje ukázka kódu.

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
5. Klikněte pravým tlačítkem na soubor **USQL** , můžete kliknout na **kompilovat skript** nebo **Odeslat úlohu** do spuštěné úlohy.

## <a name="develop-r-file"></a>Vývoj souboru R
1. Klikněte na **nový soubor** v pracovním prostoru.
2. Napište svůj kód do souboru U-SQL. Následuje ukázka kódu.
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
3. Klikněte pravým tlačítkem na soubor **USQL** a pak vyberte **ADL: vygenerujte soubor kódu R na pozadí**. 
4. Soubor **xxx. usql. r** je vygenerován v pracovní složce. Napište svůj kód do souboru R. Následuje ukázka kódu.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klikněte pravým tlačítkem na soubor **USQL** , můžete kliknout na **kompilovat skript** nebo **Odeslat úlohu** do spuštěné úlohy.

## <a name="develop-c-file"></a>Vývoj souboru C#
Soubor kódu na pozadí je soubor C# přidružený k jednomu skriptu U-SQL. V souboru kódu na pozadí můžete definovat skript vyhrazený pro UDO, UDA, UDT a UDF. UDO, UDA, UDT a UDF lze použít přímo ve skriptu bez registrace sestavení jako první. Soubor kódu na pozadí se umístí do stejné složky jako partnerský soubor skriptu U-SQL. Pokud má skript název xxx. usql, kód na pozadí se jmenuje jako xxx.usql.cs. Pokud soubor kódu na pozadí odstraníte ručně, funkce kódu na pozadí je zakázána pro svůj přidružený skript U-SQL. Další informace o psaní kódu zákazníka pro skript U-SQL najdete v tématu [zápis a použití vlastního kódu v u-SQL: User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klikněte na **nový soubor** v pracovním prostoru.
2. Napište svůj kód do souboru U-SQL. Následuje ukázka kódu.
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
3. Klikněte pravým tlačítkem na soubor **USQL** a pak vyberte **ADL: vygenerujte soubor cs za kódem**. 
4. Soubor **xxx.usql.cs** se vygeneruje v pracovní složce. Napište svůj kód do souboru CS. Následuje ukázka kódu.

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
5. Klikněte pravým tlačítkem na soubor **USQL** , můžete kliknout na **kompilovat skript** nebo **Odeslat úlohu** do spuštěné úlohy.

## <a name="next-steps"></a>Další kroky
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Místní spuštění U-SQL a místní ladění pomocí Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Začínáme s Data Lake Analytics pomocí prostředí PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md)
* [Použití Data Lake nástrojů pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití katalogu Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
