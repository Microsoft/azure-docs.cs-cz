---
title: Použití zobrazení Apache Ambari k práci s podregistrum v HDInsight (Apache Hadoop) – Azure
description: Naučte se používat zobrazení podregistru z webového prohlížeče k odesílání dotazů na podregistr. Zobrazení podregistru je součástí webového uživatelského rozhraní Ambari, které je k dispozici v clusteru HDInsight se systémem Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: da4d1ed7dec8b3b0bc61dd2959a868d03875039c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077014"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Použití zobrazení podregistru Apache Ambari s Apache Hadoop ve službě HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se spouštět dotazy na podregistry pomocí zobrazení podregistru Apache Ambari. Zobrazení podregistru umožňuje vytvářet, optimalizovat a spouštět dotazy na podregistr z webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Webový prohlížeč

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster.  Pokyny najdete v tématu [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) . Cluster se otevře v novém okně portálu.

2. Z **řídicích panelů clusteru**vyberte **zobrazení Ambari**. Po zobrazení výzvy k ověření použijte název a heslo přihlášení clusteru `admin`(výchozí), které jste zadali při vytváření clusteru.

3. V seznamu zobrazení vyberte __zobrazení podregistru__.

    ![Ambari, výběr zobrazení Apache Hive pro Apache](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Stránka zobrazení podregistru je podobná následujícímu obrázku:

    ![Obrázek listu dotazu pro zobrazení podregistru](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. Na kartě __dotaz__ vložte následující příkazy HiveQL do listu:

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

    Tyto příkazy provádějí následující akce:

   * `DROP TABLE`: Odstraní tabulku a datový soubor pro případ, že tabulka již existuje.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou "externí" tabulku v podregistru.
     Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.

   * `ROW FORMAT`: Ukazuje, jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělená mezerou.

   * `STORED AS TEXTFILE LOCATION`: Zobrazuje, kde jsou data uložená, a že je uložená jako text.

   * `SELECT`: Vybere počet všech řádků, ve kterých sloupec T4 obsahuje hodnotu [Chyba].

   > [!IMPORTANT]  
   > Ponechte výběr __databáze__ ve __výchozím nastavení__. Příklady v tomto dokumentu používají výchozí databázi, která je součástí HDInsight.

5. Chcete-li spustit dotaz, vyberte možnost **Spustit** pod listem. Tlačítko se změní na oranžová a text se změní na **Zastaveno**.

6. Po dokončení dotazu se na kartě **výsledky** zobrazí výsledky operace. Následující text je výsledkem dotazu:

        loglevel       count
        [ERROR]        3

    K zobrazení informací o protokolování, které vytvořila úloha, můžete použít kartu **protokol** .

   > [!TIP]  
   > Stáhněte nebo uložte výsledky z rozevíracího seznamu **Akce** na kartě **výsledky** .

### <a name="visual-explain"></a>Vizuálně vysvětlit

Chcete-li zobrazit vizualizaci plánu dotazu, vyberte kartu **vizuálně vysvětlit** pod listem.

Zobrazení **vizuálního vysvětlujícího** dotazu může být užitečné při seznámení s tokem složitých dotazů.

### <a name="tez-ui"></a>Uživatelské rozhraní tez

Pokud chcete zobrazit uživatelské rozhraní tez pro dotaz, vyberte kartu **uživatelské rozhraní tez** pod listem.

> [!IMPORTANT]  
> Tez se nepoužívá k vyřešení všech dotazů. Můžete vyřešit mnoho dotazů bez použití TEZ.

## <a name="view-job-history"></a>Zobrazení historie úlohy

Karta __úlohy__ zobrazuje historii dotazů na podregistr.

![Historie karty Apache Hive zobrazení úloh](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabulky databáze

Kartu __tabulky__ můžete použít pro práci s tabulkami v rámci databáze podregistru.

![Obrázek karty Apache Hive tabulky](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Uložené dotazy

Na kartě **dotaz** můžete volitelně ukládat dotazy. Po uložení se dotaz dá znovu použít na kartě __uložené dotazy__ .

![Karta zobrazení uložených dotazů Apache Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Uložené dotazy jsou uloženy ve výchozím úložišti clusteru. Uložené dotazy můžete najít pod cestou `/user/<username>/hive/scripts`. Tyto soubory jsou uloženy jako soubory ve `.hql` formátu prostého textu.
>
> Pokud cluster odstraníte, ale zachováte ho, můžete k načtení dotazů použít nástroj, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nebo data Lake Storage Explorer (na webu [Azure Portal](https://portal.azure.com)).

## <a name="user-defined-functions"></a>Uživatelsky definované funkce

Podregistr můžete roztáhnout prostřednictvím uživatelsky definovaných funkcí (UDF). Použijte systém souborů UDF k implementaci funkcí nebo logiky, která není snadno modelována v HiveQL.

Deklarujete a uložte sadu UDF pomocí karty **UDF** v horní části zobrazení podregistru. Tyto UDF se dají použít spolu s **editorem dotazů**.

![Zobrazení karty UDF zobrazení Apache Hive](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po přidání systému souborů UDF do zobrazení podregistru se v dolní části **Editoru dotazů**zobrazí tlačítko **Vložit UDF** . Výběrem této položky se zobrazí rozevírací seznam UDF definovaného v zobrazení podregistru. Když vyberete UDF, do dotazu se přidá příkazy HiveQL, které zapnete systém souborů UDF.

Například pokud jste definovali UDF s následujícími vlastnostmi:

* Název prostředku: myudfs

* Cesta prostředku:/myudfs.jar

* Název UDF: myawesomeudf

* Název třídy UDF: com. myudfs. super

Pomocí tlačítka pro **vložení UDF** se zobrazí položka s názvem **myudfs**a další rozevírací seznam pro každé UDF definované pro daný prostředek. V tomto případě je to **myawesomeudf**. Výběrem této položky se na začátek dotazu přidá následující text:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Pak můžete použít ve svém dotazu UDF. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o použití UDF s podregistrum v HDInsight najdete v následujících článcích:

* [Použití Pythonu s Apache Hive a Apache prasetem v HDInsight](python-udf-hdinsight.md)
* [Postup přidání vlastního Apache Hive UDF do HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Nastavení podregistru

Můžete změnit různá nastavení podregistru, jako je například změna spouštěcího modulu pro podregistr z tez (výchozí) na MapReduce.

## <a id="nextsteps"></a>Další kroky

Obecné informace o podregistru v HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech práce se systémem Hadoop ve službě HDInsight:

* [Použití systému Apache prasete s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce s Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
