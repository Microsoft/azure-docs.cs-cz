---
title: Architektura Apache Hadoopu – Azure HDInsight
description: Popisuje Apache Hadoop, ukládání a zpracování v clusterech HDInsight.
services: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 9f45b1603fe0e34bfdf6192fd85ecaf27311ae32
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718073"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architektura Apache Hadoop v HDInsightu

[Apache Hadoop](https://hadoop.apache.org/) zahrnuje dva základní součásti: [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) , která poskytuje úložiště, a [Apache Hadoop ještě další Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , který poskytuje zpracování. S úložištěm a možnosti zpracování, cluster bude schopný běžet [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programy k provedení požadované zpracování dat.

> [!NOTE]  
> HDFS není obvykle nasazený v clusteru HDInsight k poskytování úložiště. Vrstva rozhraní HDFS kompatibilního místo toho používá komponenty Hadoop. Funkce skutečného úložiště poskytuje Azure Storage nebo Azure Data Lake Storage. Pro Hadoop spuštění v clusteru HDInsight úlohy mapreduce je možné spustit jako kdyby byly k dispozici HDFS a proto nevyžadují žádné změny pro podporu jejich požadavky na úložiště. V systému Hadoop v HDInsight je zajištěný vnějším zdrojem úložiště, ale YARN zpracování zůstane základní součást. Další informace najdete v tématu [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Tento článek představuje YARN a jak koordinuje spuštění aplikace v HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Základní informace o Apache Hadoop YARN 

YARN řídí a orchestruje zpracování dat v Hadoopu. YARN má dvě základní služby, které běží jako proces na uzly v clusteru: 

* ResourceManager 
* NodeManager

ResourceManager uděluje clusteru výpočetní prostředky, které aplikace, jako je úloh MapReduce. ResourceManager uděluje tyto prostředky jako kontejnery, ve kterém každý kontejner se skládá z přidělení jader procesoru a paměti RAM. Pokud kombinovat všem prostředkům dostupným v clusteru a poté distribuován jader a paměti v blocích, každý blok prostředků je kontejner. Každý uzel v clusteru má kapacitu pro počet kontejnerů, proto cluster má pevný limit počtu kontejnerů, které jsou k dispozici. Plnění zdroje v kontejneru je možné konfigurovat. 

Když MapReduce aplikace běží na clusteru, ResourceManager poskytuje aplikaci kontejnery, ve kterém chcete spustit. ResourceManager sleduje stav spouštění aplikací, kapacita clusteru k dispozici a sleduje aplikací při jejich dokončování a uvolněte své prostředky. 

ResourceManager poběží i v procesu webového serveru, který poskytuje webové uživatelské rozhraní pro monitorování stavu aplikace.

Když uživatel odešle aplikace MapReduce, která běží na clusteru, aplikace se odešle do Správce prostředků. Pak ResourceManager přiděluje kontejner uzlech NodeManager k dispozici. Uzly NodeManager jsou, kde ve skutečnosti spustí aplikaci. První kontejner přidělené spustí zvláštní aplikace volá se, ApplicationMaster. Tato ApplicationMaster zodpovídá za získávají se prostředky v podobě dalších kontejnerů potřebných ke spuštění odeslanou aplikaci. ApplicationMaster zkontroluje v jednotlivých fázích aplikaci, jako je mapa fáze a snížit fáze a faktory v tom, kolik dat je potřeba zpracovat. Pak požadavky ApplicationMaster (*vyjedná*) prostředků ze Správce prostředků jménem aplikace. ResourceManager zase uděluje prostředky z NodeManagers v clusteru ApplicationMaster pro jeho použití v provádění aplikace. 

NodeManagers spouštět úlohy, které tvoří aplikace, pak zprávy jejich průběhu a stavu zpět ApplicationMaster. ApplicationMaster zase hlásí stav aplikace zpět do Správce prostředků. ResourceManager vrátí klientovi žádné výsledky.

## <a name="yarn-on-hdinsight"></a>YARN v HDInsight

Všechny typy clusterů HDInsight nasadit YARN. Správce prostředků se nasazuje pro zajištění vysoké dostupnosti s primární a sekundární instanci, která se spustí na prvním a druhém hlavní uzly v rámci clusteru názvů. Současně je aktivní jenom jednu instanci ResourceManager. Instance NodeManager provozovat dostupných pracovních uzlů v clusteru.

![YARN v HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Další postup

* [Použití MapReduce se v Apache Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md)
