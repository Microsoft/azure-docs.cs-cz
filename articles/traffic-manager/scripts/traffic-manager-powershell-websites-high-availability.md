---
title: Ukázkový skript Azure Powershellu – směrování provozu pro zajištění vysoké dostupnosti aplikací | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – směrování provozu pro zajištění vysoké dostupnosti aplikace
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 4991be7ecb252001245a0bcde8551d8432b0794b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998497"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Směrování provozu pro zajištění vysoké dostupnosti aplikací pomocí Azure Powershellu

Tento skript vytvoří skupinu prostředků, dva plány služby app service, dvě webové aplikace, profil služby traffic manager a dva koncové body traffic Manageru. Traffic Manager směruje provoz do aplikace v jedné oblasti jako primární oblasti a do sekundární oblasti, když je k dispozici aplikace v primární oblasti. Před spuštěním skriptu, musíte změnit hodnoty MyWebApp, MyWebAppL1 a MyWebAppL2 na jedinečné hodnoty v Azure. Po spuštění skriptu, můžete přístup k aplikaci v primární oblasti s mywebapp.trafficmanager.net adresy URL.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, profilu služby Traffic Manager a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. Podobá se trochu serverové farmy pro vaši webovou aplikaci Azure. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci Azure v rámci plánu služby App Service. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Vytvoří webovou aplikaci Azure v rámci plánu služby App Service. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Vytvoří profil služby Azure Traffic Manager. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Přidá do profilu Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro sítě najdete v [dokumentaci se základními informacemi k sítím Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
