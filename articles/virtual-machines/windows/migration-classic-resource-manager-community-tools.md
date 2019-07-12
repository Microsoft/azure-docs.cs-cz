---
title: Nástroje komunity – klasické prostředky přesunout do Azure Resource Manageru | Dokumentace Microsoftu
description: Tento článek obsahuje nástroje, které byly zadány pomocí migrace prostředků IaaS z modelu nasazení classic do modelu nasazení Azure Resource Manageru pomohou.
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
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 63e1ad044204bf7695d274fa46f06523fd9d460f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720277"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Nástroje komunity pro migraci prostředků IaaS z modelu Classic na Azure Resource Manager
Tento článek obsahuje nástroje, které byly zadány komunitou jako pomoc s migrací prostředků IaaS z modelu nasazení classic do modelu nasazení Azure Resource Manageru.

> [!NOTE]
> Tyto nástroje se oficiálně nepodporuje Microsoft Support. Proto jsou open source na Githubu a rádi tak, aby přijímal žádosti o přijetí změn pro opravy nebo další scénáře. Pokud chcete nahlásit problém, použijte funkci problémy Githubu.
> 
> Migrace pomocí těchto nástrojů způsobí prostoje pro klasický virtuální počítač. Pokud hledáte migrace platformy podporované, navštivte 
> 
>   * [Podporovaná platforma migrace prostředků IaaS z modelu nasazení Classic do zásobníku Azure Resource Manageru](migration-classic-resource-manager-overview.md)
>   * [Podrobné technické informace k platformy podporované migrace z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí Azure Powershellu](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Toto je sada nástrojů pomocné rutiny vytvořených jako součást migrace organizace ze správy služeb prostředí Azure do Azure Resource Manageru. Tento nástroj umožňuje replikovat infrastruktury do jiného předplatného, které lze použít pro testování před spuštěním migrace v rámci předplatného produkční migrace a železa případné potíže.

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz je další možnosti můžete migrovat kompletní sadu klasických prostředků IaaS na prostředky IaaS s nástrojem Azure Resource Manageru. Migrace může dojít v rámci stejného předplatného nebo mezi různých předplatných a typy předplatného (např: Předplatná CSP).

[Odkaz na dokumentaci k nástroji](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Další kroky

* [Přehled o platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manager pomocí Powershellu](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manageru pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy o migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

