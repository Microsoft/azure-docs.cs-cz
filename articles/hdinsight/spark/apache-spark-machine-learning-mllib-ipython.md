---
title: Příklad strojového učení s Spark MLlib ve službě HDInsight – Azure
description: Naučte se používat Spark MLlib k vytvoření aplikace pro strojové učení, která analyzuje datovou sadu pomocí klasifikace prostřednictvím logistické regrese.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: bd61c6812d794d30e28f087dabf58db51e9c3296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230411"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Použití Apache Spark MLlib k vytvoření aplikace Machine Learning a analýze datové sady

Naučte se, jak pomocí Apache Spark MLlib vytvořit aplikaci Machine Learning. Aplikace bude provádět prediktivní analýzu otevřené datové sady. Z vestavěných knihoven strojového učení Spark používá tento příklad *klasifikaci* prostřednictvím logistické regrese.

MLlib je základní knihovna Sparku, která poskytuje mnoho nástrojů užitečných pro úlohy strojového učení, jako je například:

* Classification
* Regrese
* Clustering
* Modelování
* Dekompozice hodnot v číslech (SVD) a analýza hlavních komponent (DPS)
* Testování hypotéz a výpočet ukázkových statistik

## <a name="understand-classification-and-logistic-regression"></a>Pochopení klasifikace a logistické regrese

*Klasifikace*, oblíbená úloha strojového učení, je proces řazení vstupních dat do kategorií. Je to úloha klasifikačního algoritmu k tomu, abyste zjistili, jak přiřadit jmenovky k vstupním datům, která zadáte. Můžete si například představit algoritmus strojového učení, který přijímá informace o zásobách jako vstup. Pak rozdělí zásoby do dvou kategorií: akcií, které byste měli prodávat, a zásob, které byste měli zachovat.

Logistická regrese je algoritmus, který používáte pro klasifikaci. Rozhraní API pro logistické regrese Spark je užitečné pro *binární klasifikaci*nebo pro klasifikaci vstupních dat do jedné ze dvou skupin. Další informace o logistických regresích najdete v tématu [Wikipedii](https://en.wikipedia.org/wiki/Logistic_regression).

V souhrnu proces logistické regrese vytváří *logistickou funkci*. Použijte funkci pro předpověď pravděpodobnosti, že vstupní vektor patří do jedné nebo druhé skupiny.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Příklad prediktivní analýzy pro data kontroly potravin

V tomto příkladu pomocí Sparku provedete určitou prediktivní analýzu dat kontroly potravin (**Food_Inspections1.csv**). Data získaná prostřednictvím [portálu dat města Chicago](https://data.cityofchicago.org/). Tato datová sada obsahuje informace o inspekcích, které byly prováděny v Chicagu. Včetně informací o jednotlivých závodech, zjištěných porušeních (pokud existují) a výsledcích kontroly. Datový soubor CSV je už dostupný v účtu úložiště přidruženém ke clusteru na adrese **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

V následujících krocích vytvoříte model, abyste viděli, co je potřeba k předání nebo selhání kontroly potravin.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Vytvoření aplikace Machine Learning v Apache Spark MLlib

1. Vytvořte poznámkový blok Jupyter pomocí jádra PySpark. Pokyny najdete v tématu [Vytvoření souboru poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte klávesu **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontexty Spark a podregistr se automaticky vytvoří při spuštění první buňky kódu.

## <a name="construct-the-input-dataframe"></a>Sestavit vstupní datový rámec

Použijte kontext Spark pro načtení nezpracovaných dat CSV do paměti jako nestrukturovaný text. Pak pomocí knihovny CSV v Pythonu Analyzujte jednotlivé řádky dat.

1. Spusťte následující řádky, abyste vytvořili odolnou distribuovanou datovou sadu (RDD) pomocí importu a analýzy vstupních dat.

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

2. Spusťte následující kód, který načte jeden řádek z RDD, abyste si mohli prohlédnout schéma dat:

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

    Výstup poskytuje představu o schématu vstupního souboru. Zahrnuje název každého zařízení a typ zařízení. Také adresa, data inspekcí a umístění, mimo jiné.

3. Spusťte následující kód, který vytvoří datový rámec (*DF*) a dočasnou tabulku (*CountResults*) s několika sloupci, které jsou užitečné pro prediktivní analýzu. `sqlContext` slouží k transformaci strukturovaných dat.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Čtyři sloupce zájmu v rámci datového rámce jsou **ID**, **název**, **výsledky**a **porušení**.

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

Pojďme začít získat představu o tom, co datová sada obsahuje. 

1. Spusťte následující kód pro zobrazení jedinečných hodnot ve sloupci **výsledky** :

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

2. Spusťte následující kód, který vizualizuje distribuci těchto výsledků:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    `%%sql`Magic následovaný tím `-o countResultsdf` zajistí, že výstup dotazu je trvale uložen na serveru Jupyter (obvykle hlavnímu uzlu clusteru). Výstup je trvalý jako [PANDAS](https://pandas.pydata.org/) datový rámec se zadaným názvem **countResultsdf**. Další informace o `%%sql` Magic a dalších přístupnosti, které jsou k dispozici v jádru PySpark, najdete v tématu [jádra dostupná na poznámkových blocích Jupyter s Apache Spark clustery HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Výstup bude následující:

    ![Výstup dotazu SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Výstup dotazu SQL")

3. Můžete také použít matplotlib, knihovnu, která slouží k vytváření vizualizace dat, k vytvoření grafu. Vzhledem k tomu, že je nutné vytvořit vykreslení z místně trvalého datového rámce **countResultsdf** , musí fragment kódu začínat `%%local` Magic. Tato akce zajistí, že se kód spustí místně na serveru Jupyter.

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

    Aby bylo možné předpovědět výsledek kontroly potravin, je nutné vyvinout model na základě porušení. Vzhledem k tomu, že Logistická regrese je binární metoda klasifikace, má smysl seskupit výsledná data do dvou kategorií: **selhání** a **průchod**:

   - Dána
       - Dána
       - Průchod za sekundu
   - Neúspěch
       - Neúspěch
   - Zahodit
       - Firmy se nenašlo.
       - Mimo firmu

     Data s ostatními výsledky ("obchodní Neumístěná" nebo "mimo firmu") nejsou užitečná a vznikne i malé procento výsledků.

4. Spusťte následující kód, který převede existující datový rámec ( `df` ) na nový datový rámec, kde je každá kontrola vyjádřena jako dvojice s porušením popisku. V tomto případě popisek `0.0` představuje selhání, popisek `1.0` představuje úspěch a popisek `-1.0` představuje některé výsledky Kromě těchto dvou výsledků.

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

5. Spusťte následující kód, který zobrazí jeden řádek dat s popisky:

    ```PySpark
    labeledData.take(1)
    ```

    Výstup bude následující:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Vytvořit model logistické regrese ze vstupního datového rámce

Posledním úkolem je převést označené údaje. Převeďte data do formátu, který se dá analyzovat prostřednictvím logistické regrese. Vstup do algoritmu logistické regrese potřebuje sadu *vektorových párů popisků funkcí*. Kde "Vector" funkcí "je vektor čísel, které reprezentují vstupní bod. Proto je nutné převést sloupec "porušení", který je částečně strukturovaný a obsahuje mnoho komentářů v poli Free text. Převeďte sloupec na pole reálných čísel, která může počítač snadno pochopit.

Jedním ze standardních přístupů do strojového učení za účelem zpracování přirozeného jazyka je přiřazení jednotlivých různých slov "index". Pak předejte vektor do algoritmu strojového učení. To, že hodnota každého indexu obsahuje relativní četnost tohoto slova v textovém řetězci.

MLlib poskytuje snadný způsob, jak tuto operaci provést. Nejprve "tokenizovat" každé porušení řetězce získá jednotlivá slova v každém řetězci. Pak použijte `HashingTF` k převedení každé sady tokenů na vektor funkce, který lze následně předat algoritmu logistické regrese za účelem vytvoření modelu. Všechny tyto kroky provádíte v posloupnosti pomocí "kanálu".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Vyhodnocení modelu pomocí jiné datové sady

Pomocí modelu, který jste vytvořili dříve, můžete *předpovědět* , co budou výsledky nových kontrol. Předpovědi vycházejí z porušení zásad, které byly pozorovány. Tento model jste vyškolei **Food_Inspections1.csv**datové sady. Druhou datovou sadu můžete použít **Food_Inspections2.csv**k *vyhodnocení* síly tohoto modelu u nových dat. Tato druhá datová sada (**Food_Inspections2.csv**) je ve výchozím kontejneru úložiště přidruženém ke clusteru.

1. Spusťte následující kód, který vytvoří nový datový rámec **predictionsDf** , který obsahuje předpověď vygenerovanou modelem. Fragment kódu také vytvoří dočasnou tabulku s názvem **předpovědi** založenou na dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Měl by se zobrazit výstup podobný následujícímu textu:

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

1. Podívejte se na některý z předpovědi. Spustit tento fragment kódu:

    ```PySpark
    predictionsDf.take(1)
    ```

   Pro první záznam v sadě dat testu existuje předpověď.

1. `model.transform()`Metoda aplikuje stejnou transformaci na všechna nová data se stejným schématem a dorazí na předpověď způsobu klasifikace dat. Můžete provést několik statistik, abyste získali představu o tom, jak předpovědi byly:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Výstup bude vypadat jako následující text:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Pomocí logistické regrese pomocí Sparku získáte model vztahu mezi popsanými popisy v angličtině. A to, jestli by daný podnik mohl projít nebo podařit kontrolu potravin.

## <a name="create-a-visual-representation-of-the-prediction"></a>Vytvoření vizuální reprezentace předpovědi

Nyní můžete vytvořit konečnou vizualizaci, která vám pomůžete v důsledku výsledků tohoto testu.

1. Začnete extrahováním různých předpovědi a výsledků z dočasné tabulky **předpovědi** vytvořené dříve. Následující dotazy oddělují výstup jako *true_positive*, *false_positive*, *true_negative*a *false_negative*. V následujících dotazech vypnete vizualizaci pomocí `-q` a také uložíte výstup (pomocí `-o` ) jako datový rámec, který lze použít s `%%local` Magic.

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

1. Nakonec pomocí následujícího fragmentu kódu vygenerujte vykreslení pomocí **matplotlib**.

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

    ![Výstup aplikace Spark Machine Learning – Procento neúspěšných inspekcí v potravinách v grafu](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Výstup výsledků strojového učení Sparku")

    Výsledkem "pozitivního" v tomto grafu je neúspěšná kontrola jídla, zatímco záporný výsledek odkazuje na úspěšnou kontrolu.

## <a name="shut-down-the-notebook"></a>Vypnutí poznámkového bloku

Po dokončení spuštění aplikace byste měli Poznámkový blok vypnout a uvolnit tak prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tato akce poznámkový blok vypne a zavře.

## <a name="next-steps"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit model hloubkového učení s využitím Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
