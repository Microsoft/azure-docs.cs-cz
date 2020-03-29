---
title: Technický hloubkový procit migrace klasického nástroje do Azure Resource Manageru
description: Podrobné technické informace o migraci prostředků s podporou platformy z klasického modelu nasazení do Správce prostředků Azure.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f3b4a601fe65c4227a5d876fe4db16f253ca160b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915465"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> Dnes asi 90 % virtuálních počítačích IaaS používá [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). února 2020, klasické virtuální počítačky byly zastaralé a budou plně vyřazeny v březnu 1, 2023. [Další informace]( https://aka.ms/classicvmretirement) o tomto vyřazení a [o tom, jak vás ovlivňuje](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Pojďme se hlouběji ponořit do migrace z modelu klasického nasazení Azure do modelu nasazení Azure Resource Manageru. Podíváme se na prostředky na úrovni prostředků a funkcí, které vám pomohou pochopit, jak platforma Azure migruje prostředky mezi dvěma modely nasazení. Další informace najdete v článku oznámení o službě: [Migrace prostředků IaaS podporovaná platformou z klasického na Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití příkazového příkazového příkazu k migraci prostředků IaaS z klasických do Správce prostředků Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Klasická brána VPN do migrace Správce prostředků](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Nástroje komunity pro usnadnění migrace prostředků IaaS z klasických na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
