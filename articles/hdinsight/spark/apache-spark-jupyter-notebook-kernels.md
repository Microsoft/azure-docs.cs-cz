---
title: Jádra pro poznámkový blok Jupyter v clusterech Spark v Azure HDInsight
description: Přečtěte si o jádrech PySpark, PySpark3 a Spark pro poznámkový blok Jupyter, který je k dispozici v clusterech Spark na Azure HDInsight.
keywords: jupyter notebook na jiskru, jupyter jiskra
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/20/2020
ms.openlocfilehash: a04b8fee31ffa5280bc8ad0fca35495bb87e0e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064465"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jádra pro poznámkový blok Jupyter v clusterech Apache Spark v Azure HDInsight

Clustery HDInsight Spark poskytují jádra, která můžete použít s notebookem Jupyter na [Apache Spark](./apache-spark-overview.md) pro testování vašich aplikací. Jádro je program, který spouští a interpretuje váš kód. Tři jádra jsou:

- **PySpark** - pro aplikace napsané v Pythonu2.
- **PySpark3** - pro aplikace napsané v Pythonu3.
- **Spark** - pro aplikace napsané v Scala.

V tomto článku se dozvíte, jak používat tato jádra a výhody jejich použití.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark v HDInsightu. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Vytvoření notebooku Jupyter na Spark HDInsight

1. Na [portálu Azure](https://portal.azure.com/)vyberte cluster Spark.  Pokyny naleznete [v tématu Seznam a zobrazení clusterů.](../hdinsight-administer-use-portal-linux.md#showClusters) Otevře se zobrazení **Přehled.**

2. V zobrazení **Přehled** vyberte v poli **Řídicí panely clusteru** **poznámkový blok Jupyter .** Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter notebook na Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter notebook na Spark")
  
   > [!NOTE]  
   > K poznámkovému bloku Jupyter v clusteru Spark se můžete také dostat otevřením následující adresy URL v prohlížeči. Nahraďte **název clusteru názvem clusteru:**
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Vyberte **Nový**a pak vytvořte poznámkový blok buď **Pyspark**, **PySpark3**nebo **Spark.** Jádro Spark použijte pro aplikace Scala, jádro PySpark pro aplikace Python2 a jádro PySpark3 pro aplikace Python3.

    ![Jádra pro poznámkový blok Jupyter na Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jádra pro poznámkový blok Jupyter na Spark")

4. Otevře se poznámkový blok s vybraným jádrem.

## <a name="benefits-of-using-the-kernels"></a>Výhody používání jader

Zde je několik výhod používání nových jader s poznámkovým blokem Jupyter na clusterech Spark HDInsight.

- **Přednastavené kontexty**. S **PySpark**, **PySpark3**nebo jádra **Spark,** nemusíte nastavit Spark nebo Hive kontexty explicitně před zahájením práce s aplikacemi. Ty jsou k dispozici ve výchozím nastavení. Tyto kontexty jsou:

  - **sc** - pro kontext Spark
  - **sqlContext** - pro kontext Hive

    Takže **není** třeba spouštět příkazy jako následující nastavit kontexty:

         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)

    Místo toho můžete přímo použít přednastavené kontexty ve vaší aplikaci.

- **Buněčná kouzla**. Jádro PySpark poskytuje některé předdefinované "kouzla", což jsou speciální `%%` příkazy, `%%MAGIC` `<args>`které můžete volat (například ). Příkaz magie musí být prvním slovem v buňce kódu a musí umožňovat více řádků obsahu. Kouzelné slovo by mělo být první slovo v buňce. Přidání matnic před kouzlem, dokonce i komentáře, způsobí chybu.     Další informace o kouzlech naleznete [zde](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    V následující tabulce jsou uvedena různá kouzla dostupná prostřednictvím jader.

   | Magie | Příklad | Popis |
   | --- | --- | --- |
   | Nápověda |`%%help` |Generuje tabulku všech dostupných kouzel s příkladem a popisem |
   | Info |`%%info` |Výstupy informací o relaci pro aktuální koncový bod Livy |
   | Konfigurace |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry pro vytvoření relace. Příznak síly (-f) je povinný, pokud již byla vytvořena relace, která zajišťuje, že relace je vynechána a znovu vytvořena. Podívejte se na [Livy post / sessions Request Body](https://github.com/cloudera/livy#request-body) pro seznam platných parametrů. Parametry musí být předány jako řetězec JSON a musí být na dalším řádku za magic, jak je znázorněno v příkladu sloupce. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Spustí dotaz Hive proti sqlContext. Pokud `-o` je parametr předán, výsledek dotazu je zachován v kontextu %%místní python jako datový rámec [Pandas.](https://pandas.pydata.org/) |
   | local |`%%local`<br>`a=1` |Veškerý kód v následujících řádcích je proveden místně. Kód musí být platný kód Pythonu2 i bez ohledu na jádro, které používáte. Takže i když jste při vytváření poznámkového bloku vybrali jádra **PySpark3** nebo **Spark,** pokud používáte `%%local` kouzlo v buňce, musí mít tato buňka pouze platný kód Pythonu2. |
   | Protokoly |`%%logs` |Výstupy protokoly pro aktuální relaci Livy. |
   | delete |`%%delete -f -s <session number>` |Odstraní konkrétní relaci aktuálního koncového bodu Livy. Relaci, která je iniciována pro samotné jádro, nelze odstranit. |
   | Vyčištění |`%%cleanup -f` |Odstraní všechny relace pro aktuální koncový bod Livy, včetně relace tohoto poznámkového bloku. Příznak síly -f je povinný. |

   > [!NOTE]  
   > Kromě kouzel, které přidalo jádro PySpark, můžete také použít [vestavěná kouzla IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), včetně `%%sh`. `%%sh` Kouzlo můžete použít ke spuštění skriptů a bloku kódu na směrovém uzlu clusteru.

- **Automatická vizualizace**. Jádro Pyspark automaticky vizualizuje výstup dotazů Hive a SQL. Můžete si vybrat mezi několika různými typy vizualizací, včetně tabulky, výsečového grafu, čáry, oblasti, pruhu.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry podporované pomocí %%sql magic

Kouzlo `%%sql` podporuje různé parametry, které můžete použít k řízení druhu výstupu, který obdržíte při spuštění dotazů. V následující tabulce je uveden výstup.

| Parametr | Příklad | Popis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Tento parametr slouží k zachování výsledku dotazu v kontextu %%místní python, jako datový rámec [Pandas.](https://pandas.pydata.org/) Název proměnné datového rámce je zadaný název proměnné. |
| -q |`-q` |Pomocí této funkce můžete vypnout vizualizace pro buňku. Pokud nechcete automaticky vizualizovat obsah buňky a chcete ji zachytit jako datový `-q -o <VARIABLE>`rámec, použijte . Pokud chcete vypnout vizualizace bez zachycení výsledků (například pro spuštění dotazu `CREATE TABLE` SQL, `-q` jako je `-o` příkaz), použijte bez zadání argumentu. |
| -m |`-m <METHOD>` |Pokud je **metoda** buď **odebrat,** nebo **vzorek** (výchozí je **odběr**). Pokud je metoda **take**, jádro vybere prvky z horní části sady dat výsledků určené MAXROWS (popsané dále v této tabulce). Pokud je metoda **ukázka**, jádro náhodně vzorkuje `-r` prvky datové sady podle parametru popsaného dále v této tabulce. |
| -r |`-r <FRACTION>` |Zde **FRACTION** je číslo s plovoucí desetinnou desetinnou mezi 0,0 a 1,0. Pokud je `sample`ukázková metoda pro dotaz SQL , pak jádro náhodně vzorkuje zadaný zlomek prvků sady výsledků za vás. Pokud například spustíte dotaz SQL s `-m sample -r 0.01`argumenty , bude náhodně vzorkováno 1 % řádků výsledků. |
| -n |`-n <MAXROWS>` |**MAXROWS** je celá hodnota. Jádro omezuje počet výstupních řádků na **MAXROWS**. Pokud **MAXROWS** je záporné číslo, například **-1**, pak počet řádků v sadě výsledků není omezen. |

**Příklad:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Výše uvedený příkaz činí následující:

- Vybere všechny záznamy z **tabulky podučních vzorků**.
- Protože používáme -q, vypne to autovizualizaci.
- Protože ji `-m sample -r 0.1 -n 500` používáme náhodně vzorky 10% řádků v hivesampletable a omezuje velikost sady výsledků na 500 řádků.
- Nakonec, protože `-o query2` jsme použili také uloží výstup do datového rámce s názvem **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Důležité informace při používání nových jader

Bez ohledu na to, které jádro použijete, ponechání spuštěných poznámkových bloků spotřebovává prostředky clusteru.  S těmito jádry, protože kontexty jsou přednastaveny, jednoduše ukončení poznámkových bloků nezabije kontext a proto jsou prostředky clusteru nadále používány. Dobrým postupem je použít možnost **Zavřít a zastavit** z nabídky Soubor poznámkového **bloku** po dokončení používání poznámkového bloku, který ukončí kontext a pak poznámkový blok ukončí.

## <a name="where-are-the-notebooks-stored"></a>Kde jsou uloženy poznámkové bloky?

Pokud váš cluster používá Azure Storage jako výchozí účet úložiště, poznámkové bloky Jupyter se uloží do účtu úložiště ve složce **/HdiNotebooks.**  Poznámkové bloky, textové soubory a složky, které vytvoříte z jupyteru, jsou přístupné z účtu úložiště.  Pokud například použijete jupyter k vytvoření složky **myfolder** a poznámkového bloku **myfolder/mynotebook.ipynb**, můžete k tomuto poznámkovému bloku přistupovat v `/HdiNotebooks/myfolder/mynotebook.ipynb` rámci účtu úložiště.  Opak je také pravdou, to znamená, že pokud nahrajete poznámkový blok přímo do svého účtu úložiště na adrese `/HdiNotebooks/mynotebook1.ipynb`, poznámkový blok je viditelný také z Jupyteru.  Poznámkové bloky zůstanou v účtu úložiště i po odstranění clusteru.

> [!NOTE]  
> Clustery HDInsight s Azure Data Lake Storage jako výchozím úložištěm neukládají poznámkové bloky v přidruženém úložišti.

Způsob, jakým jsou notebooky uloženy do účtu úložiště, je kompatibilní s [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Pokud tedy do clusteru vstupujete do clusteru, můžete použít příkazy pro správu souborů, jak je znázorněno v následujícím fragmentu:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it's visible from Jupyter

Bez ohledu na to, jestli cluster používá Azure Storage nebo Azure Data Lake Storage jako výchozí `/var/lib/jupyter`účet úložiště, poznámkové bloky se také ukládají na headnode clusteru na .

## <a name="supported-browser"></a>Podporovaný prohlížeč

Notebooky Jupyter na clusterech Spark HDInsight jsou podporovány pouze v prohlížeči Google Chrome.

## <a name="feedback"></a>Váš názor

Nová jádra jsou v vyvíjející se fázi a bude zrát v průběhu času. To může také znamenat, že se mohou měnit api, jak tato jádra zrají. Ocenili bychom jakoukoli zpětnou vazbu, kterou máte při používání těchto nových jader. To je užitečné při formování konečné verze těchto jader. Své komentáře/zpětnou vazbu můžete zanechat v části **Zpětná vazba** v dolní části tohoto článku.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

- [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
- [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
- [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
- [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

- [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
- [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

- [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
- [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
- [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
- [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

- [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
- [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
