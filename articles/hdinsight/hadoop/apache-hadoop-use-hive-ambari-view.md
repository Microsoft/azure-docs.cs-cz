---
title: Použití zobrazení Apache Ambari Hive s Apache Hadoop v Azure HDInsight
description: Přečtěte si, jak pomocí zobrazení Hive z webového prohlížeče odesílat dotazy hive. Zobrazení Hive je součástí webového uživatelského rozhraní Ambari dodávaného s vaším linuxovým clusterem HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745089"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive Apache s Apache Hadoopem v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Přečtěte si, jak spouštět dotazy Hive pomocí zobrazení Apache Ambari Hive View. Zobrazení Hive umožňuje vytvářet, optimalizovat a spouštět dotazy Hive z webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Na [portálu Azure](https://portal.azure.com/)vyberte svůj cluster.  Pokyny naleznete [v tématu Seznam a zobrazení clusterů.](../hdinsight-administer-use-portal-linux.md#showClusters) Cluster je otevřen v novém zobrazení portálu.

1. V **řídicích panelech clusteru**vyberte **zobrazení Ambari**. Po zobrazení výzvy k ověření použijte `admin`název účtu a heslo pro přihlášení clusteru (výchozí), které jste zadali při vytváření clusteru. Můžete také přejít na `https://CLUSTERNAME.azurehdinsight.net/#/main/views` `CLUSTERNAME` v prohlížeči, kde je název clusteru.

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

    Tyto příkazy provést následující akce:

    |Příkaz | Popis |
    |---|---|
    |DROP TABULKA|Odstraní tabulku a datový soubor v případě, že tabulka již existuje.|
    |VYTVOŘIT EXTERNÍ TABULKU|Vytvoří novou "externí" tabulku v Podregistru. Externí tabulky ukládají pouze definici tabulky v Hive. Data zůstanou v původním umístění.|
    |FORMÁT ŘÁDKU|Ukazuje, jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělena mezerou.|
    |ULOŽENÉ JAKO UMÍSTĚNÍ TEXTOVÉHO SOUBORU|Zobrazuje, kde jsou data uložena a že jsou uložena jako text.|
    |SELECT|Vybere počet všech řádků, kde sloupec t4 obsahuje hodnotu [ERROR].|

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

![Karta Uložené dotazy apache hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Uložené dotazy jsou uloženy ve výchozím úložišti clusteru. Uložené dotazy najdete pod cestou `/user/<username>/hive/scripts`. Ty jsou uloženy `.hql` jako soubory ve formátu prostého textu.
>
> Pokud odstraníte cluster, ale úložiště si ponesete, můžete k načtení dotazů použít nástroj, jako je [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) nebo Data Lake Storage Explorer (z [portálu Azure).](https://portal.azure.com)

## <a name="user-defined-functions"></a>Uživatelsky definované funkce

Můžete rozšířit Hive prostřednictvím uživatelem definovaných funkcí (UDF). Pomocí udf k implementaci funkce nebo logiky, která není snadno modelována v HiveQL.

Deklarujte a uložte sadu udf pomocí karty **UDF** v horní části zobrazení podregistru. Tyto udfs lze použít s **Editor dotazů**.

![Zobrazení karty UDF s zobrazením Apache Hive](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

V dolní části **Editoru dotazů**se zobrazí tlačítko **Vložit udfs** . Tato položka zobrazuje rozevírací seznam uofs definovaných v zobrazení Podhled. Výběrem příkazu UDF přidáte do dotazu příkazy HiveQL, aby bylo možné jej povolit.

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
* [Použití Java UDF s Apache Hive v HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Nastavení úlu

Můžete změnit různá nastavení Hive, například změnu modulu provádění pro Hive z Tez (výchozí) na MapReduce.

## <a name="next-steps"></a>Další kroky

Obecné informace o Hive na HDInsight:

* [Použití Apache Hive s Apache Hadoop na HDInsight](hdinsight-use-hive.md)

Informace o dalších způsobech práce s Hadoopem na HDInsight:

* [Použijte Apache Pig s Apache Hadoop na HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce s Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md)
