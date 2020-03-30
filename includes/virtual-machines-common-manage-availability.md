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
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961516"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Vysvětlení restartování virtuálních počítačů – údržba vs. výpadek
Existují tři scénáře, které mohou vést k ovlivnění virtuálního počítače v Azure: neplánovaná údržba hardwaru, neočekávané prostoje a plánovaná údržba.

* K **neplánovaným událostem údržby hardwaru** dochází v případě, kdy platforma Azure předpoví, že dojde k selhání hardwaru nebo jakékoli komponenty platformy přidružené k fyzickému počítači. Když platforma předpoví selhání, vydá událost neplánované údržby hardwaru, aby se snížil dopad na virtuální počítače hostované na tomto hardwaru. Azure používá [technologii migrace za provozu](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) k migraci virtuálních počítačů z nedaří hardwaru do zdravého fyzického počítače. Migrace za provozu je operace zachovávající virtuální počítač, který pozastaví jenom na krátkou dobu. Paměť, otevřené soubory a síťová připojení se zachovají, ale výkon se může před událostí a/nebo po ní snížit. V případech, kdy není možné použít Migraci za provozu, dojde k neočekávanému výpadku virtuálního počítače, jak je popsáno níže.


* **Neočekávaná doba prostojů** je, když neočekávaně selže hardware nebo fyzická infrastruktura virtuálního počítače. To může zahrnovat selhání místní sítě, selhání místního disku nebo jiné selhání na úrovni racku. Když zjistíte, platforma Azure automaticky migruje (léčí) váš virtuální počítač do zdravého fyzického počítače ve stejném datovém centru. Během opravné procedury jsou virtuální počítače odstavené (restartují se) a v některých případech dojde ke ztrátě dočasné jednotky. Připojené disky s operačním systémem a datové disky se vždy zachovají.

  Virtuální počítače mohou také zaznamenat prostoje v nepravděpodobném případě výpadku nebo havárie, která ovlivňuje celé datové centrum nebo dokonce celou oblast. Pro tyto scénáře Azure poskytuje možnosti ochrany, včetně [zón dostupnosti](../articles/availability-zones/az-overview.md) a [spárovaných oblastí](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Plánované události údržby** jsou pravidelné aktualizace základní platformy Azure prováděné Microsoftem za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače. U většiny těchto aktualizací nemá jejich provedení žádný vliv na vaše služby Virtual Machines ani Cloud Services (viz [Údržba se zachováním virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Přestože se platforma Azure pokouší použít údržbu se zachováním virtuálních počítačů kdykoli je to možné, existují výjimečné případy, kdy tyto aktualizace k aplikaci požadovaných aktualizací na základní infrastrukturu vyžadují restartování virtuálního počítače. V takovém případě můžete provést plánovanou údržbu Azure pomocí operace údržba-opětovné nasazení, která zahájí údržbu virtuálních počítačů ve vhodném časovém intervalu. Další informace najdete v tématu [Plánovaná údržba pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Pokud chcete snížit dopad výpadků kvůli jedné nebo několika takovým událostem, doporučujeme pro vaše virtuální počítače následující osvědčené postupy z hlediska vysoké dostupnosti:

* [Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]
* [Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]
* [Použití naplánovaných událostí k proaktivní reakci na události ovlivňující virtuální min.](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]
* [Zkombinujte balanceru zatížení se sadami dostupnosti]
* [Použití zón dostupnosti k ochraně před selháním na úrovni datového centra]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Použití zón dostupnosti k ochraně před selháním na úrovni datového centra

[Zóny dostupnosti](../articles/availability-zones/az-overview.md) rozšiřují úroveň kontroly, kterou potřebujete k zachování dostupnosti aplikací a dat na virtuálních počítačích. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Pro zajištění odolnosti jsou minimálně tři samostatné zóny ve všech povolených oblastech. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháním datového centra. Zónově redundantní služby replikují vaše aplikace a data napříč zónami dostupnosti, aby byly chráněny před jediným bodem selhání.

Zóna dostupnosti v oblasti Azure je kombinací **domény selhání** a **aktualizační domény**. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, vaše virtuální počítače se efektivně distribuují mezi tři domény selhání a tři aktualizační domény. Platforma Azure rozpoznává tuto distribuci mezi aktualizačními doménami, aby se ujistila, že virtuální počítače v různých zónách nejsou aktualizovány současně.

Díky zónám dostupnosti nabízí Azure nejlepší 99,99% dostupnost služby SLA virtuálního počítače. Tím, že navrhnete řešení pro použití replikovaných virtuálních počítačů v zónách, můžete chránit vaše aplikace a data před ztrátou datového centra. Pokud dojde k ohrožení zabezpečení jedné zóny, replikované aplikace a data budou okamžitě k dispozici v jiné zóně.

![Zóny dostupnosti](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Přečtěte si další informace o nasazení virtuálního počítače [se systémem Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) v zóně dostupnosti.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance
Skupiny dostupnosti jsou další konfigurace datového centra, která poskytuje redundanci a dostupnost virtuálních počítačů. Tato konfigurace v rámci datového centra zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje 99,95% azure sla. Další informace najdete v tématu [SLA pro virtuální počítače](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Virtuální počítač s jednou instancí v samotné sadě dostupnosti by měl používat premium ssd nebo ultra disk pro všechny disky operačního systému a datové disky, aby se kvalifikoval pro sla pro připojení virtuálního počítače alespoň 99,9 %.

Základní platforma Azure každému virtuálnímu počítači ve skupině dostupnosti přiřadí **aktualizační doménu** a **doménu selhání**. Dané skupině dostupnosti se ve výchozím nastavení přiřadí pět aktualizačních domén (u nasazení podle modelu Resource Manager je pak možné počet aktualizačních domén navýšit až na 20), které uživatel nemůže konfigurovat a které představují skupiny virtuálních počítačů a základního fyzického hardwaru, které lze restartovat současně. Pokud je v rámci jedné skupiny dostupnosti nakonfigurováno více než 5 virtuálních počítačů, šestý virtuální počítač se umístí do stejné aktualizační domény jako první virtuální počítač, sedmý se umístí do stejné aktualizační domény jako druhý atd. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména. Restartovaná aktualizační doména má 30 minut na zotavení, než se zahájí údržba na jiné aktualizační doméně.

Domény selhání definují skupinu virtuálních počítačů, které sdílejí společný zdroj napájení a síťový přepínač. Ve výchozím nastavení jsou virtuální počítače konfigurované v rámci skupiny dostupnosti rozdělené až do tří domén selhání pro nasazení podle modelu Resource Manager (do dvou domén selhání pro model nasazení Classic). Přestože umístění virtuálních počítačů do skupiny dostupnosti neochrání vaši aplikaci před selháním operačního systému nebo selháním spojeným s konkrétní aplikací, omezí se tím dopad potenciálních selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

<!--Image reference-->
   ![Koncepční nákres konfigurace aktualizačních domén a domén selhání](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti
Pokud aktuálně používáte virtuální počítače s nespravovanými disky, důrazně doporučujeme [převést virtuální počítače ve skupině dostupnosti na používání spravovaných disků](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Spravované disky](../articles/virtual-machines/windows/managed-disks-overview.md) poskytují vyšší spolehlivost skupiny dostupnosti tím, že zajišťují dostatečné oddělení jednotlivých disků virtuálních počítačů ve skupině dostupnosti, aby se zabránilo jedinému bodu selhání. Je to tím, že automaticky umístí disky v různých doménách selhání úložiště (clustery úložiště) a jejich zarovnání s doménou selhání virtuálního počítače. Pokud se doména selhání úložiště nezdaří z důvodu selhání hardwaru nebo softwaru, selže pouze instance virtuálního počítače s disky v doméně selhání úložiště.
![Spravované disky FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Počet domén selhání pro spravované skupiny dostupnosti se liší podle oblasti – buď dvě, nebo tři na oblast. Doménu selhání pro každou oblast můžete zobrazit spuštěním následujících skriptů.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Za určitých okolností 2 virtuální chodů ve stejné dostupnostiSet může sdílet stejné FaultDomain. To lze potvrdit tak, že přejdete do skupiny dostupnosti a zkontrolujete sloupec **Doména selhání.**
> To může být příčinou z následující sekvencí při nasazování virtuálních stránek:
> - Nasazení prvního virtuálního mon.
> - Zastavit/navrátit první virtuální můlení
> - Nasazení druhého virtuálního počítače Za těchto okolností může být disk operačního systému druhého virtuálního počítače vytvořen na stejné doméně selhání jako první virtuální počítač, a proto druhý virtuální počítač bude také přistát na stejné doméně faultdomain. 
> Chcete-li se tomuto problému vyhnout, doporučujeme nezastavit nebo navrátit virtuální počítače mezi nasazeními.

Pokud plánujete používat virtuální počítače s nespravovanými disky, postupujte podle následujících doporučených postupů pro účty úložiště, kde jsou virtuální pevné disky (VD) virtuálních počítačů uloženy jako [objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Uchovávejte všechny disky (s operačním systémem i s daty) přidružené k virtuálnímu počítači ve stejném účtu úložiště.**
2. **Zkontrolujte [omezení](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) počtu nespravovaných disků v účtu Azure Storage** před přidáním dalších virtuálních disků do účtu úložiště
3. **Použijte samostatný účet úložiště pro každý virtuální virtuální účet v sadě dostupnosti.** Nesdílejte účty služby Storage mezi více virtuálními počítači ve stejné skupině dostupnosti. Je přijatelné, aby virtuální počítače v různých sadách dostupnosti sdílely účty úložiště, pokud se používají ![výše uvedené doporučené postupy FD s nespravovaných disků](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Použití naplánovaných událostí k proaktivní reakci na události ovlivňující virtuální min.

Když se přihlásíte k odběru [naplánovaných událostí](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), bude váš virtuální počítač upozorňován na nadcházející události údržby, které můžou mít vliv na váš virtuální počítač. Pokud jsou povoleny naplánované události, virtuální počítač má minimální množství času před provedením aktivity údržby. Například aktualizace hostitelského operačního systému, které mohou mít vliv na váš virtuální počítač, jsou zařazeny do fronty jako události, které určují dopad, a také čas, kdy bude provedena údržba, pokud nebude provedena žádná akce. Události plánu jsou také zařazeny do fronty, když Azure zjistí hrozící selhání hardwaru, které by mohly mít vliv na váš virtuální počítač, což vám umožní rozhodnout, kdy má být retušování provedeno. Zákazníci mohou událost použít k provádění úkolů před údržbou, jako je například ukládání stavu, převzetí selhání do sekundárního a tak dále. Po dokončení logiky pro řádné zpracování události údržby můžete schválit nevyřízené naplánované události povolit platformě pokračovat v údržbě.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Konfigurace každé aplikační vrstvy do samostatných zón dostupnosti nebo sad dostupnosti
Pokud jsou všechny vaše virtuální počítače téměř identické a slouží stejnému účelu pro vaši aplikaci, doporučujeme nakonfigurovat zónu dostupnosti nebo sadu dostupnosti pro každou úroveň vaší aplikace.  Pokud umístíte dvě různé úrovně ve stejné zóně nebo sadě dostupnosti, všechny virtuální počítače ve stejné aplikační vrstvě lze restartovat najednou. Konfigurací alespoň dvou virtuálních počítačů v zóně dostupnosti nebo nastavených pro každou úroveň zaručujete, že je k dispozici alespoň jeden virtuální počítač v každé vrstvě.

Například můžete umístit všechny virtuální počítače v přední části aplikace se systémem IIS, Apache a Nginx v jedné zóně dostupnosti nebo nastavit. Ujistěte se, že pouze front-end virtuální počítače jsou umístěny ve stejné zóně dostupnosti nebo nastavit. Podobně se ujistěte, že pouze virtuální počítače datové vrstvy jsou umístěny v jejich vlastní zóny dostupnosti nebo nastavit, jako jsou vaše replikované sql server virtuální počítače nebo virtuální počítače MySQL.

<!--Image reference-->
   ![Úrovně aplikace](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Zkombinujte vyvažovač zatížení s dostupnostními zónami nebo sadami
Zkombinujte [Azure BalanceR](../articles/load-balancer/load-balancer-overview.md) s zónou dostupnosti nebo nastavte získat co největší odolnost aplikace. Azure Load Balancer rozděluje provoz mezi víc virtuálních počítačů. Azure Load Balancer je součástí virtuálních počítačů úrovně Standard. Ne všechny úrovně virtuálních počítačů zahrnují nástroj Azure Load Balancer. Další informace o vyrovnávání zatížení virtuálních počítačů najdete v článku [Vyrovnávání zatížení virtuálních počítačů](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Pokud nástroj pro vyrovnávání zatížení není nakonfigurovaný k vyrovnávání provozu mezi víc virtuálních počítačů, všechny plánované události údržby ovlivní jediný virtuální počítač obsluhující provoz a způsobí výpadek vrstvy aplikace. Umístěním více virtuálních počítačů na stejné vrstvě aplikace do stejné skupiny dostupnosti a pod jeden nástroj pro vyrovnávání zatížení umožníte nepřetržitou obsluhu provozu alespoň jednou instancí.

Kurz o tom, jak vyvážit zatížení mezi zónami dostupnosti, najdete v [tématu vyrovnávání zatížení virtuálních počítačích ve všech zónách dostupnosti pomocí Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Konfigurace více virtuálních počítačů ve skupině dostupnosti pro zajištění redundance]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Zkombinujte balanceru zatížení se sadami dostupnosti]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Použití spravovaných disků pro virtuální počítače ve skupině dostupnosti]: #use-managed-disks-for-vms-in-an-availability-set
[Použití zón dostupnosti k ochraně před selháním na úrovni datového centra]: #use-availability-zones-to-protect-from-datacenter-level-failures
