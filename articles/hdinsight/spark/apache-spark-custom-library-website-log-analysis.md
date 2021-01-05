---
title: Analýza webových protokolů pomocí knihoven Pythonu ve Sparku – Azure
description: Tento Poznámkový blok ukazuje, jak analyzovat data protokolu pomocí vlastní knihovny pomocí Sparku v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 61ec2db1799919eb395996b56d08b77e3be7ff5a
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822365"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analýza webových protokolů pomocí vlastní knihovny Pythonu s Apache Spark clusteru v HDInsight

Tento Poznámkový blok ukazuje, jak analyzovat data protokolu pomocí vlastní knihovny s Apache Spark v HDInsight. Vlastní knihovna, kterou používáme, je knihovna Pythonu s názvem **iislogparser.py**.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Uložení nezpracovaných dat jako RDD

V této části používáme Poznámkový blok [Jupyter](https://jupyter.org) přidružený ke clusteru Apache Spark ve službě HDInsight ke spouštění úloh, které zpracovávají nezpracovaná ukázková data a ukládají je jako tabulku podregistru. Vzorová data jsou soubor. CSV (hvac.csv), který je ve výchozím nastavení k dispozici ve všech clusterech.

Po uložení dat jako Apache Hive tabulky se v další části připojíme k tabulce podregistru pomocí nástrojů BI, jako je například Power BI a Tableau.

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net/jupyter` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. Vytvořte nový poznámkový blok. Vyberte **Nový** a potom **PySpark**.

    ![Vytvořit nový Jupyter Notebook Apache](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Vytvořit nový Jupyter Notebook")

1. Nový poznámkový blok se vytvoří a otevře s názvem Untitled.pynb. Vyberte název poznámkového bloku v horní části a zadejte popisný název.

    ![Zadání názvu poznámkového bloku](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Zadání názvu poznámkového bloku")

1. Vzhledem k tomu, že jste Poznámkový blok vytvořili pomocí jádra PySpark, nemusíte vytvářet žádné kontexty explicitně. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. Můžete začít importováním typů, které jsou požadovány pro tento scénář. Vložte následující fragment kódu do prázdné buňky a stiskněte **SHIFT + ENTER**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Vytvořte RDD pomocí ukázkových dat protokolu, která jsou už v clusteru dostupná. K datům ve výchozím účtu úložiště, který je přidružený ke clusteru, můžete přistupovat v `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Spusťte následující kód:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Načte ukázkovou sadu protokolů pro ověření, že předchozí krok byl úspěšně dokončen.

    ```pyspark
    logs.take(5)
    ```

    Měl by se zobrazit výstup podobný následujícímu textu:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analýza dat protokolu pomocí vlastní knihovny Pythonu

1. Ve výše uvedeném výstupu obsahuje první pár řádků informace záhlaví a každý zbývající řádek odpovídá schématu popsanému v této hlavičce. Analýza takových protokolů by mohla být složitá. Proto používáme vlastní knihovnu Pythonu (**iislogparser.py**), která zjednodušuje analýzu takových protokolů. Ve výchozím nastavení je tato knihovna součástí vašeho clusteru Spark v HDInsight na `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Tato knihovna však není v rozhraní, `PYTHONPATH` takže ji nemůžeme použít pomocí příkazu import, jako je `import iislogparser` . Chcete-li použít tuto knihovnu, je nutné ji distribuovat do všech pracovních uzlů. Spusťte následující fragment kódu.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` poskytuje funkci `parse_log_line` , která vrátí, `None` zda je řádek protokolu řádkem záhlaví a vrátí instanci `LogLine` třídy, pokud dojde k řádku protokolu. Použijte `LogLine` třídu k extrakci pouze řádků protokolu z RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Načtěte několik extrahovaných řádků protokolu, abyste ověřili, že krok byl úspěšně dokončen.

    ```pyspark
    logLines.take(2)
    ```

   Výstup by měl být podobný následujícímu textu:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. `LogLine`Třída zase obsahuje některé užitečné metody, jako `is_error()` například, což vrátí, zda záznam v protokolu obsahuje kód chyby. Tato třída slouží k výpočtu počtu chyb v extrahovaných řádcích protokolu a pak k zaznamenání všech chyb do jiného souboru.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    Výstup by měl být stav `There are 30 errors and 646 log entries` .

1. Pomocí **matplotlib** můžete také vytvořit vizualizaci dat. Například pokud chcete izolovat příčinu požadavků, které jsou spouštěny po dlouhou dobu, budete pravděpodobně chtít najít soubory, které mají největší čas k obsluze v průměru. Následující fragment kódu načte prvních 25 prostředků, které pomohly vyhovět žádosti.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Měl by se zobrazit výstup podobný následujícímu textu:

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

1. Tyto informace můžete také zobrazit ve formě grafu. Jako první krok k vytvoření grafu nám můžeme vytvořit dočasnou tabulku **AverageTime**. Tabulka seskupí protokoly podle času a zjistí, jestli v určitou dobu nedocházelo k nějakým neobvyklým špičkám latence.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Pak můžete spustit následující dotaz SQL a získat všechny záznamy v tabulce **AverageTime** .

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   `%%sql`Magic následovaný tím `-o averagetime` zajistí, že výstup dotazu je trvale uložen na serveru Jupyter (obvykle hlavnímu uzlu clusteru). Výstup je trvalý jako [PANDAS](https://pandas.pydata.org/) datový rámec se zadaným názvem **averagetime**.

   Měl by se zobrazit výstup podobný následujícímu obrázku:

   ![Výstup dotazu SQL HDInsight Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Výstup dotazu SQL")

   Další informace o Magic naleznete `%%sql` v tématu [Parameters Supported a%% SQL Magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Nyní můžete použít knihovnu matplotlib, která slouží k vytvoření vizualizace dat, k vytvoření grafu. Vzhledem k tomu, že je nutné vytvořit vykreslení z místně trvalého datového rámce **averagetime** , musí fragment kódu začínat `%%local` Magic. Tím se zajistí, že se kód spustí místně na serveru Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Měl by se zobrazit výstup podobný následujícímu obrázku:

   ![Diagram analýzy webového protokolu Apache Spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Výstup matplotlib")

1. Po dokončení spuštění aplikace byste měli Poznámkový blok vypnout a uvolnit tak prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tato akce vypne a zavře Poznámkový blok.

## <a name="next-steps"></a>Další kroky

Projděte si následující články:

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
