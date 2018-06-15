---
title: Při práci s Hive v HDInsight (Hadoop) - Azure používají zobrazení Ambari | Microsoft Docs
description: Naučte se používat k odesílání dotazů Hive zobrazení Hive z webového prohlížeče. Zobrazení Hive je součástí webové uživatelské rozhraní Ambari poskytuje k vašemu clusteru HDInsight se systémem Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 6c07e9a45cbfbc5e6ed6787277dcfa1bf4cf4b2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201488"
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Použití zobrazení Ambari Hive se systémem Hadoop v HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informace o spouštění dotazů Hive pomocí zobrazení Ambari Hive. Zobrazení Hive umožňuje vytvářet a optimalizovat a spouštění dotazů Hive z webového prohlížeče.

## <a name="prerequisites"></a>Požadavky

* Systémem Linux Hadoop na verzi clusteru HDInsight 3.4 nebo vyšší.

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Webový prohlížeč

## <a name="run-a-hive-query"></a>Spuštění dotazu Hive

1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte HDInsight cluster a pak vyberte **zobrazení Ambari** z **rychlé odkazy** části.

    ![Rychlé odkazy sekci portálu](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Po zobrazení výzvy k ověření pomocí přihlášení clusteru (výchozí `admin`) účet jméno a heslo, které jste zadali při vytvoření clusteru.

3. Ze seznamu zobrazení, vyberte __zobrazení Hive__.

    ![Vybrané zobrazení Hive](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Stránka zobrazení Hive se podobně jako na následujícím obrázku:

    ![Obrázek listu dotazu pro zobrazení Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Z __dotazu__ kartě, vložte následující příkazy HiveQL do listu:

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

   * `DROP TABLE`: Odstraní tabulku a datový soubor v v případě, že v tabulce již existuje.

   * `CREATE EXTERNAL TABLE`: Vytvoří novou tabulku "externí" v Hive.
   Externí tabulky uložit pouze definici tabulky Hive. Data je ponechán v původním umístění.

   * `ROW FORMAT`: Ukazuje způsob formátování data. V takovém případě polí v každém protokolu jsou oddělené mezerou.

   * `STORED AS TEXTFILE LOCATION`: Zobrazí se uloží data a že je uloženo jako text.

   * `SELECT`: Vybere počet všech řádků, kde t4 sloupec obsahuje hodnotu [Chyba].

    > [!IMPORTANT]
    > Ponechte __databáze__ výběr v __výchozí__. V příkladech v tomto dokumentu použijte výchozí databázi součástí HDInsight.

5. Pokud chcete spustit dotaz, použít **Execute** tlačítko pod listu. Tlačítko se změní oranžová a se text změní na **Zastavit**.

6. Po dokončení dotazu **výsledky** karta zobrazuje výsledky operace. Tento text je výsledkem dotazu:

        loglevel       count
        [ERROR]        3

    Můžete použít **protokoly** a zobrazit informace o protokolování, které úloha vytvořena.

   > [!TIP]
   > Stahování nebo uložení výsledků **výsledky uložit** dialogové rozevírací seznam v horní pravé **výsledky zpracování dotazu** části.

### <a name="visual-explain"></a>Vysvětlují Visual

Chcete-li zobrazit vizualizaci plán dotazu, vyberte **vysvětlují, Visual** kartě níže listu.

**Visual vysvětlují** zobrazení dotazu může být užitečné porozumět toku složitých dotazů. Textové ekvivalent v tomto zobrazení můžete zobrazit pomocí **vysvětlit** tlačítko v editoru dotazů.

### <a name="tez-ui"></a>Tez uživatelského rozhraní

Pokud chcete zobrazit uživatelské rozhraní Tez pro dotaz, vyberte **Tez** kartě níže listu.

> [!IMPORTANT]
> Chcete-li vyřešit všechny dotazy se nepoužije tez. Abyste mohli vyřešit mnoho dotazů bez použití Tez. 

Pokud Tez byl použit k vyřešení dotazu, zobrazí se přesměruje Acyklické grafu (DAG). Pokud chcete zobrazit DAG pro dotazy, které jste si vyzkoušeli v minulosti, nebo pokud chcete ladit proces Tez, použijte [Tez zobrazení](../hdinsight-debug-ambari-tez-view.md) místo.

## <a name="view-job-history"></a>Zobrazení historie úlohy

__Úlohy__ karta se zobrazuje historie dotazů Hive.

![Obrázek historie úlohy](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Databázové tabulky

Můžete použít __tabulky__ kartě práce s tabulkami v rámci databáze Hive.

![Obrázek kartu tabulky](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Uložené dotazy

Z **dotazu** kartě, můžete volitelně uložit dotazy. Po uložení dotazu se můžete znovu použít z __uložené dotazy__ kartě.

![Obrázek karty uložené dotazy](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]
> Uložené dotazy jsou uloženy v úložišti clusteru výchozí. Můžete najít uložené dotazy na cestě `/user/<username>/hive/scripts`. Tyto jsou uložené jako prostý text `.hql` soubory.
>
> Pokud odstranění clusteru, ale ponechat úložiště, můžete použít nástroj jako [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) nebo Data Lake Storage Explorer (z [portálu Azure](https://portal.azure.com)) pro načtení dotazy.

## <a name="user-defined-functions"></a>Uživatelem definované funkce

Hive můžete rozšířit pomocí uživatelem definovaných funkcí (UDF). Implementace funkce nebo logiky, která není snadno modelován v HiveQL pomocí uživatelem definovanou FUNKCI.

Deklarace a ukládání sadu UDF pomocí **UDF** karty v horní části zobrazení Hive. Tyto funkce lze použít s **Editor dotazů**.

![Obrázek karty UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Po přidání uživatelem definovanou FUNKCI k zobrazení Hive **vložit UDF** tlačítko se zobrazí v dolní části **Editor dotazů**. Výběrem této položky se zobrazí rozevírací seznam UDF definované v zobrazení Hive. Výběr UDF přidá příkazy HiveQL do dotazu povolit UDF.

Například pokud jste definovali UDF s následujícími vlastnostmi:

* Název prostředku: myudfs

* Cesta prostředku: /myudfs.jar

* Název UDF: myawesomeudf

* Název třídy UDF: com.myudfs.Awesome

Pomocí **vložit UDF** tlačítko zobrazí položka s názvem **myudfs**, s jinou rozevíracího seznamu pro každý UDF definované pro tento prostředek. V takovém případě je **myawesomeudf**. Výběrem této položky přidáte následující na začátek dotazu:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Potom můžete UDF v dotazu. Například, `SELECT myawesomeudf(name) FROM people;`.

Další informace o těchto funkcích pomocí Hive v HDInsight naleznete v následujících článcích:

* [Python pomocí Hive a Pig v HDInsight](python-udf-hdinsight.md)
* [Jak přidat vlastní Hive UDF do HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Nastavení Hive

Můžete změnit různá nastavení Hive, jako je například změna spouštěcímu modulu pro Hive z Tez (výchozí) na MapReduce.

## <a id="nextsteps"></a>Další kroky

Obecné informace o Hive v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)
