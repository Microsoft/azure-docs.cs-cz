---
title: Testování replikace virtuálních počítačů Hyper-V do sekundární lokality pomocí VMM pomocí Azure Site Recovery
description: Tento článek obsahuje informace o testování výkonu pro replikaci virtuálních počítačích Hyper-V v cloudech v cloudech VMM do sekundární lokality pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73663178"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Výsledky testů pro replikaci hyper-V do sekundární lokality


Tento článek obsahuje výsledky testování výkonu při replikaci virtuálních počítačů Hyper-V v cloudech Nástroje pro virtuální počítače (VMM) do sekundárního datového centra.

## <a name="test-goals"></a>Testovací cíle

Cílem testování bylo prozkoumat, jak funguje obnovení lokality během replikace v ustáleném stavu.

- Replikace v ustáleném stavu nastane, když virtuální ms dokončily počáteční replikaci a synchronizují rozdílové změny.
- Je důležité měřit výkon pomocí ustáleného stavu, protože je to stav, ve kterém většina virtuálních disponiálů zůstávají, pokud nedojde k neočekávaným výpadkům.
- Testovací nasazení se skládalo ze dvou místních lokalit se serverem VMM v každé lokalitě. Toto testovací nasazení je typické pro nasazení ústředí nebo pobočky, přičemž hlavní pobočka funguje jako primární lokalita a pobočka jako sekundární nebo obnovovací lokalita.

## <a name="what-we-did"></a>Co jsme udělali

Zde je to, co jsme udělali v testovacím průchodu:

1. Vytvořené virtuální počítače pomocí šablon VMM.
2. Spuštění virtuálních společností a zachycené metriky výkonu směrného plánu za 12 hodin.
3. Vytvořené cloudy na primárních serverech VMM a pro obnovení.
4. Nakonfigurovaná replikace v lokalitě Obnovení, včetně mapování mezi zdrojovým a obnovovacím cloudem.
5. Povolená ochrana virtuálních aplikací a umožněná k dokončení počáteční replikace.
6. Čekal jsem pár hodin na stabilizaci systému.
7. Zachycené metriky výkonu přes 12 hodin, kde všechny virtuální chody zůstaly ve stavu očekávané replikace pro tyto 12 hodin.
8. Změřila rozdíl mezi metrikami výkonu podle směrného plánu a metrikami výkonu replikace.


## <a name="primary-server-performance"></a>Primární výkon serveru

* Replika hyper-V (používaná site recovery) asynchronně sleduje změny v souboru protokolu s minimální režií úložiště na primárním serveru.
* Replika hyper-V využívá samoobslužnou mezipaměť k minimalizaci režie videa VOPS pro sledování. Ukládá zápisy do vhdx v paměti a vyprázdní je do souboru protokolu před čas, kdy je protokol odeslán do webu pro obnovení. Vyprázdnění disku také dojde, pokud zápisy narazí na předem stanovený limit.
* Následující graf ukazuje režii vipos ustáleného stavu pro replikaci. Vidíme, že režie IOPS z důvodu replikace je kolem 5%, což je poměrně nízká.

  ![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744913.png)

Replika technologie Hyper-V používá paměť na primárním serveru k optimalizaci výkonu disku. Jak je znázorněno v následujícím grafu, režie paměti na všech serverech v primárním clusteru je marginální. Zobrazená režie paměti je procento paměti používané replikací ve srovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744914.png)

Replika hyper-V má minimální nároky na procesor. Jak je znázorněno v grafu, režie replikace je v rozsahu 2-3%.

![Primární výsledky](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Výkon sekundárního serveru

Replika technologie Hyper-V používá malé množství paměti na serveru pro obnovení k optimalizaci počtu operací úložiště. Graf shrnuje využití paměti na serveru pro obnovení. Zobrazená režie paměti je procento paměti používané replikací ve srovnání s celkovou nainstalovanou pamětí na serveru Hyper-V.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744916.png)

Počet vstupně-vstupně-v.i. operací v lokalitě pro obnovení je funkce počtu operací zápisu v primární lokalitě. Podívejme se na celkové vstupně-va operace v lokalitě pro obnovení ve srovnání s celkovými vstupně-va operace a zápis u operací v primární lokalitě. Grafy ukazují, že celkový vipos na místě obnovení je

* Přibližně 1,5násobek zápisu IOPS na primární.
* Přibližně 37 % celkových iOPS v primární lokalitě.

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744917.png)

![Sekundární výsledky](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Vliv na využití sítě

Mezi primárními uzly a uzly pro obnovení (s povolenou kompresí) bylo použito průměrně 275 MB za sekundu oproti existující šířce pásma 5 GB za sekundu.

![Výsledky využití sítě](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Vliv na výkon virtuálních počítačů

Důležitým aspektem je dopad replikace na produkční úlohy spuštěné na virtuálních počítačích. Pokud primární lokalita je dostatečně zřízena pro replikaci, by nemělmít žádný dopad na úlohy. Zjednodušený mechanismus sledování repliky hyperv zajišťuje, že úlohy spuštěné ve virtuálních počítačích nebudou během replikace v ustáleném stavu ovlivněny. To je znázorněno v následujících grafech.

Tento graf ukazuje iOPS prováděné virtuálními počítači s různými úlohami před a po povolení replikace. Můžete pozorovat, že mezi nimi není žádný rozdíl.

![Výsledky efektu repliky](./media/hyper-v-vmm-performance-results/IC744920.png)

Následující graf ukazuje propustnost virtuálních počítačů s různými úlohami před a po povolení replikace. Můžete pozorovat, že replikace nemá žádný významný dopad.

![Výsledky repliky efekty](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Závěr

Výsledky jasně ukazují, že site recovery, spolu s Replika Hyper-V, škálování dobře s minimální režii pro velký cluster. Site Recovery poskytuje jednoduché nasazení, replikaci, správu a monitorování. Replika hyper-V poskytuje potřebnou infrastrukturu pro úspěšné škálování replikace. 

## <a name="test-environment-details"></a>Podrobnosti testovacího prostředí

### <a name="primary-site"></a>Primární lokalita

* Primární lokalita má cluster obsahující pět serverů Hyper-V se systémem 470 virtuálních počítačů.
* Virtuální servery spouštějí různé úlohy a všechny mají povolenou ochranu site recovery.
* Úložiště pro uzel clusteru poskytuje systém iSCSI SAN. Model – Hitachi HUS130.
* Každý clusterový server má čtyři síťové karty (NIC) po jednom Gb/s.
* Dvě ze síťových karet jsou připojeny k privátní síti iSCSI a dvě jsou připojeny k externí podnikové síti. Jedna z externích sítí je vyhrazena pouze pro komunikaci clusteru.

![Primární požadavky na hardware](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | Paměť RAM | Model | Procesor | Počet zpracovatelů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOSTITEL11<br />ESTLAB-HOSTITEL12<br />ESTLAB-HOSTITEL13<br />ESTLAB-HOSTITEL14<br />ESTLAB-HOSTITEL25 |128<br />ESTLAB-HOST25 má 256 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Role Windows Server Datacenter 2012 R2 (x64) + Hyper-V |
| Server VMM |2 | | |2 |1 Gb/s |Databáze Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sekundární lokalita

* Sekundární lokalita má cluster s podporou převzetí služeb při selhání se šesti uzny.
* Úložiště pro uzel clusteru poskytuje systém iSCSI SAN. Model – Hitachi HUS130.

![Primární specifikace hardwaru](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | Paměť RAM | Model | Procesor | Počet zpracovatelů | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Servery Hyper-V v clusteru: <br />ESTLAB-HOSTITEL07<br />ESTLAB-HOSTITEL08<br />ESTLAB-HOSTITEL09<br />ESTLAB-HOSTITEL10 |96 |Dell ™ PowerEdge ™ R720 |Procesor Intel(R) Xeon(R) E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Role Windows Server Datacenter 2012 R2 (x64) + Hyper-V |
| ESTLAB-HOSTITEL17 |128 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 | |Role Windows Server Datacenter 2012 R2 (x64) + Hyper-V |
| ESTLAB-HOSTITEL24 |256 |Dell ™ PowerEdge ™ R820 |Procesor Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |2 | |Role Windows Server Datacenter 2012 R2 (x64) + Hyper-V |
| Server VMM |2 | | |2 |1 Gb/s |Databáze Windows Server 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Úlohy serveru

* Pro testovací účely jsme vybrali úlohy běžně používané ve scénářích podnikových zákazníků.
* [IOMeter](http://www.iometer.org) používáme s charakteristikou pracovního vytížení shrnutou v tabulce pro simulaci.
* Všechny profily IOMeter jsou nastaveny na zápis náhodných bajtů simulovat nejhorší scénáře zápisu vzory pro úlohy.

| Úloha | Velikost vstupně-va (KB) | % přístupu | %Číst | Vynikající vstupně-va | Vstupně-vně vzor |
| --- | --- | --- | --- | --- | --- |
| Souborový server |4<br />8<br />16<br />32<br />64 |60 %<br />20 %<br />5 %<br />5 %<br />10 % |80 %<br />80 %<br />80 %<br />80 %<br />80 % |8<br />8<br />8<br />8<br />8 |Vše 100% náhodné |
| SQL Server (svazek 1)<br />SQL Server (svazek 2) |8<br />64 |100 %<br />100 % |70 %<br />0% |8<br />8 |100% náhodné<br />100% sekvenční |
| Výměna |32 |100 % |67 % |8 |100% náhodné |
| Pracovní stanice/VDI |4<br />64 |66%<br />34% |70 %<br />95% |1<br />1 |Oba 100% náhodné |
| Webový souborový server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Vše 75% náhodné |

### <a name="vm-configuration"></a>Konfigurace virtuálního počítače

* 470 virtuálních počítačích v primárním clusteru.
* Všechny virtuální počítače s diskem VHDX.
* Virtuální virtuální aplikace s úlohami shrnutými v tabulce. Všechny byly vytvořeny pomocí šablon VMM.

| Úloha | # Virtuální měna | Minimální ram (GB) | Maximální ram (GB) | Velikost logického disku (GB) na virtuální virtuální počítače | Maximální viopy |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Souborový server |50 |1 |2 |552 |22 |
| Vdi |149 |.5 |1 |80 |6 |
| Webový server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Nastavení obnovení webu

* Obnovení webu bylo nakonfigurováno pro místní ochranu místně.
* Server VMM má nakonfigurované čtyři cloudy, které obsahují clusterové servery Hyper-V a jejich virtuální počítače.

| Primární cloud VMM | Chráněné virtuální počítače | Frekvence replikace | Další body obnovení |
| --- | --- | --- | --- |
| Primární CloudRpo15m |142 |15 minut |Žádný |
| Primární CloudRpo30s |47 |30 sekund |Žádný |
| Primární CloudRpo30sArp1 |47 |30 sekund |1 |
| Primární CloudRpo5m |235 |5 minut |Žádný |

### <a name="performance-metrics"></a>Metriky výkonu

Tabulka shrnuje metriky výkonu a čítače, které byly měřeny v nasazení.

| Metrika | Čítač |
| --- | --- |
| Procesor |\Processor(_Total)\% Processor Time |
| Dostupná paměť |\Paměť\Dostupné mbajty |
| IOPS |\PhysicalDisk(_Total)\Přenosy disků/s |
| Operace čtení virtuálního zařízení (IOPS)/s |\Hyper-V Virtuální paměťové zařízení(>)\Operace\<čtení/s |
| Operace zápisu virtuálního zařízení (IOPS)/s |\Hyper-V Virtuální paměťové zařízení(>\<v pevném disku)\Operace zápisu/S |
| Propustnost pro čtení virtuálního min. |\Hyper-V Virtuální paměťové zařízení(>\<v pevném rozlišení)\Čtení bajtů/s |
| Propustnost zápisu virtuálního virtuálního montovna |\Hyper-V Virtuální paměťové zařízení(VHD\<>)\Zápis bajtů/s |

## <a name="next-steps"></a>Další kroky

[Nastavení replikace](hyper-v-vmm-disaster-recovery.md)
