---
title: Optimalizace výkonu u virtuálních počítačů Azure Lsv2-Series
description: Naučte se, jak optimalizovat výkon pro vaše řešení na virtuálních počítačích Lsv2-Series pomocí příkladu Windows.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 8d38e3f58de0fc4cc1e963c18b002dc1e16852ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556005"
---
# <a name="optimize-performance-on-the-lsv2-series-windows-virtual-machines"></a>Optimalizace výkonu na virtuálních počítačích s Windows Lsv2-Series

Virtuální počítače řady Lsv2-series podporují nejrůznější úlohy, které vyžadují vysokou vstupně-výstupní operace a propustnost v místním úložišti napříč širokou škálou aplikací a oborů.  Lsv2-Series je ideální pro velké objemy dat, SQL, NoSQL databáze, datové sklady a velké transakční databáze, včetně Cassandra, MongoDB, Cloudera a Redis.

Návrh Lsv2-Series Virtual Machines (virtuální počítače) maximalizuje procesor AMD EPYC™ 7551, který poskytuje nejlepší výkon mezi procesorem, pamětí, NVMe zařízeními a virtuálními počítači. Kromě maximalizace hardwarového výkonu jsou virtuální počítače Lsv2-Series navržené tak, aby lépe fungovaly s hardwarem a softwarem pro lepší výkon operačního systému Windows a Linux.

Výsledkem optimalizace softwaru a hardwaru je optimalizovaná verze [Windows serveru 2019 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)vydaná do začátku od 2018 do Azure Marketplace, která podporuje maximální výkon na zařízeních NVMe ve virtuálních počítačích Lsv2-Series.

Tento článek poskytuje tipy a návrhy, které zajistí, aby vaše úlohy a aplikace dosáhly maximálního výkonu, který je pro virtuální počítače určený. Informace na této stránce budou průběžně aktualizovány, protože do Azure Marketplace jsou přidány další optimalizované obrázky Lsv2.

## <a name="amd-eypc-chipset-architecture"></a>Architektura čipové sady AMD EYPC™

Virtuální počítače řady Lsv2-Series používají procesory AMD EYPC™ serveru založené na mikroarchitektuře Zen. Technologie AMD vyvinula nekonečno Fabric (pokud) pro EYPC™ jako škálovatelné propojení pro svůj model NUMA, které by bylo možné použít pro komunikaci na Die, na balíčku a komunikaci s více balíčky. V porovnání s QPI (rychlá cesta propojení) a UPI (Ultra-Path Interconnect), která se používá pro moderní monolitické procesory Intel, může architektura pro procesory AMD řady NUMA přinést jak výhody výkonu, tak i problémy. Skutečný dopad omezení šířky pásma paměti a latence se může lišit v závislosti na typu spuštěných úloh.

## <a name="tips-for-maximizing-performance"></a>Tipy pro maximalizaci výkonu

* Hardware, který využívá virtuální počítače řady Lsv2-Series, používá zařízení NVMe s dvojicemi páry vstupně-výstupních front (QP) s. Každá fronta NVMe zařízení v/v je ve skutečnosti páry: fronta pro odesílání a fronta pro doplňování. Ovladač NVMe je nastavený tak, aby optimalizoval využití těchto osmi vstupně-výstupních QPsů tím, že distribuuje vstupně-výstupní operace v plánu kruhového dotazování. Chcete-li získat maximální výkon, spusťte osm úloh na zařízení, které chcete porovnat.

* Vyhněte se kombinování příkazů pro správu NVMe (například dotazování INTELIGENTNÍch informací NVMe atd.) s příkazy NVMe vstupu/výstupu během aktivních úloh. Zařízení NVMe Lsv2 jsou založená na technologii Hyper-V NVMe Direct, která přepne do pomalého režimu při každém čekání na příkazy správce NVMe. Lsv2 uživatelé můžou v případě, že se to stane, zvýšit výkon v/v výkonu NVMe.

* Lsv2 uživatelé by neměli spoléhat na informace NUMA zařízení (všechny 0) hlášené v rámci virtuálního počítače, aby se pro své aplikace rozhodly spřažení NUMA. Doporučený způsob, jak zajistit lepší výkon, je rozložit úlohy napříč procesory, pokud je to možné. 

* Maximální podporovaná hloubka fronty na dvojici vstupně-výstupních front pro zařízení Lsv2 VM NVMe je 1024 (vs. Amazon i3 hloubka fronty 32). Lsv2 uživatelé by měli omezit své (syntetické) úlohy srovnávacích testů na hloubku fronty 1024 nebo nižší, aby nedocházelo k vystavování úplných podmínek zařazování do fronty, což může snížit výkon.

## <a name="utilizing-local-nvme-storage"></a>Využití místního úložiště NVMe

Místní úložiště na disku s 1,92 TB NVMe na všech virtuálních počítačích Lsv2 je dočasné. Během úspěšného standardního restartování virtuálního počítače se zachovají data na místním disku NVMe. Pokud se virtuální počítač znovu nasadí, zruší jeho přidělení nebo odstraněné data NVMe. Data nebudou zachovaná, pokud jiný problém způsobí, že virtuální počítač nebo hardware, na kterém je spuštěný, nebude v pořádku. V takovém případě budou všechna data na původním hostiteli bezpečně smazána.

Existují taky případy, kdy je potřeba virtuální počítač přesunout na jiný hostitelský počítač, například během plánované operace údržby. Plánované operace údržby a některé chyby hardwaru je možné očekávat [Scheduled Events](scheduled-events.md). Scheduled Events by se měla použít pro průběžnou aktualizaci na jakékoli předpovězené operace údržby a obnovení.

V případě, že plánovaná událost údržby vyžaduje, aby se virtuální počítač znovu vytvořil na novém hostiteli s prázdnými místními disky, bude nutné znovu synchronizovat data (znovu s veškerými daty na starém hostiteli, která se bezpečně maže). K tomu dochází, protože virtuální počítače řady Lsv2-Series v současné době nepodporují migraci za provozu na místním disku NVMe.

Existují dva režimy pro plánovanou údržbu.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardní údržba se správou zákazníka na síti

- Virtuální počítač se přesune na aktualizovaného hostitele během 30denní okna.
- Data místního úložiště Lsv2 se mohla ztratit, takže se doporučuje zálohovat data před událostí.

### <a name="automatic-maintenance"></a>Automatická údržba

- Vyvolá se v případě, že zákazník neprovede údržbu řízenou zákazníkem nebo v případě nouzových postupů, jako je například událost nulového zabezpečení.
- Cílem je zachovat zákaznická data, ale dojde k malému riziku při zablokování nebo restartování virtuálního počítače.
- Data místního úložiště Lsv2 se mohla ztratit, takže se doporučuje zálohovat data před událostí.

U všech nadcházejících událostí služby použijte řízený proces údržby a vyberte čas, který je pro aktualizaci pro vás nejpohodlnější. Před touto událostí můžete data zálohovat v Premium Storage. Po dokončení události údržby můžete vrátit data do aktualizovaných virtuálních počítačů s Lsv2 v místním úložišti NVMe.

Mezi scénáře, které udržují data na místních NVMe discích, patří:

- Virtuální počítač je spuštěný a v pořádku.
- Virtuální počítač se restartuje na místě (vámi nebo v Azure).
- Virtuální počítač je pozastavený (zastavený bez přidělení).
- Většina plánovaných operací údržby údržby

Mezi scénáře, které bezpečně maže data k ochraně zákazníka, patří:

- Virtuální počítač se znovu nasadí, zastaví (zruší přidělení) nebo se odstraní (vámi).
- Virtuální počítač se nemění v pořádku a musí kvůli problému s hardwarem zaretušovat ho na jiný uzel.
- Malý počet plánovaných operací údržby údržby, které vyžadují, aby se virtuální počítač znovu dělil jinému hostiteli pro obsluhu.

Další informace o možnostech zálohování dat v místním úložišti najdete v tématu [zálohování a zotavení po havárii pro disky Azure s IaaS](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Návody začít nasazovat virtuální počítače řady Lsv2-Series?**  
   Podobně jako u všech ostatních virtuálních počítačů můžete vytvořit virtuální počítač pomocí [portálu](quick-create-portal.md), rozhraní příkazového [řádku Azure](quick-create-cli.md)nebo [PowerShellu](quick-create-powershell.md) .

* **Dojde k selhání jednoho NVMe disku k selhání všech virtuálních počítačů v hostiteli?**  
   Pokud je v uzlu hardwaru zjištěna chyba disku, je hardware ve stavu selhání. V takovém případě jsou všechny virtuální počítače v uzlu automaticky nepřiděleny a přesunuty do uzlu v pořádku. U virtuálních počítačů Lsv2-Series to znamená, že data zákazníka v neúspěšném uzlu jsou také bezpečně smazána a bude nutné je znovu vytvořit zákazníkem na novém uzlu. Jak je uvedeno dříve, než bude migrace za provozu na Lsv2 k dispozici, data v neúspěšném uzlu se proaktivně přesunou s virtuálními počítači, když se přenesou do jiného uzlu.

* **Musím v systému Windows Server 2012 nebo Windows Server 2016 dělat úpravy dotazování?**  
   Cyklické dotazování NVMe je k dispozici pouze v systému Windows Server 2019 v Azure.  

* **Můžu přejít zpátky na tradiční model ISR (Interrupt Service rutiny)?**  
   Virtuální počítače řady Lsv2-Series jsou optimalizované pro cyklické dotazování NVMe. Pro zlepšení výkonu cyklického dotazování jsou průběžně poskytovány aktualizace.

* **Je možné upravit nastavení cyklického dotazování ve Windows serveru 2019?**  
   Nastavení cyklického dotazování není uživatelsky seřiditelné.
   
## <a name="next-steps"></a>Další kroky

* Podívejte se na specifikace pro všechny [virtuální počítače optimalizované pro výkon úložiště](../sizes-storage.md) v Azure.
