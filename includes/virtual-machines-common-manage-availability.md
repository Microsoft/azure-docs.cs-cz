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
ms.openlocfilehash: 42b6dde708e2a1dbda225fd95e3db964267ae48a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333778"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Vysvětlení restartování virtuálních počítačů – údržba vs. výpadek
Existují tři scénáře, které můžou vést k virtuálnímu počítači v Azure se to týká: Neplánovaná údržba hardwaru, neočekávaný výpadek a plánovaná údržba.

* K **neplánovaným událostem údržby hardwaru** dochází v případě, kdy platforma Azure předpoví, že dojde k selhání hardwaru nebo jakékoli komponenty platformy přidružené k fyzickému počítači. Když platforma předpoví selhání, vydá událost neplánované údržby hardwaru, aby se snížil dopad na virtuální počítače hostované na tomto hardwaru. Azure s využitím technologie Migrace za provozu migruje službu Virtual Machines z hardwaru, který selhává, na fyzický počítač, který je v pořádku. Migrace za provozu je operace zachovávající virtuální počítač, který pozastaví jenom na krátkou dobu. Paměť, otevřené soubory a síťová připojení se zachovají, ale výkon se může před událostí a/nebo po ní snížit. V případech, kdy není možné použít Migraci za provozu, dojde k neočekávanému výpadku virtuálního počítače, jak je popsáno níže.


* **Neočekávaný výpadek** je selhání hardwaru nebo fyzickou infrastrukturou pro virtuální počítač neočekávaně. To může zahrnovat selhání místní sítě, selhání místního disku nebo Další selhání na úrovni racku. Když se detekuje, Platforma Azure automaticky migruje (opravu) vašeho virtuálního počítače na fyzický počítač v pořádku ve stejném datacentru. Během opravné procedury jsou virtuální počítače odstavené (restartují se) a v některých případech dojde ke ztrátě dočasné jednotky. Připojené disky s operačním systémem a datové disky se vždy zachovají. 

  Virtuální počítače můžete také setkávat s výpadky v nepravděpodobném případě výpadku nebo havárii, která ovlivňuje celé datové centrum nebo dokonce celou oblast. Pro tyto scénáře Azure poskytuje možnosti ochrany včetně [zóny dostupnosti](../articles/availability-zones/az-overview.md) a [spárované oblasti](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Plánované události údržby** jsou pravidelné aktualizace základní platformy Azure prováděné Microsoftem za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače. U většiny těchto aktualizací nemá jejich provedení žádný vliv na vaše služby Virtual Machines ani Cloud Services (viz [Údržba se zachováním virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Přestože se platforma Azure pokouší použít údržbu se zachováním virtuálních počítačů kdykoli je to možné, existují výjimečné případy, kdy tyto aktualizace k aplikaci požadovaných aktualizací na základní infrastrukturu vyžadují restartování virtuálního počítače. V takovém případě můžete provést plánovanou údržbu Azure pomocí operace údržba-opětovné nasazení, která zahájí údržbu virtuálních počítačů ve vhodném časovém intervalu. Další informace najdete v tématu [Plánovaná údržba pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Pokud chcete snížit dopad výpadků kvůli jedné nebo několika takovým událostem, doporučujeme pro vaše virtuální počítače následující osvědčené postupy z hlediska vysoké dostupnosti:

* [Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]
* [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]
* [Pomocí naplánovaných událostí proaktivně odpovědět na virtuální počítač vliv na události](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]
* [Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti]
* [Zóny dostupnosti se používají k ochraně před datacenter selhání na úrovni]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance
Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Tato konfigurace v rámci datacentra zajišťuje, že během buď plánované i neplánované údržby, alespoň jeden virtuální počítač je k dispozici a splňuje 99,95 % Azure SLA. Další informace najdete v tématu [SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Nenechávejte ve skupině dostupnosti samostatné instance virtuálních počítačů. Virtuální počítače v této konfiguraci není získání způsobilosti pro smlouvu SLA se zárukou a čelí výpadkům během plánovaných událostí údržby, s výjimkou případů, kdy jeden virtuální počítač používá [Azure premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd). U jednotlivých virtuálních počítačů pomocí SSD disků úrovně premium Azure SLA vztahuje.

Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí **aktualizační doménu** a **doménu selhání**. Dané skupině dostupnosti se ve výchozím nastavení přiřadí pět aktualizačních domén (u nasazení podle modelu Resource Manager je pak možné počet aktualizačních domén navýšit až na 20), které uživatel nemůže konfigurovat a které představují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače konfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání pro nasazení podle modelu Resource Manager (do dvou domén selhání pro model nasazení Classic). Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

<!--Image reference--> ![Koncepční nákres konfigurace domény domény a selhání aktualizace](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti
Pokud aktuálně používáte virtuální počítače s nespravovanými disky, důrazně doporučujeme [převést virtuální počítače ve skupině dostupnosti na používání spravovaných disků](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md) poskytují vyšší spolehlivost skupiny dostupnosti tím, že zajišťují dostatečné oddělení jednotlivých disků virtuálních počítačů ve skupině dostupnosti, aby se zabránilo jedinému bodu selhání. Provádí to automatickým umisťováním disků v doménách selhání jiného úložiště (clustery úložiště) a zarovnání se doména selhání virtuálního počítače. Pokud doména selhání úložiště se nepovedlo kvůli selhání hardwaru nebo softwaru, pouze instance virtuálního počítače s disky na doménu selhání úložiště se nezdaří.
![Spravované disky doménami selhání](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Počet domén selhání pro spravované skupiny dostupnosti se liší podle oblasti – buď dvě, nebo tři na oblast. V následující tabulce jsou uvedeny počty podle oblastí

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Pokud máte v plánu používat virtuální počítače s nespravovanými disky, postupujte podle níže uvedených osvědčených postupů pro účty úložiště, kde jsou uloženy virtuální pevné disky (VHD) virtuálních počítačů jako [objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Uchovávejte všechny disky (s operačním systémem i s daty) přidružené k virtuálnímu počítači ve stejném účtu úložiště.**
2. Než začnete přidávat další virtuální pevné disky do účtu úložiště, **zkontrolujte [omezení](../articles/storage/common/storage-scalability-targets.md) počtu nespravovaných disků v účtu služby Storage**.
3. **Pro každý virtuální počítač ve skupině dostupnosti použijte samostatný účet úložiště.** Nesdílejte účty služby Storage mezi více virtuálními počítači ve stejné skupině dostupnosti. Je to přijatelné pro virtuální počítače v různých skupinách dostupnosti sdílení účtů úložiště, pokud nepoužijí nad osvědčené postupy ![nespravované disky doménami selhání](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Pomocí naplánovaných událostí proaktivně reagovat na virtuální počítač vliv na události

Když se přihlásíte k odběru [naplánované události](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), váš virtuální počítač je oznámení o události nadcházející údržby, které může mít vliv na váš virtuální počítač. Pokud jsou povolené naplánované události, váš virtuální počítač dostane minimální množství času, před provedením aktivity údržby. Například aktualizace hostitelského operačního systému, které může mít vliv na váš virtuální počítač je ve frontě jako události, které určují dopad, stejně jako čas, kdy bude provedena Údržba, pokud nebyla provedena žádná akce. Události plánu se také zařadí do fronty když Azure zjistí selhání bezprostřední hardwaru, který může mít vliv na váš virtuální počítač, který umožňuje rozhodnout, kdy opravy je třeba provést. Zákazníci můžou používat události a provádět úlohy před údržby, jako například ukládání stavu, převzetí služeb při selhání na sekundární a tak dále. Po dokončení svoji logiku pro řádně zpracování události údržby můžete schválit nezpracovaných naplánované události povolit platformu a pokračujte údržby.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti
Pokud jsou všechny vaše virtuální počítače téměř identické a pro vaši aplikaci slouží stejnému účelu, doporučujeme nakonfigurovat skupinu dostupnosti pro každou vrstvu aplikace.  Pokud do stejné skupiny dostupnosti umístíte dvě různé vrstvy, bude možné restartovat všechny virtuální počítače na stejné úrovni aplikace. Nakonfigurováním alespoň dvou virtuálních počítačů ve skupině dostupnosti pro každou vrstvu můžete zajistit, že v každé skupině dostupnosti bude k dispozici alespoň jeden virtuální počítač.

Můžete například umístit všechny virtuální počítače ve front-endu vaší aplikace, na nichž služba běží IIS, Apache nebo Nginx, do jedné skupiny dostupnosti. Ujistěte se, že jsou ve stejné skupině dostupnosti umístěné pouze virtuální počítače ve front-endu. Podobně se ujistěte, že jsou ve vlastní skupině dostupnosti umístěné pouze virtuální počítače datové vrstvy, jako jsou například replikované virtuální počítače s SQL Serverem nebo virtuální počítače se serverem MySQL.

<!--Image reference--> ![Aplikačních vrstev](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti
Kombinací služby [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) se skupinou dostupnosti získáte největší odolnost aplikace. Azure Load Balancer rozděluje provoz mezi víc virtuálních počítačů. Azure Load Balancer je součástí virtuálních počítačů úrovně Standard. Ne všechny úrovně virtuálních počítačů zahrnují nástroj Azure Load Balancer. Další informace o vyrovnávání zatížení virtuálních počítačů najdete v článku [Vyrovnávání zatížení virtuálních počítačů](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Pokud nástroj pro vyrovnávání zatížení není nakonfigurovaný k vyrovnávání provozu mezi víc virtuálních počítačů, všechny plánované události údržby ovlivní jediný virtuální počítač obsluhující provoz a způsobí výpadek vrstvy aplikace. Umístěním více virtuálních počítačů na stejné vrstvě aplikace do stejné skupiny dostupnosti a pod jeden nástroj pro vyrovnávání zatížení umožníte nepřetržitou obsluhu provozu alespoň jednou instancí.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Zóny dostupnosti se používají k ochraně před datacenter selhání na úrovni

[Zóny dostupnosti](../articles/availability-zones/az-overview.md), nastaví alternativu k dostupnosti, rozbalte položku úrovně ovládacího prvku, je nutné zachovat dostupnost aplikací a dat na virtuálních počítačích. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři zóny dostupnosti pro každou podporovanou oblast Azure. Každá zóna dostupnosti má samostatný napájení zdroje, síť a chlazení. proto je logicky oddělená od jiných zón dostupnosti v rámci oblasti Azure. Díky navrhování řešení pro použití replikované virtuální počítače v oblasti, můžete chránit své aplikace a data před ztrátou datacentra. Pokud dojde k ohrožení jednu zónu, pak replikované aplikace a data jsou okamžitě dostupné v jiné zóně. 

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Další informace o nasazení [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuálního počítače v zóně dostupnosti.


<!-- Link references -->
[Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]: #configure-each-application-tier-into-separate-availability-sets
[Kombinace nástroje pro vyrovnávání zatížení se skupinami dostupnosti]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]: #use-managed-disks-for-vms-in-an-availability-set
[Zóny dostupnosti se používají k ochraně před datacenter selhání na úrovni]: #use-availability-zones-to-protect-from-datacenter-level-failures
