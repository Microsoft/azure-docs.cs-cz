---
title: Komunitní nástroje – přesunutí klasických prostředků do Azure Resource Manager
description: Tento článek obsahuje katalog nástrojů poskytovaných komunitou, které vám pomůžou migrovat prostředky IaaS z modelu nasazení Classic na model nasazení Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: a4a7ed6d57e72deb7f49895935e522e65e18f7d9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033316"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Nástroje komunity pro migraci prostředků IaaS z modelu Classic na Azure Resource Manager
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

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

