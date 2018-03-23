---
title: Úvod k systému Apache Kafka ve službě HDInsight – Azure | Dokumentace Microsoftu
description: 'Přečtěte si o systému Apache Kafka ve službě HDInsight: co to je, co to dělá a kde najít příklady a informace pro začátek.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 35dc83dea7dc1310852437cd7eede32cfa890e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Představení Apache Kafka ve službě HDInsight

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Kafka také poskytuje funkci pro zprostředkování zpráv podobnou frontě zpráv, ve které můžete publikovat pojmenované datové proudy a přihlásit se k jejich odběru. Kafka ve službě HDInsight představuje spravovanou, vysoce škálovatelnou a vysoce dostupnou službu v cloudu Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Proč používat Kafka ve službě HDInsight?

Kafka pro HDInsight poskytuje následující funkce:

* __Smlouva o úrovni služeb (SLA) zajišťující 99,9% dostupnost systému Kafka:__ Další informace najdete v dokumentu [Informace o smlouvě SLA pro HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* __Odolnost vůči selhání a přehled o stojanech:__ Platforma Kafka byla navržena s jednorozměrným pohledem na stojan, což v některých prostředích dobře funguje. V prostředích jako Azure je však stojan rozdělen do dvou dimenzí – aktualizační domény a domény selhání. Microsoft poskytuje nástroje, které zajišťují obnovování rovnováhy oddílů a replik platformy Kafka mezi aktualizačními doménami a doménami selhání. 

    Další informace najdete v článku [Vysoká dostupnost se systémem Kafka ve službě HDInsight](apache-kafka-high-availability.md).

* **Integrace se službou Azure Managed Disks:** Spravované disky přináší pro disky používané systémem Kafka ve službě HDInsight lepší škálování a vyšší propustnost, a to až do 16 TB na jeden uzel v clusteru.

    Informace o konfiguraci spravovaných disků v systému Kafka v HDInsight najdete v tématu [Zvýšení škálovatelnosti systému Kafka v prostředí HDInsight](apache-kafka-scalability.md).

    Další informace o spravovaných discích najdete v tématu [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Výstrahy, monitorování a prediktivní údržba:** K monitorování systému Kafka ve službě HDInsight je možné použít službu Azure Log Analytics. Služba Log Analytics poskytuje informace na úrovni virtuálních počítačů, jako jsou metriky disků a síťových karet nebo metriky JMX z platformy Kafka.

    Další informace najdete v tématu [Analýza protokolů pro systém Kafka ve službě HDInsight](apache-kafka-log-analytics-operations-management.md).

* **Replikace dat platformy Kafka:** Kafka poskytuje nástroj MirrorMaker, který replikuje data mezi clustery Kafka.

    Informace o používání nástroje MirrorMaker najdete v tématu [Replikace témat Kafka v systému Kafka ve službě HDInsight](apache-kafka-mirroring.md).

* **Škálování clusterů:** HDInsight umožňuje změnit počet pracovních uzlů (které jsou hostiteli zprostředkovatele Kafka) po vytvoření clusteru. Kapacitu clusteru můžete při nárůstu objemu úloh vertikálně navyšovat, případně ji snižovat, abyste snížili náklady. Škálování lze provádět z webu Azure Portal, prostředí Azure PowerShell a dalších rozhraní správy Azure. V případě platformy Kafka byste po operacích škálování měli obnovit rovnováhu replik oddílů. Díky vyrovnání rovnováhy oddílů může platforma Kafka využívat nový počet pracovních uzlů.

    Další informace najdete v článku [Vysoká dostupnost se systémem Kafka ve službě HDInsight](apache-kafka-high-availability.md).

* **Vzorec zasílání zpráv na principu publikování a odběru:** Platforma Kafka poskytuje rozhraní API pro autory k publikování záznamů v tématech Kafka. Rozhraní API pro příjemce se používá při přihlášení k odběru tématu.

    Další informace najdete v článku [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md).

* **Zpracování datových proudů:** Kafka se často používá společně s Apache Storm nebo Spark ke zpracování datových proudů v reálném čase. Kafka 0.10.0.0 (HDInsight verze 3.5 a 3.6) zavedla rozhraní API pro streamování, které umožňuje vytvářet řešení streamování bez potřeby Stormu nebo Sparku.

    Další informace najdete v článku [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md).

* **Horizontální škálování:** Kafka rozděluje datové proudy mezi uzly v clusteru HDInsight. Procesy příjemců můžou být přidružené k jednotlivým oddílům pro zajištění vyrovnávání zatížení při využívání záznamů.

    Další informace najdete v článku [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md).

* **Doručení v daném pořadí:** V rámci každého oddílu se záznamy ukládají v datovém proudu v pořadí, v jakém byly přijaty. Přidružením jednoho procesu příjemce na oddíl můžete zajistit zpracování záznamů v daném pořadí.

    Další informace najdete v článku [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Případy použití

* **Zasílání zpráv:** Protože Kafka podporuje vzorec zasílání zpráv na principu publikování a odběru, často se používá jako zprostředkovatel zpráv.

* **Sledování aktivit:** Protože Kafka poskytuje protokolování záznamů v daném pořadí, je možné ji použít ke sledování a opětovnému vytvoření aktivit. Například akcí uživatelů na webu nebo v aplikaci.

* **Agregace:** Pomocí zpracování datových proudů můžete agregovat a kombinovat informace z různých datových proudů a centralizovat je v podobě provozních dat.

* **Transformace:** Pomocí zpracování datových proudů můžete kombinovat data z více vstupních témat a rozšiřovat je do jednoho nebo několika výstupních témat.

## <a name="architecture"></a>Architektura

![Konfigurace clusteru Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

Tento diagram ukazuje obvyklou konfiguraci Kafka s využitím skupin příjemců, dělení a replikace. Díky tomu nabízí paralelní čtení událostí s odolností proti chybám. Apache ZooKeeper spravuje stav clusteru Kafka a proto je postaven pro souběžné a odolné transakce s nízkou latencí. Kafka ukládá záznamy v *tématech*. Záznamy jsou vytvářeny *producenty* a spotřebovávány *konzumenty*. Producenti načítají záznamy ze *zprostředkovatelů* Kafka. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka. Pro každého příjemce se vytvoří jeden oddíl, což umožňuje paralelní zpracování streamovaných dat. K rozprostření oddílů mezi uzly se využívá replikace, která zajišťuje ochranu před výpadky uzlů (zprostředkovatelé). Oddíl s označením *(L)* je vedoucím daného uzlu. Provoz producenta se směruje do vedoucích jednotlivých uzlů pomocí stavu, který spravuje ZooKeeper.

Všichni zprostředkovatelé Kafka používají službu Azure Managed Disks. Počet disků je definovaný uživatelem a pro jednoho zprostředkovatele můžou poskytovat až 16 GB úložiště.

> [!IMPORTANT]
> Kafka nemá žádné informace o podkladovém hardwaru (rack) v datovém centru Azure. Pokud chcete zajistit správné rozmístění oddílů napříč podkladovým hardwarem, přečtěte si dokument [Konfigurace vysoké dostupnosti dat (Kafka)](apache-kafka-high-availability.md).

## <a name="next-steps"></a>Další kroky

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)

* [Vytvoření repliky Kafka ve službě HDInsight pomocí MirrorMakeru](apache-kafka-mirroring.md)

* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)