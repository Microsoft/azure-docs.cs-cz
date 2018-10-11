---
title: Výsledky testu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek obsahuje informace o testování výkonu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: c97247a14915ea3b8396f7179b4f8ca13bf92c8f
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079391"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Výsledky testu pro replikaci Hyper-V do sekundární lokality

Tento článek obsahuje výsledky výkonu testování při replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundárního datacentra.

## <a name="test-goals"></a>Testování cílů

Cílem testování bylo zkoumat, jak Site Recovery během stabilního stavu replikace.

- Steady state replication vyvolá se v případě virtuálních počítačů dokončení počáteční replikace a rozdílové změny synchronizují.
- Je důležité k měření výkonu pomocí stabilního stavu, protože se jedná o stavu, ve které většině virtuálních počítačů zůstanou, dokud neočekávaných výpadků.
- Testovací nasazení se skládal z dvě místní lokality, přičemž server VMM v každé lokalitě. Tohoto testovacího nasazení je typický pro nasazení office hlavní kanceláře či firemní, ústředí, kdy fungoval jako primární lokality a na pobočce jako sekundární nebo obnovení lokality.

## <a name="what-we-did"></a>Fungují

Zde je, co jsme v testu předat:

1. Virtuální počítače vytvořené pomocí šablon v nástroji VMM.
2. Spuštění virtuálních počítačů a zachytit základní metriky výkonu vyšší než 12 hodin.
3. Cloudy vytvořené na primární a obnovení serverů VMM.
4. Nakonfigurované replikace ve službě Site Recovery, včetně mapování mezi zdrojem a obnovení cloudy.
5. Povolili ochranu pro virtuální počítače a je povolený pro dokončení prvotní replikace.
6. Čekalo se na několik hodin pro stabilizaci systému.
7. Zachycené metriky výkonu vyšší než 12 hodin, ve kterém se všechny virtuální počítače zůstane v očekávaném stavu replikace těchto 12 hodin.
8. Měří rozdílů mezi základní metriky výkonu a metriky výkonu replikace.


## <a name="primary-server-performance"></a>Primární server výkonu

* Replika technologie Hyper-V (používané pro Site Recovery) asynchronně sleduje změny do souboru protokolu s úložištěm minimální nároky na primárním serveru.
* Replika technologie Hyper-V využívá svým udržována mezipaměti režii minimalizovat vstupně-výstupních operací pro sledování. Ukládají se zapíše do VHDX v paměti a uvolní je do souboru protokolu před časem, který je odeslán protokolu do lokality pro obnovení. Disk vyprázdnění se také stane, když zápisy dosažení limitu předem.
* Graf níže zobrazuje stálé zatížení vstupně-výstupních operací replikace. Můžeme vidět, že vstupně-výstupních operací režie z důvodu replikace je přibližně 5 %, což je značně nízká.

  ![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika technologie Hyper-V používá k optimalizaci výkonu disku paměti na primárním serveru. Jak je znázorněno v následujícím grafu, paměť nároky na všech serverech ve primárního clusteru je minimálním ziskem. Režie zobrazí paměť je procento paměti používané při replikaci ve srovnání s celkové paměti nainstalovaných na serveru Hyper-V.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika technologie Hyper-V má minimální nároky na procesor. Jak je znázorněné v grafu, režijní náklady replikace je v rozsahu 2 až 3 %.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundární server výkonu

Replika technologie Hyper-V používá k optimalizaci počet operací úložiště malé množství paměti na serveru pro obnovení. Graf obsahuje souhrn využití paměti na serveru pro obnovení. Režie zobrazí paměť je procento paměti používané při replikaci ve srovnání s celkové paměti nainstalovaných na serveru Hyper-V.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744916.png)

Množství vstupně-výstupních operací v lokalitě pro obnovení je funkce počet operací zápisu na primární lokalitě. Pojďme podívat na celkový počet vstupně-výstupních operací v lokalitě pro obnovení ve srovnání s celkový počet vstupně-výstupních operací a operací v primární lokalitě zápisu. Zobrazit grafy, celkový počet vstupně-výstupních operací v lokalitě pro obnovení je

* Přibližně 1, 5krát zápis IOPS na primární.
* Přibližně 37 % celosvětové celkový počet IOPS na primární lokalitě.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Vliv na využití sítě

Průměrně 275 Mb za sekundu šířky pásma sítě byla použita mezi primárními a obnovovacími uzly (pomocí komprese zapnuta), proti existující pásma 5 GB za sekundu.

![Výsledky využití sítě](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Dopad na výkon virtuálního počítače

Důležitým aspektem, který je dopad existence replikace na produkční úlohy běžící na virtuálních počítačích. Pokud primární lokalita je odpovídajícím způsobem pro replikaci zřídit, by neměly být žádný vliv na jiné úlohy. Replika technologie Hyper-V lightweight mechanismus sledování zajišťuje, že se to týká úlohy běžící na virtuálních počítačích během pravidelné replikace. To je znázorněno v následující grafy.

Tento graf ukazuje, že se že vstupně-výstupních operací provést virtuální počítače spuštěné různé úlohy, před a po povolení replikace. Můžete si všimnout, že není žádný rozdíl mezi nimi.

![Výsledky efekt repliky](./media/hyper-v-vmm-performance-results/IC744920.png)

Následující graf ukazuje výkon virtuálních počítačů spuštěných různými úlohami, před a po povolení replikace. Můžete si všimnout, že replikace nemá významný dopad.

![Výsledky repliky efekty](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Závěr

Ve výsledcích zobrazí jasně, že s minimální režie pro velký cluster škáluje Site Recovery s replikou technologie Hyper-V s velkou provázaností. Site Recovery poskytuje jednoduché nasazení, replikace, Správa a monitorování. Replika technologie Hyper-V poskytuje infrastrukturu nezbytné pro úspěšnou replikaci škálování. 

## <a name="test-environment-details"></a>Podrobnosti testovacího prostředí

### <a name="primary-site"></a>Primární lokalita

* Cluster s pěti servery Hyper-V, spouštění 470 virtuálních počítačů má primární lokalita.
* Virtuální počítače spustit různé úlohy, a všechny mají povolenou ochranu Site Recovery.
* Sítě SAN iSCSI poskytuje úložiště pro uzel clusteru. Model – Hitachi HUS130.
* Každý server clusteru má čtyři síťové karty (síťové adaptéry) jeden Gbps.
* Jsou dvě síťové karty připojené k privátní síť iSCSI a dva jsou připojené k externí podnikové síti. Mezi externími sítěmi je vyhrazený pro pouze komunikace clusteru.

![Požadavky na hardware](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 má 256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 |Můžu x 4 GB/s |Windows Server Datacenter 2012 R2 (x64) + roli Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server databáze 2012 R2 (x 64) a VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundární lokality

* Sekundární lokalita bude mít cluster převzetí služeb při selhání uzlu současně všech šest.
* Sítě SAN iSCSI poskytuje úložiště pro uzel clusteru. Model – Hitachi HUS130.

![Primární hardwarové specifikace](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB HOST10 |96 |Dell™ PowerEdge™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30 GHz |2 |Můžu x 4 GB/s |Windows Server Datacenter 2012 R2 (x64) + roli Hyper-V |
| ESTLAB HOST17 |128 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + roli Hyper-V |
| ESTLAB HOST24 |256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + roli Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server databáze 2012 R2 (x 64) a VMM 2012 R2 |

### <a name="server-workloads"></a>Jiné úlohy serveru

* Pro účely testování výběru úloh používaných v podnikových scénářích zákazníka.
* Používáme [IOMeter](http://www.iometer.org) s charakteristika úlohy uvedené v tabulce pro simulaci.
* Všechny profily IOMeter jsou nastaveny na zápis náhodných bajtů pro simulaci nejhorším zápisu vzory pro úlohy.

| Úloha | Vstupně-výstupních operací velikost (KB) | % Přístupu | % Čtení | Nezpracovaných vstupně-výstupních operací | Vzor vstupně-výstupních operací |
| --- | --- | --- | --- | --- | --- |
| Souborový server |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Náhodné 100 % |
| SQL Server (svazku 1) SQL serveru (svazku. 2) |864 |100%100% |70%0% |88 |100 % random100 % sekvenční |
| Výměna |32 |100% |67% |8 |100 % náhodné |
| Pracovní stanice/VDI |464 |66%34% |70%95% |11 |Obě náhodné 100 % |
| Web souborového serveru |4864 |33%34%33% |95%95%95% |888 |Náhodné 75 % |

### <a name="vm-configuration"></a>Konfigurace virtuálního počítače

* 470 virtuální počítače v primární clusteru.
* Všechny virtuální počítače s diskem VHDX.
* Virtuální počítače spuštěné úlohy, které jsou uvedené v tabulce. Všechny vytvořené pomocí šablon v nástroji VMM.

| Úloha | # Virtuálních počítačů | Minimální velikost paměti RAM (GB) | Maximální paměť RAM (GB) | Logický disk size (GB) na virtuální počítač | Maximální IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Souborový server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webový server |149 |.5 |1 |80 |6 |
| CELKEM |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Nastavení Site Recovery

* Site Recovery byl nakonfigurován pro ochranu místních místní
* VMM server obsahuje čtyři cloudy nakonfigurovaná, obsahující servery clusteru Hyper-V a jejich virtuálních počítačů.

| Primární cloud VMM | Chráněné virtuální počítače | Četnost replikace | Další body obnovení |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut. |Žádný |
| PrimaryCloudRpo30s |47 |30 sekund |Žádný |
| PrimaryCloudRpo30sArp1 |47 |30 sekund |1 |
| PrimaryCloudRpo5m |235 |5 minut |Žádný |

### <a name="performance-metrics"></a>Metriky výkonu

Tabulka shrnuje metriky výkonu a čítače, které se měří v nasazení.

| Metrika | Čítač |
| --- | --- |
| Procesor |\Processor(_Total)\% Processor Time |
| Dostupná paměť |\Memory\Available paměť v megabajtech |
| IOPS |Přenosy \Disk \PhysicalDisk (využití _celkem) za sekundu |
| Operací za sekundu (IOPS) čtení virtuálního počítače |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Read operace/s |
| Operace zápisu (IOPS) virtuálního počítače za sekundu |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Write operací za sekundu |
| Propustnost čtení virtuálního počítače |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Read bajty/s |
| Propustnost zápisu virtuálního počítače |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Write bajty/s |

## <a name="next-steps"></a>Další postup

[Nastavení replikace](hyper-v-vmm-disaster-recovery.md)
