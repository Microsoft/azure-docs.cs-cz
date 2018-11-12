---
title: Příklad Machine learning s MLlib Spark v HDInsight – Azure
description: Další informace o použití Spark MLlib k vytvoření aplikace machine learning, která analyzuje datové sady pomocí klasifikace prostřednictvím logistické regrese.
keywords: Spark machine learning, spark machine learning příklad
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e553833f8b9a5daab5c454cea628acdda0320e76
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257635"
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Knihovna Spark MLlib používat k sestavení služby machine learning aplikací a analyzovat datové sady

Další informace o použití Sparku [MLlib](https://spark.apache.org/mllib/) k vytvoření služby machine learning aplikace provést jednoduché prediktivní analýzy pro otevřete datovou sadu. Spark integrované strojového učení knihovny, tento příklad používá *klasifikace* prostřednictvím logistické regrese. 

> [!TIP]
> V tomto příkladu jsou také dostupné jako poznámkový blok Jupyter v clusteru Spark (Linux), který vytvoříte v HDInsight. Plnohodnotném poznámkovém bloku umožňuje spouštět fragmenty kódu Pythonu z poznámkového bloku samotný. Chcete-li postupovat podle kurzu z v rámci poznámkového bloku, vytvoření clusteru Spark a spustit Poznámkový blok Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Spusťte Poznámkový blok **Spark Machine Learning – prediktivní analýzy na data kontroly potravin pomocí MLlib.ipynb** pod **Python** složky.
>
>

MLlib je knihovna Spark core, která poskytuje řadu nástrojů, které jsou užitečné pro úlohy strojového učení, včetně nástroje, které jsou vhodné pro:

* Klasifikace
* Regrese
* Clustering
* Téma modelování
* Rozložené singulární hodnotu (SVD) a analýzy hlavních komponentách (DPS)
* Předpoklad testování a výpočtu statistik vzorku

## <a name="understand-classification-and-logistic-regression"></a>Klasifikace a logistické regrese
*Klasifikace*, oblíbené služby machine learning úkol, je proces řazení do kategorií vstupní data. Je úloha klasifikační algoritmus, zjistěte, jak přiřadit "popisky" pro vstupní data, která zadáte. Například může představit algoritmu strojového učení, který přijímá jako vstup základní informace a stock rozdělí do dvou kategorií:, které by měl prodávat a populací, které byste měli mít.

Logistické regrese je algoritmus, který používáte pro klasifikaci. Spark logistické regrese rozhraní API je užitečné pro *binární klasifikace*, nebo klasifikaci vstupní data do jedné ze dvou skupin. Další informace o logistické regrese, naleznete v tématu [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Stručně řečeno, vytvoří proces logistickou regresi *logistické funkce* , který lze použít k predikci pravděpodobnosti, že vstupní vektoru patří do jedné skupiny, nebo druhé.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Příklad prediktivní analýzy na data kontroly potravin
V tomto příkladu pomocí Spark provádět některé prediktivní analýzy dat kontroly potravin (**Food_Inspections1.csv**), který byl získán prostřednictvím [portál data město Chicago](https://data.cityofchicago.org/). Tato datová sada obsahuje informace o kontroly potravin zařízení, které byly provedeny v Chicagu, včetně informací o jednotlivých zařízení (pokud existuje) bylo zjištěno narušení a výsledky kontroly. Datový soubor CSV je již k dispozici v účtu úložiště přidruženého clusteru na **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

V následujících krocích vývoj modelu, pokud chcete zobrazit, co je potřeba k úspěšné nebo neúspěšné kontroly potravin.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Vytvoření aplikace Spark MLlib machine learning

1. Vytvořte poznámkový blok Jupyter pomocí jádra PySpark. Pokyny najdete v tématu [Vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte klávesu **SHIRT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Z důvodu jádra PySpark není nutné explicitně tvořit kontexty. Kontexty Spark a Hive se automaticky vytvoří za vás při spuštění první buňky kódu. 

## <a name="construct-the-input-dataframe"></a>Vytvořit vstupní datový rámec

Vzhledem k tomu, že nezpracovaná data se ve formátu CSV, můžete o přijetí změn souboru do paměti jako nestrukturovaného textu pomocí kontextu Spark a pak použít k analýze každý řádek dat knihovna Python pro sdílený svazek clusteru.

1. Spusťte následující řádky k vytvoření odolné Distributed Dataset (RDD) tak, že import a analýza vstupní data.

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

2. Spusťte následující kód k načtení jeden řádek z RDD, aby mohl podívat schématu dat:

    ```PySpark
    inspections.take(1)
    ```

    Výstup bude:

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

    Výstup obsahuje představu o schéma vstupního souboru. Obsahuje název každé zařízení, na typu zařízení, adresu, data z kontrol a umístění, mimo jiné. 

3. Spuštěním následujícího kódu vytvořte datový rámec (*df*) a dočasnou tabulku (*CountResults*) s několika sloupců, které jsou užitečné pro prediktivní analýzu. `sqlContext` slouží k provádění transformací na strukturovaná data. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Čtyři sloupce zájem datového rámce jsou **id**, **název**, **výsledky**, a **porušení**.

4. Spusťte následující kód slouží k získání malým vzorkem dat:

    ```PySpark
    df.show(5)
    ```

    Výstup bude:

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

Začněme představu, co obsahuje datovou sadu. 

1. Spusťte následující kód k zobrazení jedinečných hodnot v **výsledky** sloupce:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Výstup bude:

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

2. Spusťte následující kód k vizualizaci distribuce tyto výsledky:

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    `%%sql` Magic, za nímž následuje `-o countResultsdf` zajistí, že výstup dotazu se ukládají místně na serveru Jupyter (obvykle hlavního uzlu clusteru). Výstup se ukládají jako [Pandas](http://pandas.pydata.org/) datový rámec se zadaným názvem **countResultsdf**. Další informace o magických příkazech `%%sql` a dalších magických příkazech, které jsou k dispozici s jádrem PySpark, najdete v části [Jádra dostupná v poznámkových blocích Jupyter s clustery Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Výstup bude:

    ![Výstup dotazu SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "výstup dotazu SQL")


3. Můžete také použít [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), knihovna použitý k vytvoření vizualizace dat, k vytvoření vykreslení. Protože vykreslení musí být vytvořeny z místně trvalý **countResultsdf** datového rámce, fragment kódu musí začínat `%%local` magic. Tím se zajistí, že je kód spuštěn místně na serveru Jupyter.

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

    Výstup bude:

    ![Výstup Spark machine learningu aplikace - výsečový graf s pěti různých výsledků](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark machine learningu výstupu výsledku")

    Existují 5 odlišné výsledky, které mohou mít kontrolu:

    - Firmy, které se nenachází
    - Chyba
    - Úspěch
    - Předejte plánovaným bodem obnovení kratším podmínky
    - Nevyvíjí obchodní činnost

    Pro předpověď výsledků kontroly potravin, budete muset vyvinout model založený na porušení zásad. Protože logistické regrese je metoda binární klasifikace, je vhodné seskupit Výsledná data do dvou kategorií: **selhání** a **předat**:

    - Úspěch
        - Úspěch
        - Předejte plánovaným bodem obnovení kratším podmínky
    - Chyba
        - Chyba
    - Zahodit
        - Firmy, které se nenachází
        - Nevyvíjí obchodní činnost

    Nejsou užitečné údaje s další výsledky ("Obchodní není umístěný" nebo "obchodní z") a jsou velmi malé procento výsledky přesto provést.

4. Spusťte následující kód pro převod existujícího datového rámce (`df`) do struktury dataframe nové, kde každé kontrole reprezentované jako dvojici popisek porušení. V tomto případě popisek `0.0` představuje selhání popisek `1.0` představuje úspěch a jako popisek `-1.0` představuje některé výsledky kromě těchto dvou. 

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

5. Spusťte následující kód, který zobrazí jeden řádek s popisky dat:

    ```PySpark
    labeledData.take(1)
    ```

    Výstup bude:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Vytvoření modelu logistické regrese ze vstupního datového rámce

Posledním úkolem je označené data převést do formátu, který mohou být analyzovány pomocí logistické regrese. Vstup algoritmu logistické regrese musí být sada *funkce popisků vektoru dvojice*, kde vektoru"funkce" představuje vektor čísel vstupní bod. Ano musíte převést sloupci "porušení", který je částečně strukturovaných a obsahuje mnoho komentáře v prostého textu, do pole reálná čísla, které může snadno pochopit na počítači.

Jeden standardní strojového učení pro zpracování přirozeného jazyka přístup je k přiřazení všech jedinečných slov "index" a pak předejte vektor se strojovým učením algoritmus, tak, aby každý index hodnota obsahuje relativní četnost toto slovo v textovém řetězci.

MLlib poskytuje snadný způsob, jak provést tuto operaci. Nejprve "tokenizaci" každý řetězec narušení zobrazíte jednotlivých slov v každém řetězci. Potom použijte `HashingTF` k převedení každou sadu tokeny do funkce vektoru, který je pak možné předat algoritmu logistické regrese k vytvoření modelu. Proveďte všechny kroky v pořadí pomocí "kanál".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Vyhodnocení modelu pomocí jiné datové sady

Můžete použít model, který jste vytvořili dříve na *předpovědět* co nových kontrol, budou výsledky, založené na porušení zásad, které byly dodrženy. Školení tento model na datové sadě **Food_Inspections1.csv**. Můžete použít datové sady druhé **Food_Inspections2.csv**do *vyhodnotit* sílu tento model na nová data. Druhé sadě dat (**Food_Inspections2.csv**) je ve výchozím kontejneru úložiště přidružené ke clusteru.

1. Spusťte následující kód k vytvoření nového datového rámce, **predictionsDf** předpovědi generované modelem, který obsahuje. Fragment kódu vytvoří dočasné tabulky nazývané také **předpovědi** podle datového rámce.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Zobrazený výstup by měl vypadat asi takto:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

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

1. Podívejte se na jednu předpovědí. Spusťte tento fragment kódu:

    ```PySpark
    predictionsDf.take(1)
    ```

   Není k dispozici předpovědi pro první položku v sadě dat testu.
1. `model.transform()` Metoda vztahuje stejnou transformaci na žádná nová data se stejným schématem a získat predikce toho, jak klasifikovat data. Můžete provést některé jednoduché statistické představu, jak přesný bylo předpovědí:

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
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Logistické regrese pomocí Spark poskytuje model přesné relace mezi porušení popisy v angličtině a určuje, zda by daný obchodní úspěšné nebo neúspěšné kontroly potravin.

## <a name="create-a-visual-representation-of-the-prediction"></a>Vytvoření vizuální znázornění do predikce.
Nyní můžete vytvořit finální vizualizace umožňující odůvodnitelný výsledky tohoto testu.

1. Začnete tím, že extrahování různých předpovědi a výsledky z **předpovědi** dočasnou tabulku vytvořili dříve. Následující dotazy oddělení výstup jako *true_positive*, *false_positive*, *true_negative*, a *false_negative*. V níže uvedené dotazy vypnete vizualizace s využitím `-q` a také uložit výstup (s použitím `-o`) jako datových rámců, který lze potom použít s `%%local` magic.

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

1. Nakonec použijte následující fragment kódu a vytvořte pomocí vykreslení **Matplotlib**.

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

    ![Spark machine learningu výstup aplikace – procenta výsečový graf neúspěšných potravin kontrol. ](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark machine learningu výstupu výsledku")

    V tomto grafu "pozitivní" výsledek odkazuje na potravin neúspěšné kontroly, zatímco záporný výsledek odkazuje na úspěch kontroly.

## <a name="shut-down-the-notebook"></a>Poznámkový blok vypnout
Po dokončení spuštění aplikace byste měli vypínat Poznámkový blok a uvolnit tak prostředky. To provedete kliknutím na položku **Zavřít a zastavit** z nabídky **Soubor** v poznámkovém bloku. Toto vypnutí a zavření poznámkového bloku.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
