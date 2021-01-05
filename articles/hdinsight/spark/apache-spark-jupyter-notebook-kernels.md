---
title: Jádra pro Jupyter Notebook v clusterech Spark v Azure HDInsight
description: Přečtěte si o jádrech PySpark, PySpark3 a Spark pro Jupyter Notebook k dispozici v clusterech Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: b1139ce2358d5e5b3b52376892524e7ee0dd5611
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822229"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jádra pro Jupyter Notebook v clusterech Apache Spark ve službě Azure HDInsight

Clustery HDInsight Spark poskytují jádra, která můžete použít s Jupyter Notebook v [Apache Spark](./apache-spark-overview.md) pro testování aplikací. Jádro je program, který spouští a interpretuje váš kód. Tři jádra:

- **PySpark** – pro aplikace napsané v Python2.
- **PySpark3** – pro aplikace napsané v python3.
- **Spark** – pro aplikace napsané v Scala.

V tomto článku se naučíte používat tyto jádra a výhody jejich používání.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Vytvoření Jupyter Notebook ve službě Spark HDInsight

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster Spark.  Pokyny najdete v tématu [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) . Otevře se zobrazení **přehledu** .

2. V zobrazení **Přehled** vyberte v poli **řídicí panely clusteru** **Jupyter notebook**. Po vyzvání zadejte přihlašovací údaje správce clusteru.

    ![Jupyter Notebook v Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook ve Sparku")
  
   > [!NOTE]  
   > K Jupyter Notebook v clusteru Spark se můžete dostat taky tak, že v prohlížeči otevřete následující adresu URL. Nahraďte **název_clusteru** názvem vašeho clusteru:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Vyberte **Nový** a pak vyberte buď **Pyspark**, **PySpark3** nebo **Spark** , a vytvořte Poznámkový blok. Používejte jádro Sparku pro aplikace Scala, jádro PySpark pro aplikace Python2 a jádro PySpark3 pro aplikace python3.

    ![Jádra pro Jupyter Notebook ve Sparku](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jádra pro Jupyter Notebook ve Sparku")

4. Otevře se Poznámkový blok s vybraným jádrem.

## <a name="benefits-of-using-the-kernels"></a>Výhody používání jader

Tady je několik výhod používání nových jader s Jupyter Notebook v clusterech Spark HDInsight.

- **Přednastavené kontexty**. V prostředích  **PySpark**, **PySpark3** nebo **Sparku** nemusíte před zahájením práce s aplikacemi nastavovat kontexty Sparku nebo podregistru explicitně. Tyto kontexty jsou ve výchozím nastavení k dispozici. Tyto kontexty jsou:

  - **SC** – pro kontext Spark
  - **kontext SqlContext** – pro kontext podregistru

    Takže **nemusíte při** nastavování kontextů provádět příkazy, jako je následující:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Místo toho můžete přímo použít předdefinované kontexty ve vaší aplikaci.

- **Buňky Magic**. Jádro PySpark poskytuje některé předdefinované "Magic", což jsou speciální příkazy, které můžete volat pomocí `%%` (například `%%MAGIC` `<args>` ). Příkaz Magic musí být první slovo v buňce kódu a povoluje se více řádků obsahu. Slovo Magic by mělo být první slovo v buňce. Přidání cokoli před Magic, včetně komentářů, způsobí chybu.     Další informace o Magic najdete [tady](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    V následující tabulce jsou uvedeny různé Magic dostupné prostřednictvím jader.

   | Paket | Příklad | Popis |
   | --- | --- | --- |
   | Nápověda |`%%help` |Vygeneruje tabulku všech dostupných MAGICS s příkladem a popisem. |
   | příjemce |`%%info` |Vytvoří výstup informací o relaci pro aktuální koncový bod Livy. |
   | konfigurace |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry pro vytvoření relace. Příznak Force ( `-f` ) je povinný, pokud už je relace vytvořená, což zajistí, že se relace vynechá a znovu vytvoří. Seznam platných parametrů najdete v [textu žádosti post/Sessions pro Livy](https://github.com/cloudera/livy#request-body) . Parametry musí být předány jako řetězec JSON a musí být na dalším řádku po Magic, jak je znázorněno v příkladu sloupce. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Spustí dotaz na podregistr pro kontext SqlContext. Pokud `-o` je předán parametr, výsledek dotazu je trvalý v kontextu%% Local Python jako [PANDAS](https://pandas.pydata.org/) dataframe. |
   | local |`%%local`<br>`a=1` |Veškerý kód v pozdějších řádcích se spustí místně. Kód musí být platný kód Python2 bez ohledu na to, kterou jádro používáte. Takže i když jste při vytváření poznámkového bloku vybrali jádro **PySpark3** nebo **Spark** , v případě, `%%local` že jste v buňce použili Magic, musí mít tato buňka platný Python2 kód. |
   | Protokoly |`%%logs` |Vytvoří výstup protokolů pro aktuální relaci Livy. |
   | delete |`%%delete -f -s <session number>` |Odstraní konkrétní relaci aktuálního koncového bodu Livy. Nemůžete odstranit relaci, která je spuštěná pro samotný jádro. |
   | Vyčištění |`%%cleanup -f` |Odstraní všechny relace pro aktuální koncový bod Livy, včetně relace tohoto poznámkového bloku. Příznak Force-f je povinný. |

   > [!NOTE]  
   > Kromě Magic přidaných jádrem PySpark můžete také použít [integrované IPython Magic](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), včetně `%%sh` . Pomocí `%%sh` Magic můžete spouštět skripty a bloky kódu v clusteru hlavnímu uzlu.

- **Automatická vizualizace**. Jádro Pyspark automaticky vizualizuje výstup dotazů v podregistru a SQL. Můžete si vybrat mezi několika různými typy vizualizací, mezi které patří tabulka, výsečový, spojnicový, plošný a pruhový.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry podporované s%% SQL Magic

`%%sql`Magic podporuje různé parametry, které lze použít k řízení typu výstupu, který se zobrazí při spouštění dotazů. V následující tabulce je uveden výstup.

| Parametr | Příklad | Popis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Tento parametr použijte k uchování výsledku dotazu v kontextu%% Local Python jako [PANDAS](https://pandas.pydata.org/) dataframe. Název proměnné datového rámce je název proměnné, kterou zadáte. |
| -q |`-q` |Pomocí tohoto parametru můžete pro buňku vypnout vizualizace. Pokud nechcete, aby se obsah buňky využíval, a chcete ji pouze zachytit jako datový rámec, použijte `-q -o <VARIABLE>` . Pokud chcete vypnout vizualizace bez zachycení výsledků (například pro spuštění dotazu SQL, jako je například `CREATE TABLE` příkaz), použijte `-q` bez zadání `-o` argumentu. |
| -m |`-m <METHOD>` |Kde **Metoda** je buď buď **převzít** , nebo **vzorek** (výchozí **nastavení je)**. Pokud je metoda **`take`** , jádro vybere prvky z horní části sady výsledků dat určené v MAXROWS (popsané dále v této tabulce). Pokud je metoda **ukázková**, jádro náhodně vypíše prvky datové sady podle `-r` parametru popsaného dále v této tabulce. |
| -r |`-r <FRACTION>` |Pro **zlomek** je číslo s plovoucí desetinnou čárkou v rozsahu od 0,0 do 1,0. Pokud je ukázkovou metodou pro dotaz SQL `sample` , pak jádro náhodně vyvzorkuje zadaný zlomek prvků sady výsledků. Například pokud spustíte dotaz SQL s argumenty `-m sample -r 0.01` , pak je 1% řádků výsledků náhodně vzorkovat. |
| -n |`-n <MAXROWS>` |**MAXROWS** je celočíselná hodnota. Jádro omezuje počet výstupních řádků na **MAXROWS**. Pokud je **MAXROWS** záporné číslo, jako je hodnota **-1**, počet řádků v sadě výsledků není omezený. |

**Příklad:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

Výše uvedený příkaz provede následující akce:

- Vybere všechny záznamy z **hivesampletable**.
- Protože používáme-q, vypne se vizuálně.
- Protože používáme `-m sample -r 0.1 -n 500` , náhodně vyvzorkuje 10% řádků v hivesampletable a omezí velikost sady výsledků na 500 řádků.
- Nakonec, protože jsme `-o query2` ho použili také k uložení výstupu do dataframe s názvem **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Předpoklady při používání nových jader

Bez ohledu na to, kterou jádro použijete, ponechání poznámkových bloků se systémem spotřebovává prostředky clusteru.  S těmito jádry, protože kontexty jsou přednastaveny, jednoduše ukončení poznámkových bloků neukončí kontext. A proto se prostředky clusteru nadále používají. Dobrým postupem je použít možnost **Zavřít a zastavit** z nabídky **soubor** poznámkového bloku, až budete hotovi s používáním poznámkového bloku. Ukončení ukončuje kontext a pak ukončí Poznámkový blok.

## <a name="where-are-the-notebooks-stored"></a>Kde jsou poznámkové bloky uložené?

Pokud váš cluster používá Azure Storage jako výchozí účet úložiště, poznámkové bloky Jupyter se uloží do účtu úložiště ve složce **/HdiNotebooks** .  Poznámkové bloky, textové soubory a složky, které vytvoříte v rámci Jupyter, jsou přístupné z účtu úložiště.  Pokud například použijete Jupyter k vytvoření složky **`myfolder`** a poznámkového bloku **myFolder/MyNotebook. ipynb**, získáte přístup k tomuto poznámkovému bloku v `/HdiNotebooks/myfolder/mynotebook.ipynb` rámci účtu úložiště.  To znamená, že Pokud nahrajete Poznámkový blok přímo na účet úložiště v `/HdiNotebooks/mynotebook1.ipynb` , Poznámkový blok se také zobrazí z Jupyter.  Poznámkové bloky zůstávají v účtu úložiště i po odstranění clusteru.

> [!NOTE]  
> Clustery HDInsight s Azure Data Lake Storage jako výchozí úložiště neukládají poznámkové bloky do přidruženého úložiště.

Způsob ukládání poznámkových bloků do účtu úložiště je kompatibilní s [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Pokud ke clusteru použijete SSH, můžete použít příkazy pro správu souborů:

| Příkaz | Popis |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Seznam všeho v kořenovém adresáři – všechno, co je v tomto adresáři viditelné, se Jupyter z domovské stránky. |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # Stažení obsahu složky HdiNotebooks|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Nahrajte příklad poznámkového bloku. ipynb do kořenové složky tak, aby byl viditelný z Jupyter |

Bez ohledu na to, jestli cluster používá jako výchozí účet úložiště Azure Storage nebo Azure Data Lake Storage, poznámkové bloky se také ukládají do hlavnímu uzlu clusteru v `/var/lib/jupyter` .

## <a name="supported-browser"></a>Podporovaný prohlížeč

Jupyter poznámkové bloky v clusterech Spark HDInsight jsou podporované jenom na Google Chrome.

## <a name="suggestions"></a>Návrhy

Nové jádra jsou v rozvíjející se fázi a budou v průběhu času. Rozhraní API se tak můžou v případě vyspělých jader změnit. Vyhodnotili jsme jakoukoli zpětnou vazbu, kterou máte při používání těchto nových jader. Zpětná vazba je užitečná při vytváření finální verze těchto jader. Komentáře a připomínky můžete opustit v části **Zpětná vazba** v dolní části tohoto článku.

## <a name="next-steps"></a>Další kroky

- [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)
- [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
- [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
- [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)
