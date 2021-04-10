---
title: Průvodce nastavením velikosti clusteru interaktivních dotazů ve službě Azure HDInsight
description: Průvodce plánováním interaktivních dotazů ve službě Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933058"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Průvodce nastavením velikosti clusteru interaktivních dotazů ve službě Azure HDInsight

Tento dokument popisuje velikost clusteru HDInsight Interactive Query (LLAP) pro typickou úlohu, aby dosáhla přijatelného výkonu. Doporučení uvedená v tomto dokumentu jsou obecná a konkrétní úlohy můžou potřebovat konkrétní ladění.

## <a name="default-vm-types-for-interactive-query"></a>Výchozí typy virtuálních počítačů pro interaktivní dotaz

| Typ uzlu | Instance | Velikost |
|---|---|---|
| Head | D13 v2 | 8 VCPU, 56-GB RAM, 400 GB SSD |
| Zaměstnanec | D14 v2 | 16 VCPU, 112-GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPU, 8 GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Doporučené konfigurace

Doporučené hodnoty konfigurace jsou založené na pracovním uzlu typ D14 v2.

| Klíč | Hodnota | Popis |
|---|---|---|
| příz. NodeManager. Resource. paměť-MB | 102400 (MB) | Celková velikost paměti, která je zadána v MB pro všechny kontejnery PŘÍZe na uzlu. |
| příze. Scheduler. maximum – přidělení-MB | 102400 (MB) | Maximální přidělení pro každou žádost kontejneru v RM v MB. Požadavky na paměť vyšší než tato hodnota se projeví. |
| příze. Scheduler. maximum-přidělení – virtuální jádra | 12 |Maximální počet jader procesoru pro každou žádost kontejneru na Správce prostředků. Žádosti, které jsou vyšší než tato hodnota, se neprojeví. |
| příze. Scheduler. Capacity. root. llap. Capacity | 90 % | Přidělení kapacity PŘÍZe pro frontu LLAP.  |
| podregistr. Server2. TEZ. Sessions. per. default. Queue | number_of_worker_nodes |Počet relací pro každou frontu s názvem v podregistru. Server2. TEZ. default. Queues. Toto číslo odpovídá počtu koordinátorů dotazů (tez AMs). |
| TEZ. am. Resource. Memory. MB | 4096 (MB) | Velikost paměti v MB, kterou má tez AppMaster použít. |
| podregistr. TEZ. Container. Size | 4096 (MB) | Zadaná velikost kontejneru tez v MB |
| hive.llap.daemon.num.executors | 12 | Počet prováděcích modulů na démona LLAP |
| podregistr. llap. IO. nevláken. Size | 12 | Velikost fondu vláken pro prováděče. |
| podregistr. llap. démon. příze. Container. MB | 86016 (MB) | Celková paměť v MB používaná jednotlivými démony LLAP (paměť na démon).|
| podregistr. llap. IO. Memory. Size | 409600 (MB) | Velikost mezipaměti v MB na LLAP démona poskytnutá pro mezipaměť SSD je povolená. |
| podregistr. auto. Convert. Join. noconditionaltask. Size | 2048 (MB) | velikost paměti v MB pro připojení k mapě |

## <a name="llap-daemon-size-estimations"></a>Odhad velikosti LLAP démona  

### <a name="yarnnodemanagerresourcememory-mb"></a>příz. NodeManager. Resource. paměť-MB

Tato hodnota indikuje maximální součet paměti v MB, který používají kontejnery PŘÍZe na každém uzlu. Určuje velikost paměti, kterou lze v tomto uzlu použít, takže tato hodnota by měla být menší než celková paměť v tomto uzlu.

Nastavte tuto hodnotu = [celková fyzická paměť v uzlu] – [paměť pro operační systém + další služby].

Doporučuje se nastavit tuto hodnotu na ~ 90% dostupné paměti RAM. V případě D14 v2 je doporučená hodnota **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>příze. Scheduler. maximum – přidělení-MB

Tato hodnota označuje maximální přidělení pro každou žádost kontejneru v Správce prostředků v MB. Požadavky na paměť vyšší, než je zadaná hodnota, se projeví. Správce prostředků může poskytnout paměť pouze kontejnerům v přírůstcích po `yarn.scheduler.minimum-allocation-mb` a nemůže překročit velikost určenou parametrem `yarn.scheduler.maximum-allocation-mb` . Tato hodnota by neměla být větší než celková velikost dané paměti uzlu, která je určena parametrem `yarn.nodemanager.resource.memory-mb` .

V případě pracovních uzlů D14 v2 je doporučená hodnota **102400 MB** .

### <a name="yarnschedulermaximum-allocation-vcores"></a>příze. Scheduler. maximum-přidělení – virtuální jádra

Tato konfigurace označuje maximální počet jader virtuálního procesoru pro každou žádost kontejneru na Správce prostředků. Požaduje se vyšší hodnota, než tato konfigurace vstoupí v platnost. Tato konfigurace je globální vlastností plánovače PŘÍZe. V případě kontejneru démona LLAP lze tuto hodnotu nastavit na 75% z celkového počtu dostupných virtuálních jader (virtuální jádra). Zbývající 25% by mělo být rezervované pro NodeManager, datanode a další služby spuštěné na pracovních uzlech.  

Pro pracovní uzly D14 v2 je možné předávat 16 virtuální jádra a 75% z 16 virtuální jádra. Proto je doporučená hodnota pro kontejner LLAP démona **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>podregistr. Server2. TEZ. Sessions. per. default. Queue

Tato hodnota konfigurace určuje počet tez relací, které mají být spuštěny paralelně pro každou frontu určenou parametrem `hive.server2.tez.default.queues` . Hodnota odpovídá počtu tez AMs (koordinátory dotazů). Doporučuje se to být stejné jako počet pracovních uzlů, které mají jeden tez na uzel. Počet tez AMs může být vyšší než počet uzlů LLAP démon. Jejich primární zodpovědností je koordinovat provádění dotazů a přiřadit fragmenty plánu dotazů odpovídajícím démonům LLAP ke spuštění. Doporučuje se uchovávat jako násobek několika uzlů LLAP démona, aby dosáhli vyšší propustnosti.  

Výchozí cluster HDInsight má čtyři procesy démony LLAP spuštěné na čtyřech pracovních uzlech, proto je doporučená hodnota **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>TEZ. am. Resource. Memory. MB, podregistr. TEZ. Container. Size

`tez.am.resource.memory.mb` definuje velikost hlavní databáze aplikace TEZ.  
Doporučená hodnota je **4096 MB**.

`hive.tez.container.size` definuje množství paměti zadané pro kontejner TEZ. Tato hodnota musí být nastavena mezi minimální velikostí kontejneru PŘÍZe ( `yarn.scheduler.minimum-allocation-mb` ) a maximální velikostí kontejneru příze ( `yarn.scheduler.maximum-allocation-mb` ).  
Doporučuje se nastavit na **4096 MB**.  

Obecným pravidlem je zachování menšího množství paměti na procesor, který zvažuje jeden procesor na kontejner. `Rreserve` paměť pro počet tez AMs na uzlu před přidělením paměti pro démona LLAP. Například pokud používáte dvě tez AMs (4 GB každé) na uzel, dejte 82 GB z 90 GB pro LLAP démona, který zachovává 8 GB pro dvě tez AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>příze. Scheduler. Capacity. root. llap. Capacity

Tato hodnota označuje procento kapacity zadané pro frontu LLAP. Cluster interaktivních dotazů HDInsights poskytuje 90% celkové kapacity pro frontu LLAP a zbývajících 10% je nastavené na výchozí fronty pro další přidělení kontejnerů.  
Pro pracovní uzly D14v2 je doporučená hodnota **90** pro frontu LLAP.

### <a name="hivellapdaemonyarncontainermb"></a>podregistr. llap. démon. příze. Container. MB

Celková velikost paměti pro démona LLAP je závislá na následujících součástech:

* Konfigurace velikosti kontejneru PŘÍZ ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Paměť haldy využívané prováděcími moduly (XMX)

    Velikost paměti RAM, která je k dispozici po pořízení velikosti rezervy.  
    Pro D14 v2, HDI 4,0 – tato hodnota je (86 GB-6 GB) = 80 GB  
    Pro D14 v2, HDI 3,6 – Tato hodnota je (84 GB-6 GB) = 78 GB

* Mezipaměť v paměti mimo paměť na démona (podregistr. llap. IO. Memory. Size)

* Rezerva počtu

    Je to část paměti z jiné haldy, která se používá pro režijní náklady na virtuální počítače Java (metaspace, Thread Stack, datové struktury GC atd.). Tato část je pozorována, aby byla okolo 6% velikosti haldy (XMX). Aby bylo možné na bezpečnější straně, je možné ji vypočítat jako 6% z celkové velikosti paměti démona LLAP. Vzhledem k tomu, že je povolená mezipaměť SSD, umožní démonovi LLAP použít veškerý dostupný prostor v paměti, který se použije jenom pro haldu.  
    V případě D14 v2 je doporučená hodnota ceil – (86 GB × 0,06) ~ = **6 GB**.  

Paměť na démona = [velikost mezipaměti v paměti] + [velikost haldy] + [rezerva].

Dá se vypočítat takto:

Tez paměti na uzel = [(počet tez AMs/počet uzlů LLAP démon) * tez velikost].
Velikost kontejneru démona LLAP = [90% z maximální paměti kontejneru vlákna] – [paměť tez na uzel].

Pro pracovní uzel D14 v2, HDI 4,0 – doporučená hodnota je (90-(1/1 × 4 GB)) = **86 GB**.
(Pro HDI 3,6 je doporučená hodnota **84 GB** , protože byste měli rezervovat ~ 2 GB pro posuvník.)  

### <a name="hivellapiomemorysize"></a>podregistr. llap. IO. Memory. Size

Tato konfigurace představuje velikost paměti, která je k dispozici jako mezipaměť pro démona LLAP. Procesy démon LLAP můžou jako mezipaměť použít SSD. Při nastavení `hive.llap.io.allocator.mmap` = true se povolí ukládání do mezipaměti SSD. D14 v2 obsahuje ~ 800 GB SSD a ukládání do mezipaměti SSD je ve výchozím nastavení povolené pro cluster interaktivních dotazů (LLAP). Je nakonfigurovaná tak, aby používala 50% prostoru SSD pro mezipaměť mimo haldu.

V případě D14 v2 je doporučená hodnota **409600 MB**.  

U jiných virtuálních počítačů bez povoleného ukládání do mezipaměti SSD je výhodné dát k dispozici část dostupné paměti RAM pro ukládání do mezipaměti LLAP, aby bylo dosaženo lepšího výkonu. Nastavte celkovou velikost paměti pro démona LLAP následujícím způsobem:  

Celková paměť démona LLAP = [velikost mezipaměti LLAP] + [velikost haldy] + [rezerva].

Doporučuje se upravit velikost mezipaměti a velikost haldy, která je nejvhodnější pro vaše zatížení.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Tato konfigurace řídí počet prováděcích modulů, které mohou paralelně provádět úlohy na LLAP démon. Tato hodnota je zůstatkem počtu dostupných virtuální jádra, velikosti paměti zadané na vykonavateli a celkové dostupné paměti na LLAP démon. Obvykle by tato hodnota měla být co nejblíže k počtu jader.

Pro D14 v2 je k dispozici 16 virtuální jádra, ale ne všechny virtuální jádra. Pracovní uzly také spouštějí další služby, jako jsou NodeManager, datanode a monitory metriky, které vyžadují určitou část dostupných virtuální jádra. Tato hodnota se dá nakonfigurovat až na 75% z celkového počtu virtuální jádra dostupných v tomto uzlu.

V případě D14 v2 je doporučená hodnota (. 75 × 16) = **12**

Doporučuje se rezervovat po 6 GB prostoru haldy na vykonavatele. Upravte počet prováděcích modulů na základě dostupné velikosti démona LLAP a počtu dostupných virtuální jádra na uzel.  

### <a name="hivellapiothreadpoolsize"></a>podregistr. llap. IO. nevláken. Size

Tato hodnota určuje velikost fondu vláken pro vykonavatele. Vzhledem k tomu, že vykonavatelé jsou pevně určení, bude stejný jako počet prováděcích modulů na démon LLAP.

Pro D14 v2 se doporučuje nastavit tuto hodnotu na **12**.

Tato konfigurace nemůže přesáhnout `yarn.nodemanager.resource.cpu-vcores` hodnotu.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>podregistr. auto. Convert. Join. noconditionaltask. Size

Ujistěte se, že jste `hive.auto.convert.join.noconditionaltask` povolili uplatnění tohoto parametru. Tato konfigurace umožňuje uživateli určit velikost tabulek, které mohou být do paměti přizpůsobeny, aby se připojily k mapě. Je-li součet velikosti n-1 `tables/partitions` pro n-Way spojení menší než konfigurovaná hodnota, bude zvolena možnost spojení map. Velikost paměti prováděcího modulu LLAP by měla být použita k výpočtu prahové hodnoty autoConvert pro připojení k mapě.

Pro D14 v2 doporučujeme nastavit tuto hodnotu na **2048 MB**.

Doporučujeme upravit tuto hodnotu, která je vhodná pro vaše zatížení, protože nastavení této hodnoty je příliš nízké, ale nemusí používat funkci autoConvert. Nastavení příliš vysokého může způsobit pozastavení GC, což může negativně ovlivnit výkon dotazů.

## <a name="next-steps"></a>Další kroky

* [Pokyny pro bránu](gateway-best-practices.md)
* [Ladění demystifikovat Apache Tez paměti – krok za krokem](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Mapování velikosti paměti spojení pro LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP – přehled architektury s jednou stránkou](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [LLAP hloubkové podrobně pro podregistr](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
