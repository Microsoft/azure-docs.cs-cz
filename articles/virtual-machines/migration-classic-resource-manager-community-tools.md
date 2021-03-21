---
title: Komunitní nástroje – přesunutí klasických prostředků do Azure Resource Manager
description: Tento článek obsahuje katalog nástrojů poskytovaných komunitou, které vám pomůžou migrovat prostředky IaaS z modelu nasazení Classic na model nasazení Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f165acb72fdf881a0828c38db577be1f8741384e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674747"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Komunitní nástroje pro migraci prostředků IaaS z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).

Tento článek obsahuje katalog nástrojů poskytovaných komunitou pro pomoc s migrací prostředků IaaS z modelu Classic na model nasazení Azure Resource Manager.

> [!NOTE]
> Podpora Microsoftu tyto nástroje oficiálně nepodporují. Proto jsou otevřené na GitHubu a máme rádi přijmout PR pro opravy nebo další scénáře. K nahlášení problému použijte funkci problémů GitHubu.
> 
> Migrace pomocí těchto nástrojů způsobí výpadek pro klasický virtuální počítač. Pokud hledáte migraci podporovanou platformou, navštivte 
> 
>   * [Podporovaná platforma migrace prostředků IaaS z modelu Classic na Azure Resource Manager Stack](migration-classic-resource-manager-overview.md)
>   * [Migrace s technickou hloubkovou Podrobněou na platformě podporovanou migrací z klasického na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
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

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)
