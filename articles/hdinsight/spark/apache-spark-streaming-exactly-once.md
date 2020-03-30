---
title: Streamování spark & právě jednou zpracování událostí – Azure HDInsight
description: Jak nastavit Apache Spark Streaming pro zpracování události jednou a jen jednou.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228976"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Vytvářejte úlohy Apache Spark Streaming pomocí právě jednou zpracování událostí

Aplikace pro zpracování datových proudů mají různé přístupy k tomu, jak zpracovávají zprávy o přepracování po selhání systému:

* Alespoň jednou: Každá zpráva je zaručena zpracování, ale může získat zpracovány více než jednou.
* Maximálně jednou: Každá zpráva může nebo nemusí být zpracována. Pokud je zpráva zpracována, je zpracována pouze jednou.
* Přesně jednou: Každá zpráva je zaručena, že budou zpracovány jednou a pouze jednou.

Tento článek ukazuje, jak nakonfigurovat Streamování Spark tak, aby bylo dosaženo přesně jednou zpracování.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Přesně jednou sémantika s Apache Spark Streaming

Nejprve zvažte, jak se po problému restartují všechny systémové body selhání a jak se můžete vyhnout ztrátě dat. Aplikace Spark Streaming má:

* Vstupní zdroj.
* Jeden nebo více příjemců procesů, které vytahují data ze vstupního zdroje.
* Úkoly, které zpracovávají data.
* Výstupní jímka.
* Proces ovladače, který spravuje dlouhotrvající úlohu.

Přesně jednou sémantiku vyžadují, aby žádná data ztracena v libovolném bodě a že zpracování zpráv je restartovatelný, bez ohledu na to, kde dojde k selhání.

### <a name="replayable-sources"></a>Přehrání zdrojů

Zdroj, ze který aplikace Spark Streaming čte vaše události, musí být *přehratelný*. To znamená, že v případech, kdy byla zpráva načtena, ale potom systém selhal před zprávu může být trvalé nebo zpracovány, zdroj musí poskytnout stejnou zprávu znovu.

V Azure azure event huby a [Apache Kafka](https://kafka.apache.org/) na HDInsight poskytují replayable zdroje. Dalším příkladem přehrávatelného zdroje je systém souborů odolný proti chybám, jako je [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), objekty BLOB azure storage nebo Azure Data Lake Storage, kde jsou všechna data uchovávána navždy a kdykoli můžete znovu číst data v plném rozsahu.

### <a name="reliable-receivers"></a>Spolehlivé přijímače

V Streamování Spark mají zdroje jako Event Hubs a Kafka *spolehlivé přijímače*, kde každý přijímač sleduje průběh čtení zdroje. Spolehlivý přijímač přetrvá na svůj stav do úložiště odolného proti chybám, a to buď v rámci [Apache ZooKeeper](https://zookeeper.apache.org/) nebo v kontrolních bodech Streamování Spark zapsaných do HDFS. Pokud takový přijímač selže a je později restartován, může pokračovat tam, kde skončil.

### <a name="use-the-write-ahead-log"></a>Použití protokolu pro zápis do předu

Spark Streaming podporuje použití protokolu pro zápis do předu, kde každá přijatá událost je nejprve zapsána do adresáře kontrolního bodu Spark v úložišti odolném proti chybám a poté uložena v odolné distribuované datové sadě (RDD). V Azure je úložiště odolné proti chybám HDFS podporované buď Azure Storage nebo Azure Data Lake Storage. V aplikaci Spark Streaming je protokol pro zápis dopředného `spark.streaming.receiver.writeAheadLog.enable` zápisu `true`povolen pro všechny příjemce nastavením nastavení konfigurace na . Protokol pro zápis dopředposkytuje odolnost proti chybám při chybách ovladače i prováděcích modulů.

Pro pracovníky, kteří spouštějí úlohy s daty událostí, je každý rdd podle definice replikován a distribuován mezi více pracovníky. Pokud se úloha nezdaří, protože pracovník, který ji spustil, havaroval, bude úloha restartována u jiného pracovníka, který má repliku dat události, takže událost nebude ztracena.

### <a name="use-checkpoints-for-drivers"></a>Použití kontrolních bodů pro řidiče

Ovladače úloh musí být restartovatelné. Pokud dojde k chybě ovladače spuštěné aplikace Spark Streaming, vezme s sebou všechny spuštěné přijímače, úlohy a všechny RDD, které ukládají data událostí. V takovém případě musíte být schopni uložit průběh úlohy, abyste ji mohli pokračovat později. Toho lze dosáhnout pomocí kontrolních bodů řízeného acyklického grafu (DAG) DStream pravidelně na úložiště odolné proti chybám. Metadata DAG zahrnuje konfiguraci použitou k vytvoření aplikace pro streamování, operace, které definují aplikaci, a všechny dávky, které jsou zařazeny do fronty, ale ještě nejsou dokončeny. Tato metadata umožňují restartování neúspěšného ovladače z informací kontrolního bodu. Po restartování ovladače se spustí nové přijímače, které samy obnoví data událostí zpět do RDD z protokolu zápisu dopředu.

Kontrolní body jsou povoleny v streamování Spark ve dvou krocích.

1. V objektu StreamingContext nakonfigurujte cestu úložiště pro kontrolní body:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    V HDInsight by se tyto kontrolní body měly ukládat do výchozího úložiště připojeného k vašemu clusteru, buď Azure Storage, nebo Azure Data Lake Storage.

2. Dále zadejte interval kontrolního bodu (v sekundách) na DStream. V každém intervalu jsou data stavu odvozená ze vstupní události zachována do úložiště. Trvalá data stavu může snížit výpočtu potřebné při opětovném sestavení stavu ze zdrojové události.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Používejte idempotentní dřezy

Cílové jímky, do kterého vaše úloha zapisuje výsledky musí být schopen zpracovat situaci, kdy je uveden stejný výsledek více než jednou. Jímka musí být schopna rozpoznat takové duplicitní výsledky a ignorovat je. *Idempotentní* jímka může být volána vícekrát se stejnými daty bez e-mailu beze změny stavu.

Můžete vytvořit idempotentní jímky implementací logiky, která nejprve zkontroluje existenci příchozí výsledek v datastore. Pokud výsledek již existuje, zápis by se měl jevit jako úspěšný z hlediska úlohy Spark, ale ve skutečnosti vaše úložiště dat ignorovalo duplicitní data. Pokud výsledek neexistuje, jímka by měla vložit tento nový výsledek do svého úložiště.

Můžete například použít uloženou proceduru s Azure SQL Database, která vloží události do tabulky. Tato uložená procedura nejprve vyhledá událost podle klíčových polí a pouze v případě, že nebyl nalezen žádný odpovídající událost je záznam vložen do tabulky.

Dalším příkladem je použití rozděleného souborového systému, jako jsou objekty BLOB Azure Storage nebo Azure Data Lake Storage. V takovém případě logika jímky není nutné zkontrolovat existenci souboru. Pokud soubor představující událost existuje, je jednoduše přepsán stejnými daty. V opačném případě je na vypočítané cestě vytvořen nový soubor.

## <a name="next-steps"></a>Další kroky

* [Přehled streamování Apache Spark](apache-spark-streaming-overview.md)
* [Vytváření vysoce dostupných pracovních míst Apache Spark Streaming v Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
