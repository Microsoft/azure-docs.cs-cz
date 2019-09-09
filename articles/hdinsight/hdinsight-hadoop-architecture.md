---
title: Architektura Apache Hadoop – Azure HDInsight
description: Popisuje Apache Hadoop ukládání a zpracování v clusterech Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: d41d671cf773bdab20c3f105c7d1abb6c7bde840
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810233"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura Apache Hadoop v HDInsightu

[Apache Hadoop](https://hadoop.apache.org/) obsahuje dvě základní komponenty: [Apache HADOOP systém souborů DFS (Distributed File System) (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) , která poskytuje úložiště, a [Apache HADOOP ještě jiný Negociační (příz)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , který poskytuje zpracování. Díky funkcím úložiště a zpracování se cluster bude schopný spouštět programy [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) a provádět požadované zpracování dat.

> [!NOTE]  
> HDFS není obvykle nasazen v rámci clusteru HDInsight, aby poskytoval úložiště. Místo toho se používají komponenty Hadoop, které jsou kompatibilní s HDFS. Skutečná schopnost úložiště je poskytována buď Azure Storage, nebo Azure Data Lake Storage. Pro Hadoop MapReduce úlohy spuštěné v clusteru HDInsight, jako kdyby existovala HDFS, a proto nevyžadují žádné změny, které by podporovaly jejich požadavky na úložiště. V systému Hadoop ve službě HDInsight je úložiště ve zdroji, ale zpracování PŘÍZe zůstává základní komponentou. Další informace najdete v tématu [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Tento článek představuje nitě a způsob, jakým koordinuje spouštění aplikací v HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Základy Apache Hadoop nitě 

PŘÍZe řídí a orchestruje zpracování dat v Hadoop. PŘÍZ má dvě základní služby, které běží jako procesy na uzlech v clusteru: 

* ResourceManager 
* NodeManager

Správce prostředků uděluje výpočetní prostředky clusteru aplikacím jako MapReduce úlohy. Správce prostředků tyto prostředky uděluje jako kontejnery, kde se každý kontejner skládá z přidělení PROCESORových jader a paměti RAM. Pokud jste spojili všechny prostředky, které jsou k dispozici v clusteru, a poté byly distribuovány jádra a paměť v blocích, každý blok prostředků je kontejnerem. Každý uzel v clusteru má kapacitu pro určitý počet kontejnerů, proto má cluster pevný limit počtu dostupných kontejnerů. Plnění prostředků v kontejneru lze konfigurovat. 

Když aplikace MapReduce běží na clusteru, správce prostředků poskytne aplikaci kontejnery, ve kterých se má provést. Správce prostředků sleduje stav spuštěných aplikací, dostupnou kapacitu clusteru a sleduje aplikace podle jejich dokončení a uvolní své prostředky. 

Správce prostředků taky spouští proces webového serveru, který poskytuje webové uživatelské rozhraní pro monitorování stavu aplikací.

Když uživatel odešle aplikaci MapReduce ke spuštění v clusteru, aplikace se odešle do Správce prostředků. Správce prostředků pak přiděluje kontejner na dostupných uzlech NodeManager. Uzly NodeManager jsou místo, kde se aplikace skutečně spouští. První přidělený kontejner spustí speciální aplikaci s názvem ApplicationMaster. Tento ApplicationMaster zodpovídá za získání prostředků ve formě dalších kontejnerů potřebných ke spuštění odeslané aplikace. ApplicationMaster prověřuje fáze aplikace, jako je například fáze mapy a zmenšení fáze, a okolnosti, kolik dat je potřeba zpracovat. ApplicationMaster pak vyžádá (*vyjednávat*) prostředky od správce prostředků jménem aplikace. Správce prostředků pak udělí prostředky z NodeManagers v clusteru do ApplicationMaster, aby ho mohl použít při spouštění aplikace. 

NodeManagers spustí úlohy, které tvoří aplikaci, a pak oznámí jejich průběh a stav zpět do ApplicationMaster. ApplicationMaster v nástroji zase hlásí stav aplikace zpět do Správce prostředků. Správce prostředků vrátí všechny výsledky klientovi.

## <a name="yarn-on-hdinsight"></a>PŘÍZe v HDInsight

Všechny typy clusterů HDInsight nasazují PŘÍZi. Správce prostředků je nasazen pro vysokou dostupnost s primární a sekundární instancí, která běží na prvním a druhém hlavním uzlu v rámci clusteru. V jednom okamžiku je aktivní jenom jedna instance Správce prostředků. Instance NodeManager se spouštějí napříč dostupnými pracovními uzly v clusteru.

![PŘÍZe v HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Další postup

* [Použití MapReduce v Apache Hadoopu ve službě HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Seznámení se službou Azure HDInsight](hadoop/apache-hadoop-introduction.md)
