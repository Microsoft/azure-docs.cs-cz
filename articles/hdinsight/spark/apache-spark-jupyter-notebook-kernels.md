---
title: Jádra pro poznámkový blok Jupyter v clusterech Spark v Azure HDInsight
description: Další informace o jádrech PySpark, PySpark3 a Spark pro poznámkový blok Jupyter s clustery Spark v Azure HDInsight k dispozici.
keywords: Poznámkový blok jupyter pro spark, jupyter spark
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 937f6ffb9865419611c35b95ac84832bb2f1f3fe
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791806"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jádra pro poznámkový blok Jupyter v clusterech Apache Spark v Azure HDInsight 

Clustery HDInsight Spark poskytují jader, které můžete použít s poznámkovým blokem Jupyter na [Apache Spark](https://spark.apache.org/) pro testování vašich aplikací. Jádra je program, který se spustí a interpretuje váš kód. Jsou tři jádra:

- **PySpark** – pro aplikace napsané v Python2.
- **PySpark3** – pro aplikace napsané v pythonu3.
- **Spark** – pro aplikace napsané v jazyce Scala.

V tomto článku se dozvíte, jak používat tyto jádrech a výhody jejich používání.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Vytvoření poznámkového bloku Jupyter na HDInsight Spark

1. Z [webu Azure portal](https://portal.azure.com/), otevřete svůj cluster.  Zobrazit [výpisu a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) pokyny. Cluster se otevře v novém okně portálu.

2. Z **rychlé odkazy** klikněte na tlačítko **řídicí panely clusteru** otevřít **řídicí panely clusteru** okno.  Pokud nevidíte **rychlé odkazy**, klikněte na tlačítko **přehled** v levé nabídce v okně.

    ![Poznámkový blok Jupyter ve Sparku](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Poznámkový blok Jupyter ve Sparku") 

3. Klikněte na tlačítko **Poznámkový blok Jupyter**. Po vyzvání zadejte přihlašovací údaje správce clusteru.
   
   > [!NOTE]  
   > Může také Poznámkový blok Jupyter v clusteru Spark tak, že otevřete následující adresu URL v prohlížeči. Nahraďte **CLUSTERNAME** názvem clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`


3. Klikněte na tlačítko **nový**a potom klikněte na možnost **Pyspark**, **PySpark3**, nebo **Spark** vytvoření poznámkového bloku. Použijte Spark jádra pro aplikace Scala, jádra PySpark pro Python2 aplikace a PySpark3 jádra pro aplikace Python3.
   
    ![Jádra pro Jupyter notebook ve Sparku](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "jádra pro Jupyter notebook ve Sparku") 

4. Poznámkový blok se otevře s jádra, který jste vybrali.

## <a name="benefits-of-using-the-kernels"></a>Výhody použití jader

Tady je několik výhod pomocí jádrech nový poznámkový blok Jupyter v clusterech HDInsight Spark.

- **Přednastavení kontexty**. S **PySpark**, **PySpark3**, nebo **Spark** jader, můžete nemusíte nastavovat kontexty Spark a Hive explicitně předtím, než se pustíte do práce s vašimi aplikacemi. Toto jsou k dispozici ve výchozím nastavení. Kontexty jsou:
   
   * **sc** – pro kontext Spark
   * **kontext sqlContext** – pro kontext Hive
   
   Proto není nutné spouští příkazy následujícím postupem nastavit kontexty:
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   Místo toho můžete přímo použít přednastavených kontextech ve vaší aplikaci.

- **Buňky Magic**. Jádra PySpark poskytuje některé předdefinované "Magic", které jsou speciální příkazy, které lze volat s `%%` (například `%%MAGIC` <args>). Magický příkaz musí být první slovo do buňky kódu a umožňují více řádků obsahu. Magický slov by měla být první slovo v buňce. Přidání nic před magic, dokonce i komentáře, způsobí chybu.     Další informace o Magic, naleznete v tématu [tady](https://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    V následující tabulce jsou uvedeny různé Magic, které jsou k dispozici prostřednictvím jádrech.

   | Magic | Příklad: | Popis |
   | --- | --- | --- |
   | Nápověda |`%%help` |Vytvoří tabulku ze všech dostupných Magic příklad a popis |
   | informace |`%%info` |Informace o relaci výstupy pro aktuální koncový bod Livy |
   | konfigurovat |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Nakonfiguruje parametry pro vytvoření relace. Příznak force (-f) je povinný, pokud relace již byla vytvořena, což zajistí, že je relace vyřadit a vytvořit znovu. Podívejte se na [/sessions příspěvek Livy text žádosti](https://github.com/cloudera/livy#request-body) seznam platných parametrů. Parametry musí být předán v podobě řetězce JSON a musí být na dalším řádku za všechno, jak je znázorněno v příkladu sloupce. |
   | SQL |`%%sql -o <variable name>`<br> `SHOW TABLES` |Spustí dotaz Hive proti kontext sqlContext. Pokud `-o` parametr se předává, výsledek dotazu se ukládají v %% místní kontext Python jako [Pandas](https://pandas.pydata.org/) datového rámce. |
   | místní |`%%local`<br>`a=1` |Veškerý kód v dalších řádcích je spuštěn místně. Kód musí být platný kód Python2 i bez ohledu na jádro, které používáte. Ano, i v případě, že jste vybrali **PySpark3** nebo **Spark** jádrech při vytváření Poznámkový blok, pokud použijete `%%local` magic v buňce, tato buňka musí mít pouze platný kód Python2... |
   | Protokoly |`%%logs` |Protokoly pro aktuální relaci Livy výstupy. |
   | delete |`%%delete -f -s <session number>` |Odstraní konkrétní relace aktuální Livy koncového bodu. Nelze odstranit relace je zahájeno pro jádra, samotného. |
   | Vyčištění |`%%cleanup -f` |Odstraní všechny relace pro aktuální Livy koncový bod, včetně relace tento poznámkový blok. Příznak force -f je povinný. |

   > [!NOTE]  
   > Kromě Magic přidal jádra PySpark, můžete také použít [integrované IPython Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), včetně `%%sh`. Můžete použít `%%sh` magic spouštět skripty a blok kódu na hlavního uzlu clusteru.

2. **Auto vizualizace**. **Pyspark** jádra automaticky vizualizuje výstup dotazy Hive a SQL. Můžete zvolit několik různých typů vizualizace včetně tabulky, výsečové, řádek, oblasti, panel.

## <a name="parameters-supported-with-the-sql-magic"></a>Podporované s parametry %% magický příkaz jazyka sql
`%%sql` Magic podporuje různé parametry, které můžete použít k řízení druh výstupu, který se zobrazí při spuštění dotazů. V následující tabulce jsou uvedeny ve výstupu.

| Parametr | Příklad: | Popis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Tento parametr použijte k uchování výsledků dotazu v %% kontextu místního Pythonu, jako [Pandas](https://pandas.pydata.org/) datového rámce. Název proměnné dataframe je název proměnné, které zadáte. |
| -q |`-q` |Použijte k vypnutí možnosti vizualizace pro buňku. Pokud nechcete automaticky vizualizovat obsah buňky a chcete jenom pro zachycení jako datový rámec, a následné použití `-q -o <VARIABLE>`. Pokud chcete vypnout vizualizace bez zaznamenání výsledků (například pro spuštění dotazu SQL, jako je třeba `CREATE TABLE` příkaz), použijte `-q` bez zadání `-o` argument. |
| -m |`-m <METHOD>` |Kde **metoda** je buď **trvat** nebo **ukázka** (výchozí hodnota je **trvat**). Pokud je metoda **trvat**, jádro vybere prvky z horní části datové sady výsledků dotazu určeno MAXROWS (popsáno dále v této tabulce). Pokud je metoda **ukázka**, jádro náhodně ukázky prvky sady dat podle `-r` parametr, je popsáno dále v této tabulce. |
| -r |`-r <FRACTION>` |Tady **ZLOMEK** je číslo s plovoucí desetinnou čárkou mezi 0,0 a 1,0. Pokud je ukázka metody pro dotaz SQL `sample`, pak jádra náhodně ukázky zadaný zlomek prvky sady výsledků za vás. Například můžete spustit dotaz SQL s argumenty `-m sample -r 0.01`, pak 1 % výsledné řádky se vzorkují náhodně. |
| -n |`-n <MAXROWS>` |**MAXROWS** je celočíselná hodnota. Jádra omezuje počet řádků výstupu **MAXROWS**. Pokud **MAXROWS** , jako je záporné číslo **-1**, pak není omezený počet řádků v sadě výsledků. |

**Příklad:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

Příkaz výše provede následující akce:

* Vybere všechny záznamy z **hivesampletable**.
* Protože používáme - q, vypne automatické vizualizace.
* Protože používáme `-m sample -r 0.1 -n 500` náhodně 10 % řádků v hivesampletable – ukázky a omezení velikosti sady výsledků do 500 řádků.
* A konečně protože jsme použili `-o query2` také uloží výstup do struktury dataframe volá **dotaz2**.

## <a name="considerations-while-using-the-new-kernels"></a>Důležité informace při používání nového jádrech

Jádra, podle toho, která používáte, byste museli opustit poznámkové bloky s spotřebovávají prostředky clusteru.  S těmito jádra protože jsou přednastaveny kontexty, jednoduše ukončení poznámkových bloků neukončí kontextu a proto i nadále používat prostředky clusteru. Je vhodné je použít **zavřít a zastavit** možnost z poznámkového bloku **souboru** nabídky, až budete hotovi, pomocí poznámkového bloku, který ukončí kontext a následně skončí poznámkového bloku.     

## <a name="show-me-some-examples"></a>Zobrazit příklady

Při otevření poznámkového bloku Jupyter, se zobrazí dvě složky na kořenové úrovni.

* **PySpark** složka obsahuje ukázkové poznámkové bloky, které používají nový **Python** jádra.
* **Scala** složka obsahuje ukázkové poznámkové bloky, které používají nový **Spark** jádra.

Můžete otevřít **00 - [přečtěte si NEJPRVE] funkce jádra Magic Spark** Poznámkový blok z **PySpark** nebo **Spark** složky Další informace o různých Magic, které jsou k dispozici. Také vám pomůže v jiných ukázkové poznámkové bloky k dispozici ve dvou složkách zjistěte, jak dosáhnout různé scénáře použití poznámkových bloků Jupyter s clustery HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Kde jsou uložené poznámkové bloky?

Pokud váš cluster používá jako výchozí účet úložiště Azure Storage, poznámkové bloky Jupyter se uloží do účtu úložiště **/HdiNotebooks** složky.  Poznámkové bloky, textové soubory a složky, které vytvoříte z v rámci Jupyter jsou přístupné z účtu úložiště.  Například, pokud použijete k vytvoření složky Jupyter **Moje_složka** a Poznámkový blok **myfolder/mynotebook.ipynb**, dostanete tento poznámkový blok v `/HdiNotebooks/myfolder/mynotebook.ipynb` v rámci účtu úložiště.  Platí to i hodnotu true, to znamená, pokud nahrání poznámkového bloku přímo do vašeho účtu úložiště v `/HdiNotebooks/mynotebook1.ipynb`, a je viditelná z Jupyter Poznámkový blok.  Poznámkové bloky zůstanou v účtu úložiště i po odstranění clusteru.

> [!NOTE]  
> Clustery HDInsight s Azure Data Lake Storage jako výchozím úložištěm neukládejte poznámkových bloků v přidružené úložiště.

Způsob, jakým poznámkových bloků se uloží do účtu úložiště je kompatibilní s [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Takže pokud je SSH do clusteru, které můžete použít soubor příkazy pro správu, jak je znázorněno v následujícím fragmentu kódu:

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Bez ohledu na to, zda cluster používá Azure Storage nebo Azure Data Lake Storage jako výchozí účet úložiště, poznámkových bloků jsou uložené taky na hlavního uzlu clusteru v `/var/lib/jupyter`.

## <a name="supported-browser"></a>Podporovaný prohlížeč

Poznámkové bloky Jupyter v clusterech HDInsight Spark jsou podporovány pouze v prohlížeči Google Chrome.

## <a name="feedback"></a>Váš názor
Nové jádrech jsou ve fázi se vyvíjejí a bude pro dospělé v čase. To může znamenat, že rozhraní API může změnit, protože tyto jádra pro dospělé. Uvítáme jakoukoli zpětnou vazbu, kterou máte při použití těchto nových jádra. To je užitečné v tvarování finální verzi těchto jádra. Můžete nechat vaše komentáře/zpětnou vazbu v části **komentáře** části v dolní části tohoto článku.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
