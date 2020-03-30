---
title: Architektura Apache Hadoop - Azure HDInsight
description: Popisuje úložiště a zpracování Apache Hadoop v clusterech Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162204"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura Apache Hadoop v HDInsightu

[Apache Hadoop](https://hadoop.apache.org/) obsahuje dvě základní komponenty: [Apache Hadoop Distributed File System (HDFS),](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) který poskytuje úložiště, a [Apache Hadoop Ještě další resource negotiator (YARN),](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) který poskytuje zpracování. Díky možnostem ukládání a zpracování je cluster schopen spouštět programy [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) k provedení požadovaného zpracování dat.

> [!NOTE]  
> HDFS se obvykle nenasadí v clusteru HDInsight k poskytování úložiště. Místo toho je vrstva rozhraní kompatibilní s HDFS používána součástmi Hadoop. Skutečná možnost úložiště je poskytována azure storage nebo Azure Data Lake Storage. Pro Hadoop, MapReduce úlohy provádění v clusteru HDInsight spustit, jako kdyby HDFS byly přítomny, a tak vyžadují žádné změny pro podporu jejich potřeby úložiště. V Hadoop na HDInsight úložiště je zajišťována, ale zpracování YARN zůstává základní součástí. Další informace najdete [v tématu Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Tento článek představuje YARN a jak koordinuje provádění aplikací na HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Základy Apache Hadoop YARN

YARN řídí a organizuje zpracování dat v Hadoopu. YARN má dvě základní služby, které běží jako procesy na uzlech v clusteru:

* Resourcemanager
* NodeManager

ResourceManager uděluje výpočetní prostředky clusteru aplikacím, jako jsou úlohy MapReduce. ResourceManager uděluje tyto prostředky jako kontejnery, kde každý kontejner se skládá z přidělení procesoru jader a paměti RAM. Pokud jste zkombinovali všechny prostředky dostupné v clusteru a pak distribuovali jádra a paměť v blocích, každý blok prostředků je kontejner. Každý uzel v clusteru má kapacitu pro určitý počet kontejnerů, proto má cluster pevný limit na počet dostupných kontejnerů. Přidělení prostředků v kontejneru je konfigurovatelné.

Při spuštění aplikace MapReduce v clusteru, ResourceManager poskytuje aplikace kontejnery, ve kterých chcete spustit. ResourceManager sleduje stav spuštěných aplikací, dostupnou kapacitu clusteru a sleduje aplikace při jejich dokončení a uvolnění jejich prostředků.

ResourceManager také spustí proces webového serveru, který poskytuje webové uživatelské rozhraní pro sledování stavu aplikací.

Když uživatel odešle aplikaci MapReduce ke spuštění v clusteru, aplikace je odeslána do ResourceManager. Na druhé straně ResourceManager přiděluje kontejner na dostupné uzly NodeManager. Uzly NodeManager jsou místa, kde se aplikace skutečně spustí. První přidělený kontejner spustí speciální aplikaci nazvanou ApplicationMaster. Tento ApplicationMaster je zodpovědný za získání prostředků, ve formě následných kontejnerů, potřebné ke spuštění odeslané aplikace. ApplicationMaster zkoumá fáze aplikace, jako je například fáze mapy a snížit fázi a faktory v tom, kolik dat je třeba zpracovat. ApplicationMaster pak požaduje *(vyjedná*) prostředky z ResourceManager jménem aplikace. ResourceManager zase uděluje prostředky z NodeManagers v clusteru, aby jej použít při provádění aplikace.

NodeManagers spustit úlohy, které tvoří aplikaci, pak hlásit jejich průběh a stav zpět ApplicationMaster. ApplicationMaster zase hlásí stav aplikace zpět Do ResourceManager. ResourceManager vrátí všechny výsledky klientovi.

## <a name="yarn-on-hdinsight"></a>Příze na HDInsight

Všechny typy clusterů HDInsight nasazují YARN. ResourceManager je nasazen pro vysokou dostupnost s primární a sekundární instance, která běží na první a druhý hlavní uzly v clusteru v uvedeném pořadí. Současně je aktivní pouze jedna instance ResourceManageru. Instance NodeManager běží přes dostupné pracovní uzly v clusteru.

![Apache YARN na Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Obnovitelné odstranění

Pokud chcete zrušit odstranění souboru z účtu úložiště, přečtěte si tyto informace:

### <a name="azure-storage"></a>Azure Storage

* [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/storage-blob-soft-delete.md)
* [Zrušit odstranit objekt blob](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Úložiště datového jezera Azure Gen 1

[Obnovit-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2

[Známé problémy s Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Čištění odpadků

Vlastnost `fs.trash.interval` z **HDFS** > **Advanced core-site** by `0` měla zůstat na výchozí hodnotu, protože byste neměli ukládat žádná data v místním systému souborů. Tato hodnota nemá vliv na účty vzdáleného úložiště (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Další kroky

* [Použití MapReduce v Apache Hadoopu ve službě HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md)
