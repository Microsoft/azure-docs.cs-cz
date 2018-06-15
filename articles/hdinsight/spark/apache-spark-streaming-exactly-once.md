---
title: Vytvoření úlohy streamování Spark se přesně-jednou událostí zpracování - Azure HDInsight | Microsoft Docs
description: Jak nastavit vysílání datového proudu Spark pro zpracování událostí po a pouze jednou.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: 48b1d3811f3a8f6190c58e9646ab0d820859fc21
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164854"
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>Vytvoření úlohy streamování Spark se přesně-jednou událostí zpracování

Aplikace zpracování datového proudu trvat různý přístup jak jejich zpracování opětovné zpracování zprávy po některé selhání v systému:

* Alespoň jednou: Každá zpráva záruku, že se mají být zpracovány, ale může získat zpracování více než jednou.
* Maximálně jednou: Každá zpráva může nebo nemusí být dokončeno. Pokud zpracování zpráva je pouze zpracována jednou.
* Právě jednou: Každá zpráva záruku, že se mají být zpracovány jednou a pouze jednou.

V tomto článku se dozvíte, jak nakonfigurovat k dosažení přesně vysílání datového proudu Spark – jedno zpracování.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Přesně-jednou sémantiku pomocí vysílání datového proudu Spark

Nejprve, zvažte, jak všechny systému body selhání restartovat po s problémem, a ztrátě dat se můžete vyhnout. Má k vysílání datového proudu Spark aplikaci:

* Vstupní zdroj
* Jeden nebo více procesy příjemce, které používají data z vstupního zdroje
* Úlohy, které zpracovávají data
* Výstupní jímku
* Proces ovladač, který spravuje dlouho běžící úlohy

Přesně-po sémantiku vyžadovat, aby žádná data dojde ke ztrátě v libovolném bodě, a zpracování této zprávy se dá se restartovat, bez ohledu na to, kde dojde k selhání.

### <a name="replayable-sources"></a>Replayable zdroje

Musí být zdroj vysílání datového proudu Spark aplikace je načítání událostí z *replayable*. To znamená, že v případech, kdy byla načtena zprávy, ale pak systému se nezdařila před zpráva může být trvalý nebo zpracování, zdroj nutné zadat stejnou zprávu znovu.

V Azure Azure Event Hubs a Kafka v HDInsight poskytují replayable zdroje. Dalším příkladem replayable zdroje je systém odolný proti chybám souborů jako HDFS, objektů BLOB služby Azure Storage, nebo Azure Data Lake Store, kde všechna data se ukládají navždy a v libovolném bodě můžete znovu můžete číst data v celé jeho šíři.

### <a name="reliable-receivers"></a>Spolehlivé příjemci

V datové proudy Spark, zdroje jako Event Hubs a Kafka *spolehlivé příjemci*, kde každý příjemce uchovává informace o jejím průběhu čtení zdroji. Spolehlivé příjemce trvá stavu do úložiště odolné proti chybám, v rámci ZooKeeper nebo v datové proudy Spark kontrolní body zapsána do HDFS. Pokud takový příjemce selže a je pozdější restartování rozpoznal kde bylo přerušeno.

### <a name="use-the-write-ahead-log"></a>Použít předběžné protokolování

Vysílání datového proudu Spark podporuje použití předběžné protokolování, kde je každý přijatou událost nejprve zapisovat do adresáře kontrolního bodu Spark v odolné proti chybám úložiště a pak uloženy v odolné distribuované datovou sadu (RDD). V Azure je odolný proti chybám úložiště HDFS založenou na Azure Storage nebo Azure Data Lake Store. V aplikaci vysílání datového proudu Spark předběžné protokolování povoleno pro všechny příjemci nastavením `spark.streaming.receiver.writeAheadLog.enable` nastavení konfigurace pro `true`. Předběžné protokolování poskytuje odolnost proti chybám pro selhání ovladače a vykonavatelů.

Pro pracovníky spuštěným úlohy pro data události je každý RDD podle definice replikovat i rozdělené mezi více pracovníků. Pokud úloha selže, protože pracovní spuštěním došlo k chybě, úloha se restartuje u dalšího pracovního procesu, která má repliku dat událostí, takže události není ztraceny.

### <a name="use-checkpoints-for-drivers"></a>Pomocí kontrolních bodů pro ovladače

Ovladače úlohy musí být nabízet možnost restartování. Pokud dojde k chybě ovladače spuštění aplikace vysílání datového proudu Spark, jak dlouho trvá s ním všechny spuštěné příjemci, úlohy a všechny RDDs ukládání dat události. V takovém případě musíte moci ukládat průběh úlohy, můžete ji později obnovit. To lze provést vytváření kontrolních bodů směrované Acyklické grafu (DAG) systému DStream pravidelně do úložiště odolné proti chybám. DAG metadata zahrnuje konfiguraci použít k vytvoření datových proudů aplikace, operace, které definují aplikaci a všechny balíků, které jsou zařazeny do fronty, ale ještě nebyla dokončena. Tato metadata umožňuje ovladač se nezdařilo restartovat z kontrolního bodu informací. Po restartování ovladače, spustíte nové příjemci sami obnovit data události zpět do RDDs z předběžné protokolování.

Kontrolní body jsou povolené v vysílání datového proudu Spark ve dvou krocích. 

1. V objektu StreamingContext nakonfigurujte cesta k úložišti pro kontrolní body:

    Val ssc = nové StreamingContext (spark, Seconds(1)) ssc.checkpoint("/path/to/checkpoints")

    V prostředí HDInsight je vhodné tyto kontrolní body uložit do výchozí úložiště připojené ke clusteru, Azure Storage nebo Azure Data Lake Store.

2. Na DStream dále zadáním intervalu kontrolního bodu (v sekundách). V příslušném intervalu data o stavu odvozené od vstupní událost jako trvalý, do úložiště. Setrvalý stav dat může snížit výpočet potřebné při opětovném sestavování stav ze zdroje událostí.

    Val řádky = ssc.socketTextStream ("název hostitele", 9999) lines.checkpoint(30) ssc.start() ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Použití idempotent jímky

Cílový jímky kam úlohu zapisuje výsledky musí být schopna zpracovávat situaci, kde ji je zadána stejný výsledek více než jednou. Jímky musí být schopen zjistit taková duplicitní výsledky a je ignorovat. *Idempotent* podřízený lze volat vícekrát se stejnými daty beze změny stavu.

Implementací logiky, která nejprve zkontroluje existenci příchozí výsledek v úložišti dat můžete vytvořit idempotent jímky. Pokud výsledek již existuje, by se měla objevit zápis z pohledu úlohy Spark proběhla úspěšně, ale ve skutečnosti data store ignorována duplicitní data. Pokud výsledek neexistuje, pak jímky vhodné vložit tento nový výsledek do jeho úložiště. 

Můžete například použít uložené procedury s Azure SQL Database, která vloží do tabulky události. Tuto uloženou proceduru nejdříve vyhledá události klíčová pole a jenom v případě, že není žádná odpovídající událost najít záznam vloženy do tabulky.

Dalším příkladem je použití systému souborů oddílů, jako jsou objektů BLOB Azure Storage nebo Azure Data Lake store. V takovém případě logika podřízený nemusí kontrolovat existenci souboru. Pokud existuje soubor představující události, se jednoduše přepíše se stejnými daty. V cestě počítaný, jinak hodnota je vytvořen nový soubor.

## <a name="next-steps"></a>Další postup

* [Přehled vysílání datového proudu Spark](apache-spark-streaming-overview.md)
* [Vytváření vysoce dostupných úloh streamování Spark v YARN](apache-spark-streaming-high-availability.md)
