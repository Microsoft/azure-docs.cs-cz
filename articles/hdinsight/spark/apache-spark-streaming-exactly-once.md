---
title: Vytvoření úlohy streamování Sparku s přesně-událostí zpracování – Azure HDInsight
description: Jak vytvořit streamování Sparku pro zpracování událostí pouze jednou a jednou.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 78d18bfe0f47517067fbb053a2d7e076b15761a7
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52580996"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Vytvoření úlohy streamování Apache Sparku s přesně-událostí zpracování

Stream zpracování aplikací využít různé přístupy jak jejich zpracování opětovné zpracování zpráv po některé selhání v systému:

* Alespoň jednou: Každá zpráva je zaručeno, že má být zpracován, ale může získat zpracuje více než jednou.
* Nanejvýš jednou: Každá zpráva může nebo nemusí být zpracován. Pokud zpráva se zpracuje, to je zpracována pouze jednou.
* Právě jednou: Každá zpráva je zaručeno, že mají být zpracovány pouze jednou a jednou.

Tento článek ukazuje, jak nakonfigurovat Spark Streaming k dosažení přesně – jedno zpracování.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Přesně-jednou sémantika s Apache Spark Streaming

Nejprve, zvažte, jak všechny body selhání systému restartuje se vyskytl problém, a jak se můžete vyhnout ztrátě dat. Aplikace Spark Streaming má:

* Vstupní zdroj
* Jeden nebo více procesů příjemce, které o přijetí změn dat ze vstupního zdroje
* Úlohy, které zpracovávají data
* Výstupní jímky
* Ovladač proces, který spravuje dlouho běžící úlohy

Přesně – Jakmile sémantiku vyžaduje, aby se neztratí kdykoli a zpracování této zprávy je restartování, bez ohledu na to, kde dojde k selhání.

### <a name="replayable-sources"></a>Opakovatelná zdroje

Aplikace Spark Streaming je čtení události ze zdroje musí být *opakovatelná*. To znamená, že v případech, ve kterém zpráva se načetla, ale pak systém selhal předtím, než může být zachována nebo zpracovat zprávu, zdroj musí poskytnout stejnou zprávu znovu.

V Azure, jak Azure Event Hubs a [Apache Kafka](https://kafka.apache.org/) na HDInsight poskytují opakovatelná zdroje. Další příklad opakovatelná zdroje je odolné proti chybám souborový systém jako [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), objekty BLOB služby Azure Storage nebo Azure Data Lake Store, ve kterém všechna data se ukládají navždy a v libovolném okamžiku můžete znovu načtěte data v celém rozsahu.

### <a name="reliable-receivers"></a>Spolehlivé příjemců

Ve Spark Streaming, zdrojů, jako jsou Event Hubs a Kafka *spolehlivé příjemci*, kde každý příjemce uchovává informace o průběhu čtení zdroji. Spolehlivé příjemce nevyřeší stavu do úložiště odolné proti chybám, buď v rámci [Apache ZooKeeper](https://zookeeper.apache.org/) nebo Spark Streaming kontrolní body zapisovat do rozhraní HDFS. Pokud takový příjemce selže a je pozdější restartování, ho můžete pokračovat tam, kde skončila.

### <a name="use-the-write-ahead-log"></a>Použití protokolu dávky zápisu

Spark Streaming podporuje použití dávky zápisu protokolu, kde každé přijaté události, je nejprve zapisovat do adresáře kontrolního bodu pro Spark ve službě storage odolné proti chybám a pak uloženy v odolné Distributed Dataset (RDD). V Azure je odolné úložiště HDFS se opírá o Azure Storage nebo Azure Data Lake Store. V aplikaci Spark Streaming dávky zápisu protokolu se povoluje pro všechny příjemce nastavením `spark.streaming.receiver.writeAheadLog.enable` nastavení konfigurace `true`. Dávky zápisu protokolu poskytuje odolnost proti chybám pro selhání ovladače a prováděcí moduly.

Pro pracovní procesy spuštěné úkoly s daty události je každý RDD podle definice replikovat i distribuované napříč několika pracovních procesů. Pokud se úkol nezdaří, protože pracovní proces spuštění by došlo k chybě, úloha se restartuje u dalšího pracovního procesu, která má repliku dat událostí, takže události nedojde ke ztrátě.

### <a name="use-checkpoints-for-drivers"></a>Použití kontrolních bodů pro ovladače

Ovladače úlohy musí být nabízet možnost restartování. Pokud dojde k chybě ovladač spuštění aplikace Spark Streaming, trvá s ním všechny spuštěné přijímače, úkoly a všechny Rdd ukládání dat událostí. V takovém případě musíte být schopni uložit průběh úlohy, takže ji lze později obnovit. To lze provést pomocí vytváření kontrolních bodů orientovaného acyklický graf (DAG) z DStream pravidelně do úložiště odolné proti chybám. DAG metadata obsahují konfigurace použité k vytvoření datových proudů aplikace, operace, které definují aplikaci a všechny listy, které jsou zařazeny do fronty, ale ještě není dokončený. Tato metadata umožňuje selhání ovladače restartovat z kontrolního bodu informací. Po restartování ovladač spustí nové příjemce, že sami zpět do z protokolu předběžné Rdd obnovit data události.

Kontrolní body jsou povolené v Spark Streaming ve dvou krocích. 

1. V objektu StreamingContext konfiguraci cesty úložiště pro body obnovení:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    V HDInsight tyto kontrolní body uložit do výchozího úložiště připojené k vašemu clusteru služby Azure Storage nebo Azure Data Lake Store.

2. Dále určete kontrolního bodu interval (v sekundách) na DStream. V každém intervalu se ukládají data o stavu odvozené ze vstupních událostí do úložiště. Trvalý stav dat může snížit výpočetní potřeby při opětovném sestavování stav ze zdroje události.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Použití idempotentní jímky

Cílové jímky, do které vaši úlohu zapisuje výsledky musí být schopný zvládnout situaci, kdy se klíči přiřadí stejného výsledku více než jednou. Jímka musí být schopen rozpoznat tyto duplicitní výsledky a je ignorovat. *Idempotentní* jímky lze volat vícekrát se stejnými daty beze změny stavu.

Implementací logiku, která zkontroluje existenci příchozí výsledek v úložišti dat, můžete vytvořit jímky idempotentní. Pokud výsledek již existuje, zápis by se měla zobrazit na úspěšné z hlediska vaší úlohy Spark, ale ve skutečnosti vaším úložištěm dat ignorována duplicitní data. Pokud výsledek neexistuje, pak jímka vhodné vložit tento nový výsledek do jeho úložiště. 

Můžete například použít uloženou proceduru s Azure SQL Database, která se vloží do tabulky událostí. Tuto uloženou proceduru nejprve vyhledal události na základě klíčových polí a jenom v případě, že není žádná odpovídající událost nalezen záznam vloženy do tabulky.

Dalším příkladem je pomocí systému souborů oddílů, jako jsou objekty BLOB služby Azure Storage nebo Azure Data Lake store. V tomto případě logiky jímky nemusí kontrolovat přítomnost souboru. Pokud existuje soubor, který na událost, jednoduše přepíše se stejnými daty. V opačném případě se vytvoří nový soubor v umístění počítaný.

## <a name="next-steps"></a>Další postup

* [Apache Spark Streaming přehled](apache-spark-streaming-overview.md)
* [Vytvoření vysoce dostupné úlohy streamování Apache Sparku v Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
