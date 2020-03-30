---
title: Příklad strojového učení se Sparkm MLlib na HDInsight – Azure
description: Naučte se používat Spark MLlib k vytvoření aplikace pro strojové učení, která analyzuje datovou sadu pomocí klasifikace prostřednictvím logistické regrese.
keywords: strojové učení, příklad strojového učení
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494095"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Použití Apache Spark MLlib k vytvoření aplikace pro strojové učení a analýze datové sady

Naučte se používat Apache Spark [MLlib](https://spark.apache.org/mllib/) k vytvoření aplikace strojového učení pro jednoduchou prediktivní analýzu otevřené datové sady. Z integrovaných knihoven strojového učení Spark tento příklad používá *klasifikaci* prostřednictvím logistické regrese. 

MLlib je základní knihovna Spark, která poskytuje mnoho nástrojů užitečných pro úlohy strojového učení, včetně nástrojů, které jsou vhodné pro:

* Classification
* Regrese
* Clustering
* Modelování témat
* Rozklad singulární hodnoty (SVD) a analýza hlavních součástí (PCA)
* Testování hypotéz a výpočet statistiky vzorků

## <a name="understand-classification-and-logistic-regression"></a>Porozumět klasifikaci a logistické regresi
*Klasifikace*, populární úloha strojového učení, je proces třídění vstupních dat do kategorií. Úlohou klasifikačního algoritmu je zjistit, jak přiřadit "popisky" vstupním datům, která zadáte. Můžete si například myslet algoritmus strojového učení, který přijímá informace o akciích jako vstup a rozděluje akcie do dvou kategorií: akcie, které byste měli prodat, a akcie, které byste měli zachovat.

Logistická regrese je algoritmus, který používáte pro klasifikaci. Logistické regresní rozhraní API Spark je užitečné pro *binární klasifikaci*nebo klasifikaci vstupních dat do jedné ze dvou skupin. Další informace o logistických regresích naleznete na [Wikipedii](https://en.wikipedia.org/wiki/Logistic_regression).

Stručně řečeno, proces logistické regrese vytváří *logistickou funkci,* kterou lze použít k předvídání pravděpodobnosti, že vstupní vektor patří do jedné nebo druhé skupiny.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Příklad prediktivní analýzy údajů o kontrole potravin
V tomto příkladu použijete Spark k provedení prediktivní analýzy dat kontroly potravin **(Food_Inspections1.csv),** která byla získána prostřednictvím [datového portálu Město Chicago](https://data.cityofchicago.org/). Tento soubor údajů obsahuje informace o inspekcích potravinářských zařízení, které byly provedeny v Chicagu, včetně informací o každém zařízení, zjištěných porušeních (pokud existuje) a výsledcích inspekce. Datový soubor CSV je již k dispozici v účtu úložiště přidruženém ke clusteru na adrese **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

V následujících krocích vyvinete model, abyste zjistili, co je zapotřebí k provedení nebo selhání kontroly potravin.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Vytvoření aplikace apache spark mllib strojového učení

1. Vytvořte poznámkový blok Jupyter pomocí jádra PySpark. Pokyny najdete v tématu [Vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte **klávesu SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Z důvodu jádra PySpark není nutné explicitně vytvářet žádné kontexty. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. 

## <a name="construct-the-input-dataframe"></a>Vytvoření vstupního datového rámce

Vzhledem k tomu, že nezpracovaná data jsou ve formátu CSV, můžete použít kontext Spark k natáknou soubor do paměti jako nestrukturovaný text a potom pomocí knihovny CSV v Pythonu analyzovat každý řádek dat.

1. Spusťte následující řádky a vytvořte odolnou distribuovanou datovou sadu (RDD) importem a analýzou vstupních dat.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Spusťte následující kód pro načtení jednoho řádku z RDD, takže se můžete podívat na schéma dat:

    ```PySpark
    inspections.take(1)
    ```

    Výstup bude následující:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Výstup poskytuje představu o schématu vstupního souboru. Obsahuje mimo jiné název každého zařízení, typ zařízení, adresu, údaje o inspekcích a místo. 

3. Spusťte následující kód a vytvořte datový rámec (*df*) a dočasnou tabulku (*CountResults*) s několika sloupci, které jsou užitečné pro prediktivní analýzu. `sqlContext`se používá k provádění transformací na strukturovaných datech. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Čtyři sloupce zájmu v datovém rámci jsou **id**, **název**, **výsledky**a **porušení**.

4. Spusťte následující kód, abyste získali malý vzorek dat:

    ```PySpark
    df.show(5)
    ```

    Výstup bude následující:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Pochopení dat

Začněme získat představu o tom, co obsahuje datová sada. 

1. Spusťte následující kód, který zobrazí odlišné hodnoty ve sloupci **výsledků:**

    ```PySpark
    df.select('results').distinct().show()
    ```

    Výstup bude následující:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Chcete-li vizualizovat distribuci těchto výsledků, spusťte následující kód:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Kouzlo `%%sql` následuje `-o countResultsdf` zajišťuje, že výstup dotazu je trvalé místně na serveru Jupyter (obvykle headnode clusteru). Výstup je trvalý jako datový rámec [Pandas](https://pandas.pydata.org/) se zadaným **názvem countResultsdf**. Další informace o `%%sql` magii a dalších kouzlech dostupných v jádře PySpark najdete [v tématu Jádra dostupná na poznámkových blocích Jupyter u clusterů Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Výstup bude následující:

    ![Výstup dotazu SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Výstup dotazu SQL")


3. Můžete také použít [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), knihovnu používanou k vytvoření vizualizace dat, k vytvoření obrázku. Vzhledem k tomu, že vykreslení musí být vytvořeno z místně trvalého datového `%%local` rámce **countResultsdf,** musí fragment kódu začínat magicem. Tím je zajištěno, že kód je spuštěn místně na serveru Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Výstup bude následující:

    ![Výstup aplikace pro strojové učení spark - výsečový graf s pěti odlišnými výsledky kontroly](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Výstup výsledků strojového učení jiskry")

    Chcete-li předpovědět výsledek kontroly potravin, musíte vytvořit model založený na porušení. Vzhledem k tomu, že logistická regrese je binární klasifikační metoda, má smysl seskupit výsledná data do dvou kategorií: **Selhání** a **Průchod**:

   - Předat
       - Předat
       - Pass w/ podmínky
   - Neúspěch
       - Neúspěch
   - Zahodit
       - Firma se nenachází
       - Z podnikání

     Data s ostatními výsledky ("Firma není umístěna" nebo "Out of Business") nejsou užitečné a stejně tvoří velmi malé procento výsledků.

4. Spusťte následující kód a převeďte existující datový rámec(`df`) na nový datový rámec, kde je každá kontrola reprezentována jako dvojice porušení popisků. V tomto případě popisek `0.0` představuje selhání, `1.0` popisek představuje úspěch a `-1.0` popisek představuje některé výsledky kromě těchto dvou. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Spusťte následující kód, který zobrazí jeden řádek označených dat:

    ```PySpark
    labeledData.take(1)
    ```

    Výstup bude následující:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Vytvoření logistického regresního modelu ze vstupního datového rámce

Konečným úkolem je převést označená data do formátu, který lze analyzovat logistickou regresí. Vstup emitačního logistického regresního algoritmu musí být sada *vektorových párů label-feature*, kde "vektor prvku" je vektor čísel představujících vstupní bod. Takže musíte převést sloupec "porušení", který je částečně strukturovaný a obsahuje mnoho komentářů ve volném textu, na řadu reálných čísel, kterým by stroj mohl snadno pochopit.

Jeden standardní přístup strojového učení pro zpracování přirozeného jazyka je přiřadit každé odlišné slovo "index" a pak předat vektor algoritmu strojového učení tak, aby hodnota každého indexu obsahuje relativní frekvenci tohoto slova v textovém řetězci.

MLlib poskytuje snadný způsob, jak tuto operaci provést. Za prvé, "tokenize" každý řetězec porušení získat jednotlivá slova v každém řetězci. Potom použijte `HashingTF` k převodu každé sady tokenů na vektor prvku, který pak může být předán algoritmu logistické regrese k vytvoření modelu. Všechny tyto kroky provádíte v pořadí pomocí "kanálu".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Vyhodnocení modelu pomocí jiné datové sady

Model, který jste vytvořili dříve, můžete použít k *předvídání* výsledků nových kontrol na základě zjištěných porušení. Tento model jste vycvičili v datové sadě **Food_Inspections1.csv**. Můžete použít druhou datovou **sadu, Food_Inspections2.csv**, k *vyhodnocení* síly tohoto modelu na nová data. Tato druhá sada dat **(Food_Inspections2.csv**) je ve výchozím kontejneru úložiště přidruženém ke clusteru.

1. Spusťte následující kód k vytvoření nového datového rámce, **predictionsDf,** který obsahuje předpověď generované modelem. Výstřižek také vytvoří dočasnou tabulku s názvem **Předpovědi** na základě datového rámce.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Zobrazený výstup by měl vypadat asi takto:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Podívejte se na jednu z předpovědí. Spusťte tento úryvek:

    ```PySpark
    predictionsDf.take(1)
    ```

   Existuje předpověď pro první položku v sadě testovacích dat.
1. Metoda `model.transform()` použije stejnou transformaci na všechna nová data se stejným schématem a dorazí k předpovědi, jak klasifikovat data. Můžete udělat několik jednoduchých statistik získat představu o tom, jak přesné předpovědi byly:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Výstup vypadá takto:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Použití logistické regrese se Sparkem vám dává přesný model vztahu mezi popisy porušení v angličtině a tím, zda by daný podnik prošel nebo neprošel kontrolou potravin.

## <a name="create-a-visual-representation-of-the-prediction"></a>Vytvoření vizuální reprezentace předpovědi
Nyní můžete vytvořit konečnou vizualizaci, která vám pomůže důvod o výsledcích tohoto testu.

1. Můžete začít extrahování různé předpovědi a výsledky z **předpovědi** dočasné tabulky vytvořené dříve. Následující dotazy oddělují výstup jako *true_positive*, *false_positive*, *true_negative*a *false_negative*. V níže uvedených dotazech vypnete `-q` vizualizaci pomocí a také `-o`uložíte výstup (pomocí) jako `%%local` datové rámce, které lze pak použít s magicem.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Nakonec použijte následující úryvek ke generování obrázku pomocí **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Měl by se zobrazit následující výstup:

    ![Spark strojové učení výstup aplikace - koláč graf procenta neúspěšných kontrol potravin.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Výstup výsledků strojového učení jiskry")

    V tomto grafu se "pozitivní" výsledek týká neúspěšné kontroly potravin, zatímco negativní výsledek se týká schválené inspekce.

## <a name="shut-down-the-notebook"></a>Vypnutí poznámkového bloku
Po dokončení spuštění aplikace byste měli poznámkový blok vypnout, abyste uvolnili prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tím se vypne a zavře notebook.

## <a name="see-also"></a><a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
