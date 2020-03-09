---
title: Jádra pro Jupyter Poznámkový blok v clusterech Spark v Azure HDInsight
description: Přečtěte si o jádrech PySpark, PySpark3 a Spark pro notebook Jupyter, které jsou k dispozici pro clustery Spark v Azure HDInsight.
keywords: Poznámkový blok Jupyter ve Sparku, Jupyter Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 44089ea4b997e06cb7654fc6665a1a9a59ae2658
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389702"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jádra pro Poznámkový blok Jupyter na clusterech s Apache Spark ve službě Azure HDInsight

Clustery HDInsight Spark poskytují jádra, která můžete použít s poznámkovým blokem Jupyter na [Apache Spark](https://spark.apache.org/) pro testování vašich aplikací. Jádro je program, který spouští a interpretuje váš kód. Tři jádra:

- **PySpark** – pro aplikace napsané v Python2.
- **PySpark3** – pro aplikace napsané v python3.
- **Spark** – pro aplikace napsané v Scala.

V tomto článku se naučíte používat tyto jádra a výhody jejich používání.

## <a name="prerequisites"></a>Předpoklady

Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Vytvoření poznámkového bloku Jupyter ve službě Spark HDInsight

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster Spark.  Pokyny najdete v tématu [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) . Otevře se zobrazení **přehledu** .

2. V zobrazení **Přehled** vyberte v poli **řídicí panely clusteru** **Jupyter Poznámkový blok**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter Poznámkový blok na Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Poznámkový blok Jupyter ve Sparku")
  
   > [!NOTE]  
   > Do Jupyter poznámkového bloku se můžete dostat také otevřením následující adresy URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Vyberte **Nový**a pak vyberte buď **Pyspark**, **PySpark3**nebo **Spark** , a vytvořte Poznámkový blok. Používejte jádro Sparku pro aplikace Scala, jádro PySpark pro aplikace Python2 a jádro PySpark3 pro aplikace python3.

    ![Jádra pro Poznámkový blok Jupyter ve Sparku](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jádra pro Poznámkový blok Jupyter ve Sparku")

4. Otevře se Poznámkový blok s vybraným jádrem.

## <a name="benefits-of-using-the-kernels"></a>Výhody používání jader

Tady je několik výhod používání nových jader s poznámkovým blokem Jupyter v clusterech Spark HDInsight.

- **Přednastavené kontexty**. V prostředích **PySpark**, **PySpark3**nebo **Sparku** nemusíte před zahájením práce s aplikacemi nastavovat kontexty Sparku nebo podregistru explicitně. Jsou k dispozici ve výchozím nastavení. Kontexty jsou:

  - **SC** – pro kontext Spark
  - **kontext SqlContext** – pro kontext podregistru

    Takže nemusíte při nastavování kontextů provádět příkazy, jako je následující:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Místo toho můžete přímo použít předdefinované kontexty ve vaší aplikaci.

- **Buňky Magic**. Jádro PySpark poskytuje některé předdefinované "MAGICS", což jsou speciální příkazy, které můžete volat pomocí `%%` (například `%%MAGIC` `<args>`). Příkaz Magic musí být první slovo v buňce kódu a povoluje se více řádků obsahu. Slovo Magic by mělo být první slovo v buňce. Přidání cokoli před Magic, včetně komentářů, způsobí chybu.     Další informace o Magic najdete [tady](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    V následující tabulce jsou uvedeny různé Magic dostupné prostřednictvím jader.

   | Paket | Příklad | Popis |
   | --- | --- | --- |
   | Nápověda |`%%help` |Vygeneruje tabulku všech dostupných MAGICS s příkladem a popisem. |
   | info |`%%info` |Vytvoří výstup informací o relaci pro aktuální koncový bod Livy. |
   | Konfigurace |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry pro vytvoření relace. Příznak Force (-f) je povinný, pokud už je relace vytvořená, což zajistí, že se relace vynechá a znovu vytvoří. Seznam platných parametrů najdete v [textu žádosti post/Sessions pro Livy](https://github.com/cloudera/livy#request-body) . Parametry musí být předány jako řetězec JSON a musí být na dalším řádku po Magic, jak je znázorněno v příkladu sloupce. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Spustí dotaz Hive proti kontext sqlContext. Pokud je předán parametr `-o`, výsledek dotazu je trvalý v kontextu%% Local Python jako [PANDAS](https://pandas.pydata.org/) dataframe. |
   | místní |`%%local`<br>`a=1` |Veškerý kód v následných řádcích se spustí místně. Kód musí být platný Python2 kód, a to i bez ohledu na používané jádro. Takže i když jste při vytváření poznámkového bloku vybrali **PySpark3** nebo **Spark** , v případě, že v buňce použijete `%%local` Magic, musí mít tato buňka platný Python2 kód. |
   | Protokoly |`%%logs` |Vytvoří výstup protokolů pro aktuální relaci Livy. |
   | delete |`%%delete -f -s <session number>` |Odstraní konkrétní relaci aktuálního koncového bodu Livy. Nemůžete odstranit relaci, která je inicializovaná pro jádro samotné. |
   | Vyčištění |`%%cleanup -f` |Odstraní všechny relace pro aktuální koncový bod Livy, včetně relace tohoto poznámkového bloku. Příznak Force-f je povinný. |

   > [!NOTE]  
   > Kromě Magic přidaných jádrem PySpark můžete také použít [integrované IPython Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), včetně `%%sh`. Pomocí `%%sh` Magic můžete spouštět skripty a bloky kódu v clusteru hlavnímu uzlu.

- **Automatická vizualizace**. Jádro Pyspark automaticky vizualizuje výstup dotazů v podregistru a SQL. Můžete si vybrat mezi několika různými typy vizualizací, mezi které patří tabulka, výsečový, spojnicový, plošný a pruhový.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry podporované s%% SQL Magic

`%%sql` Magic podporuje různé parametry, které lze použít k řízení typu výstupu, který jste obdrželi při spouštění dotazů. V následující tabulce je uveden výstup.

| Parametr | Příklad | Popis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Tento parametr použijte k uchování výsledku dotazu v kontextu%% Local Python jako [PANDAS](https://pandas.pydata.org/) dataframe. Název proměnné datového rámce je název proměnné, kterou zadáte. |
| -q |`-q` |Pomocí této vlastnosti můžete pro buňku vypnout vizualizace. Pokud nechcete, aby se obsah buňky využíval, a chcete ji pouze zachytit jako datový rámec, použijte `-q -o <VARIABLE>`. Pokud chcete vypnout vizualizace bez zachycení výsledků (například pro spuštění dotazu SQL, jako je například příkaz `CREATE TABLE`), použijte `-q` bez zadání argumentu `-o`. |
| -m |`-m <METHOD>` |Kde **Metoda** je buď buď **převzít** , nebo **vzorek** (výchozí **nastavení je)** . Pokud metoda **trvá**, jádro vybere prvky z horní části sady výsledků dat určené v MAXROWS (popsané dále v této tabulce). Pokud je metoda **ukázková**, jádro náhodně vypíše prvky datové sady podle parametru `-r`, popsaného dále v této tabulce. |
| -r |`-r <FRACTION>` |Pro **zlomek** je číslo s plovoucí desetinnou čárkou v rozsahu od 0,0 do 1,0. Pokud je metoda ukázky pro dotaz SQL `sample`, pak jádro náhodně vyvzorkuje zadaný zlomek prvků sady výsledků. Například pokud spustíte dotaz SQL s argumenty `-m sample -r 0.01`, pak 1% řádků výsledků je náhodně vzorkovat. |
| -n |`-n <MAXROWS>` |**MAXROWS** je celočíselná hodnota. Jádro omezuje počet výstupních řádků na **MAXROWS**. Pokud je **MAXROWS** záporné číslo, jako je hodnota **-1**, počet řádků v sadě výsledků není omezený. |

**Příklad:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Výše uvedený příkaz provede následující akce:

- Vybere všechny záznamy z **hivesampletable**.
- Protože používáme-q, vypne se vizuálně.
- Protože používáme `-m sample -r 0.1 -n 500` náhodně vyvzorkuje 10% řádků v hivesampletable a omezí velikost sady výsledků na 500 řádků.
- A konečně, protože jsme použili `-o query2` ukládá také výstup do dataframe s názvem **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Předpoklady při používání nových jader

Bez ohledu na to, kterou jádro použijete, ponechání poznámkových bloků se systémem spotřebovává prostředky clusteru.  Vzhledem k tomu, že kontexty jsou přednastavené, protože kontexty jsou přednastavené, pouhým ukončením poznámkových bloků nedojde ke zrušení kontextu, takže se prostředky clusteru budou dál používat. Dobrým postupem je použít možnost **Zavřít a zastavit** z nabídky **soubor** poznámkového bloku, když jste dokončili používání poznámkového bloku, který ukončuje kontext a potom Poznámkový blok ukončí.

## <a name="where-are-the-notebooks-stored"></a>Kde jsou poznámkové bloky uložené?

Pokud váš cluster používá Azure Storage jako výchozí účet úložiště, poznámkové bloky Jupyter se uloží do účtu úložiště ve složce **/HdiNotebooks** .  Poznámkové bloky, textové soubory a složky, které vytvoříte v rámci Jupyter, jsou přístupné z účtu úložiště.  Pokud například použijete Jupyter k vytvoření složky **myFolder** a poznámkového bloku **myFolder/MyNotebook. ipynb**, můžete k tomuto poznámkovém bloku získat přístup `/HdiNotebooks/myfolder/mynotebook.ipynb` v rámci účtu úložiště.  To znamená, že Pokud nahrajete Poznámkový blok přímo na účet úložiště na `/HdiNotebooks/mynotebook1.ipynb`, Poznámkový blok se také zobrazí z Jupyter.  Poznámkové bloky zůstávají v účtu úložiště i po odstranění clusteru.

> [!NOTE]  
> Clustery HDInsight s Azure Data Lake Storage jako výchozí úložiště neukládají poznámkové bloky do přidruženého úložiště.

Způsob ukládání poznámkových bloků do účtu úložiště je kompatibilní s [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Pokud tedy SSH do clusteru, můžete použít příkazy správy souborů, jak je znázorněno v následujícím fragmentu kódu:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Bez ohledu na to, jestli cluster používá Azure Storage nebo Azure Data Lake Storage jako výchozí účet úložiště, poznámkové bloky se také ukládají do clusteru hlavnímu uzlu na `/var/lib/jupyter`.

## <a name="supported-browser"></a>Podporovaný prohlížeč

Jupyter poznámkové bloky v clusterech Spark HDInsight jsou podporované jenom na Google Chrome.

## <a name="feedback"></a>Váš názor

Nové jádra jsou v rozvíjející se fázi a budou v průběhu času. To by také znamenalo, že rozhraní API se v těchto jádrech můžou měnit. Vyhodnotili jsme jakoukoli zpětnou vazbu, kterou máte při používání těchto nových jader. To je užitečné při vytváření finální verze těchto jader. Komentáře a připomínky můžete opustit v části **Zpětná vazba** v dolní části tohoto článku.

## <a name="seealso"></a>Viz také

- [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

- [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
- [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
- [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

- [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
- [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

- [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
- [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
- [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

- [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
- [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
