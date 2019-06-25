---
title: Optimalizace výkonu na virtuální počítače Azure řady Lsv2 – Storage | Dokumentace Microsoftu
description: Zjistěte, jak optimalizovat výkon pro vaše řešení na virtuálních počítačích řady Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 7be86c8934b8766217f9fca432327d254204f0c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60738940"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimalizace výkonu na virtuálních počítačích řady Lsv2

Virtuální počítače řady Lsv2 podporují celou řadu úloh, které potřebují k zajištění široké škály aplikací a obory vysoké vstupně-výstupních operací a propustnosti na místní úložiště.  Lsv2-series je ideální pro velké objemy dat, SQL, NoSQL databáze, datových skladů a velké transakční databáze, včetně Cassandra, MongoDB, Cloudera a Redis.

Návrh Lsv2-series virtuálních počítačů (VM) maximalizuje procesor AMD EPYC 7551™, který poskytovat nejlepší výkon mezi procesoru, paměti, zařízení NVMe a virtuální počítače. Kromě dosažení maximálního výkonu hardwaru, jsou virtuální počítače řady Lsv2 navrženy pro práci s potřebami operačních systémů Linux pro lepší výkon pomocí hardwaru a softwaru.

Ladění softwarových a hardwarových výsledkem optimalizovanou verzi [Ubuntu 18.04 od firmy Canonical a 16.04](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer?tab=Overview), kterou jsme vydali v rané fázi prosince 2018 na webu Azure Marketplace, který podporuje maximální výkon u zařízení NVMe v Virtuální počítače řady Lsv2.

Tento článek poskytuje tipy a návrhy, aby úlohy a aplikace dosáhnout maximálního výkonu, které jsou navržené k virtuálním počítačům. Informace na této stránce se průběžně aktualizuje při přidání další Lsv2 optimalizované Image na webu Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura čipové sady AMD EYPC™

Virtuální počítače řady Lsv2 používají procesory AMD EYPC™ serveru podle mikroarchitekturu Zen. AMD mimo jiné vyvinuto nekonečno prostředků infrastruktury (Pokud) pro EYPC™ škálovatelné propojení pro svůj model NUMA, která se dá použít pro komunikaci na kostka na balíček a více balíčků. Ve srovnání s QPI (cesta rychlé vzájemné propojení) a UPI (propojení Ultra-Path) používá u moderních monolitické kostka procesorů Intel, AMD n NUMA (krátkodobé používání) – kostka architektura zpřístupněte i výkon výhody i výzvy. Skutečný dopad omezení šířky pásma a čekací doba paměti může lišit v závislosti na typu úlohy spuštěné.

## <a name="tips-to-maximize-performance"></a>Tipy pro zajištění maximálního výkonu

* Pokud nahráváte vlastní GuestOS Linuxu pro úlohy, mějte na paměti, že bude Akcelerovanými síťovými službami **OFF** ve výchozím nastavení. Pokud máte v úmyslu povolit Akcelerovanými síťovými službami, povolte ho v době vytváření virtuálních počítačů pro zajištění nejlepšího výkonu.

* Hardware, který využívá virtuální počítače řady Lsv2 využívá zařízení NVMe s osmi s páry fronty vstupně-výstupních operací (QP). Každé zařízení vstupně-výstupní operace fronty NVMe je ve skutečnosti pár: fronty odesílání a dokončení fronty. Ovladač NVMe nastaven k optimalizaci využití těchto osm QPs vstupně-výstupních operací díky distribuci můžu / naplánovat vstupů a do kruhové dotazování. Pokud chcete získat maximální výkon, spusťte osm úloh na zařízení tak, aby odpovídaly.

* Nekombinujte NVMe správce příkazů (například NVMe INTELIGENTNÍ informace o dotazu, atd.) pomocí příkazů NVMe vstupně-výstupních operací během aktivní úlohy. Zařízení Lsv2 NVMe využívají technologie Hyper-V NVMe přímé, přepnout režim"pomalé" pokaždé, když se všechny příkazy Správce NVMe čekají na vyřízení. Lsv2 uživatelům může zobrazit výrazné výkon pokles NVMe vstupně-výstupní výkon, pokud k tomu dojde.

* Lsv2 uživatelé neměli byste tedy spoléhat na technologii NUMA informace o zařízení (všechny 0) ze zaznamenaný v rámci virtuálního počítače pro datové jednotky rozhodnout spřažení NUMA pro své aplikace. Doporučený způsob pro lepší výkon je rozdělíte úlohy mezi procesory, pokud je to možné.

* Hloubka maximální podporované fronty na vstupně-výstupní operace fronty pár zařízení NVMe Lsv2 virtuálního počítače je 1024 (vs. Amazon i3 hloubka fronty 32 limit). Lsv2 uživatelé měli omezit jejich (syntetické) srovnávací testy úloh do hloubky fronty 1024 nebo nižší neměl spouštět podmínkách plného fronty, které může snížit výkon.

## <a name="utilizing-local-nvme-storage"></a>Použití místního úložiště NVMe

Místní úložiště na disku NVMe 1.92 TB na všech virtuálních počítačích Lsv2 je dočasný. Při úspěšném standardní restartování virtuálního počítače se bude uchovávat data na místním disku NVMe. Data nezachovají na NVMe, pokud dojde znovu nasadil, zrušit přidělení nebo odstranění virtuálního počítače. Data nezachovají, pokud jiný problém způsobí, že se virtuální počítač nebo hardwaru, který je spuštěn na serveru, přestane není v pořádku. Pokud k tomu dojde, všechna data na staré hostitele je bezpečně vymaže.

Existuje také občas budete virtuální počítač se musí přesunout na jiný hostitelský počítač, například během operace plánované údržby. Operace plánované údržby a některá selhání hardwaru můžete očekávat s [Scheduled Events](scheduled-events.md). Naplánované události by měla sloužit k aktualizace žádné operace obnovení a prediktivní Údržba.

V případě, že události plánované údržby vyžaduje virtuální počítač znovu vytvořit na nového hostitele se prázdný místní disky bude potřeba data Opakovaná synchronizace (znovu, s daty na staré hostiteli se bezpečně vymaže). K tomu dochází, protože virtuální počítače řady Lsv2 na místním disku NVMe aktuálně nepodporují migraci za provozu.

Existují dva režimy pro plánované údržby.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standardní řízené zákazníka Údržba virtuálního počítače

- Virtuální počítač se přesune na aktualizovaném hostiteli během časového období 30 dnů.
- Lsv2 místního úložiště dat by mohly být ztraceny, proto doporučujeme zálohování dat před události.

### <a name="automatic-maintenance"></a>Automatická údržba

- Nastane, pokud zákazník nespustí řízené zákazníka údržby nebo v případě nouzové postupy, jako je zabezpečení události nultého dne.
- Určený pro zachování zákaznická data, ale se malé riziku zablokování virtuálních počítačů nebo restartovat počítač.
- Lsv2 místního úložiště dat by mohly být ztraceny, proto doporučujeme zálohování dat před události.

Pro všechny události připravovanou službu pomocí procesu řízené údržby můžete vybrat vždy nejvhodnější pro aktualizaci. Před událostí může zálohovat data ve službě premium storage. Po dokončení události údržby můžete vrátit data aktualizovat virtuální počítače Lsv2 místní NVMe úložiště.

Mezi scénáře, které udržují data na místní disky NVMe patří:

- Je virtuální počítač běží a je v pořádku.
- Virtuální počítač restartuje na místě (podle musíte nebo Azure).
- Virtuální počítač je pozastavený (zastavené bez vyhovělo).
- Většina plánované údržby pro obsluhu operace.

Scénáře, které bezpečně odstranit data určená k ochraně zákazník patří:

- Nové nasazení virtuálního počítače zastaveno (přidělení zrušeno), nebo odstranit, (vy).
- Virtuální počítač nebude v pořádku a má službě opravy do jiného uzlu kvůli problému hardwaru.
- Malý počet plánované údržby operací údržby, které vyžaduje virtuální počítač, aby znovu přidělit do jiného hostitele pro obsluhu.

Další informace o možnostech zálohování dat v místním úložišti, najdete v článku [zálohování a zotavení po havárii pro disky Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* **Jak můžu začít nasazovat virtuální počítače řady Lsv2?**  
   Podobně jako jakýkoli jiný virtuální počítač, použijte [portál](quick-create-portal.md), [rozhraní příkazového řádku Azure](quick-create-cli.md), nebo [Powershellu](quick-create-powershell.md) vytvoření virtuálního počítače.

* **Selhání jednoho disku NVMe způsobí, že všechny virtuální počítače na hostiteli selhání?**  
   Když je v uzlu hardware selhání disku, hardwarové je ve stavu selhání. V tomto případě jsou všechny virtuální počítače na uzlu automaticky zrušit přidělení a přesunout do uzlu v pořádku. Pro virtuální počítače řady Lsv2 to znamená, data zákazníka v uzlu selhání je také bezpečně vymaže a bude nutné znovu vytvořit zákazník na novém uzlu. Jak je uvedeno, než bude k dispozici na Lsv2 migrace za provozu, data v uzlu selhání proaktivně přesunou se s virtuálními počítači jako se přesunou do jiného uzlu.

* **Je potřeba provést nějaké úpravy rq_affinity výkonu?**  
   Při použití absolutní maximální vstupně výstupní operace za sekundu (IOPS), je nastavení rq_affinity menší úpravu. Jakmile všechno ostatní funguje dobře, zkuste rq_affinity nastavena na hodnotu 0, pokud chcete zobrazit, pokud záleží na tom.

* **Budu muset změnit nastavení blk_mq?**  
   RHEL/CentOS 7.x automaticky použije blk Magic quadrant pro zařízení NVMe. Žádné změny konfigurace nebo nastavení nejsou nezbytné. Nastavení scsi_mod.use_blk_mq je jenom pro SCSI a byla použita ve verzi Lsv2 Preview, protože zařízení NVMe byly viditelné ve virtuálních počítačích hosta jako zařízení SCSI. V současné době se zobrazují jako zařízení NVMe, zařízení NVMe, takže je bezvýznamná nastavení blk mq SCSI.

* **Budu muset změnit "fio"?**  
   Chcete-li získat maximální vstupně-výstupních operací s výkonem nástroje, například "fio" velikostí L64v2 a virtuálních počítačů L80v2 měření, nastavena na 0 v každém zařízení NVMe "rq_affinity".  Například ve Virtuálním počítači L80v2 tento příkaz nastaví "rq_affinity" na nulu pro všechna zařízení NVMe, 10:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Všimněte si také, že získáte nejlepší výkon při vstupně-výstupní operace se provádí přímo do každé z nezpracované zařízení NVMe s nejsou žádné oddíly, žádné systémy souborů bez RAID 0 konfigurace atd. Před zahájením relaci testování, zkontrolujte konfigurace je do známého stavu čerstvé/clean spuštěním `blkdiscard` na každém zařízení NVMe.
   
## <a name="next-steps"></a>Další postup

* V tématu Specifikace pro všechny [virtuální počítače optimalizované pro výkon úložiště](sizes-storage.md) v Azure
