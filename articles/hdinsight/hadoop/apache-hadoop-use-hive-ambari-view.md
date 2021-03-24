---
title: Použití zobrazení podregistru Apache Ambari s Apache Hadoop ve službě Azure HDInsight
description: Naučte se používat zobrazení podregistru z webového prohlížeče k odesílání dotazů na podregistr. Zobrazení podregistru je součástí webového uživatelského rozhraní Ambari, které je k dispozici v clusteru HDInsight se systémem Linux.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 87a4d3960937450713747fa16bd473b4c34eff0e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867861"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive Apache s Apache Hadoopem v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Naučte se spouštět dotazy na podregistry pomocí zobrazení podregistru Apache Ambari. Zobrazení podregistru umožňuje vytvářet, optimalizovat a spouštět dotazy na podregistr z webového prohlížeče.

## <a name="prerequisites"></a>Předpoklady

Cluster Hadoop ve službě HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Z [Azure Portal](https://portal.azure.com/)vyberte svůj cluster.  Pokyny najdete v tématu [seznam a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) . Cluster se otevře v novém zobrazení portálu.

1. Z **řídicích panelů clusteru** vyberte **zobrazení Ambari**. Po zobrazení výzvy k ověření použijte název a heslo přihlášení clusteru (výchozí `admin` ), které jste zadali při vytváření clusteru. Můžete také přejít do `https://CLUSTERNAME.azurehdinsight.net/#/main/views` prohlížeče v prohlížeči, kde `CLUSTERNAME` je název vašeho clusteru.

1. V seznamu zobrazení vyberte __zobrazení podregistru__.

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png" alt-text="Ambari, výběr zobrazení Apache Hive pro Apache" border="true":::

    Stránka zobrazení podregistru je podobná následujícímu obrázku:

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png" alt-text="Obrázek listu dotazu pro zobrazení podregistru" border="true":::

1. Na kartě __dotaz__ vložte následující příkazy HiveQL do listu:

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

    Tyto příkazy provedou následující akce:

    |Příkaz | Popis |
    |---|---|
    |DROP TABLE|Odstraní tabulku a datový soubor pro případ, že tabulka již existuje.|
    |VYTVOŘIT EXTERNÍ TABULKU|Vytvoří novou "externí" tabulku v podregistru. Externí tabulky ukládají pouze definici tabulky v podregistru. Data zůstanou v původním umístění.|
    |FORMÁT ŘÁDKU|Ukazuje, jak jsou data formátována. V tomto případě jsou pole v každém protokolu oddělená mezerou.|
    |ULOŽENO JAKO UMÍSTĚNÍ TEXTFILE|Zobrazuje, kde jsou data uložená, a že je uložená jako text.|
    |SELECT|Vybere počet všech řádků, ve kterých sloupec T4 obsahuje hodnotu [Chyba].|

   > [!IMPORTANT]  
   > Ponechte výběr __databáze__ ve __výchozím nastavení__. Příklady v tomto dokumentu používají výchozí databázi, která je součástí HDInsight.

1. Chcete-li spustit dotaz, vyberte možnost **Spustit** pod listem. Tlačítko se změní na oranžová a text se změní na **Zastaveno**.

1. Po dokončení dotazu se na kartě **výsledky** zobrazí výsledky operace. Následující text je výsledkem dotazu:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    K zobrazení informací o protokolování, které vytvořila úloha, můžete použít kartu **protokol** .

   > [!TIP]  
   > Stáhněte nebo uložte výsledky z rozevíracího seznamu **Akce** na kartě  **výsledky** .

### <a name="visual-explain"></a>Vizuálně vysvětlit

Chcete-li zobrazit vizualizaci plánu dotazu, vyberte kartu **vizuálně vysvětlit** pod listem.

Zobrazení **vizuálního vysvětlujícího** dotazu může být užitečné při seznámení s tokem složitých dotazů.

### <a name="tez-ui"></a>Uživatelské rozhraní tez

Pokud chcete zobrazit uživatelské rozhraní tez pro dotaz, vyberte kartu **uživatelské rozhraní tez** pod listem.

> [!IMPORTANT]  
> Tez se nepoužívá k vyřešení všech dotazů. Můžete vyřešit mnoho dotazů bez použití TEZ.

## <a name="view-job-history"></a>Zobrazení historie úlohy

Karta __úlohy__ zobrazuje historii dotazů na podregistr.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png" alt-text="Historie karty Apache Hive zobrazení úloh" border="true":::

## <a name="database-tables"></a>Tabulky databáze

Kartu __tabulky__ můžete použít pro práci s tabulkami v rámci databáze podregistru.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png" alt-text="Obrázek karty Apache Hive tabulky" border="true":::

## <a name="saved-queries"></a>Uložené dotazy

Na kartě **dotaz** můžete volitelně ukládat dotazy. Po uložení se dotaz dá znovu použít na kartě __uložené dotazy__ .

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png" alt-text="Karta uložené dotazy v Apache Hive zobrazení" border="true":::

> [!TIP]  
> Uložené dotazy jsou uloženy ve výchozím úložišti clusteru. Uložené dotazy můžete najít pod cestou `/user/<username>/hive/scripts` . Tyto soubory jsou uloženy jako soubory ve formátu prostého textu `.hql` .
>
> Pokud cluster odstraníte, ale zachováte ho, můžete k načtení dotazů použít nástroj, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nebo data Lake Storage Explorer (na webu [Azure Portal](https://portal.azure.com)).

## <a name="user-defined-functions"></a>Uživatelem definované funkce

Podregistr můžete roztáhnout prostřednictvím uživatelsky definovaných funkcí (UDF). Použijte systém souborů UDF k implementaci funkcí nebo logiky, která není snadno modelována v HiveQL.

Deklarujete a uložte sadu UDF pomocí karty **UDF** v horní části zobrazení podregistru. Tyto UDF se dají použít spolu s **editorem dotazů**.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png" alt-text="Zobrazení karty UDF zobrazení Apache Hive" border="true":::

V dolní části **Editoru dotazů** se zobrazí tlačítko **Vložit UDF** . Tato položka zobrazí rozevírací seznam UDF definovaného v zobrazení podregistru. Když vyberete UDF, do dotazu se přidá příkazy HiveQL, které zapnete systém souborů UDF.

Například pokud jste definovali UDF s následujícími vlastnostmi:

* Název prostředku: myudfs

* Cesta prostředku:/myudfs.jar

* Název UDF: myawesomeudf

* Název třídy UDF: com. myudfs. super

Pomocí tlačítka pro **vložení UDF** se zobrazí položka s názvem **myudfs** a další rozevírací seznam pro každé UDF definované pro daný prostředek. V tomto případě je to **myawesomeudf**. Výběrem této položky se na začátek dotazu přidá následující text:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Pak můžete použít ve svém dotazu UDF. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o použití UDF s podregistrum v HDInsight najdete v následujících článcích:

* [Použití Pythonu s Apache Hive a Apache prasetem v HDInsight](python-udf-hdinsight.md)
* [Použití Java UDF s Apache Hive v HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Nastavení podregistru

Můžete změnit různá nastavení podregistru, jako je například změna spouštěcího modulu pro podregistr z tez (výchozí) na MapReduce.

## <a name="next-steps"></a>Další kroky

Obecné informace o podregistru v HDInsight:

* [Použití Apache Hive s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití klienta Apache Beeline s Apache Hivem](apache-hadoop-use-hive-beeline.md)
