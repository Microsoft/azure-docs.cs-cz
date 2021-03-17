---
title: Testování replikace virtuálních počítačů Hyper-V do sekundární lokality pomocí nástroje VMM pomocí Azure Site Recovery
description: Tento článek poskytuje informace o testování výkonu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327633"
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

  ![Graf, který zobrazuje režijní náklady na stabilní stav IOPS pro replikaci.](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika technologie Hyper-V používá paměť na primárním serveru k optimalizaci výkonu disku. Jak je znázorněno v následujícím grafu, režie paměti na všech serverech v primárním clusteru je mezní. Zobrazená režie paměti je procento paměti využívané replikací v porovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika technologie Hyper-V má minimální nároky na výkon procesoru. Jak je znázorněno v grafu, režie replikace je v rozsahu 2-3%.

![Graf, který zobrazuje režii replikace, je v rozsahu 2-3%.](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Výkon sekundárního serveru

Replika technologie Hyper-V používá k optimalizaci počtu operací úložiště malou velikost paměti na serveru pro obnovení. Graf shrnuje využití paměti na serveru pro obnovení. Zobrazená režie paměti je procento paměti využívané replikací v porovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Graf, který shrnuje využití paměti na serveru pro obnovení.](./media/hyper-v-vmm-performance-results/IC744916.png)

Množství vstupně-výstupních operací na webu obnovení je funkce počtu operací zápisu v primární lokalitě. Pojďme se podívat na celkový počet vstupně-výstupních operací v lokalitě pro obnovení v porovnání s celkovými vstupně-výstupními operacemi a operacemi zápisu v primární lokalitě. Grafy ukazují, že celkový počet IOPS v lokalitě pro obnovení je

* Přibližně 1,5 krát zápis IOPS na primárním počítači.
* Přibližně 37% z celkového IOPS v primární lokalitě.

![Graf, který zobrazuje porovnání IOPS v primárních a sekundárních lokalitách.](./media/hyper-v-vmm-performance-results/IC744917.png)

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
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 má 256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 |V/v mi × 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| Server VMM |2 | | |2 |1 Gb/s |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundární lokalita

* Sekundární lokalita má cluster s podporou převzetí služeb při selhání se šesti uzly.
* Úložiště pro uzel clusteru zajišťuje síť SAN iSCSI. Model – Hitachi HUS130.

![Specifikace primárního hardwaru](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (R) Xeon (R) CPU E5-2630 0 \@ 2.30 GHz |2 |V/v mi × 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| Server VMM |2 | | |2 |1 Gb/s |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Zatížení serveru

* Pro testovací účely jsme vybrali úlohy, které se běžně používají ve scénářích podnikového zákazníka.
* [IOMeter](http://www.iometer.org) používáme s charakteristikou úlohy shrnutou v tabulce pro simulaci.
* Všechny profily IOMeter jsou nastavené na zápis náhodných bajtů, aby se simulovaly vzory zápisu s nejhoršími písmeny pro úlohy.

| Úloha | I/O velikost (KB) | % Přístup | % Čtení | Nezpracované vstupně-výstupních | Vzor I/O |
| --- | --- | --- | --- | --- | --- |
| Souborový server |4<br />8<br />16<br />32<br />64 |60 %<br />20 %<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Všechny 100% náhodné |
| SQL Server (svazek 1)<br />SQL Server (svazek 2) |8<br />64 |100 %<br />100 % |70 %<br />0 % |8<br />8 |100% náhodný<br />100% sekvenční |
| Výměna |32 |100 % |67 % |8 |100% náhodný |
| Pracovní stanice/VDI |4<br />64 |66%<br />34% |70 %<br />95 % |1<br />1 |100% náhodný |
| Webový souborový server |4<br />8<br />64 |33%<br />34%<br />33% |95 %<br />95 %<br />95 % |8<br />8<br />8 |Všechny 75% náhodné |

### <a name="vm-configuration"></a>Konfigurace virtuálního počítače

* 470 virtuálních počítačů v primárním clusteru.
* Všechny virtuální počítače s diskem VHDX.
* Virtuální počítače, na kterých běží úlohy, jsou shrnuté v tabulce. Všechny byly vytvořeny pomocí šablon VMM.

| Úloha | Počet virtuálních počítačů | Minimální velikost paměti RAM (GB) | Maximální velikost paměti RAM (GB) | Velikost logického disku (GB) na virtuální počítač | Maximální IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Souborový server |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Webový server |149 |0,5 |1 |80 |6 |
| TOTAL |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Nastavení Site Recovery

* Site Recovery bylo nakonfigurováno pro místní ochranu místně.
* Server VMM má nakonfigurované čtyři cloudy, které obsahují servery clusteru Hyper-V a jejich virtuální počítače.

| Primární Cloud VMM | Chráněné virtuální počítače | Četnost replikací | Další body obnovení |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut |Žádné |
| PrimaryCloudRpo30s |47 |30 sekund |Žádné |
| PrimaryCloudRpo30sArp1 |47 |30 sekund |1 |
| PrimaryCloudRpo5m |235 |5 minut |Žádné |

### <a name="performance-metrics"></a>Metriky výkonu

Tabulka shrnuje metriky výkonu a čítače, které byly v nasazení měřeny.

| Metrika | Čítač |
| --- | --- |
| Procesor |\Processor(_Total)\% Processor Time |
| Dostupná paměť |\Memory\Available MB |
| IOPS |\PhysicalDisk (_Total) \ přenosy za sekundu |
| Operace čtení z virtuálního počítače (IOPS) za sekundu |Operace \Read virtuálního úložného zařízení ( \<VHD> ) \Hyper-V/s |
| Operace zápisu do virtuálního počítače (IOPS) za sekundu |Operace \Write virtuálního úložného zařízení ( \<VHD> ) \Hyper-V/S |
| Propustnost čtení virtuálního počítače |\Hyper-V virtuálního úložného zařízení ( \<VHD> ) \Read bajty/s |
| Propustnost zápisu virtuálních počítačů |\Hyper-V virtuálního úložného zařízení ( \<VHD> ) \Write bajty/s |

## <a name="next-steps"></a>Další kroky

[Nastavení replikace](hyper-v-vmm-disaster-recovery.md)
