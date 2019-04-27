---
title: Optimalizace výkonu na virtuální počítače Azure řady Lsv2 – Storage | Dokumentace Microsoftu
description: Zjistěte, jak optimalizovat výkon pro vaše řešení na virtuálních počítačích řady Lsv2.
services: virtual-machines-windows
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: f84e81a5a9e9c9cf6f477adefa0869d776f7dd71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487337"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Optimalizace výkonu na virtuálních počítačích řady Lsv2

Virtuální počítače řady Lsv2 podporují celou řadu úloh, které potřebují k zajištění široké škály aplikací a obory vysoké vstupně-výstupních operací a propustnosti na místní úložiště.  Lsv2-series je ideální pro velké objemy dat, SQL, NoSQL databáze, datových skladů a velké transakční databáze, včetně Cassandra, MongoDB, Cloudera a Redis.

Návrh Lsv2-series virtuálních počítačů (VM) maximalizuje procesor AMD EPYC 7551™, který poskytovat nejlepší výkon mezi procesoru, paměti, zařízení NVMe a virtuální počítače. Kromě dosažení maximálního výkonu hardwaru, jsou virtuální počítače řady Lsv2 navrženy pro práci s potřebami operačních systémů Windows a Linux pro lepší výkon pomocí hardwaru a softwaru.

Ladění softwarových a hardwarových výsledkem optimalizovanou verzi [systému Windows Server. 2019 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview), kterou jsme vydali v rané fázi prosince 2018 na webu Azure Marketplace, který podporuje maximální výkon u zařízení NVMe řady Lsv2 Virtuální počítače.

Tento článek poskytuje tipy a návrhy, aby úlohy a aplikace dosáhnout maximálního výkonu, které jsou navržené k virtuálním počítačům. Informace na této stránce se průběžně aktualizuje při přidání další Lsv2 optimalizované Image na webu Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architektura čipové sady AMD EYPC™

Virtuální počítače řady Lsv2 používají procesory AMD EYPC™ serveru podle mikroarchitekturu Zen. AMD mimo jiné vyvinuto nekonečno prostředků infrastruktury (Pokud) pro EYPC™ škálovatelné propojení pro svůj model NUMA, která se dá použít pro komunikaci na kostka na balíček a více balíčků. Ve srovnání s QPI (cesta rychlé vzájemné propojení) a UPI (propojení Ultra-Path) používá u moderních monolitické kostka procesorů Intel, AMD n NUMA (krátkodobé používání) – kostka architektura zpřístupněte i výkon výhody i výzvy. Skutečný dopad omezení šířky pásma a čekací doba paměti může lišit v závislosti na typu úlohy spuštěné.

## <a name="tips-for-maximizing-performance"></a>Tipy pro dosažení maximálního výkonu

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

* **Je potřeba provést úpravy dotazování ve Windows ve Windows serveru 2012 nebo Windows serveru 2016?**  
   NVMe cyklického dotazování je dostupný jenom u 2019 systému Windows Server v Azure.  

* **Můžu přejít zpět na model rutiny (ISR) tradiční přerušení služby?**  
   Virtuální počítače Lsv2-series jsou optimalizované pro NVMe cyklického dotazování. Aktualizace jsou nepřetržitě k dispozici pro zvýšení výkonu dotazování.

* **Můžete upravit nastavení cyklického dotazování v systému Windows Server 2019?**  
   Nastavení cyklického dotazování nejsou uživatelsky měnitelné.
   
## <a name="next-steps"></a>Další postup

* V tématu Specifikace pro všechny [virtuální počítače optimalizované pro výkon úložiště](sizes-storage.md) v Azure
