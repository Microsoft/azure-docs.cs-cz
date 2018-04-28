---
title: Jádra pro poznámkový blok Jupyter na clustery Spark v Azure HDInsight | Microsoft Docs
description: Další informace o jádra PySpark, PySpark3 a Spark pro poznámkový blok Jupyter s clustery Spark v Azure HDInsight k dispozici.
keywords: Poznámkový blok jupyter na spark, jupyter spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: nitinme
ms.openlocfilehash: 58a0bf27109af3131bd102fd43e9367d267525f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Jádra pro poznámkový blok Jupyter na clustery Spark v Azure HDInsight 

Clustery HDInsight Spark poskytují jádra, která můžete použít s poznámkovým blokem Jupyter na Spark pro testování vašich aplikací. Jádro je program, který spouští a interpretuje vašeho kódu. Jsou tři jádra:

- **PySpark** – pro aplikace napsané v Python2
- **PySpark3** – pro aplikace napsané v Python3
- **Spark** – pro aplikace napsané v jazyce Scala

V tomto článku můžete další informace o použití těchto jádra a výhody jejich používání.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Vytvoření poznámkového bloku Jupyter v Spark HDInsight

1. Z [portál Azure](https://portal.azure.com/), otevřete váš cluster.  V tématu [seznamu a zobrazit clustery](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters) pokyny. Cluster se otevře v novém okně portálu.

2. Z **rychlé odkazy** klikněte na tlačítko **clusteru řídicí panely** otevřete **clusteru řídicí panely** okno.  Pokud nevidíte **rychlé odkazy**, klikněte na tlačítko **přehled** v levé nabídce v okně.

    ![Poznámkový blok Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Poznámkový blok Jupyter na Spark") 

3. Klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.
   
   > [!NOTE]
   > Otevření následující adresy URL v prohlížeči, mohou také spojit poznámkového bloku Jupyter v clusteru Spark. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Klikněte na tlačítko **nový**a pak klikněte buď **Pyspark**, **PySpark3**, nebo **Spark** k vytvoření poznámkového bloku. Použijte jádra Spark Scala aplikací, jádra PySpark pro Python2 aplikace a PySpark3 jádra pro Python3 aplikace.
   
    ![Jádra pro poznámkový blok Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "jádra pro poznámkový blok Jupyter na Spark") 

4. Poznámkový blok se otevře s jádrem, které jste vybrali.

## <a name="benefits-of-using-the-kernels"></a>Výhody použití jader

Zde naleznete několik výhod nového jádrech pomocí poznámkového bloku Jupyter na clustery Spark HDInsight.

- **Předvolby kontexty**. S **PySpark**, **PySpark3**, nebo **Spark** jádra, není nutné explicitně nastavovat kontexty Spark nebo Hive před zahájením práce s vašimi aplikacemi. Toto jsou k dispozici ve výchozím nastavení. Tyto kontexty jsou:
   
   * **sc** – pro kontext Spark
   * **sqlContext** – pro kontext Hive
   
   Ano nemusíte spouštět příkazy jako je třeba následující příkaz a nastavovat kontexty:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Místo toho můžete přímo použít přednastavení kontexty ve vaší aplikaci.

- **Buňky Magic**. Poskytuje jádra PySpark některé předdefinované "Magic", které jsou speciální příkazy, které můžete volat s `%%` (například `%%MAGIC` <args>). Příkaz magic musí být první slovo v buňce kódu a povolit pro více řádků obsahu. Magic slovo by měl být první slovo v buňce. Přidání nic před magic, i komentáře, výsledkem bude chyba.     Další informace o Magic, které najdete v tématu [zde](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    Následující tabulka uvádí různé Magic, které jsou k dispozici prostřednictvím jádrech.

   | Magic | Příklad: | Popis |
   | --- | --- | --- |
   | nápověda |`%%help` |Vytvoří tabulku všechny dostupné Magic s příklad a popis |
   | Informace o |`%%info` |Výstupy informací o relaci pro aktuální koncový bod Livy |
   | konfigurovat |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Nakonfiguruje parametry pro vytvoření relace. Příznak force (-f) je povinný, pokud je relace již bylo vytvořeno, který zajistí, že je relace vyřadit a vytvořit znovu. Podívejte se na [/sessions POST na Livy text žádosti](https://github.com/cloudera/livy#request-body) pro seznam platných parametrů. Parametry musí být předán jako řetězec formátu JSON a musí být na další řádek po magic, jak je znázorněno v příkladu sloupec. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Provede dotaz Hive proti sqlContext. Pokud `-o` parametr se předává, výsledek dotazu je uchován v %% lokální kontext Python jako [Pandas](http://pandas.pydata.org/) dataframe. |
   | místní |`%%local`<br>`a=1` |Všechny kód na další řádek je spustit místně. Kód musí být platný kód Python2 i bez ohledu na jádra, který používáte. Ano, i v případě, že jste vybrali **PySpark3** nebo **Spark** jádra při vytváření poznámkového bloku, pokud použijete `%%local` magic v buňce, dané buňky musí mít pouze platný kód Python2... |
   | Protokoly |`%%logs` |Protokoly pro aktuální relaci Livy výstupy. |
   | odstraňovat |`%%delete -f -s <session number>` |Odstraní relaci konkrétní aktuální Livy koncového bodu. Nelze odstranit iniciovaného relace pro jádra sám sebe. |
   | Vyčištění |`%%cleanup -f` |Odstraní všechny relace pro aktuální Livy koncový bod, včetně relace tento poznámkový blok. Příznak force -f je povinný. |

   > [!NOTE]
   > Kromě Magic přidal jádra PySpark, můžete také použít [předdefinované Magic IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), včetně `%%sh`. Můžete použít `%%sh` magic spouštět skripty a blok kódu na headnode clusteru.
   >
   >
2. **Automaticky vizualizace**. **Pyspark** jádra automaticky vizualizuje výstup dotazy Hive a SQL. Můžete zvolit několika různých typů vizualizace včetně tabulky, kruhový, řádku, oblasti, panelu.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry podporovány s %% sql magic
`%%sql` Magic podporuje různé parametry, které můžete použít k řízení druh výstup, která se zobrazí při spuštění dotazů. Následující tabulka uvádí výstup.

| Parametr | Příklad: | Popis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Tento parametr použijte pro uchování v výsledek dotazu, %% lokální kontext Python, jako [Pandas](http://pandas.pydata.org/) dataframe. Název proměnné dataframe je název proměnné, které zadáte. |
| -q |`-q` |Použijte k vypnutí možnosti vizualizace pro buňky. Pokud nechcete automaticky vizualizovat obsah buňky a chcete jen zaznamenat jako dataframe, potom použijte `-q -o <VARIABLE>`. Pokud chcete vypnout vizualizace bez zaznamenávání výsledky (například pro spuštění příkazu jazyka SQL, jako je třeba `CREATE TABLE` příkaz), použijte `-q` bez zadání `-o` argument. |
| -m |`-m <METHOD>` |Kde **metoda** je buď **trvat** nebo **ukázka** (výchozí hodnota je **trvat**). Pokud je metoda **trvat**, jádra vybere elementy z horní části datové sady výsledků dotazu určeného MAXROWS (popsané dál v této tabulce). Pokud je metoda **ukázka**, jádra náhodně ukázky elementy sady dat podle `-r` parametr popsána dále v této tabulce. |
| -r |`-r <FRACTION>` |Zde **ZLOMEK** je číslo s plovoucí desetinnou čárkou mezi 0,0 a 1,0. Pokud je metoda ukázka pro příkaz jazyka SQL `sample`, pak jádra náhodně ukázky zadaný podíl elementy výsledku nastavení za vás. Například pokud spustíte dotaz SQL s argumenty `-m sample -r 0.01`, pak se náhodně vzorkovat 1 % řádků výsledek. |
| -n |`-n <MAXROWS>` |**MAXROWS** celočíselná hodnota. Jádra omezuje počet řádků výstup do **MAXROWS**. Pokud **MAXROWS** záporné číslo, jako je **-1**, pak není omezený počet řádků v sadě výsledků dotazu. |

**Příklad:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Výše uvedený příkaz provede následující akce:

* Vybere všechny záznamy z **hivesampletable**.
* Vzhledem k tomu, že používáme - q, vypne automatické vizualizace.
* Vzhledem k tomu, že používáme `-m sample -r 0.1 -n 500` náhodně ukázky 10 % řádků v hivesampletable a omezení velikosti sady výsledků do 500 řádků.
* Nakonec protože jsme použili `-o query2` také uloží výstup do dataframe, nazývá **dotaz2**.

## <a name="considerations-while-using-the-new-kernels"></a>Aspekty při použití nové jádra

Podle toho, která jádra, které používáte, ponechat poznámkových bloků systémem spotřebovává prostředky clusteru.  S těmito jádra protože kontexty jsou předvolby, jednoduše ukončení poznámkových bloků kill není kontextu a proto nadále používat prostředky clusteru. Je vhodné použít **zavřít a zastavit** možnost poznámkového bloku **souboru** nabídky po skončení pomocí poznámkového bloku, který ukončí kontext a poté ukončí poznámkového bloku.     

## <a name="show-me-some-examples"></a>Ukázat některé příklady

Otevřete Poznámkový blok Jupyter, uvidíte dvě složky k dispozici na kořenové úrovni.

* **PySpark** složka obsahuje ukázkové poznámkových bloků, které používají nové **Python** jádra.
* **Scala** složka obsahuje ukázkové poznámkových bloků, které používají nové **Spark** jádra.

Můžete otevřít **00 - [přečtěte si NEJPRVE] funkce jádra Magic Spark** poznámkového bloku z **PySpark** nebo **Spark** složku pro další informace o různých Magic, které jsou k dispozici. Můžete taky dostupná ukázková notebooky pod dvě složky se dozvíte, jak zajistit různé scénáře použití poznámkové bloky Jupyter s clustery HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Kde jsou uložené poznámkových bloků?

Pokud váš cluster používá jako výchozí účet úložiště Azure Storage, poznámkové bloky Jupyter se uloží do účtu úložiště **/HdiNotebooks** složky.  Poznámkové bloky, textové soubory a složky, které vytvoříte z v rámci Jupyter jsou přístupné z účtu úložiště.  Například, pokud používáte Jupyter vytvořit složku **Moje_složka** a Poznámkový blok **myfolder/mynotebook.ipynb**, dostanete tento poznámkový blok v `/HdiNotebooks/myfolder/mynotebook.ipynb` v rámci účtu úložiště.  Platí také nastavena hodnota true, to znamená, pokud nahrát Poznámkový blok přímo do účtu úložiště na `/HdiNotebooks/mynotebook1.ipynb`, Poznámkový blok je také zobrazit z Jupyter.  Poznámkové bloky zůstat v účtu úložiště i po odstranění clusteru.

> [!NOTE]
> Clustery HDInsight s Azure Data Lake Store jako výchozí úložiště neukládejte poznámkových bloků v přidruženého úložiště.
>

Způsob, jakým poznámkových bloků se uloží do účtu úložiště je kompatibilní s HDFS. Pokud tedy můžete SSH do clusteru, který můžete použít soubor příkazy pro správu, jak je znázorněno v následujícím fragmentu kódu:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Bez ohledu na to, jestli cluster používá Azure Storage nebo Azure Data Lake Store jako výchozí účet úložiště, jsou také uloženy poznámkových bloků v clusteru headnode v `/var/lib/jupyter`.

## <a name="supported-browser"></a>Podporovaný prohlížeč

Poznámkové bloky Jupyter na clustery Spark HDInsight jsou podporovány pouze na Google Chrome.

## <a name="feedback"></a>Váš názor
Nové jádrech jsou v vyvíjející se fáze a bude pro dospělé v čase. To může znamenat, že rozhraní API může změnit, protože tyto jádra pro dospělé. Uvítáme jakékoli zpětnou vazbu, která máte při použití těchto nových jádra. To je užitečné v shaping finální verzi nástroje tyto jádra. Můžete ponechat vaše komentáře nebo zpětné vazby v části **komentáře** v dolní části tohoto článku.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
