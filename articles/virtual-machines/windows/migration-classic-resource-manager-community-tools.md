---
title: Nástroje komunity – přesunutí klasických prostředků do Správce prostředků Azure
description: Tento článek katalogy nástroje, které byly poskytnuty komunitou pomoci migrovat prostředky IaaS z klasické ho do modelu nasazení Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 9839f411458eeb4fd071177ec8208baa94dca3a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866161"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Nástroje komunity pro migraci prostředků IaaS z klasických do Správce prostředků Azure

> [!IMPORTANT]
> Dnes asi 90 % virtuálních počítačích IaaS používá [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). února 2020, klasické virtuální počítačky byly zastaralé a budou plně vyřazeny v březnu 1, 2023. [Další informace]( https://aka.ms/classicvmretirement) o tomto vyřazení a [o tom, jak vás ovlivňuje](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Tento článek katalogy nástroje, které byly poskytnuty komunitou na pomoc s migrací prostředků IaaS z klasické na model nasazení Azure Resource Manager.

> [!NOTE]
> Tyto nástroje nejsou oficiálně podporovány podporou společnosti Microsoft. Proto jsou open sourced na GitHubu a my jsme rádi, že přijímat PR pro opravy nebo další scénáře. Chcete-li nahlásit problém, použijte funkci problémů GitHubu.
> 
> Migrace s těmito nástroji způsobí prostoje klasického virtuálního počítače. Pokud hledáte migraci podporovanou platformou, navštivte 
> 
>   * [Platforma podporovaná migrace prostředků IaaS z klasického zásobníku Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Migrace z Classicu na Azure Resource Manager podporuje podrobné informace o platformě](migration-classic-resource-manager-deep-dive.md)
>   * [Migrace prostředků IaaS z Klasické ho do Azure Resource Managerpomocí Azure PowerShellu](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Toto je kolekce pomocných nástrojů vytvořených jako součást podnikových migrací ze správy služeb Azure do Správce prostředků Azure. Tento nástroj umožňuje replikovat infrastrukturu do jiného předplatného, které lze použít k testování migrace a vyžehlení všech problémů před spuštěním migrace na produkční předplatné.

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz je další možnost migrovat kompletní sadu klasických prostředků IaaS do prostředků Azure Resource Manager IaaS. K migraci může dojít v rámci stejného předplatného nebo mezi různými předplatnými a typy předplatného (například odběry CSP).

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití příkazového příkazového příkazu k migraci prostředků IaaS z klasických do Správce prostředků Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

