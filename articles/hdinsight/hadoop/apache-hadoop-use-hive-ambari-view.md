---
title: Použití zobrazení Apache Ambari Hive s Apache Hadoop v Azure HDInsight
description: Přečtěte si, jak pomocí zobrazení Hive z webového prohlížeče odesílat dotazy hive. Zobrazení Hive je součástí webového uživatelského rozhraní Ambari dodávaného s vaším linuxovým clusterem HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 6c199a0dd75b89d9c9368e799c97a28b73758d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73097103"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive Apache s Apache Hadoopem v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Přečtěte si, jak spouštět dotazy Hive pomocí zobrazení Apache Ambari Hive View. Zobrazení Hive umožňuje vytvářet, optimalizovat a spouštět dotazy Hive z webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).
* Webový prohlížeč

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Na [portálu Azure](https://portal.azure.com/)vyberte svůj cluster.  Pokyny naleznete [v tématu Seznam a zobrazení clusterů.](../hdinsight-administer-use-portal-linux.md#showClusters) Cluster je otevřen v novém portálu blade.

1. V **řídicích panelech clusteru**vyberte **zobrazení Ambari**. Po zobrazení výzvy k ověření použijte `admin`název účtu a heslo pro přihlášení clusteru (výchozí), které jste zadali při vytváření clusteru. Případně přejděte `https://CLUSTERNAME.azurehdinsight.net/#/main/views` v prohlížeči, kde `CLUSTERNAME` je název clusteru.

1. V seznamu zobrazení vyberte __zobrazit zobrazení Hive__.

    ![Apache Ambari vybrat Apache Hive zobrazení](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Stránka zobrazení Hive je podobná následujícímu obrázku:

    ![Obrázek listu dotazu pro zobrazení Úl](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Na kartě __Dotaz__ vložte do listu následující příkazy HiveQL:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Tyto příkazy provádět následující akce:

   * `DROP TABLE`: Odstraní tabulku a datový soubor v případě, že tabulka již existuje.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou "externí" tabulku v podregistru.
     Externí tabulky ukládají pouze definici tabulky v Hive. Data zůstanou v původním umístění.

   * `ROW FORMAT`: Zobrazuje formátdat. V tomto případě jsou pole v každém protokolu oddělena mezerou.

   * `STORED AS TEXTFILE LOCATION`: Zobrazuje, kde jsou data uložena a která jsou uložena jako text.

   * `SELECT`: Vybere počet všech řádků, kde sloupec t4 obsahuje hodnotu [ERROR].

   > [!IMPORTANT]  
   > Ponechejte výběr __databáze__ __ve výchozím nastavení__. Příklady v tomto dokumentu používají výchozí databázi, která je součástí hdinsightu.

1. Chcete-li dotaz spustit, vyberte **spustit** pod listem. Tlačítko se změní na oranžovou barvu a text se změní na **Zastavit**.

1. Po dokončení dotazu se na kartě **Výsledky** zobrazí výsledky operace. Výsledkem dotazu je následující text:

        loglevel       count
        [ERROR]        3

    Kartu **LOG** můžete použít k zobrazení informací protokolování, které úloha vytvořila.

   > [!TIP]  
   > Stáhněte nebo uložte výsledky z rozevíracího okna **Akce** na kartě **Výsledky.**

### <a name="visual-explain"></a>Vizuální vysvětlení

Chcete-li zobrazit vizualizaci plánu dotazů, vyberte pod listem kartu **Vizuální vysvětlení.**

**Visual Vysvětlit** zobrazení dotazu může být užitečné při pochopení toku složitých dotazů.

### <a name="tez-ui"></a>Uz ui

Chcete-li zobrazit uhlavní zobrazení tezu pro dotaz, vyberte kartu **Tez UI** pod listem.

> [!IMPORTANT]  
> Tez se nepoužívá k vyřešení všech dotazů. Mnoho dotazů můžete vyřešit bez použití aplikace Tez.

## <a name="view-job-history"></a>Zobrazení historie úlohy

Na kartě __Úlohy__ se zobrazuje historie dotazů Hive.

![Historie karet karty Zobrazení Apache Hive](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Databázové tabulky

Kartu __Tabulky__ můžete použít k práci s tabulkami v databázi Hive.

![Obrázek karty Tabulek Apache Hive](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Uložené dotazy

Na kartě **Dotaz** můžete volitelně ukládat dotazy. Po uložení dotazu jej můžete znovu použít na kartě __Uložené dotazy.__

![Karta Uložené dotazy zobrazení Apache Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Uložené dotazy jsou uloženy ve výchozím úložišti clusteru. Uložené dotazy najdete pod cestou `/user/<username>/hive/scripts`. Ty jsou uloženy `.hql` jako soubory ve formátu prostého textu.
>
> Pokud odstraníte cluster, ale úložiště si ponesete, můžete k načtení dotazů použít nástroj, jako je [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) nebo Data Lake Storage Explorer (z [portálu Azure).](https://portal.azure.com)

## <a name="user-defined-functions"></a>Uživatelsky definované funkce

Můžete rozšířit Hive prostřednictvím uživatelem definovaných funkcí (UDF). Pomocí udf k implementaci funkce nebo logiky, která není snadno modelována v HiveQL.

Deklarujte a uložte sadu udf pomocí karty **UDF** v horní části zobrazení podregistru. Tyto udfs lze použít s **Editor dotazů**.

![Zobrazení karty UDF s zobrazením Apache Hive](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po přidání udf do zobrazení Hive se v dolní části **Editoru dotazů**zobrazí tlačítko **Vložit udfs** . Výběrem této položky se zobrazí rozevírací seznam ufdefinovaných v zobrazení Podhled. Výběrem příkazu UDF přidáte do dotazu příkazy HiveQL, aby bylo možné jej povolit.

Pokud jste například definovali zdroj UDF s následujícími vlastnostmi:

* Název zdroje: myudfs

* Cesta k prostředku: /myudfs.jar

* UDF jméno: myawesomeudf

* Název třídy UDF: com.myudfs.Awesome

Pomocí tlačítka **Vložit udfs** se zobrazí položka s názvem **myudfs**s dalším rozevíracím seznamem pro každý udf definovaný pro tento zdroj. V tomto případě je to **myawesomeudf**. Výběrem této položky přidáte na začátek dotazu následující:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Potom můžete použít UDF v dotazu. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o používání udimfů s hive na HDInsight, naleznete v následujících článcích:

* [Používání Pythonu s Apache Hive a Apache Pig v HDInsightu](python-udf-hdinsight.md)
* [Jak přidat vlastní Apache Hive UDF do HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Nastavení úlu

Můžete změnit různá nastavení Hive, například změnu modulu provádění pro Hive z Tez (výchozí) na MapReduce.

## <a name="next-steps"></a><a id="nextsteps"></a>Další kroky

Obecné informace o Hive na HDInsight:

* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Informace o dalších způsobech práce s Hadoopem na HDInsight:

* [Použijte Apache Pig s Apache Hadoop na HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
