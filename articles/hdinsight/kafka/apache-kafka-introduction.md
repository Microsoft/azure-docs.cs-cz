---
title: Úvod k Apache Kafka ve službě HDInsight – Azure
description: 'Přečtěte si o systému Apache Kafka ve službě HDInsight: co to je, co to dělá a kde najít příklady a informace pro začátek.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 92f56f3b405470bc8ae0e9ebab2450ddc31b3c6a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77672170"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Co je Apache Kafka v Azure HDInsight

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Kafka také poskytuje funkci pro zprostředkování zpráv podobnou frontě zpráv, ve které můžete publikovat pojmenované datové proudy a přihlásit se k jejich odběru.

Tady jsou konkrétní charakteristiky systému Kafka ve službě HDInsight:

* Jedná se o spravovanou službu, která poskytuje zjednodušený proces konfigurace. Výsledkem je konfigurace otestovaná a podporovaná Microsoftem.

* Microsoft poskytuje smlouvu o úrovni služeb (SLA) zajišťující 99,9% dostupnost platformy Kafka. Další informace najdete v dokumentu [Informace o smlouvě SLA pro službu HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Jako záložní úložiště pro platformu Kafka se používají Spravované disky Azure. Spravované disky mohou pro každého zprostředkovatele Kafka poskytovat až 16 TB úložiště. Informace o konfiguraci spravovaných disků pomocí Kafky na hdinsightu najdete v [tématu Zvýšení škálovatelnosti Apache Kafka na HDInsight](apache-kafka-scalability.md).

    Další informace o spravovaných discích najdete v tématu [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* Systém Kafka byl navržený s jednorozměrným pohledem na stojan. Azure rozděluje stojan do dvou dimenzí – aktualizační domény a domény selhání. Microsoft poskytuje nástroje, které obnovují rovnováhu oddílů a replik platformy Kafka mezi aktualizačními doménami a doménami selhání.

    Další informace naleznete [v tématu Vysoká dostupnost s Apache Kafka na HDInsight](apache-kafka-high-availability.md).

* HDInsight umožňuje změnit počet pracovních uzlů (které jsou hostiteli zprostředkovatele Kafka) po vytvoření clusteru. Škálování lze provádět z webu Azure Portal, prostředí Azure PowerShell a dalších rozhraní správy Azure. V případě platformy Kafka byste po operacích škálování měli obnovit rovnováhu replik oddílů. Díky vyrovnání rovnováhy oddílů může platforma Kafka využívat nový počet pracovních uzlů.

    Další informace naleznete [v tématu Vysoká dostupnost s Apache Kafka na HDInsight](apache-kafka-high-availability.md).

* Protokoly Azure Monitor umíte monitorovat Kafka na HDInsight. Azure Monitor protokoly povrchy informace o úrovni virtuálního počítače, jako je disk a nic metriky a Metriky JMX z Kafka.

    Další informace naleznete v [tématu Analyze logs for Apache Kafka on HDInsight](apache-kafka-log-analytics-operations-management.md).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka na architektuře HDInsight

Následující diagram ukazuje obvyklou konfiguraci platformy Kafka s využitím skupin příjemců, dělení a replikace. Díky tomu nabízí paralelní čtení událostí s odolností proti chybám:

![Diagram konfigurace clusteru Kafka](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

Apache ZooKeeper spravuje stav clusteru Kafka. Služba ZooKeeper je navržená pro souběžné a odolné transakce s nízkou latencí.

Kafka ukládá záznamy (data) v **tématech**. Záznamy jsou vytvářeny **producenty** a spotřebovávány **konzumenty**. Producenti odesílají záznamy do **zprostředkovatelů** Kafka. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka.

Témata rozdělují záznamy do oddílů napříč zprostředkovateli. Při využívání záznamů můžete dosáhnout paralelního zpracování dat díky použití až jednoho konzumenta pro každý oddíl.

K duplikaci oddílů mezi uzly se využívá replikace, která zajišťuje ochranu před výpadky uzlů (zprostředkovatelů). V diagramu je oddíl s označením *(L)* vedoucím daného oddílu. Provoz producenta se směruje do vedoucích jednotlivých uzlů pomocí stavu, který spravuje ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Proč používat Apache Kafka na HDInsight?

Tady jsou běžné úlohy a postupy, které je možné provádět s využitím systému Kafka ve službě HDInsight:

|Použití |Popis |
|---|---|
|Replikace dat Apache Kafka|Kafka poskytuje nástroj MirrorMaker, který replikuje data mezi clustery Kafka. Informace o používání MirrorMakeru najdete v [tématu Replikace témat Apache Kafka pomocí Apache Kafka na HDInsight](apache-kafka-mirroring.md).|
|Vzor zasílání zpráv publikování a odběru|Kafka poskytuje producer api pro publikování záznamů na téma Kafka. Rozhraní API pro příjemce se používá při přihlášení k odběru tématu. Další informace [najdete v tématu Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Zpracování streamů|Kafka se často používá s Apache Storm nebo Spark pro zpracování streamů v reálném čase. Kafka 0.10.0.0 (HDInsight verze 3.5 a 3.6) zavedla rozhraní API pro streamování, které umožňuje vytvářet řešení streamování bez potřeby Stormu nebo Sparku. Další informace [najdete v tématu Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Horizontální škálování|Kafka oddíly proudy přes uzly v clusteru HDInsight. Procesy příjemců můžou být přidružené k jednotlivým oddílům pro zajištění vyrovnávání zatížení při využívání záznamů. Další informace [najdete v tématu Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Doručení v pořadí|V rámci každého oddílu jsou záznamy uloženy v datovém proudu v pořadí, v jakém byly přijaty. Přidružením jednoho procesu příjemce na oddíl můžete zajistit zpracování záznamů v daném pořadí. Další informace [najdete v tématu Start with Apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Zasílání zpráv|Vzhledem k tomu, že podporuje vzor zprávy publikování a odběru, Kafka se často používá jako zprostředkovatel zpráv.|
|Sledování aktivity|Vzhledem k tomu, že Kafka poskytuje protokolování záznamů v pořadí, lze ji použít ke sledování a opětovnému vytvoření aktivit. Například akcí uživatelů na webu nebo v aplikaci.|
|Agregace|Pomocí zpracování datového proudu můžete agregovat informace z různých datových proudů a centralizovat je do provozních dat.|
|Transformace|Pomocí zpracování datového proudu můžete kombinovat a obohacovat data z více vstupních témat do jednoho nebo více výstupních témat.|

## <a name="next-steps"></a>Další kroky

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Úvodní příručka: Vytvoření Apache Kafka na HDInsight](apache-kafka-get-started.md)

* [Použití Kafky s rest proxy](rest-proxy.md)

* [Kurz: Použijte Apache Spark s Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Kurz: Použití Apache Stormu se systémem Apache Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
