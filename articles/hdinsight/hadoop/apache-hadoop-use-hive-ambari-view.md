---
title: Použití zobrazení Ambari k práci s Hive v HDInsight (Hadoop) – Azure
description: Další informace o použití zobrazení Hivu z webového prohlížeče k odesílání dotazů Hive. Zobrazení Hive je součástí webového rozhraní Ambari, opatřeného vašeho clusteru HDInsight se systémem Linux.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 43b82070ced57c0654d646fbea5a12aeab7c2a31
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048517"
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive se systémem Hadoop v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Zjistěte, jak spouštět dotazy Hive pomocí zobrazení Ambari Hive. Zobrazení Hive umožňuje vytvářet, optimalizovat a spouštět dotazy Hive ze ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

* Hadoop založených na Linuxu v clusteru HDInsight verze 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Webový prohlížeč

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte svůj cluster HDInsight a pak vyberte **zobrazení Ambari** z **rychlé odkazy** oddílu.

    ![Části Rychlé odkazy na portálu](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Po zobrazení výzvy k ověření pomocí přihlášení ke clusteru (výchozí `admin`) účtu jméno a heslo, které jste zadali při vytváření clusteru.

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

   * `DROP TABLE`: Odstraní tabulku a datový soubor v v případě, že tabulka již existuje.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku "externí" v podregistru.
   Externí tabulky uložte definici tabulky Hive. Data zůstane v původním umístění.

   * `ROW FORMAT`: Zobrazí formátování data. V tomto případě pole v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Označuje, kde jsou data uložená a uložená jako text.

   * `SELECT`: Vybere počet všech řádků, kde t4 sloupec obsahuje hodnotu [Chyba].

    > [!IMPORTANT]
    > Nechte __databáze__ výběr na __výchozí__. V příkladech v tomto dokumentu pomocí výchozí databáze je součástí HDInsight.

5. Chcete-li spustit dotaz, použijte **Execute** tlačítko pod listem. Tlačítko oranžově a se text změní na **Zastavit**.

6. Po dokončení dotazu **výsledky** karta zobrazuje výsledky operace. Následující text je výsledek dotazu:

        loglevel       count
        [ERROR]        3

    Můžete použít **protokoly** kartu, chcete-li zobrazit informace o vytvoření úlohy protokolování.

   > [!TIP]
   > Stáhnout nebo uložit výsledky z **uložit výsledky** rozevíracím seznamu dialogového okna v horním levém rohu **výsledky zpracování dotazu** oddílu.

### <a name="visual-explain"></a>Vysvětlují vizuálu

Chcete-li zobrazit vizualizaci plán dotazu, vyberte **Visual vysvětlují** kartu pod listem.

**Visual vysvětlují** zobrazení dotazu může být užitečné porozumět toku složitým dotazům. Textový ekvivalent toto zobrazení můžete zobrazit pomocí **vysvětlit** tlačítko v editoru dotazů.

### <a name="tez-ui"></a>Uživatelského rozhraní tez

Chcete-li zobrazit uživatelského rozhraní Tez pro dotaz, vyberte **Tez** kartu pod listem.

> [!IMPORTANT]
> Chcete-li vyřešit všechny dotazy se nepoužívá tez. Mnoho dotazů lze vyřešit bez použití Tez. 

Pokud Tez se použil při překladu dotazu, zobrazí se orientovaného Acyklického grafu (DAG). Pokud chcete zobrazit orientovaného acyklického grafu pro dotazy, které jste spustili v minulosti, nebo pokud chcete ladit proces Tez, použijte [Tez zobrazení](../hdinsight-debug-ambari-tez-view.md) místo.

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

* Název třídy UDF: com.myudfs.Awesome

Použití **vložit UDF** tlačítku zobrazí položka s názvem **myudfs**, s jinou rozevíracího seznamu pro každý UDF definované pro daný prostředek. V takovém případě je **myawesomeudf**. Výběrem této položky přidá na začátek dotazu následující:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Pak můžete systému souborů UDF v dotazu. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o používání funkcí UDF pomocí Hive v HDInsight najdete v následujících článcích:

* [Použití Pythonu s Hivem a Pigem ve HDInsight](python-udf-hdinsight.md)
* [Jak přidat vlastní Hive UDF pro HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Nastavení hivu

Můžete změnit různá nastavení Hive, jako je například změna prováděcí modul pro Hive na MapReduce z Tez (výchozí).

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive v HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)

Další informace o dalších způsobech můžete pracovat s Hadoop v HDInsight:

* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)
