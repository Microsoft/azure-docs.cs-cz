---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: dc54c232b972c25e6b21dbbb8a91a0218f17d584
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670203"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Vysvětlení restartování virtuálních počítačů – údržba vs. výpadek
Existují tři scénáře, které můžou vést k virtuálnímu počítači v Azure ovlivněný: neplánované hardwaru údržby, nečekané výpadky a plánované údržby.

* K **neplánovaným událostem údržby hardwaru** dochází v případě, kdy platforma Azure předpoví, že dojde k selhání hardwaru nebo jakékoli komponenty platformy přidružené k fyzickému počítači. Když platforma předpoví selhání, vydá událost neplánované údržby hardwaru, aby se snížil dopad na virtuální počítače hostované na tomto hardwaru. Azure s využitím technologie Migrace za provozu migruje službu Virtual Machines z hardwaru, který selhává, na fyzický počítač, který je v pořádku. Migrace za provozu je operace zachovávající virtuální počítač, který pozastaví jenom na krátkou dobu. Paměť, otevřené soubory a síťová připojení se zachovají, ale výkon se může před událostí a/nebo po ní snížit. V případech, kdy není možné použít Migraci za provozu, dojde k neočekávanému výpadku virtuálního počítače, jak je popsáno níže.


* **Nečekané výpadky** je, když hardware nebo fyzické infrastruktuře pro virtuální počítač neočekávaně selže. To může zahrnovat selhání k místní síti, selhání místní disk nebo jiné chyby úrovně racku. Při zjištění, platformu Azure automaticky migruje (heals) virtuálního počítače do pořádku fyzického počítače ve stejném datovém centru. Během opravné procedury jsou virtuální počítače odstavené (restartují se) a v některých případech dojde ke ztrátě dočasné jednotky. Připojené disky s operačním systémem a datové disky se vždy zachovají. 

  Virtuální počítače můžete také plánovaná odstávka k nepravděpodobnému výpadku nebo po havárii, ovlivní celého datového centra nebo i celou oblast. Pro tyto scénáře Azure poskytuje možnosti ochrany včetně [dostupnost zóny](../articles/availability-zones/az-overview.md) a [spárovat oblasti](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Plánované události údržby** jsou pravidelné aktualizace základní platformy Azure prováděné Microsoftem za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače. U většiny těchto aktualizací nemá jejich provedení žádný vliv na vaše služby Virtual Machines ani Cloud Services (viz [Údržba se zachováním virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Přestože se platforma Azure pokouší použít údržbu se zachováním virtuálních počítačů kdykoli je to možné, existují výjimečné případy, kdy tyto aktualizace k aplikaci požadovaných aktualizací na základní infrastrukturu vyžadují restartování virtuálního počítače. V takovém případě můžete provést plánovanou údržbu Azure pomocí operace údržba-opětovné nasazení, která zahájí údržbu virtuálních počítačů ve vhodném časovém intervalu. Další informace najdete v tématu [Plánovaná údržba pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Pokud chcete snížit dopad výpadků kvůli jedné nebo několika takovým událostem, doporučujeme pro vaše virtuální počítače následující osvědčené postupy z hlediska vysoké dostupnosti:

* [Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]
* [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]
* [Naplánované události použít k proaktivně odpovědi na virtuální počítač události, které mají vliv] (https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]
* [Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti]
* [Dostupnost zóny používat k ochraně úrovně selhání datacenter]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance
Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Tato konfigurace v datacentru zajišťuje, že během buď plánované i neplánované údržby alespoň jeden virtuální počítač je k dispozici a splňuje podmínku 99.95 % smlouva Azure SLA. Další informace najdete v tématu [SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Nenechávejte ve skupině dostupnosti samostatné instance virtuálních počítačů. Na virtuální počítače v takové konfiguraci se nevztahuje garance SLA a během plánovaných událostí údržby čelí výpadkům, kromě případu, kdy samostatný virtuální počítač využívá službu [Premium Storage](../articles/virtual-machines/windows/premium-storage.md). Na samostatné virtuální počítače, které využívají službu Premium Storage, se Azure SLA vztahuje.

Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí **aktualizační doménu** a **doménu selhání**. Dané skupině dostupnosti se ve výchozím nastavení přiřadí pět aktualizačních domén (u nasazení podle modelu Resource Manager je pak možné počet aktualizačních domén navýšit až na 20), které uživatel nemůže konfigurovat a které představují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače konfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání pro nasazení podle modelu Resource Manager (do dvou domén selhání pro model nasazení Classic). Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

<!--Image reference-->
   ![Koncepční nákres konfigurace aktualizačních domén a domén selhání](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti
Pokud aktuálně používáte virtuální počítače s nespravovanými disky, důrazně doporučujeme [převést virtuální počítače ve skupině dostupnosti na používání spravovaných disků](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md) poskytují vyšší spolehlivost skupiny dostupnosti tím, že zajišťují dostatečné oddělení jednotlivých disků virtuálních počítačů ve skupině dostupnosti, aby se zabránilo jedinému bodu selhání. Dělá to tak, že automaticky uvedení disky domén selhání jiného úložiště (clustery úložiště) a zarovnáním domény selhání virtuálního počítače. Pokud domény selhání úložiště nezdaří z důvodu selhání hardwaru nebo softwaru, nezdaří se pouze instance virtuálního počítače s disky na domény selhání úložiště.
![Spravované disky FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Počet domén selhání pro spravované skupiny dostupnosti se liší podle oblasti – buď dvě, nebo tři na oblast. V následující tabulce jsou uvedeny počty podle oblastí

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Pokud se chystáte používat virtuální počítače s [nespravovanými disky](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks), postupujte podle níže uvedených osvědčených postupů pro účty služby Storage, ve kterých se virtuální pevné disky (VHD) virtuálních počítačů ukládají jako [objekty blob stránky](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Uchovávejte všechny disky (s operačním systémem i s daty) přidružené k virtuálnímu počítači ve stejném účtu úložiště.**
2. Než začnete přidávat další virtuální pevné disky do účtu úložiště, **zkontrolujte [omezení](../articles/storage/common/storage-scalability-targets.md) počtu nespravovaných disků v účtu služby Storage**.
3. **Pro každý virtuální počítač ve skupině dostupnosti použijte samostatný účet úložiště.** Nesdílejte účty služby Storage mezi více virtuálními počítači ve stejné skupině dostupnosti. Je přijatelné pro virtuální počítače v různých sad dostupnosti můžete sdílet účty úložiště v případě výše doporučených postupů dodržíte ![nespravované disky FDs](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti
Pokud jsou všechny vaše virtuální počítače téměř identické a pro vaši aplikaci slouží stejnému účelu, doporučujeme nakonfigurovat skupinu dostupnosti pro každou vrstvu aplikace.  Pokud do stejné skupiny dostupnosti umístíte dvě různé vrstvy, bude možné restartovat všechny virtuální počítače na stejné úrovni aplikace. Nakonfigurováním alespoň dvou virtuálních počítačů ve skupině dostupnosti pro každou vrstvu můžete zajistit, že v každé skupině dostupnosti bude k dispozici alespoň jeden virtuální počítač.

Můžete například umístit všechny virtuální počítače ve front-endu vaší aplikace, na nichž služba běží IIS, Apache nebo Nginx, do jedné skupiny dostupnosti. Ujistěte se, že jsou ve stejné skupině dostupnosti umístěné pouze virtuální počítače ve front-endu. Podobně se ujistěte, že jsou ve vlastní skupině dostupnosti umístěné pouze virtuální počítače datové vrstvy, jako jsou například replikované virtuální počítače s SQL Serverem nebo virtuální počítače se serverem MySQL.

<!--Image reference-->
   ![Úrovně aplikace](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti
Kombinací služby [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) se skupinou dostupnosti získáte největší odolnost aplikace. Azure Load Balancer rozděluje provoz mezi víc virtuálních počítačů. Azure Load Balancer je součástí virtuálních počítačů úrovně Standard. Ne všechny úrovně virtuálních počítačů zahrnují nástroj Azure Load Balancer. Další informace o vyrovnávání zatížení virtuálních počítačů najdete v článku [Vyrovnávání zatížení virtuálních počítačů](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Pokud nástroj pro vyrovnávání zatížení není nakonfigurovaný k vyrovnávání provozu mezi víc virtuálních počítačů, všechny plánované události údržby ovlivní jediný virtuální počítač obsluhující provoz a způsobí výpadek vrstvy aplikace. Umístěním více virtuálních počítačů na stejné vrstvě aplikace do stejné skupiny dostupnosti a pod jeden nástroj pro vyrovnávání zatížení umožníte nepřetržitou obsluhu provozu alespoň jednou instancí.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Dostupnost zóny používat k ochraně úrovně selhání datacenter

[Dostupnost zóny](../articles/availability-zones/az-overview.md), nastaví alternativu k dostupnosti, rozbalte položku úroveň řízení, budete muset zachovat dostupnost aplikace a data na virtuální počítače. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři zóny dostupnosti za podporovanou oblast Azure. Každou zónu dostupnosti má samostatnou spotřeby zdroje, sítě a chlazení a logicky oddělené od dalších dostupnost zóny v rámci oblasti Azure. Pomocí architektury řešení pro použití replikované virtuální počítače v zóny, můžete chránit vaše aplikace a data před ztrátou datacentru. Pokud dojde k narušení jednu zónu, pak replikované aplikace a data jsou okamžitě dostupné v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Další informace o nasazení [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuálního počítače v zóně dostupnosti.


<!-- Link references -->
[Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]: #configure-each-application-tier-into-separate-availability-sets
[Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]: #use-managed-disks-for-vms-in-an-availability-set
[Dostupnost zóny používat k ochraně úrovně selhání datacenter]: #use-availability-zones-to-protect-from-datacenter-level-failures
