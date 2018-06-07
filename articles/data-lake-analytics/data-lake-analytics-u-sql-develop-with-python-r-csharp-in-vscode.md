---
title: Vývoj U-SQL s Python, R a C# pro Azure Data Lake Analytics v kódu aplikace Visual Studio
description: Další informace o použití kódu na pozadí s Python, R a C# se odeslat úlohu v Azure Data Lake.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 171aef186fd681adf9b3d92deb8691c852ea1038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624903"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Vývoj U-SQL s Python, R a C# pro Azure Data Lake Analytics v kódu aplikace Visual Studio
Naučte se používat Visual Studio Code (VSCode) pro zápis Python, R a C# kódu na pozadí pomocí U-SQL a odesílání úloh do služby Azure Data Lake. Další informace o nástrojů Azure Data Lake pro VSCode najdete v tématu [pomocí nástroje Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Před zápisem kódu vlastní kód, budete muset otevřít složku nebo pracovního prostoru v VSCode.


## <a name="prerequisites-for-python-and-r"></a>Předpoklady pro Python a R
Zaregistrujte Python a R sestavení rozšíření pro váš účet ADL. 
1. Otevřete svůj účet na portálu.
   - Vyberte **Přehled**. 
   - Klikněte na tlačítko **ukázkový skript**.
2. Klikněte na tlačítko **Další**.
3. Vyberte **instalovat rozšíření U-SQL**. 
4. Po instalaci rozšíření U-SQL se zobrazí potvrzovací zpráva. 

  ![Nastavení prostředí pro python a R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

  > [!Note]
  > Nejlepší prostředí na služba jazyka Python a R nainstalujte VSCode Python a R rozšíření. 

## <a name="develop-python-file"></a>Vytvořte soubor Python
1. Klikněte **nový soubor** v pracovním prostoru.
2. Zápis kódu v U-SQL. Zde je ukázka kódu.
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
    
3. Klikněte pravým tlačítkem na soubor skriptu a potom vyberte **ADL: generování kódu Python za soubor**. 
4. **Xxx.usql.py** souboru se generuje ve pracovní složky. Zápis kódu v souboru Python. Zde je ukázka kódu.

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
5. Klikněte pravým tlačítkem na **USQL** souboru, můžete kliknout na **zkompilovat skriptu** nebo **odeslat úlohu** spuštěná úloha.

## <a name="develop-r-file"></a>Vytvořte soubor R
1. Klikněte **nový soubor** v pracovním prostoru.
2. Zápis kódu v souboru U-SQL. Zde je ukázka kódu.
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
3. Klikněte pravým tlačítkem na **USQL** souboru a potom vyberte **ADL: generovat kód R za soubor**. 
4. **Xxx.usql.r** souboru se generuje ve pracovní složky. Zápis kódu v souboru R. Zde je ukázka kódu.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klikněte pravým tlačítkem na **USQL** souboru, můžete kliknout na **zkompilovat skriptu** nebo **odeslat úlohu** spuštěná úloha.

## <a name="develop-c-file"></a>Vývoj souboru C#
Soubor kódu je soubor C# přidružené jednoho skriptu U-SQL. Můžete definovat skript vyhrazené UDO, UDA, UDT a UDF v souboru kódu na pozadí. UDO, UDA, UDT a UDF lze přímo ve skriptu bez nejprve registrace sestavení. Soubor kódu je uvést ve stejné složce jako jeho partnerského vztahu soubor skriptu U-SQL. Pokud tento skript má název xxx.usql, název modelu code-behind jako xxx.usql.cs. Pokud odstraníte ručně souboru kódu na pozadí, je zakázaná kódu pro jeho přidružené skript U-SQL. Další informace o psaní kódu zákazníka pro skript U-SQL najdete v tématu [zápis a pomocí vlastní kód v U-SQL: funkce definované uživatelem]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Klikněte **nový soubor** v pracovním prostoru.
2. Zápis kódu v souboru U-SQL. Zde je ukázka kódu.
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
3. Klikněte pravým tlačítkem na **USQL** souboru a potom vyberte **ADL: generování kódu CS za soubor**. 
4. **Xxx.usql.cs** souboru se generuje ve pracovní složky. Zápis kódu v souboru CS. Zde je ukázka kódu.

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
5. Klikněte pravým tlačítkem na **USQL** souboru, můžete kliknout na **zkompilovat skriptu** nebo **odeslat úlohu** spuštěná úloha.

## <a name="next-steps"></a>Další postup
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL místní spuštění a místní ladění s kódem jazyka Visual Studio](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Začínáme s Data Lake Analytics pomocí prostředí PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Pomocí nástrojů Data Lake pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
