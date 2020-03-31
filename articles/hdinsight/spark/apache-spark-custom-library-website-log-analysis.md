---
title: Analýza protokolů webových stránek pomocí knihoven Pythonu ve Sparku – Azure
description: Tento poznámkový blok ukazuje, jak analyzovat data protokolu pomocí vlastní knihovny pomocí Spark na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552708"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analýza protokolů webových stránek pomocí vlastní knihovny Pythonu s clusterem Apache Spark na HDInsightu

Tento poznámkový blok ukazuje, jak analyzovat data protokolu pomocí vlastní knihovny s Apache Spark na HDInsight. Vlastní knihovna, kterou používáme, je knihovna Pythonu s názvem **iislogparser.py**.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Uložení nezpracovaných dat jako RDD

V této části používáme poznámkový blok [Jupyter](https://jupyter.org) přidružený ke clusteru Apache Spark v HDInsightu ke spuštění úloh, které zpracovávají nezpracovaná ukázková data, a ukládáme je jako tabulku Hive. Ukázková data jsou soubor .csv (hvac.csv) dostupný ve všech clusterech ve výchozím nastavení.

Jakmile se vaše data uloží jako tabulka Apache Hive, v další části se připojíme k tabulce Hive pomocí nástrojů BI, jako je Power BI a Tableau.

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/jupyter`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Vytvořte nový poznámkový blok. Vyberte **nový**a potom **PySpark**.

    ![Vytvoření nového notebooku Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Vytvoření nového poznámkového bloku Jupyter")

1. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Vhorní části vyberte název poznámkového bloku a zadejte popisný název.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Zadání názvu poznámkového bloku")

1. Vzhledem k tomu, že jste vytvořili poznámkový blok pomocí jádra PySpark, nemusíte vytvářet žádné kontexty explicitně. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. Můžete začít importem typů, které jsou požadovány pro tento scénář. Vložte do prázdné buňky následující úryvek a stiskněte **Shift + Enter**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Vytvořte rdd pomocí ukázkových dat protokolu, která jsou již v clusteru k dispozici. K datům můžete přistupovat ve výchozím účtu `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log`úložiště přidruženém ke clusteru na adrese . Spusťte následující kód:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Načtěte ukázkovou sadu protokolů a ověřte, zda byl předchozí krok úspěšně dokončen.

    ```pyspark
    logs.take(5)
    ```

    Měli byste vidět výstup podobný následujícímu textu:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analýza dat protokolu pomocí vlastní knihovny Pythonu

1. Ve výše uvedeném výstupu obsahuje prvních pár řádků informace záhlaví a každý zbývající řádek odpovídá schématu popsanému v tomto záhlaví. Analýza takových protokolů může být komplikovaná. Takže používáme vlastní knihovnu Pythonu (**iislogparser.py),** která usnadňuje analýzu takových protokolů. Ve výchozím nastavení je tato knihovna součástí `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py`clusteru Spark na adrese HDInsight.

    Tato knihovna však není `PYTHONPATH` v, takže ji nemůžeme použít `import iislogparser`pomocí příkazu import jako . Chcete-li použít tuto knihovnu, musíme ji distribuovat do všech pracovních uzlů. Spusťte následující úryvek.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser`poskytuje funkci, `parse_log_line` `None` která vrátí, pokud řádek protokolu je řádek `LogLine` záhlaví a vrátí instanci třídy, pokud narazí na řádek protokolu. Pomocí `LogLine` třídy extrahujte pouze řádky protokolu z RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Načtěte několik extrahovaných řádků protokolu a ověřte, zda byl krok úspěšně dokončen.

    ```pyspark
    logLines.take(2)
    ```

   Výstup by měl být podobný následujícímu textu:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. Třída `LogLine` má zase některé užitečné metody, `is_error()`jako je například , který vrátí, zda položka protokolu má kód chyby. Pomocí této třídy můžete vypočítat počet chyb v extrahovaných řádcích protokolu a potom všechny chyby zasílat do jiného souboru.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    Výstup by `There are 30 errors and 646 log entries`měl být ustavován .

1. **Matplotlib** můžete také použít k vytvoření vizualizace dat. Například pokud chcete izolovat příčinu požadavků, které běží po dlouhou dobu, můžete najít soubory, které se nejvíce čas sloužit v průměru. Výstřižek níže načte 25 nejlepších prostředků, které trvalo nejvíce času na zpracování požadavku.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Měli byste vidět výstup, jako je následující text:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Tyto informace můžete také prezentovat ve formě parcely. Jako první krok k vytvoření pozemku, pojďme nejprve vytvořit dočasnou tabulku **AverageTime**. Tabulka seskupuje protokoly podle času, aby zjistila, zda v určitém okamžiku došlo k neobvyklému nárůstu latence.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Potom můžete spustit následující dotaz SQL získat všechny záznamy v tabulce **AverageTime.**

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   Kouzlo `%%sql` následuje `-o averagetime` zajišťuje, že výstup dotazu je trvalé místně na serveru Jupyter (obvykle headnode clusteru). Výstup je trvalý jako datový rámec [Pandas](https://pandas.pydata.org/) se zadaným názvem **averagetime**.

   Měli byste vidět výstup, jako je následující obrázek:

   ![hdinsight jupyter sql výstup dotazu](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Výstup dotazu SQL")

   Další informace o `%%sql` magii naleznete v [tématu Parametry podporované %%sql magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Nyní můžete použít Matplotlib, knihovnu používanou k vytvoření vizualizace dat, k vytvoření obrázku. Vzhledem k tomu, že vykreslení musí být vytvořeno z místně trvalého datového rámce **averagetime,** musí fragment kódu začínat magicem. `%%local` Tím je zajištěno, že kód je spuštěn místně na serveru Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Měli byste vidět výstup, jako je následující obrázek:

   ![apache jiskra web log analýza plot](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib výstup")

1. Po dokončení spuštění aplikace byste měli poznámkový blok vypnout, abyste uvolnili prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tato akce zavře a zavře poznámkový blok.

## <a name="next-steps"></a>Další kroky

Prozkoumejte následující články:

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)