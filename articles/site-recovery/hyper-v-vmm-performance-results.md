---
title: Výsledky testu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality s Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje informace o testování výkonu pro replikaci virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: e15f435a3f32b8908b5b93bccc6c57710ab589bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378894"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Výsledky testu pro replikaci technologie Hyper-V do sekundární lokality

Tento článek obsahuje výsledky testování při replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundárního datacentra výkonu.

## <a name="test-goals"></a>Test cíle

Cílem testování se zjistit, jak se Site Recovery provede během stabilního stavu replikace.

- Po dokončení počáteční replikace virtuálních počítačů a synchronizují rozdílové změny dojde k replikaci stabilního stavu.
- Je důležité k měření výkonu pomocí stabilního stavu, protože se jedná o stavu, ve které nejvíc virtuálních počítačů zachovaná, dokud neočekávaných výpadků.
- Testovací nasazení se skládal z dvěma místními lokalitami, s server VMM v každé lokalitě. Toto testovací nasazení je typické nasazení office head office nebo větev, s sídlo funguje jako primární lokality a na pobočce jako sekundární nebo obnovení lokality.

## <a name="what-we-did"></a>Co jsme se

Zde je seznam toho, co jsme v testu předat:

1. Virtuální počítače vytvořené pomocí šablony nástroje VMM.
2. Spustit virtuální počítače a zachycení základní metriky výkonu vyšší než 12 hodin.
3. Cloudy vytvořené na primárním a obnovení serverů VMM.
4. Nakonfigurované replikace ve službě Site Recovery, včetně mapování mezi zdrojem a obnovení cloudy.
5. Povolit ochranu pro virtuální počítače, je povoleno dokončení počáteční replikace.
6. Čekali pár hodin pro ustálení systému.
7. Zaznamená metriky výkonu vyšší než 12 hodin, kde všech virtuálních počítačů zůstal v očekávaném stavu replikace těchto 12 hodin.
8. Měří rozdíl mezi metriky výkonu směrného plánu a metriky výkonu replikace.


## <a name="primary-server-performance"></a>Primární server výkonu

* Replika technologie Hyper-V (používá se službou Site Recovery) asynchronně sleduje změny do souboru protokolu s úložištěm minimální nároky na primárním serveru.
* Replika technologie Hyper-V využívá samoobslužné zachována mezipaměti režie minimalizovat IOPS pro sledování. Ukládají se zapíše na formát VHDX v paměti a uvolní je do souboru protokolu před časem odeslaný protokol na lokalitě pro obnovení. Vyprázdnění disk se také stane, když zápisů dosáhl předem určený limit.
* Následující graf ukazuje režii IOPS stabilního stavu pro replikaci. Vidíme, že IOPS režie z důvodu replikace je přibližně 5 %, což je značně nízká.

  ![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika technologie Hyper-V používá paměť na primárním serveru za účelem optimalizace výkonu disku. Jak je znázorněno v následujícím grafu, je okrajového paměti nároky na všech serverech v primární clusteru. Paměť režie vidět je procento paměti používané při replikaci, ve srovnání s celkovou velikost paměti nainstalované na serveru Hyper-V.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika technologie Hyper-V má minimální nároky na procesor. Jak je znázorněno v grafu, režijní náklady replikace je v rozsahu 2 až 3 %.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Sekundární server výkonu

Replika technologie Hyper-V používá malé množství paměti na serveru pro obnovení k optimalizaci počet operací úložiště. Graf obsahuje souhrn využití paměti na serveru pro obnovení. Paměť režie vidět je procento paměti používané při replikaci, ve srovnání s celkovou velikost paměti nainstalované na serveru Hyper-V.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744916.png)

Objem vstupně-výstupních operací na lokalitě pro obnovení je funkce počet operací zápisu na primární lokalitě. Pojďme podívejte se na celkový počet vstupně-výstupních operací na lokalitě pro obnovení ve srovnání s celkový počet vstupně-výstupních operací a operací v primární lokalitě zápisu. V grafech ukáže, že celkový počet IOPS v lokalitě pro obnovení

* Přibližně 1, 5krát zápis IOPS na primární.
* Přibližně 37 % celkový počet IOPS v primární lokalitě.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Vliv na využití sítě

V průměru 275 Mb za sekundu šířky pásma sítě byl použit mezi primárními a obnovovacími uzly (s komprese zapnuta), proti existující pásma 5 Gb za sekundu.

![Využití sítě výsledky](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Vliv na výkon virtuálního počítače

Důležitý aspekt spočívá v dopad replikace na produkční úlohy běžící na virtuálních počítačích. Pokud primární lokalita je zajištěna dostatečná pro replikaci, by neměl být žádný vliv na jiné úlohy. Sledování mechanismus lightweight replika technologie Hyper-V zajistí, že úlohy běžící ve virtuálních počítačích nejsou dopad během stabilního stavu replikace. To je znázorněno v následující grafy.

Tento graf znázorňuje, že IOPS provést virtuální počítače spuštěné různé úlohy, před a po povolení replikace. Můžete sledovat, že není žádný rozdíl mezi nimi.

![Výsledky vliv repliky](./media/hyper-v-vmm-performance-results/IC744920.png)

Následující graf ukazuje propustnost virtuálních počítačů spuštěných různé úlohy, před a po povolení replikace. Můžete si všimnout, že replikace má významný dopad.

![Výsledky repliky efekty](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Závěr

Výsledky jasnému určení, že Site Recovery, kombinaci s replikou technologie Hyper-V, škáluje s minimální režie pro cluster s podporou velké. Site Recovery poskytuje jednoduché nasazení, replikace, Správa a monitorování. Replika technologie Hyper-V poskytuje potřebnou infrastrukturu pro škálování úspěšná replikace. 

## <a name="test-environment-details"></a>Podrobnosti testovacího prostředí

### <a name="primary-site"></a>Primární lokality

* Primární lokalita má clusteru, který obsahuje pět serverů Hyper-V běžící 470 virtuální počítače.
* Virtuální počítače spustit různé úlohy a všechny mají povolenou ochranu Site Recovery.
* ISCSI SAN poskytuje úložiště pro uzel clusteru. Model – Hitachi HUS130.
* Každý server clusteru má čtyři síťové karty (síťové adaptéry) jeden GB/s každou.
* Dvě síťové karty jsou připojené k privátní síti iSCSI a dva jsou připojené k externí podnikové síti. Jeden z externí sítě je vyhrazený pro komunikaci pouze mezi clustery.

![Požadavky na hardware](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB HOST25 má 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server databáze 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundární lokality

* Sekundární lokalitu má clusteru s podporou převzetí služeb při selhání půl uzlu.
* ISCSI SAN poskytuje úložiště pro uzel clusteru. Model – Hitachi HUS130.

![Specifikace primární hardwaru](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | Paměť RAM | Model | Procesor | Počet procesorů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + role Hyper-V |
| VMM Server |2 | | |2 |1 Gb/s |Windows Server databáze 2012 R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Úlohy serveru

* Pro účely testování můžeme zachyceny úlohy běžně používaný v podnikové scénáře zákazníka.
* Používáme [IOMeter](http://www.iometer.org) s vlastnosti úlohy souhrnu pro simulaci v tabulce.
* Všechny profily IOMeter se nastavují pro zápis náhodných bajtů k simulaci nejhorší zápisu vzory pro úlohy.

| Úloha | Vstupně-výstupních operací velikost (KB) | % Přístup | % Pro čtení | Počet nezpracovaných vstupně-výstupních operací | Vzor vstupně-výstupních operací |
| --- | --- | --- | --- | --- | --- |
| Souborový server |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Náhodné 100 % |
| SQL Server (svazku 1) SQL Server (svazku 2) |864 |100%100% |70%0% |88 |100 % random100 % sekvenčních |
| Výměna |32 |100% |67% |8 |náhodné 100 % |
| Pracovní stanice/VDI |464 |66%34% |70%95% |11 |Obě náhodných 100 % |
| Webové souborového serveru |4864 |33%34%33% |95%95%95% |888 |Náhodné 75 % |

### <a name="vm-configuration"></a>Konfigurace virtuálního počítače

* 470 virtuální počítače v primární clusteru.
* Všechny virtuální počítače s diskem VHDX.
* Virtuální počítače spuštěné úlohy souhrnu v tabulce. Všechny byly vytvořeny pomocí šablony nástroje VMM.

| Úloha | # Virtuální počítače | Minimální velikost paměti RAM (GB) | Maximální paměť RAM (GB) | Velikost logický disk (GB) na virtuální počítač | Maximální IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Souborový server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webový server |149 |.5 |1 |80 |6 |
| CELKEM |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Nastavení obnovení lokality

* Site Recovery byl nakonfigurován na místním nasazením a místní ochrany
* VMM server obsahuje čtyři cloudy nakonfigurovaná, obsahující servery clusteru Hyper-V a jejich virtuální počítače.

| Primární cloud VMM | Chráněné virtuální počítače | Četnost replikace | Další body obnovení |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 minut |Žádné |
| PrimaryCloudRpo30s |47 |30 sekund. |Žádné |
| PrimaryCloudRpo30sArp1 |47 |30 sekund. |1 |
| PrimaryCloudRpo5m |235 |5 minut |Žádné |

### <a name="performance-metrics"></a>Metriky výkonu

Tabulka shrnuje metriky výkonu a čítače, které byly měřená v nasazení.

| Metrika | Čítač |
| --- | --- |
| Procesor |\Processor(_Total)\% Processor Time |
| Dostupná paměť |\Memory\Available MB |
| IOPS |Přenosy \Disk \PhysicalDisk (_celkem) za sekundu |
| Virtuální počítač čtení operací za sekundu (IOPS) |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Read operace/s |
| Operace zápisu (IOPS) virtuálních počítačů/s |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Write operace/S |
| Virtuální počítač číst propustnost |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Read bajty/s |
| Propustnost zápisu virtuálních počítačů |Zařízení virtuálního úložiště \Hyper-V (<VHD>) \Write bajty/s |

## <a name="next-steps"></a>Další postup

[Nastavení replikace](hyper-v-vmm-disaster-recovery.md)
