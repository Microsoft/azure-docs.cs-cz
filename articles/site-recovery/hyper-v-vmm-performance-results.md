---
title: Výsledky testů pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality s Azure Site Recovery | Microsoft Docs
description: Tento článek poskytuje informace o testování výkonu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377227"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Výsledky testů pro replikaci Hyper-V do sekundární lokality


Tento článek poskytuje výsledky testování výkonu při replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundárního datacentra.

## <a name="test-goals"></a>Testovací cíle

Cílem testování bylo zjistit, jak Site Recovery provádí během replikace ustáleného stavu.

- K replikaci ustálených stavů dojde, když se virtuální počítače dokončily počáteční replikace a synchronizují se rozdílové změny.
- Je důležité změřit výkon pomocí stabilního stavu, protože se jedná o stav, ve kterém většina virtuálních počítačů zůstává, pokud nedošlo k neočekávanému výpadku.
- Testovací nasazení se skládá ze dvou místních webů se serverem VMM v každé lokalitě. Toto testovací nasazení je typické pro nasazení sídla na pracovišti, jako je primární lokalita a pobočka jako sekundární lokalita nebo lokalita pro obnovení.

## <a name="what-we-did"></a>Co máme

V tomto příkladu jsme provedli test Pass:

1. Vytvořili jste virtuální počítače pomocí šablon VMM.
2. Začali jste virtuální počítače a zaznamenané metriky výkonu směrného plánu za 12 hodin.
3. Na primárních serverech VMM a obnovení se vytvořily cloudy.
4. Nakonfigurovali se replikace v Site Recovery, včetně mapování mezi zdrojovými a obnovovacími cloudy.
5. Povolení ochrany pro virtuální počítače a umožnění dokončení počáteční replikace.
6. Čekání na stabilizaci systému je několik hodin.
7. Zaznamenané metriky výkonu za 12 hodin, kdy všechny virtuální počítače zůstaly v očekávaném stavu replikace po dobu 12 hodin.
8. Byl změřen rozdíl mezi metrikami základního výkonu a metrikami výkonu replikace.


## <a name="primary-server-performance"></a>Výkon primárního serveru

* Replika technologie Hyper-V (používaná v Site Recovery) asynchronně sleduje změny v souboru protokolu s minimálním zatížením úložiště na primárním serveru.
* Replika technologie Hyper-V používá k minimalizaci režie za IOPS na základě samy zachované paměti mezipaměť. Ukládá zápisy do VHDX v paměti a vyprázdní je do souboru protokolu před tím, než se protokol pošle do lokality pro obnovení. K vyprázdnění disku dojde také v případě, že zápisy narazí na předem stanovený limit.
* Následující graf zobrazuje režijní náklady na stabilní stav IOPS pro replikaci. Zjistili jsme, že režie IOPS v důsledku replikace je okolo 5%, což je poměrně nízké.

  ![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika technologie Hyper-V používá paměť na primárním serveru k optimalizaci výkonu disku. Jak je znázorněno v následujícím grafu, režie paměti na všech serverech v primárním clusteru je mezní. Zobrazená režie paměti je procento paměti využívané replikací v porovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika technologie Hyper-V má minimální nároky na výkon procesoru. Jak je znázorněno v grafu, režie replikace je v rozsahu 2-3%.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Výkon sekundárního serveru

Replika technologie Hyper-V používá k optimalizaci počtu operací úložiště malou velikost paměti na serveru pro obnovení. Graf shrnuje využití paměti na serveru pro obnovení. Zobrazená režie paměti je procento paměti využívané replikací v porovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Vedlejší výsledky](./media/hyper-v-vmm-performance-results/IC744916.png)

Množství vstupně-výstupních operací na webu obnovení je funkce počtu operací zápisu v primární lokalitě. Pojďme se podívat na celkový počet vstupně-výstupních operací v lokalitě pro obnovení v porovnání s celkovými vstupně-výstupními operacemi a operacemi zápisu v primární lokalitě. Grafy ukazují, že celkový počet IOPS v lokalitě pro obnovení je

* Přibližně 1,5 krát zápis IOPS na primárním počítači.
* Přibližně 37% z celkového IOPS v primární lokalitě.

![Vedlejší výsledky](./media/hyper-v-vmm-performance-results/IC744917.png)

![Vedlejší výsledky](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Vliv na využití sítě

Na základě stávající šířky pásma 5 GB za sekundu se použil průměr 275 MB za sekundu šířky pásma sítě (s povolenou kompresí).

![Výsledky využití sítě](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Vliv na výkon virtuálního počítače

Důležitým aspektem je dopad replikace na produkční úlohy běžící na virtuálních počítačích. Pokud je primární lokalita dostatečně zřízená pro replikaci, nemělo by to mít žádný vliv na úlohy. Mechanismus zjednodušeného sledování repliky technologie Hyper-V zajišťuje, aby úlohy běžící ve virtuálních počítačích nebyly při replikaci ustáleného stavu ovlivněny. To je znázorněno v následujících grafech.

Tento graf znázorňuje IOPS prováděné virtuálními počítači, které používají různé úlohy, před a po povolení replikace. Můžete si všimnout, že mezi nimi není žádný rozdíl.

![Výsledky efektu repliky](./media/hyper-v-vmm-performance-results/IC744920.png)

Následující graf znázorňuje propustnost virtuálních počítačů, na kterých běží různé úlohy, před a po povolení replikace. Můžete sledovat, že replikace nemá žádný významný dopad.

![Efekty repliky výsledků](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Závěr

Výsledky jasně ukazují, že Site Recovery, společně s replikou technologie Hyper-V, se dobře škálují s minimálními nároky na velký cluster. Site Recovery poskytuje jednoduché nasazení, replikaci, správu a monitorování. Replika technologie Hyper-V poskytuje nezbytnou infrastrukturu pro úspěšné škálování replikace. 

## <a name="test-environment-details"></a>Podrobnosti testovacího prostředí

### <a name="primary-site"></a>Primární lokalita

* Primární lokalita má cluster, který obsahuje pět serverů Hyper-V s 470 virtuálními počítači.
* Virtuální počítače spouštějí různé úlohy a všechny mají povolenou ochranu Site Recovery.
* Úložiště pro uzel clusteru zajišťuje síť SAN iSCSI. Model – Hitachi HUS130.
* Každý clusterový server má čtyři síťové karty (nic) s jedním GB/s.
* Dvě síťové karty jsou připojené k privátní síti iSCSI a dvě jsou připojené k externí podnikové síti. Jedna z externích sítí je vyhrazena jenom pro komunikaci s clustery.

![Požadavky na primární hardware](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 má 256 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |4 |V/v mi × 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundární lokalita

* Sekundární lokalita má cluster s podporou převzetí služeb při selhání se šesti uzly.
* Úložiště pro uzel clusteru zajišťuje síť SAN iSCSI. Model – Hitachi HUS130.

![Specifikace primárního hardwaru](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (r) Xeon (r) CPU E5-2630 0 \@ 2.30 GHz |2 |V/v mi × 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Zatížení serveru

* Pro testovací účely jsme vybrali úlohy, které se běžně používají ve scénářích podnikového zákazníka.
* [IOMeter](http://www.iometer.org) používáme s charakteristikou úlohy shrnutou v tabulce pro simulaci.
* Všechny profily IOMeter jsou nastavené na zápis náhodných bajtů, aby se simulovaly vzory zápisu s nejhoršími písmeny pro úlohy.

| Úloha | I/O velikost (KB) | % Přístup | % Čtení | Nezpracované vstupně-výstupních | Vzor I/O |
| --- | --- | --- | --- | --- | --- |
| Souborový server |4<br />8<br />16<br />32<br />64 |60%<br />20 %<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Všechny 100% náhodné |
| SQL Server (svazek 1)<br />SQL Server (svazek 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% náhodný<br />100% sekvenční |
| Exchange |32 |100% |67% |8 |100% náhodný |
| Pracovní stanice/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |100% náhodný |
| Webový souborový server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Všechny 75% náhodné |

### <a name="vm-configuration"></a>Konfigurace virtuálního počítače

* 470 virtuálních počítačů v primárním clusteru.
* Všechny virtuální počítače s diskem VHDX.
* Virtuální počítače, na kterých běží úlohy, jsou shrnuté v tabulce. Všechny byly vytvořeny pomocí šablon VMM.

| Úloha | Počet virtuálních počítačů | Minimální velikost paměti RAM (GB) | Maximální velikost paměti RAM (GB) | Velikost logického disku (GB) na virtuální počítač | Maximální IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Souborový server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webový server |149 |.5 |1 |80 |6 |
| CELKEM |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Nastavení Site Recovery

* Site Recovery bylo nakonfigurováno pro místní ochranu místně.
* Server VMM má nakonfigurované čtyři cloudy, které obsahují servery clusteru Hyper-V a jejich virtuální počítače.

| Primární Cloud VMM | Chráněné virtuální počítače | Četnost replikací | Další body obnovení |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut |Žádné |
| PrimaryCloudRpo30s |47 |30 sekund |Žádné |
| PrimaryCloudRpo30sArp1 |47 |30 sekund |1 |
| PrimaryCloudRpo5m |235 |5 minut |Žádný |

### <a name="performance-metrics"></a>Metriky výkonu

Tabulka shrnuje metriky výkonu a čítače, které byly v nasazení měřeny.

| Metrika | Čítač |
| --- | --- |
| Procesor |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Dostupná paměť |\Memory\Available MB |
| IOPS |\PhysicalDisk (_Total) \ přenosy za sekundu |
| Operace čtení z virtuálního počítače (IOPS) za sekundu |Operace \Read virtuálního úložného\<zařízení \Hyper-V (VHD >) za sekundu |
| Operace zápisu do virtuálního počítače (IOPS) za sekundu |\Hyper-V \Write operací virtuálního úložného zařízení (\<VHD >) |
| Propustnost čtení virtuálního počítače |\Hyper-V virtuálního úložného zařízení\<(VHD >) \Read bajty/s |
| Propustnost zápisu virtuálních počítačů |\Hyper-V virtuálního úložného zařízení\<(VHD >) \Write bajty/s |

## <a name="next-steps"></a>Další postup

[Nastavení replikace](hyper-v-vmm-disaster-recovery.md)
