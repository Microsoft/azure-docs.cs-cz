---
title: Použití zobrazení Ambari Apache pro práci s Hive v HDInsight (Apache Hadoop) – Azure
description: Další informace o použití zobrazení Hivu z webového prohlížeče k odesílání dotazů Hive. Zobrazení Hive je součástí webového rozhraní Ambari, opatřeného vašeho clusteru HDInsight se systémem Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095315"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive Apache s Apache Hadoop v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Zjistěte, jak spouštět dotazy Hive pomocí zobrazení Hive Apache Ambari. Zobrazení Hive umožňuje vytvářet, optimalizovat a spouštět dotazy Hive ze ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](./apache-hadoop-linux-tutorial-get-started.md).
* Webový prohlížeč

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Z [webu Azure portal](https://portal.azure.com/), vyberte svůj cluster.  Zobrazit [výpisu a zobrazení clusterů](../hdinsight-administer-use-portal-linux.md#showClusters) pokyny. Cluster se otevře v novém okně portálu.

2. Z **řídicí panely clusteru**vyberte **zobrazení Ambari**. Po zobrazení výzvy k ověření pomocí přihlášení ke clusteru (výchozí `admin`) účtu jméno a heslo, které jste zadali při vytváření clusteru.

3. V seznamu zobrazení, vyberte __zobrazení Hive__.

    ![Vybrané zobrazení Hivu](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Stránka zobrazení Hive je podobně jako na následujícím obrázku:

    ![Obrázek listu dotazů zobrazení Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Z __dotazu__ kartu, vložte následující příkazy HiveQL do pracovního listu:

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

   * `DROP TABLE`: Odstraní tabulku a datový soubor, v případě, že tabulka již existuje.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku "externí" v podregistru.
     Externí tabulky uložte definici tabulky Hive. Data zůstane v původním umístění.

   * `ROW FORMAT`: Ukazuje, jak je ve formátu data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Ukazuje, kde jsou data uložená a uložená jako text.

   * `SELECT`: Počet všech řádků, kde t4 sloupec obsahuje hodnotu [Chyba] vybere.

   > [!IMPORTANT]  
   > Nechte __databáze__ výběr na __výchozí__. V příkladech v tomto dokumentu pomocí výchozí databáze je součástí HDInsight.

5. Chcete-li spustit dotaz, vyberte **Execute** pod listem. Tlačítko oranžově a se text změní na **Zastavit**.

6. Po dokončení dotazu **výsledky** karta zobrazuje výsledky operace. Následující text je výsledek dotazu:

        loglevel       count
        [ERROR]        3

    Můžete použít **protokolu** kartu, chcete-li zobrazit informace o vytvoření úlohy protokolování.

   > [!TIP]  
   > Stáhnout nebo uložit výsledky z **akce** rozevíracím seznamu dialogového okna **výsledky** kartu.

### <a name="visual-explain"></a>Vysvětlují vizuálu

Chcete-li zobrazit vizualizaci plán dotazu, vyberte **Visual vysvětlují** kartu pod listem.

**Visual vysvětlují** zobrazení dotazu může být užitečné porozumět toku složitým dotazům.

### <a name="tez-ui"></a>Tez UI

Chcete-li zobrazit uživatelského rozhraní Tez pro dotaz, vyberte **uživatelského rozhraní Tez** kartu pod listem.

> [!IMPORTANT]  
> Chcete-li vyřešit všechny dotazy se nepoužívá tez. Mnoho dotazů lze vyřešit bez použití Tez. 

## <a name="view-job-history"></a>Zobrazení historie úlohy

__Úlohy__ karta zobrazuje historii dotazů Hive.

![Snímek historie úlohy](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>databázové tabulky

Můžete použít __tabulky__ kartu pro práci s tabulkami v databázi Hive.

![Obrázek karty tabulek](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Uložené dotazy

Z **dotazu** kartu, můžete volitelně ukládat dotazy. Po uložení dotazu, můžete znovu použít ho __uložené dotazy__ kartu.

![Obrázek karty uložené dotazy](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Uložené dotazy jsou uloženy v výchozí úložiště clusteru. Uložené dotazy v rámci cesty můžete najít `/user/<username>/hive/scripts`. Tyto jsou uložené jako prostý text `.hql` soubory.
>
> Pokud cluster odstranit, ale zachovat úložiště, můžete použít nástroje, jako je třeba [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nebo Data Lake Storage Explorer (z [webu Azure Portal](https://portal.azure.com)) načíst dotazy.

## <a name="user-defined-functions"></a>Uživatelem definované funkce

Hive je možné rozšířit pomocí uživatelem definovaných funkcí (UDF). Uživatelem definovanou FUNKCI použijte k implementaci funkce nebo logiku, která se snadno modelován v HiveQL.

Deklarace a uložit pomocí sady funkcí UDF **UDF** kartě v horní části zobrazení Hive. Tyto funkce lze použít s **editoru dotazů**.

![Obrázek karty UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po přidání uživatelem definovanou FUNKCI zobrazení Hive **vložit UDF** tlačítko se zobrazí v dolní části **editoru dotazů**. Výběrem této položky se zobrazí rozevírací seznam UDF definované v zobrazení Hive. Výběr UDF přidá příkazy HiveQL do váš dotaz, který povolí UDF.

Například pokud jste definovali UDF s následujícími vlastnostmi:

* Název prostředku: myudfs

* Cesta k prostředku: /myudfs.jar

* Název systému souborů UDF: myawesomeudf

* UDF class name: com.myudfs.Awesome

Použití **vložit UDF** tlačítku zobrazí položka s názvem **myudfs**, s jinou rozevíracího seznamu pro každý UDF definované pro daný prostředek. V takovém případě je **myawesomeudf**. Výběrem této položky přidá na začátek dotazu následující:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Pak můžete systému souborů UDF v dotazu. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o používání funkcí UDF pomocí Hive v HDInsight najdete v následujících článcích:

* [Použití Pythonu s Apache Hivu a Apache Pig v HDInsight](python-udf-hdinsight.md)
* [Jak přidat vlastní účet Apache Hive UDF pro HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Nastavení hivu

Můžete změnit různá nastavení Hive, jako je například změna prováděcí modul pro Hive na MapReduce z Tez (výchozí).

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive v HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
