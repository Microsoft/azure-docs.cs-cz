---
title: Optimalizace výkonu na virtuálních počítačích řady Azure Lsv2 – úložiště
description: Přečtěte si, jak optimalizovat výkon vašeho řešení na virtuálních počítačích řady Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 8d99f63ae084b4f1dae3c0125420eaecf5655e2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034753"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimalizace výkonu na virtuálních počítačích řady Lsv2

Virtuální počítače řady Lsv2 podporují celou řadu úloh, které vyžadují vysoké vstupně-va a propustnost v místním úložišti v celé řadě aplikací a odvětví.  Řada Lsv2 je ideální pro big data, SQL, NoSQL databáze, datové sklady a velké transakční databáze, včetně Cassandra, MongoDB, Cloudera a Redis.

Návrh virtuálních počítačů (VM) řady Lsv2 maximalizuje procesor AMD EPYC™ 7551 a poskytuje nejlepší výkon mezi procesorem, pamětí, zařízeními NVMe a virtuálními počítači. Ve spolupráci s partnery v Linuxu je k dispozici několik sestavení Azure Marketplace, která jsou optimalizovaná pro výkon řady Lsv2 a v současné době zahrnují:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Tento článek obsahuje tipy a návrhy, které zajistí, že vaše úlohy a aplikace dosáhnou maximálního výkonu navrženého do virtuálních počítačů. Informace na této stránce budou průběžně aktualizovány, protože další imitované bitové kopie Lsv2 se přidají na Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura čipové sady AMD EYPC™

Virtuální počítače řady Lsv2 používají serverové procesory AMD EYPC™ založené na mikroarchitektuře Zen. Společnost AMD vyvinula pro EY™PC zařízení Infinity Fabric (IF) jako škálovatelné propojení pro svůj model NUMA, které by bylo možné použít pro komunikaci na obalu, na obalu a více balíček. Ve srovnání s QPI (Quick-Path Interconnect) a UPI (Ultra-Path Interconnect) používanými na moderních monolitických procesorech Intel může architektura AMD many-NUMA small-die přinést výhody výkonu i výzvy. Skutečný dopad šířky pásma paměti a omezení latence se může lišit v závislosti na typu spuštěných úloh.

## <a name="tips-to-maximize-performance"></a>Tipy pro maximalizaci výkonu

* Pokud nahráváte vlastní Linux GuestOS pro vaše úlohy, všimněte si, že akcelerované sítě budou ve výchozím nastavení **vypnuté.** Pokud máte v úmyslu povolit akcelerované sítě, povolte ji v době vytvoření virtuálního zařízení pro nejlepší výkon.

* Hardware, který pohání virtuální zařízení řady Lsv2, využívá zařízení NVMe s osmi dvojicemi front vstupně-in (QP). Každá fronta vstupně-va/v zařízení NVMe je ve skutečnosti pár: fronta odeslání a fronta dokončení. Ovladač NVMe je nastaven tak, aby optimalizoval využití těchto osmi vstupně-o qp distribucí vstupně-odpovědí v plánu kruhového dotazování. Chcete-li získat maximální výkon, spusťte osm úloh na zařízení tak, aby odpovídaly.

* Vyhněte se míchání příkazů správce NVMe (například informační dotaz NVMe SMART atd.) s příkazy NVMe I/O během aktivních úloh. Zařízení Lsv2 NVMe jsou podporována technologií Hyper-V NVMe Direct, která se přepne do "pomalého režimu" vždy, když čekající na vyřízení na vyřízení jsou příkazy správce NVMe. Uživatelé LSV2 mohli vidět dramatický pokles výkonu NVMe I / O, pokud se to stane.

* Uživatelé Lsv2 by neměli spoléhat na informace NUMA zařízení (všechny 0) hlášené z v rámci virtuálního počítači pro datové jednotky rozhodnout numa spřažení pro jejich aplikace. Doporučenýzpůsob, jak pro lepší výkon je rozložení úloh y mezi procesory, pokud je to možné.

* Maximální podporovaná hloubka fronty na dvojici front vstupně-i/o pro zařízení Lsv2 VM NVMe je 1024 (vs. limit 32 QD 32 společnosti Amazon i3). Uživatelé Lsv2 by měli omezit své (syntetické) srovnávací úlohy na hloubku fronty 1024 nebo nižší, aby se zabránilo aktivaci úplných podmínek fronty, což může snížit výkon.

## <a name="utilizing-local-nvme-storage"></a>Využití místního úložiště NVMe

Místní úložiště na disku NVMe o velikosti 1,92 TB na všech virtuálních počítačích Lsv2 je dočasné. Během úspěšného standardního restartování virtuálního počítače budou data na místním disku NVMe přetrvávat. Data nebudou přetrvávat na NVMe, pokud je virtuální počítač znovu nasazené, de-přidělené nebo odstraněné. Data nebudou přetrvávat, pokud jiný problém způsobí, že virtuální počítač nebo hardware, na který běží, se stanou nefunkčními. V takovém případě jsou všechna data na starém hostiteli bezpečně vymazána.

Budou také případy, kdy virtuální počítač musí být přesunuty do jiného hostitelského počítače, například během operace plánované údržby. Plánované operace údržby a některé selhání hardwaru lze očekávat s [naplánované události](scheduled-events.md). Plánované události by měly být použity k aktualizaci všech předpovídaných operací údržby a obnovení.

V případě, že plánovaná událost údržby vyžaduje, aby byl virtuální počítač znovu vytvořen na novém hostiteli s prázdnými místními disky, bude nutné data znovu synchronizovat (opět s tím, že všechna data o starém hostiteli jsou bezpečně vymazána). K tomu dochází, protože virtuální počítače řady Lsv2 aktuálně nepodporují migraci za provozu na místním disku NVMe.

Pro plánovanou údržbu existují dva režimy.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardní údržba řízená zákazníkem virtuálního virtuálního účtu

- Virtuální počítač se přesune do aktualizovaného hostitele během 30denního okna.
- Lsv2 místní úložiště dat může dojít ke ztrátě, takže zálohování dat před událostí se doporučuje.

### <a name="automatic-maintenance"></a>Automatická údržba

- Vyvolá se v případě, že zákazník neprovede údržbu řízenou zákazníkem, nebo v případě nouzových postupů, jako je například událost nulového dne zabezpečení.
- Určeno k zachování dat zákazníků, ale existuje malé riziko zmrazení nebo restartování virtuálního počítače.
- Lsv2 místní úložiště dat může dojít ke ztrátě, takže zálohování dat před událostí se doporučuje.

Pro všechny nadcházející události služby použijte proces řízené údržby k výběru času, který je pro vás nejvhodnější pro aktualizaci. Před událostí můžete zálohovat data v úložišti premium. Po dokončení události údržby můžete vrátit data do obnoveného místního úložiště NVMe virtuálních zařízení Lsv2.

Scénáře, které udržují data na místních discích NVMe, zahrnují:

- Virtuální virtuální měsíč je spuštěný a v pořádku.
- Virtuální počítač se restartuje na místě (vámi nebo Azure).
- Virtuální měkce se pozastaví (zastaví se bez přidělení).
- Většina plánovaných servisních operací údržby.

Mezi scénáře, které bezpečně vymažou data za účelem ochrany zákazníka, patří:

- Virtuální virtuální město se znovu nasadí, zastaví (zrušilo přidělení) nebo odstraní (vámi).
- Virtuální modul se stane nefunkční a má služby léčit do jiného uzlu z důvodu problému s hardwarem.
- Malý počet operací údržby plánované údržby, které vyžaduje, aby virtuální město přerozděleny na jiného hostitele pro údržbu.

Další informace o možnostech zálohování dat v místním úložišti najdete v [tématu Zálohování a zotavení po havárii pro disky Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jak začít nasazovat virtuální chod řady Lsv2?**  
   Stejně jako každý jiný virtuální počítač, použijte [portál](quick-create-portal.md), [Azure CLI](quick-create-cli.md)nebo [PowerShell](quick-create-powershell.md) k vytvoření virtuálního počítače.

* **Způsobí selhání jednoho disku NVMe selhání všech virtuálních počítačů na hostiteli?**  
   Pokud je v hardwarovém uzlu zjištěna chyba disku, hardware je ve stavu selhání. V takovém případě jsou všechny virtuální počítače v uzlu automaticky de-přiděleny a přesunuty do uzlu v pořádku. Pro virtuální počítače řady Lsv2 to znamená, že data zákazníka na uzlu selhání je také bezpečně vymazána a bude muset být znovu vytvořenzákazníkem v novém uzlu. Jak již bylo uvedeno, než migrace za provozu bude k dispozici na Lsv2, data na uzlu selhání budou proaktivně přesunuty s virtuálními počítači, protože jsou přeneseny do jiného uzlu.

* **Musím provést nějaké úpravy rq_affinity pro výkon?**  
   Nastavení rq_affinity je menší úprava při použití absolutní maximální vstupní a výstupní operace za sekundu (IOPS). Jakmile všechno ostatní funguje dobře, pak se pokuste nastavit rq_affinity na 0, abyste zjistili, zda to dělá rozdíl.

* **Musím změnit nastavení blk_mq?**  
   RHEL/CentOS 7.x automaticky používá blk-mq pro zařízení NVMe. Nejsou nutné žádné změny konfigurace ani nastavení. Nastavení scsi_mod.use_blk_mq je pouze pro SCSI a bylo použito během náhledu Lsv2, protože zařízení NVMe byla viditelná v hostovaném virtuálním virtuálním zařízení jako zařízení SCSI. V současné době jsou zařízení NVMe viditelná jako zařízení NVMe, takže nastavení Blk-mq SCSI je irelevantní.

* **Musím změnit "fio"?**  
   Chcete-li získat maximální vipery s nástrojem pro měření výkonu, jako je "fio" ve velikostech virtuálních zařízení L64v2 a L80v2, nastavte "rq_affinity" na 0 na každém zařízení NVMe.  Například tento příkazový řádek nastaví "rq_affinity" na nulu pro všech 10 zařízení NVMe ve virtuálním m2 L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Všimněte si také, že nejlepší výkon je dosaženo, když I / O se provádí přímo na každé z nezpracovaných Zařízení NVMe bez dělení, žádné souborové systémy, žádný RAID 0 config, atd. Před zahájením testovací relace se ujistěte, že je konfigurace `blkdiscard` ve známém čerstvém/čistém stavu spuštěním na každém zařízení NVMe.
   
## <a name="next-steps"></a>Další kroky

* Podívejte se na specifikace pro všechny [virtuální počítače optimalizované pro výkon úložiště](sizes-storage.md) v Azure
