---
title: Streamování Sparku & právě jednou při zpracování událostí – Azure HDInsight
description: Jak nastavit streamování Apache Spark pro zpracování události jednou a jenom jednou.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2018
ms.openlocfilehash: 8e0037f6aea4aef53efc192066027e0a0143bda1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086173"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Vytváření úloh Apache Spark streamování s právě jedním zpracováním událostí

Aplikace pro zpracování datových proudů mají různé přístupy k tomu, jak zpracovávají zprávy o rezpracování po určité chybě v systému:

* Alespoň jednou: je zaručeno, že každá zpráva bude zpracována, ale může být zpracována více než jednou.
* Nejvýše jednou: každá zpráva může nebo nemusí být zpracována. Pokud je zpráva zpracována, je zpracována pouze jednou.
* Přesně jednou: každou zprávu je zaručeno, že bude zpracována pouze jednou a pouze jednou.

V tomto článku se dozvíte, jak nakonfigurovat streamování Sparku tak, aby bylo možné provést právě jedno zpracování.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Sémantika právě jednou pomocí Apache Spark streaming

Nejprve zvažte, jak se všechny systémové body po selhání restartují po problému a jak se můžete vyhnout ztrátě dat. Aplikace pro streamování Spark má:

* Vstupní zdroj.
* Jeden nebo více procesů přijímače, které vyžádají data ze vstupního zdroje.
* Úlohy, které zpracovávají data.
* Výstupní jímka.
* Proces ovladače, který spravuje dlouhodobě běžící úlohu.

Sémantika právě jednou vyžaduje, aby nedošlo ke ztrátě dat, a zpracování zprávy je znovu spuštěno bez ohledu na to, kde dojde k selhání.

### <a name="replayable-sources"></a>Prohrajteelné zdroje

Zdroj aplikace streamování Sparku čte vaše události z musí být možné *Přehrát*. To znamená, že v případech, kdy byla zpráva načtena, ale systém selhal předtím, než mohla být zpráva trvala nebo zpracována, zdroj musí znovu zadat stejnou zprávu.

V Azure můžou Azure Event Hubs i [Apache Kafka](https://kafka.apache.org/) v HDInsight poskytovat nahrajteelné zdroje. Dalším příkladem opakovaného zdroje je souborový systém odolný proti chybám, jako je [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure Storage BLOBs nebo Azure Data Lake Storage, kde jsou všechna data trvale zachovaná a v jakémkoli okamžiku můžete data znovu přečíst.

### <a name="reliable-receivers"></a>Spolehlivé přijímače

Ve streamování Spark mají zdroje, jako je Event Hubs a Kafka, *spolehlivé přijímače*, kde každý příjemce sleduje svůj průběh čtení zdroje. Spolehlivý přijímač uchovává svůj stav do úložiště odolného proti chybám, a to buď v [Apache Zookeeper](https://zookeeper.apache.org/) , nebo ve kontrolním bodu Spark streamování zapsaným na HDFS. Pokud takový přijímač neuspěje a později se restartuje, může se tam, kde se přestal, nacházet.

### <a name="use-the-write-ahead-log"></a>Použití protokolu Write-Ahead

Služba Spark Stream podporuje použití protokolu Write-Ahead, kde se každá přijatá událost nejdřív do úložiště odolného proti chybám zapisuje do adresáře kontrolního bodu Sparku a pak se uloží do odolné distribuované datové sady (RDD). V Azure je úložiště odolné proti chybám HDFS v Azure Storage nebo Azure Data Lake Storage. V aplikaci pro streamování Sparku je protokol Write-Ahead povolený pro všechny přijímače nastavením `spark.streaming.receiver.writeAheadLog.enable` nastavení konfigurace na `true` . Protokol Write-Ahead poskytuje odolnost proti chybám při selhání ovladače i prováděcích modulů.

Pro pracovníky, kteří spouštějí úlohy s daty události, je každá RDD podle definice replikovaná i distribuovaná napříč více procesy. Pokud úloha selže, protože v pracovním procesu došlo k chybě, úloha bude restartována v jiném pracovním procesu, který má repliku dat události, takže dojde ke ztrátě události.

### <a name="use-checkpoints-for-drivers"></a>Použití kontrolních bodů pro ovladače

Ovladače úlohy je potřeba spustit znovu. Pokud ovladač, který spouští aplikaci streamování Sparku, selže, poběží se všemi běžícími přijímači, úlohami a všemi RDD uloženými daty události. V takovém případě musíte být schopni Uložit průběh úlohy, abyste ji mohli později obnovit. K tomu je potřeba vytvořit kontrolní bod orientovaného acyklického grafu (DAG) DStream v pravidelných intervalech do úložiště odolného proti chybám. Metadata DAG obsahují konfiguraci, která se používá k vytvoření aplikace pro streamování, operací, které definují aplikaci, a všech dávek, které jsou ve frontě, ale ještě nedokončené. Tato metadata umožňují restartování ovladače, který selhal, z informací kontrolního bodu. Po restartování ovladače se spustí noví příjemci, kteří sami obnoví data události zpátky do RDD z protokolu Write-Ahead.

Kontrolní body jsou povolené ve streamování Sparku ve dvou krocích.

1. V objektu StreamingContext nakonfigurujte cestu úložiště pro kontrolní body:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    V HDInsight by se měly tyto kontrolní body ukládat do výchozího úložiště připojeného ke clusteru, a to buď Azure Storage, nebo Azure Data Lake Storage.

2. Dále zadejte interval kontrolního bodu (v sekundách) na DStream. V každém intervalu jsou data o stavu odvozená ze vstupní události trvale uložena do úložiště. Trvalá data stavu můžou snižovat výpočet potřebný při sestavování stavu ze zdrojové události.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Použití jímky idempotentní

Cílová jímka, do které vaše úloha zapisuje výsledky, musí být schopna zpracovat situaci, kdy je výsledkem stejného výsledku více než jednou. Jímka musí být schopna detekovat takové duplicitní výsledky a ignorovat je. Jímka *idempotentní* může být volána vícekrát se stejnými daty bez změny stavu.

Můžete vytvořit idempotentní jímka pomocí implementace logiky, která nejprve kontroluje existenci příchozího výsledku v úložišti dat. Pokud výsledek již existuje, měl by se zdát, že zápis bude úspěšný z perspektivy vaší úlohy Sparku, ale ve skutečnosti vaše úložiště dat ignoruje duplicitní data. Pokud výsledek neexistuje, jímka by měla tento nový výsledek vložit do svého úložiště.

Můžete například použít uloženou proceduru s Azure SQL Database, která vloží události do tabulky. Tato uložená procedura nejprve vyhledá událost pomocí klíčových polí a pouze v případě, že se nenajde žádná shodná událost, záznam vložený do tabulky.

Dalším příkladem je použití děleného systému souborů, například Azure Storage objektů BLOB nebo Azure Data Lake Storage. V takovém případě vaše logika jímky nemusí kontrolovat existenci souboru. Pokud soubor představující událost existuje, je jednoduše přepsán stejnými daty. V opačném případě se vytvoří nový soubor na vypočítané cestě.

## <a name="next-steps"></a>Další kroky

* [Přehled streamování Apache Spark](apache-spark-streaming-overview.md)
* [Vytváření úloh s vysokou dostupností Apache Spark streamování v Apache Hadoop nitě](apache-spark-streaming-high-availability.md)
