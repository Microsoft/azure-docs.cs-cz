---
title: Optimalizace výkonu u virtuálních počítačů Azure Lsv2-Series – úložiště
description: Naučte se, jak optimalizovat výkon pro vaše řešení na virtuálních počítačích Lsv2-Series pomocí příkladu pro Linux.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: 650164556223a73a722bc91ecb31491ee98cb8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307097"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>Optimalizace výkonu na virtuálních počítačích s Lsv2-Series Linux

Virtuální počítače řady Lsv2-series podporují nejrůznější úlohy, které vyžadují vysokou vstupně-výstupní operace a propustnost v místním úložišti napříč širokou škálou aplikací a oborů.  Lsv2-Series je ideální pro velké objemy dat, SQL, NoSQL databáze, datové sklady a velké transakční databáze, včetně Cassandra, MongoDB, Cloudera a Redis.

Návrh Lsv2-Series Virtual Machines (virtuální počítače) maximalizuje procesor AMD EPYC™ 7551, který poskytuje nejlepší výkon mezi procesorem, pamětí, NVMe zařízeními a virtuálními počítači. Při práci s partnery v systému Linux jsou k dispozici několik buildů Azure Marketplace, které jsou optimalizované pro výkon řady Lsv2-Series a v současné době zahrnují:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Tento článek poskytuje tipy a návrhy, které zajistí, aby vaše úlohy a aplikace dosáhly maximálního výkonu, který je pro virtuální počítače určený. Informace na této stránce budou průběžně aktualizovány, protože do Azure Marketplace jsou přidány další optimalizované obrázky Lsv2.

## <a name="amd-eypc-chipset-architecture"></a>Architektura čipové sady AMD EYPC™

Virtuální počítače řady Lsv2-Series používají procesory AMD EYPC™ serveru založené na mikroarchitektuře Zen. Technologie AMD vyvinula nekonečno Fabric (pokud) pro EYPC™ jako škálovatelné propojení pro svůj model NUMA, které by bylo možné použít pro komunikaci na Die, na balíčku a komunikaci s více balíčky. V porovnání s QPI (rychlá cesta propojení) a UPI (Ultra-Path Interconnect), která se používá pro moderní monolitické procesory Intel, může architektura pro procesory AMD řady NUMA přinést jak výhody výkonu, tak i problémy. Skutečný dopad omezení šířky pásma paměti a latence se může lišit v závislosti na typu spuštěných úloh.

## <a name="tips-to-maximize-performance"></a>Tipy pro maximalizaci výkonu

* Pokud nahráváte vlastní GuestOS pro Linux pro vaše úlohy, pamatujte, že urychlené síťové služby budou ve výchozím nastavení **vypnuté** . Pokud máte v úmyslu povolit akcelerované síťové služby, povolte ji v době vytváření virtuálního počítače, aby se co nejlépe vypnul.

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

* **Musím rq_affinity pro výkon udělat nějaké úpravy?**  
   Nastavení rq_affinity je menší úprava při použití absolutních maximálních vstupně-výstupních operací za sekundu (IOPS). Jakmile všechno ostatní funguje dobře, zkuste nastavit rq_affinity na 0, abyste viděli, jestli se jedná o rozdíl.

* **Potřebuji změnit nastavení blk_mq?**  
   RHEL/CentOS 7. x automaticky používá BLK-MQ pro zařízení NVMe. Nejsou nutné žádné změny konfigurace ani nastavení. Nastavení scsi_mod. use _blk_mq je pouze pro SCSI a bylo použito během Lsv2 Preview, protože zařízení NVMe byla na virtuálních počítačích hosta zobrazena jako zařízení SCSI. V současné době se zařízení NVMe zobrazují jako zařízení NVMe, takže nastavení SCSI BLK-MQ není důležité.

* **Potřebuji změnit "Fio"?**  
   Pokud chcete získat maximální IOPS s nástrojem pro měření výkonu, jako je FIO ve velikosti virtuálního počítače L64v2 a L80v2, nastavte u každého zařízení NVMe hodnotu rq_affinity na 0.  Tento příkazový řádek například nastaví "rq_affinity" na nulu pro všechna 10 zařízení NVMe ve virtuálním počítači s L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Upozorňujeme také, že nejlepší výkon získáte, když se v/v dokončí přímo na každé nezpracované zařízení NVMe bez dělení, žádné souborové systémy, žádná konfigurace RAID 0 atd. Než začnete s testovací relací, ujistěte se, že je konfigurace ve známém stavu a v čistém stavu, a to spuštěním `blkdiscard` na každém zařízení NVMe.
   
## <a name="next-steps"></a>Další kroky

* Podívejte se na specifikace pro všechny [virtuální počítače optimalizované pro výkon úložiště](../sizes-storage.md) v Azure.
