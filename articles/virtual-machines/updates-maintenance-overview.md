---
title: Přehled aktualizací a údržby
description: Seznamte se s možnostmi aktualizace a údržby dostupnými u virtuálních počítačů v Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022248"
---
# <a name="updates-and-maintenance-overview"></a>Přehled aktualizací a údržby
Tento článek poskytuje přehled různých možností aktualizace a údržby pro virtuální počítače Azure (VM).

## <a name="automatic-os-image-upgrade"></a>Automatický upgrade bitové kopie operačního systému

Povolení [automatických upgradů bitových kopií operačního systému](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) v sadě škálování pomáhá zjednodušit správu aktualizací tím, že bezpečně a automaticky upgraduje disk s operačním systémem pro všechny instance v sadě škálování.

[Automatický upgrade operačního systému](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) má následující vlastnosti:

- Po nakonfigurování se pro sadu škálování bez zásahu uživatele automaticky použije nejnovější bitová kopie operačního systému publikovaná vydavateli imagí.
- Pokaždé, když vydavatel publikuje novou image, upgraduje dávky instancí.
- Integruje se s sondami stavu aplikace a s [rozšířením stavu aplikace](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Funguje pro všechny velikosti virtuálních počítačů a pro image Windows i Linux.
- Automatické upgrady můžete kdykoli odhlásit (upgrady operačního systému je možné iniciovat také ručně).
- Disk s operačním systémem virtuálního počítače se nahradí novým diskem s operačním systémem vytvořeným pomocí nejnovější verze image. Nakonfigurovaná rozšíření a vlastní datové skripty se spouštějí, zatímco jsou zachovány trvalé datové disky.
- [Sekvence rozšíření](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) je podporovaná.
- Automatickou aktualizaci image operačního systému lze povolit v sadě škálování libovolné velikosti.


## <a name="automatic-vm-guest-patching-preview"></a>Automatické opravy hosta virtuálního počítače (Preview)

Povolení [Automatické opravy hosta virtuálního počítače](automatic-vm-guest-patching.md) pro vaše virtuální počítače Azure usnadňuje správu aktualizací tím, že bezpečně a automaticky opraví virtuální počítače, aby se zachovalo dodržování předpisů zabezpečení.

[Automatické opravy hosta virtuálního počítače](automatic-vm-guest-patching.md) mají následující vlastnosti:
- Opravy klasifikované jako *kritické* nebo *zabezpečení* se automaticky stáhnou a aplikují na virtuálním počítači.
- Opravy se aplikují v době mimo špičku v časovém pásmu virtuálního počítače.
- Orchestrace opravy se spravuje v Azure a opravy se používají po [principech dostupnosti – první](automatic-vm-guest-patching.md#availability-first-patching).
- Stav virtuálního počítače, jak je určený prostřednictvím signálů stavu platformy, se monitoruje, aby se zjistilo selhání oprav.
- Funguje pro všechny velikosti virtuálních počítačů.


## <a name="automatic-extension-upgrade-preview"></a>Automatický upgrade rozšíření (Preview)

[Automatický upgrade rozšíření](automatic-extension-upgrade.md) je k dispozici ve verzi Preview pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Pokud je na virtuálním počítači nebo v sadě škálování povolený automatický upgrade rozšíření, rozšíření se automaticky upgraduje, kdykoli Vydavatel rozšíření uvolní novou verzi pro toto rozšíření.

 Automatický upgrade rozšíření má následující funkce:
- Podporováno pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets nejsou aktuálně podporovány.
- Upgrady se aplikují v modelu nasazení s první dostupností.
- V případě sady škálování virtuálních počítačů se v jedné dávce upgraduje maximálně 20% virtuálních počítačů sady škálování. Minimální velikost dávky je jeden virtuální počítač.
- Funguje pro všechny velikosti virtuálních počítačů a pro rozšíření systému Windows i Linux.
- Automatické upgrady můžete kdykoli odhlásit.
- Automatickou aktualizaci rozšíření lze povolit na Virtual Machine Scale Sets libovolné velikosti.
- Každé podporované rozšíření je zaregistrované individuálně a můžete zvolit, která rozšíření se budou automaticky upgradovat.
- Podporováno ve všech oblastech veřejného cloudu.

## <a name="hotpatch"></a>Hotpatch 

[Technologie HotPatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) je nový způsob, jak nainstalovat aktualizace na nové virtuální počítače s Windows serverem Azure Edition (VM), které po instalaci nevyžadují restart. Hotpatch pro virtuální počítače s Windows serverem Azure Edition přináší následující výhody:

- Nižší dopad na úlohy s menším restartováním
- Rychlejší nasazení aktualizací, když jsou balíčky menší, je potřeba je nainstalovat rychleji a mít snazší orchestraci oprav pomocí Azure Update Manageru
- Lepší ochrana, protože balíčky aktualizací hotpatch jsou vymezeny na aktualizace zabezpečení systému Windows, které se instalují rychleji bez restartování


## <a name="azure-update-management"></a>Správa aktualizací Azure

[Update Management v Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) můžete použít ke správě aktualizací operačního systému pro virtuální počítače s Windows a Linux v Azure, v místních prostředích a v dalších cloudových prostředích. Můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro servery.

## <a name="maintenance-control"></a>Řízení údržby

Spravujte aktualizace platforem, které nevyžadují restart, pomocí [řízení údržby](maintenance-control.md). Azure často aktualizuje svoji infrastrukturu, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako jsou hraní her, streamování médií a finanční transakce, neumožňují tolerovat zamrznutí nebo odpojení virtuálních počítačů za účelem údržby ani pár sekund. Řízení údržby vám dává možnost počkat na aktualizace platformy a použít ji v průběhu 35ého okna. 

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure.

Pomocí [řízení údržby](maintenance-control.md)můžete:
- Dávka se aktualizuje do jednoho balíčku aktualizace.
- Počkejte až 35 dní, než se aktualizace použijí. 
- Automatizujte aktualizace platforem konfigurací plánu údržby nebo pomocí [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Konfigurace údržby pracují v rámci předplatných a skupin prostředků. 


## <a name="scheduled-events"></a>Naplánované události

Scheduled Events je Azure Metadata Service, který umožňuje čas vaší aplikace připravit se na údržbu virtuálních počítačů. Poskytuje informace o nadcházejících událostech údržby (například restartování), aby se aplikace mohla připravit na jejich přerušení a omezit jejich přerušení. Je k dispozici pro všechny typy Azure Virtual Machines, včetně PaaS a IaaS v systémech Windows i Linux. 

Informace o Scheduled Events najdete v tématu [Scheduled Events pro virtuální počítače s Windows](./windows/scheduled-events.md) a [Scheduled Events pro Linux](./linux/scheduled-events.md) .

## <a name="next-steps"></a>Další kroky

Další způsoby, jak prodloužit dobu provozu vašich aplikací a služeb, najdete v dokumentaci k [dostupnosti a škálování](availability.md) . 