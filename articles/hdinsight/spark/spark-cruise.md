---
title: Urychlení Apache Spark dotazů pomocí SparkCruise ve službě Azure HDInsight
description: Naučte se používat optimalizační platformu SparkCruise ke zvýšení efektivity Apache Spark dotazů.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 1a73b4707f83d6a23dffc20d95aa7b8a0fa465b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649053"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise ve službě Azure HDInsight

Tento dokument popisuje funkci Azure HDInsight *SparkCruise*, která automaticky znovu používá výpočty Apache Spark k zvýšení efektivity dotazů.

## <a name="overview"></a>Přehled

Dotazy, které spouštíte na analytické platformě, jako je například Apache Spark, jsou rozloženy do plánu dotazů, který obsahuje menší poddotazy. Tyto poddotazy se můžou v plánech dotazů opakovaně zobrazovat v různých dotazech. Pokaždé, když k nim dojde, se znovu spustí, aby vracely výsledky. Opětovné spuštění stejného dotazu však může být neefektivní a může vést k zbytečným nákladům na výpočetní výkon.

*SparkCruise* je platforma pro optimalizaci zatížení, která může znovu použít běžné výpočty a snížit celkovou dobu provádění dotazů a náklady na přenos dat. Platforma používá koncept *materializované zobrazení*, což je dotaz, jehož výsledky jsou uloženy v předem vypočítané podobě. Tyto výsledky se pak dají znovu použít, když se dotaz znovu zobrazí později, než se znovu dovede k výsledkům.

## <a name="setup-and-installation"></a>Nastavení a instalace

SparkCruise je k dispozici na všech clusterech HDInsight 4,0 s Spark 2,3 nebo 2,4. Soubory knihovny SparkCruise se nainstalují do `/opt/peregrine/` adresáře v clusteru HDInsight. Pro správné fungování *SparkCruise* vyžaduje následující vlastnosti konfigurace, které jsou ve výchozím nastavení nastaveny.

* `spark.sql.queryExecutionListeners` je nastaven na `com.microsoft.peregrine.spark.listeners.PlanLogListener` , což umožňuje protokolování plánů dotazů.
* `spark.sql.extensions` je nastaven na `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , který umožňuje pravidla optimalizace pro online materializace a opakované použití.

## <a name="computation-reuse-in-spark-sql"></a>Opakované použití výpočtu v Spark SQL

Následující vzorový scénář ukazuje, jak použít *SparkCruise* k optimalizaci Apache Spark dotazů. 

1. SSH do hlavního uzlu clusteru Spark.
1. Zadejte příkaz `spark-shell`.
1. Spusťte následující fragment kódu, který spouští několik základních dotazů využívajících ukázková data v clusteru.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Pomocí nástroje pro analýzu dotazů *SparkCruise* můžete analyzovat plány dotazů předchozích dotazů, které jsou uloženy v protokolech aplikace Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Zobrazit výstup procesu analýzy, který je souborem zpětné vazby. Tento soubor zpětné vazby obsahuje poznámky k budoucím dotazům SQL Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze`Příkaz analyzuje plány dotazů a vytvoří tabulkovou reprezentaci úlohy. K této tabulce úloh se dá dotázat pomocí poznámkového bloku *WorkloadInsights* , který je zahrnutý v úložišti [ukázek HDInsight SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples) . Pak `views` příkaz identifikuje běžné výrazy dílčího plánu a vybere zajímavé výrazy dílčího plánu pro budoucí materializaci a opakované použití. Výstupem je soubor zpětné vazby, který obsahuje poznámky k budoucím dotazům SQL Spark. 

`show`Příkaz zobrazí výstup podobný následujícímu textu:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Soubor zpětné vazby obsahuje položky v následujícím formátu: `subplan-identifier [Materialize|Reuse] input/path/to/action` . V tomto příkladu jsou dva jedinečné podpisy: jeden představuje první dva opakované dotazy a druhý představující predikát filtru v posledních dvou dotazech. V tomto souboru zpětné vazby se teď následující dotazy automaticky vyhodnotit a znovu použijí běžné podplány. 

Chcete-li otestovat optimalizace, spusťte jinou sadu ukázkových dotazů.

1. Zadejte příkaz `spark-shell`.
1. Spustit následující fragment kódu

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Opětovným zobrazením souboru zpětné vazby zobrazíte podpisy používaných dotazů.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show`Příkaz poskytuje výstup podobný následujícímu textu:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

I když se hodnota literálu v dotazu změnila z `'11%'` na `'12%'` , *SparkCruise* může stále odpovídat předchozím dotazům s mírnými variantami, jako je například vývoj hodnot literálů a verzí datových sad. Pokud v dotazu dojde k významným změnám, můžete znovu spustit nástroj pro analýzu a identifikovat nové dotazy, které se dají znovu použít.

Na pozadí *SparkCruise* spustí poddotaz pro vyhodnocování vybraného podplánu z prvního dotazu, který ho obsahuje. Později dotazy mohou přímo číst materializované podplány namísto jejich zpracování. V této úloze budou podplány vyhodnoceny online způsobem podle prvního a třetího dotazu. Až budou běžné podplány materializované, můžeme zobrazit plán změny dotazů.

Vidíte, že teď jsou k dispozici čtyři nové materializované podvýrazy, které by se měly znovu použít v dalších dotazech.

## <a name="clean-up"></a>Vyčištění

Soubory zpětné vazby, materializované podplány a protokoly dotazů jsou trvale v rámci relací Spark. Pokud chcete tyto soubory odebrat, spusťte následující příkaz:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Další kroky

* [K určení výhod SparkCruise použijte Poznámkový blok přehled úloh](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Zvýšení výkonu Apache Spark úloh pomocí Azure HDInsight v/v cache](apache-spark-improve-performance-iocache.md)
* [Optimalizace úloh Apache Spark v HDInsight](./apache-spark-perf.md)
* [SparkCruise: opakované použití výpočtu handsfree ve Sparku](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Pokyny pro Apache Spark v Azure HDInsight](./spark-best-practices.md)
