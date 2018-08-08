---
title: Architektura Hadoop – Azure HDInsight
description: Popisuje Hadoop ukládání a zpracování v clusterech HDInsight.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 754f4538f7c2a8de6286198094b38d40c466a15f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599468"
---
# <a name="hadoop-architecture-in-hdinsight"></a>Architektura Hadoop ve službě HDInsight

Hadoop zahrnuje dvě klíčové složky, Vysoká hustota File System (HDFS), poskytující úložiště a ještě další Resource Negotiator (YARN), která poskytuje zpracování. S úložnou a výpočetní kapacitu stane umožňující spouštění programů MapReduce k provedení požadované zpracování dat clusteru.

> [!NOTE]
> HDFS není obvykle nasazený v clusteru HDInsight k poskytování úložiště. Vrstva rozhraní HDFS kompatibilního místo toho používá komponenty Hadoop. Funkce skutečného úložiště poskytuje Azure Storage nebo Azure Data Lake Store. Pro Hadoop spuštění v clusteru HDInsight úlohy mapreduce je možné spustit jako kdyby byly k dispozici HDFS a proto nevyžadují žádné změny pro podporu jejich požadavky na úložiště. V systému Hadoop v HDInsight je zajištěný vnějším zdrojem úložiště, ale YARN zpracování zůstane základní součást. Další informace najdete v tématu [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Tento článek představuje YARN a jak koordinuje spuštění aplikace v HDInsight.

## <a name="yarn-basics"></a>Základy YARN 

YARN řídí a orchestruje zpracování dat v Hadoopu. YARN má dvě základní služby, které běží jako proces na uzly v clusteru: 

* Správce prostředků 
* NodeManager

ResourceManager uděluje clusteru výpočetní prostředky, které aplikace, jako je úloh MapReduce. ResourceManager uděluje tyto prostředky jako kontejnery, ve kterém každý kontejner se skládá z přidělení jader procesoru a paměti RAM. Pokud jste kombinovat všem prostředkům dostupným v clusteru a potom je v blocích o daný počet jader a paměti, každý blok prostředků je kontejner. Každý uzel v clusteru má kapacitu pro počet kontejnerů, a proto cluster má pevný limit počtu kontejnerů, které jsou k dispozici. Plnění zdroje v kontejneru je možné konfigurovat. 

Když MapReduce aplikace běží na clusteru, ResourceManager poskytuje aplikaci kontejnery, ve kterém chcete spustit. ResourceManager sleduje stav spouštění aplikací, kapacita clusteru k dispozici a sleduje aplikací při jejich dokončování a uvolněte své prostředky. 

ResourceManager poběží i v procesu webového serveru, který poskytuje webové uživatelské rozhraní, které lze použít k monitorování stavu aplikace. 

Když uživatel odešle aplikace MapReduce, která běží na clusteru, aplikace se odešle do Správce prostředků. Pak ResourceManager přiděluje kontejner uzlech NodeManager k dispozici. Uzly NodeManager jsou, kde ve skutečnosti spustí aplikaci. První kontejner přidělené spustí zvláštní aplikace volá se, ApplicationMaster. Tato ApplicationMaster zodpovídá za získávají se prostředky v podobě dalších kontejnerů potřebných ke spuštění odeslanou aplikaci. ApplicationMaster zkontroluje v jednotlivých fázích aplikace (mapy fáze a fáze snížit) a faktory v tom, kolik dat je potřeba zpracovat. Pak požadavky ApplicationMaster (*vyjedná*) prostředků ze Správce prostředků jménem aplikace. ResourceManager zase uděluje prostředky z NodeManagers v clusteru ApplicationMaster pro jeho použití v provádění aplikace. 

NodeManagers spouštět úlohy, které tvoří aplikace, pak zprávy jejich průběhu a stavu zpět ApplicationMaster. ApplicationMaster zase hlásí stav aplikace zpět do Správce prostředků. ResourceManager vrátí klientovi žádné výsledky.

## <a name="yarn-on-hdinsight"></a>YARN v HDInsight

Všechny typy clusterů HDInsight nasadit YARN. Správce prostředků se nasazuje pro zajištění vysoké dostupnosti s primárním a sekundárním instancí, kterého spouští v uvedeném pořadí na první a druhý hlavní uzly v clusteru. Současně je aktivní jenom jednu instanci ResourceManager. Instance NodeManager provozovat dostupných pracovních uzlů v clusteru.

![YARN v HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Další postup

* [Použití MapReduce se v clusteru Hadoop v HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Úvod do služby Azure HDInsight](hadoop/apache-hadoop-introduction.md)
