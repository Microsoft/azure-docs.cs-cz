---
title: Architektura Hadoop - Azure HDInsight | Microsoft Docs
description: Popisuje úložiště Hadoop a zpracování v clusterech HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5ec5f1f24d3bf953115bfa5023faf81df132f510
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31397449"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Architektura Hadoop ve službě HDInsight

Hadoop zahrnuje dvě klíčové složky, vysokou hustotou File System (HDFS), který poskytuje úložiště a ještě jiný prostředek Vyjednavač (YARN) poskytující zpracování. Funkce úložiště a zpracování stane umožňuje spustit programy MapReduce k provedení požadované zpracování dat clusteru.

> [!NOTE]
> HDFS není obvykle nasazuje v rámci clusteru HDInsight k poskytování úložiště. Místo toho vrstva HDFS kompatibilního rozhraní používá komponent systému Hadoop. Funkce skutečného úložiště poskytuje Azure Storage nebo Azure Data Lake Store. Pro Hadoop MapReduce úloh spouštějících v clusteru HDInsight spustit jako by byly HDFS a proto vyžadovat žádné změny k podpoře jejich požadavky na úložiště. V Hadoop v HDInsight je zajištěný vnějším zdrojem úložiště, ale YARN zpracování zůstane základní součástí. Další informace najdete v tématu [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Tento článek představuje YARN a jak ho koordinuje spuštění aplikace v HDInsight.

## <a name="yarn-basics"></a>YARN základy 

YARN řídí a orchestruje zpracování dat v Hadoop. YARN má dvě základní služby, které běží jako procesy na uzlech v clusteru: 

* ResourceManager 
* NodeManager

ResourceManager uděluje clusteru výpočetní prostředky, které aplikace, jako je úloh MapReduce. ResourceManager uděluje tyto prostředky jako kontejnery, kde každý kontejner se skládá z přidělení jader procesoru a paměti RAM. Pokud kombinaci všechny prostředky, které jsou k dispozici v clusteru a pak distribuovat je v blocích po zadaný počet jader a paměti, každý blok prostředků je kontejner. Každý uzel v clusteru má kapacitu pro počet kontejnerů, a proto clusteru má pevný limit počtu kontejnery, které jsou k dispozici. Přidělení prostředků v kontejneru je možné konfigurovat. 

Když MapReduce aplikace běží na clusteru, ResourceManager poskytuje aplikace kontejnery, do kterého chcete provést. ResourceManager sleduje stav spuštěných aplikací, kapacity clusteru k dispozici a sleduje aplikací podle jejich dokončení a uvolnění jejich prostředků. 

ResourceManager poběží i v procesu webového serveru, který poskytuje webové uživatelské rozhraní, které můžete použít k monitorování stavu aplikace. 

Když uživatel odešle MapReduce aplikace na spouštění v clusteru, aplikace je odeslán do ResourceManager. Pak ResourceManager přiděluje kontejner na k dispozici NodeManager uzlech. NodeManager uzly jsou, kde aplikace ve skutečnosti provádí. První kontejneru přidělené spustí speciální aplikaci s názvem ApplicationMaster. Tato ApplicationMaster zodpovídá za získávání prostředků ve formě následné kontejnery, potřebné ke spuštění odeslaná aplikace. ApplicationMaster prozkoumá fázích aplikace (mapy dvoufázové instalace a snížit fázi) a faktory pro množství dat, je potřeba zpracovat. Pak požadavky ApplicationMaster (*vyjedná*) prostředky z ResourceManager jménem aplikace. ResourceManager zase uděluje prostředky z NodeManagers v clusteru ApplicationMaster pro něj používat při provádění aplikace. 

NodeManagers spouštět úlohy, které tvoří aplikaci, pak zprávy jejich průběh a stav zpět ApplicationMaster. ApplicationMaster zase hlásí stav aplikace vrátí do ResourceManager. ResourceManager vrací výsledky do klienta.

## <a name="yarn-on-hdinsight"></a>YARN v HDInsight

Všechny typy clusterů HDInsight nasadit YARN. ResourceManager nasazení pro zajištění vysoké dostupnosti s primární a sekundární instancí, které spustit na prvním a druhém hlavních uzlech v rámci clusteru v uvedeném pořadí. Pouze jednu instanci ResourceManager je aktivní v čase. Instance NodeManager spustit ve dostupných pracovních uzlech v clusteru.

![YARN v HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Další postup

* [Používání nástroje MapReduce v Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md)
