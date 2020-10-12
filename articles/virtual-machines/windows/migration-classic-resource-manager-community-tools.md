---
title: Komunitní nástroje – přesunutí klasických prostředků do Azure Resource Manager
description: Tento článek obsahuje katalog nástrojů poskytovaných komunitou, které vám pomůžou migrovat prostředky IaaS z modelu nasazení Classic na model nasazení Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: afc20a39a5b426f37d6a6752056e3dd35dd8438a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88504955"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Komunitní nástroje pro migraci prostředků IaaS z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](../classic-vm-deprecation.md#how-does-this-affect-me).

Tento článek obsahuje katalog nástrojů poskytovaných komunitou pro pomoc s migrací prostředků IaaS z modelu Classic na model nasazení Azure Resource Manager.

> [!NOTE]
> Podpora Microsoftu tyto nástroje oficiálně nepodporují. Proto jsou otevřené na GitHubu a máme rádi přijmout PR pro opravy nebo další scénáře. K nahlášení problému použijte funkci problémů GitHubu.
> 
> Migrace pomocí těchto nástrojů způsobí výpadek pro klasický virtuální počítač. Pokud hledáte migraci podporovanou platformou, navštivte 
> 
>   * [Podporovaná platforma migrace prostředků IaaS z modelu Classic na Azure Resource Manager Stack](migration-classic-resource-manager-overview.md)
>   * [Migrace s technickou hloubkovou Podrobněou na platformě podporovanou migrací z klasického na Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md)
>   * [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Jedná se o kolekci pomocných nástrojů vytvořených v rámci migrace Enterprise z Azure Service Management na Azure Resource Manager. Tento nástroj umožňuje replikovat infrastrukturu do jiného předplatného, které se dá použít k testování migrace a odstranění potíží před spuštěním migrace v produkčním předplatném.

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz je další možnost migrace kompletní sady klasických prostředků IaaS na prostředky Azure Resource Manager IaaS. K migraci může dojít v rámci stejného předplatného nebo mezi různými předplatnými a typy předplatného (např.: předplatná CSP).

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
