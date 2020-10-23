---
title: Komponenty vysoké dostupnosti ve službě Azure HDInsight
description: Přehled různých komponent vysoké dostupnosti používaných clustery HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 4d0405df1863ee47374242ba4fba5b845711d3a1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424524"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Služby vysoké dostupnosti podporované službou Azure HDInsight

Pro zajištění optimální úrovně dostupnosti pro komponenty analýzy se služba HDInsight vyvinula s jedinečnou architekturou pro zajištění vysoké dostupnosti důležitých služeb (HA). Některé součásti této architektury byly vyvinuty společností Microsoft za účelem poskytování automatického převzetí služeb při selhání. Další komponenty jsou standardní komponenty Apache, které jsou nasazené pro podporu konkrétních služeb. Tento článek popisuje architekturu modelu služby HA v HDInsight, jak HDInsight podporuje převzetí služeb při selhání pro služby HA a osvědčené postupy pro obnovení z dalších přerušení služby.

> [!NOTE]
> Tento článek obsahuje odkazy na *podřízený*termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

## <a name="high-availability-infrastructure"></a>Infrastruktura vysoké dostupnosti

HDInsight nabízí přizpůsobenou infrastrukturu, která zajistí vysokou dostupnost čtyř primárních služeb díky funkcím automatického převzetí služeb při selhání:

- Server Apache Ambari
- Časová osa aplikace Server pro Apache nitě
- Server historie úlohy pro Hadoop MapReduce
- Apache Livy

Tato infrastruktura se skládá z řady služeb a softwarových komponent, které některé z nich navrhují Microsoft. Následující komponenty jsou pro platformu HDInsight jedinečné:

- Podřízený kontroler převzetí služeb při selhání
- Hlavní kontroler převzetí služeb při selhání
- Služba s vysokou dostupností na podřízené úrovni
- Hlavní služba vysoké dostupnosti

![infrastruktura vysoké dostupnosti](./media/hdinsight-high-availability-components/high-availability-architecture.png)

K dispozici jsou také další služby vysoké dostupnosti, které jsou podporovány v části Open Source součásti pro spolehlivost Apache. Tyto součásti jsou také k dispozici v clusterech HDInsight:

- Systém souborů Hadoop (HDFS) NameNode
- Správce prostředků PŘÍZe
- HBase Master

Následující části poskytují další podrobnosti o tom, jak tyto služby spolupracují.

## <a name="hdinsight-high-availability-services"></a>Služby HDInsight s vysokou dostupností

Společnost Microsoft poskytuje podporu pro čtyři služby Apache v následující tabulce v clusterech HDInsight. Aby je bylo možné odlišit od služeb s vysokou dostupností, které jsou podporovány součástmi z Apache, označují se jako *služby HDInsight ha*.

| Služba | Uzly clusteru | Typy clusterů | Účel |
|---|---|---|---|
| Server Apache Ambari| Aktivní hlavnímu uzlu | Vše | Monitoruje a spravuje cluster.|
| Časová osa aplikace Server pro Apache nitě | Aktivní hlavnímu uzlu | Vše kromě Kafka | Udržuje ladicí informace o úlohách PŘÍZe spuštěných v clusteru.|
| Server historie úlohy pro Hadoop MapReduce | Aktivní hlavnímu uzlu | Vše kromě Kafka | Udržuje data ladění pro úlohy MapReduce.|
| Apache Livy | Aktivní hlavnímu uzlu | Spark | Umožňuje snadnou interakci s clusterem Spark přes rozhraní REST. |

>[!Note]
> Clustery HDInsight Balíček zabezpečení podniku (ESP) aktuálně poskytují pouze vysokou dostupnost serveru Ambari. Časová osa aplikace Server, server historie úloh a Livy jsou spuštěné jenom na headnode0 a předají se převzetí služeb při selhání headnode1 při Ambari failsover. Databáze časové osy aplikace je také na headnode0 a ne na Ambari SQL serveru.

### <a name="architecture"></a>Architektura

Každý cluster HDInsight má dva hlavních v aktivním a pohotovostním režimu. Služby HDInsight HA běží jenom na hlavních. Tyto služby by měly být vždy spuštěné na aktivním hlavnímu uzlu a zastaveny a přepnuty do režimu údržby v pohotovostním hlavnímu uzlu.

Aby se zajistilo správné stavy služeb HA a poskytovaly rychlé převzetí služeb při selhání, využívá služba HDInsight Apache ZooKeeper, což je koordinační služba pro distribuované aplikace, která umožňuje aktivní volby hlavnímu uzlu. HDInsight také zřizuje několik procesů Java na pozadí, které koordinují postup převzetí služeb při selhání pro služby HDInsight HA. Jedná se o následující služby: hlavní kontroler převzetí služeb při selhání, podřízený řadič pro převzetí služeb při selhání, *hlavní-ha-Service*a *podřízený-ha-Service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper je vysoce výkonná koordinační služba pro distribuované aplikace. V produkčním prostředí se ZooKeeper obvykle spouští v replikovaném režimu, kde replikovaná skupina serverů ZooKeeper tvoří kvorum. Každý cluster HDInsight má tři uzly ZooKeeper, které umožňují třem serverům ZooKeeper vytvořit kvorum. HDInsight má souběžně spuštěné dvě ZooKeeper kvora. Jeden kvorum rozhoduje o aktivním hlavnímu uzlu v clusteru, ve kterém by se měly spustit služby HDInsight HA. Další kvorum slouží ke koordinaci služeb HA poskytovaných službou Apache, jak je popsáno v dalších částech.

### <a name="slave-failover-controller"></a>Podřízený kontroler převzetí služeb při selhání

Podřízený kontroler převzetí služeb při selhání běží na všech uzlech v clusteru HDInsight. Tento kontroler zodpovídá za spouštění agenta Ambari a *podřízeného-ha-Service* na každém uzlu. Pravidelně se dotazuje prvního ZooKeeper kvora o aktivním hlavnímu uzlu. Když se hlavních aktivní a pohotovostní, kontroler převzetí služeb při selhání provede toto:

1. Aktualizuje konfigurační soubor hostitele.
1. Restartuje agenta Ambari.

*Podřízená služba ha-Service* zodpovídá za zastavování služeb HDInsight ha (s výjimkou Ambari serveru) v pohotovostním hlavnímu uzlu.

### <a name="master-failover-controller"></a>Hlavní kontroler převzetí služeb při selhání

Hlavní kontroler převzetí služeb při selhání běží na obou hlavních. Hlavní řadiče pro převzetí služeb při selhání komunikují s prvním ZooKeeper kvorem, aby bylo možné jmenovat hlavnímu uzlu, na kterém běží jako aktivní hlavnímu uzlu.

Pokud například hlavní řadič pro převzetí služeb při selhání na hlavnímu uzlu 0 vyhledá výběr, probíhají tyto změny:

1. Hlavnímu uzlu 0 bude aktivní.
1. Hlavní kontroler převzetí služeb při selhání spustí server Ambari a *hlavní-ha-Service* na hlavnímu uzlu 0.
1. Druhý hlavní řadič pro převzetí služeb při selhání zastaví Ambari Server a *hlavní-ha-Service* na hlavnímu uzlu 1.

Hlavní-ha-Service se spouští jenom na aktivním hlavnímu uzlu, zastaví služby HDInsight HA (s výjimkou serveru Ambari) na pohotovostním hlavnímu uzlu a spustí je v aktivním hlavnímu uzlu.

### <a name="the-failover-process"></a>Proces převzetí služeb při selhání

![proces převzetí služeb při selhání](./media/hdinsight-high-availability-components/failover-steps.png)

Monitor stavu běží na každém hlavnímu uzlu spolu s hlavním řadičem pro převzetí služeb při selhání pro odesílání oznámení prezenčního signálu do kvora Zookeeper. Hlavnímu uzlu se v tomto scénáři považuje za službu HA. Monitor stavu zkontroluje, jestli je každá služba vysoké dostupnosti v pořádku a jestli je připravená k zapojení do volby vedoucího vedení. Pokud ano, bude tento hlavnímu uzlu konkurovat ve volbách. Pokud ne, ukončí volbu, dokud nebude znovu připravena.

Pokud se v pohotovostním režimu hlavnímu uzlu kdykoli dosáhne vedoucího a bude aktivní (například v případě selhání s předchozím aktivním uzlem), zahájí hlavní řadič pro převzetí služeb při selhání všechny služby HDInsight HA. Hlavní kontroler převzetí služeb při selhání také zastaví tyto služby na ostatních hlavnímu uzlu.

V případě selhání služby HDInsight HA, jako je stav výpadku služby nebo není v pořádku, by měl hlavní kontroler převzetí služeb při selhání automaticky restartovat nebo zastavit služby podle stavu hlavnímu uzlu. Uživatelé by neměli ručně spouštět službu HDInsight HA na obou hlavní uzlech. Místo toho povolte automatické nebo ruční převzetí služeb při selhání pomocí služby obnovení.

### <a name="inadvertent-manual-intervention"></a>Neúmyslná ruční zásah

Služby HDInsight HA by se měly spouštět jenom na aktivních hlavnímu uzlu a v případě potřeby se automaticky restartují. Vzhledem k tomu, že individuální služby HA nemají své vlastní monitorování stavu, nejde převzetí služeb při selhání aktivovat na úrovni jednotlivých služeb. Převzetí služeb při selhání je zajištěno na úrovni uzlu a nikoli na úrovni služby.

### <a name="some-known-issues"></a>Některé známé problémy

- Při ručním spuštění služby HA v pohotovostním režimu hlavnímu uzlu přestane fungovat, dokud nedojde k dalšímu převzetí služeb při selhání. Pokud jsou na obou hlavních spuštěné služby HA, můžou některé potenciální problémy zahrnovat: uživatelské rozhraní Ambari je nedostupné, Ambari vyvolává chyby, operace PŘÍZe, Spark a Oozie se můžou zablokovat.

- Když se služba HA na aktivním hlavnímu uzlu zastaví, nerestartuje se, dokud nedojde k dalšímu převzetí služeb při selhání, nebo pokud je hlavní řadič pro převzetí služeb při selhání nebo hlavní server, který Když se jedna nebo víc služeb HA zastaví na aktivním hlavnímu uzlu, obzvláště když se Ambari Server zastaví, uživatelské rozhraní Ambari je nedostupné, další možné problémy zahrnují selhání úloh PŘÍZ, Spark a Oozie.

## <a name="apache-high-availability-services"></a>Služby Apache High Availability

Apache poskytuje vysokou dostupnost pro HDFS NameNode, PŘÍZ ResourceManager a HBase Master, které jsou také k dispozici v clusterech HDInsight. Na rozdíl od služeb HDInsight HA jsou podporované v clusterech ESP. Služba Apache HA komunikuje s druhým ZooKeeper kvorem (popsaným v předchozí části) a volí stav aktivní/pohotovostní a provádí automatické převzetí služeb při selhání. Následující části obsahují podrobnosti o fungování těchto služeb.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop systém souborů DFS (Distributed File System) (HDFS) NameNode

Clustery HDInsight založené na Apache Hadoop 2,0 nebo vyšší poskytují vysokou dostupnost NameNode. Existují dva NameNodes spuštěné v hlavních, které jsou nakonfigurovány pro automatické převzetí služeb při selhání. NameNodes používá *ZKFailoverController* ke komunikaci s Zookeeper pro stav aktivního/úsporného režimu. *ZKFailoverController* běží na obou hlavních a funguje stejným způsobem jako hlavní kontroler převzetí služeb při selhání.

Druhé kvorum Zookeeper je nezávislé na prvním kvoru, takže aktivní NameNode nemusí běžet na aktivním hlavnímu uzlu. Pokud je aktivní NameNode v nesprávném stavu nebo není v pořádku, v pohotovostním NameNode se tato volba vystaví jako aktivní.

### <a name="yarn-resourcemanager"></a>Správce prostředků PŘÍZe

Clustery HDInsight založené na Apache Hadoop 2,4 nebo vyšší podporují vysokou dostupnost prostředků PŘÍZového správce. Existují dva správce prostředků, RM1 a RM2, spuštěné v hlavnímu uzlu 0 a hlavnímu uzlu 1 v uvedeném pořadí. Podobně jako NameNode je také pro automatické převzetí služeb při selhání nakonfigurován správce prostředků PŘÍZe. Pokud aktuální aktivní Správce prostředků přestane reagovat nebo nereaguje, bude se automaticky aktivovat jiný správce prostředků.

PŘÍZ ResourceManager používá svůj vložený *ActiveStandbyElector* jako detektor selhání a vodicího voliče. Na rozdíl od HDFS NameNode nepotřebuje PŘÍZe ResourceManager samostatný ZKFC démon. Aktivní Správce prostředků zapisuje své stavy do Apache Zookeeper.

Vysoká dostupnost PŘÍZového správce prostředků je nezávislá na NameNode a dalších službách HDInsight HA. Aktivní Správce prostředků se nemusí spustit na aktivním hlavnímu uzlu nebo hlavnímu uzlu, kde běží aktivní NameNode. Další informace o vysoké dostupnosti správce prostředků PŘÍZe najdete v tématu [Vysoká dostupnost správce prostředků](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

Clustery HDInsight HBA podporují HBase Master vysoké dostupnosti. Na rozdíl od jiných služeb HA, které běží na hlavních, se hlavní servery spouští na třech uzlech Zookeeper, kde jedna z nich je aktivní hlavní a druhá druhá z nich je pohotovostní. Podobně jako NameNode HBase Master koordinuje s Apache Zookeeper a umožňuje automatické převzetí služeb při selhání, když má aktuální aktivní hlavní server problémy. V tuto chvíli existuje jenom jedna aktivní HBase Master.

## <a name="next-steps"></a>Další kroky

- [Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsight](hdinsight-high-availability-linux.md)
- [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
