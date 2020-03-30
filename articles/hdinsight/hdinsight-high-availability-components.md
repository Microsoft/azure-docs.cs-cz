---
title: Komponenty s vysokou dostupností v Azure HDInsight
description: Přehled různých komponent s vysokou dostupností používaných clustery HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069633"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Služby s vysokou dostupností podporované službou Azure HDInsight

 Abyvám bylo možné poskytnout optimální úroveň dostupnosti pro komponenty analýzy, byl hdinsight vyvinut s jedinečnou architekturou pro zajištění vysoké dostupnosti (HA) kritických služeb. Některé součásti této architektury byly vyvinuty společností Microsoft poskytovat automatické převzetí služeb při selhání. Další součásti jsou standardní součásti Apache, které jsou nasazeny pro podporu konkrétních služeb. Tento článek vysvětluje architekturu modelu služeb HA v HDInsight, jak HDInsight podporuje převzetí služeb při selhání pro služby HA a osvědčené postupy pro obnovení z jiných přerušení služeb.

## <a name="high-availability-infrastructure"></a>Infrastruktura s vysokou dostupností

HDInsight poskytuje přizpůsobenou infrastrukturu, která zajišťuje vysokou dostupnost čtyř primárních služeb s automatickými možnostmi převzetí služeb při selhání:

- Server Apache Ambari
- Server časové osy aplikace pro Apache YARN
- Server historie úloh pro hadoopovou mapuSnížit
- Apačská Livy

Tato infrastruktura se skládá z řady služeb a softwarových součástí, z nichž některé jsou navrženy společností Microsoft. Platforma HDInsight je jedinečná následující součást:

- Řadič převzetí služeb při selhání slave
- Hlavní řadič převzetí služeb při selhání
- Služba vysoké dostupnosti slave
- Hlavní služba s vysokou dostupností

![infrastruktura s vysokou dostupností](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Existují také další služby s vysokou dostupností, které jsou podporovány komponentami spolehlivosti Apache s otevřeným zdrojovým kódem. Tyto součásti jsou také k dispozici v clusterech HDInsight:

- Hadoop souborový systém (HDFS) NameNode
- Správce prostředků příze
- HZákladní vzor

V následujících částech naleznete další podrobnosti o tom, jak tyto služby spolupracují.

## <a name="hdinsight-high-availability-services"></a>HDInsight služby s vysokou dostupností

Společnost Microsoft poskytuje podporu pro čtyři služby Apache v následující tabulce v clusterech HDInsight. Aby se odlišily od služeb s vysokou dostupností podporovaných komponentami od Apache, nazývají se *služby HDInsight HA*.

| Služba | Uzly clusteru | Typy clusterů | Účel |
|---|---|---|---|
| Server Apache Ambari| Aktivní headnode | Všechny | Monitoruje a spravuje cluster.|
| Server časové osy aplikace pro Apache YARN | Aktivní headnode | Všichni kromě Kafky | Udržuje ladění informací o úlohách YARN spuštěných v clusteru.|
| Server historie úloh pro hadoopovou mapuSnížit | Aktivní headnode | Všichni kromě Kafky | Udržuje ladicí data pro úlohy MapReduce.|
| Apačská Livy | Aktivní headnode | Spark | Umožňuje snadnou interakci s clusterem Spark přes rozhraní REST |

>[!Note]
> Clustery balíčků HDInsight Enterprise Security Package (ESP) aktuálně poskytují pouze server Ambari vysokou dostupnost.

### <a name="architecture"></a>Architektura

Každý cluster HDInsight má dva headnodes v aktivním a pohotovostním režimu. Služby HDInsight HA běží pouze na headnodes. Tyto služby by měly být vždy spuštěny na aktivním headnode a zastaveny a uvedeny do režimu údržby v pohotovostním uzlu.

Pro udržování správných stavů služeb HA a poskytování rychlého převzetí služeb při selhání využívá HDInsight Apache ZooKeeper, což je koordinační služba pro distribuované aplikace, k provádění aktivních voleb headnode. HDInsight také zřstanoví několik procesů java na pozadí, které koordinují postup převzetí služeb při selhání pro služby HDInsight HA. Tyto služby jsou následující: hlavní řadič převzetí služeb při selhání, řadič převzetí služeb při selhání slave, *master-ha-service*a *slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper je vysoce výkonná koordinační služba pro distribuované aplikace. V produkčním prostředí zookeeper obvykle běží v replikovaném režimu, kde replikovaná skupina serverů ZooKeeper tvoří kvorum. Každý cluster HDInsight má tři uzly ZooKeeper, které umožňují tři zookeeper servery tvořit kvorum. HDInsight má dvě kvora ZooKeeper běží paralelně s sebou. Jedno kvorum rozhodne o aktivním headnode v clusteru, ve kterém by měly být spuštěny služby HDInsight HA. Další kvorum se používá ke koordinaci služeb HA poskytovaných Apache, jak je podrobně popsáno v pozdějších částech.

### <a name="slave-failover-controller"></a>Řadič převzetí služeb při selhání slave

Řadič převzetí služeb při selhání slave běží na každém uzlu v clusteru HDInsight. Tento řadič je zodpovědný za spuštění agenta Ambari a *slave-ha-service* na každém uzlu. Pravidelně se dotazuje prvního kvora ZooKeeper o aktivním headnode. Při změně aktivní ho a pohotovostního režimu headnodes, podřízený řadič převzetí služeb při selhání provede následující:

1. Aktualizuje konfigurační soubor hostitele.
1. Restartuje agenta Ambari.

Služba *slave-ha* je zodpovědná za zastavení služeb HDInsight HA (s výjimkou serveru Ambari) v pohotovostním uzlu.

### <a name="master-failover-controller"></a>Hlavní řadič převzetí služeb při selhání

Hlavní řadič převzetí služeb při selhání běží na obou hlavové uzly. Oba hlavní řadiče převzetí služeb při selhání komunikují s prvním kvorem ZooKeeper a nominují headnode, na které běží jako aktivní headnode.

Pokud například hlavní řadič převzetí služeb při selhání na headnode 0 vyhraje volby, dojde k následujícím změnám:

1. Headnode 0 se aktivuje.
1. Hlavní řadič převzetí služeb při selhání spustí server Ambari a *službu master-ha-service* na headnode 0.
1. Druhý hlavní řadič převzetí služeb při selhání zastaví server Ambari a *službu master-ha* na headnode 1.

Master-ha-service běží pouze na aktivním headnode, zastaví služby HDInsight HA (s výjimkou serveru Ambari) v pohotovostním uzlu a spustí je na aktivním headnode.

### <a name="the-failover-process"></a>Proces převzetí služeb při selhání

![proces převzetí služeb při selhání](./media/hdinsight-high-availability-components/failover-steps.png)

Na každém headnode je spuštěn monitor stavu spolu s hlavním řadičem převzetí služeb při selhání, který odesílá oznámení o poslechu do kvora Zookeeper. Headnode je v tomto scénáři považován za službu HA. Monitor stavu kontroluje, zda je každá služba s vysokou dostupností v pořádku a zda je připravena k účasti ve volbách do vedení. Pokud ano, bude tento headnode soutěžit ve volbách. Pokud ne, skončí volby, dokud se znovu nepřipraví.

Pokud přeplože nýtoviny někdy dosáhne vedení a stane se aktivní (například v případě selhání s předchozím aktivním uzřem), jeho hlavní řadič převzetí služeb při selhání spustí všechny služby HDInsight HA na něm. Hlavní řadič převzetí služeb při selhání také zastaví tyto služby na druhém uzlu.

V případě selhání služby HDInsight HA, jako je například služba je dolů nebo není v pořádku, hlavní řadič převzetí služeb při selhání by měl automaticky restartovat nebo zastavit služby podle stavu headnode. Uživatelé by neměli ručně spouštět služby HDInsight HA na obou hlavních uzlech. Místo toho povolte automatické nebo ruční převzetí služeb při selhání, které službu obnoví.

### <a name="inadvertent-manual-intervention"></a>Neúmyslný ruční zásah

Služby HDInsight HA by měly být spuštěny pouze na aktivním headnode a v případě potřeby budou automaticky restartovány. Vzhledem k tomu, že jednotlivé služby HA nemají vlastní monitor stavu, nelze převzetí služeb při selhání spustit na úrovni jednotlivých služeb. Převzetí služeb při selhání je zajištěno na úrovni uzlu a nikoli na úrovni služby.

### <a name="some-known-issues"></a>Některé známé problémy

- Při ručním spuštění služby HA v pohotovostním uzlu se nezastaví, dokud nedojde k dalšímu převzetí služeb při selhání. Když ha služby běží na obou headnodes, některé potenciální problémy patří: Ambari UI je nepřístupný, Ambari hází chyby, YARN, Spark, a Oozie pracovních míst může uvíznout.

- Když se služba HA na aktivním hlavním uzlu zastaví, nerestartuje se, dokud nedojde k dalšímu převzetí služeb při selhání nebo dokud se hlavní řadič převzetí služeb při selhání/hlavní služba ha-service nerestartuje. Když se jedna nebo více služeb HA zastaví na aktivním headnode, zvláště když se zastaví server Ambari, je ui Ambari nepřístupné, mezi další potenciální problémy patří selhání úloh YARN, Spark a Oozie.

## <a name="apache-high-availability-services"></a>Služby s vysokou dostupností Apache

Apache poskytuje vysokou dostupnost pro HDFS NameNode, YARN ResourceManager a HBase Master, které jsou také k dispozici v clusterech HDInsight. Na rozdíl od služeb HDInsight HA jsou podporovány v clusterech ESP. Služby Apache HA komunikují s druhým kvorem ZooKeeper (popsaným ve výše uvedené části) a volí aktivní/pohotovostní stavy a provádějí automatické převzetí služeb při selhání. V následujících částech jsou podrobně popsány informace o tom, jak tyto služby fungují.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Distribuovaný souborový systém (HDFS) NameNode

HdInsight clustery založené na Apache Hadoop 2.0 nebo vyšší poskytují NameNode vysokou dostupnost. Na hlavových uzlech jsou spuštěny dva názvové uzly, které jsou nakonfigurovány pro automatické převzetí služeb při selhání. NameNodes použít *ZKFailoverController* komunikovat s Zookeeper zvolit pro aktivní/pohotovostní stav. *ZKFailoverController* běží na obou headnodes a funguje stejným způsobem jako hlavní řadič převzetí služeb při selhání výše.

Druhé kvorum Zookeeper je nezávislé na prvním kvoru, takže aktivní NameNode nemusí běžet na aktivním headnode. Pokud je aktivní název mrtvé nebo není v pořádku, pohotovostní názevnode vyhraje volby a stane se aktivní.

### <a name="yarn-resourcemanager"></a>Správce prostředků příze

HDInsight clustery založené na Apache Hadoop 2.4 nebo vyšší, podporují YARN ResourceManager vysokou dostupnost. Existují dva ResourceManagers, rm1 a rm2, běží na headnode 0 a headnode 1, resp. Podobně jako NameNode je správce prostředků YARN také nakonfigurován pro automatické převzetí služeb při selhání. Jiný ResourceManager je automaticky zvolen být aktivní, když aktuální aktivní ResourceManager přejde dolů nebo neodpovídá.

YARN ResourceManager používá svůj vestavěný *ActiveStandbyElector* jako detektor selhání a vůdce voliče. Na rozdíl od HDFS NameNode, YARN ResourceManager nepotřebuje samostatný daemon ZKFC. Aktivní ResourceManager zapisuje své stavy do Apache Zookeeper.

Vysoká dostupnost YARN ResourceManager je nezávislá na NameNode a dalších službách HDInsight HA. Aktivní ResourceManager nemusí být spuštěn na aktivní madou nebo v headnode, kde je spuštěn aktivní NázevNode. Další informace o vysoké dostupnosti yarn resourcemanageru naleznete v [tématu ResourceManager High Availability](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HZákladní vzor

Clustery HDInsight HBase podporují vysokou dostupnost HBase Master. Na rozdíl od jiných služeb HA, které běží na headnodes, HBase Masters běží na třech uzlech Zookeeper, kde jeden z nich je aktivní master a další dva jsou v pohotovostním režimu. Stejně jako NameNode, HBase Master koordinuje s Apache Zookeeper pro volby vůdce a dělá automatické převzetí služeb při selhání, když aktuální aktivní master má problémy. V každém okamžiku existuje pouze jeden aktivní hbase master.

## <a name="next-steps"></a>Další kroky

- [Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsightu](hdinsight-high-availability-linux.md)
- [Architektura virtuální sítě Azure HDInsight](hdinsight-virtual-network-architecture.md)
