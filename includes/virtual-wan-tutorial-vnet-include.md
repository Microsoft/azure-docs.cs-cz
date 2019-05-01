---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744031"
---
Rychle vytvořit virtuální síť, že můžete kliknete na tlačítko "Vyzkoušet" v tomto článku, otevřete konzolu Powershellu. Upravte hodnoty, potom příkazy zkopírujte a vložte je do okna konzoly. Další informace o novém modulu Az a kompatibility AzureRM najdete v tématu [představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az). Pokyny k instalaci modulu Az, naleznete v tématu [instalace Azure Powershellu](/powershell/azure/install-az-ps).

Nezapomeňte si ověřit, že se adresní prostor pro virtuální síť, kterou vytvoříte, nepřekrývá s žádným z rozsahů adres pro jiné virtuální sítě, ke kterým se chcete připojit, ani s adresními prostory místní sítě.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud ještě nemáte skupinu prostředků, kterou chcete použít, vytvořte novou. Upravte příkazy prostředí PowerShell tak, aby odrážely název skupiny prostředků, že kterou chcete použít, a spusťte následující rutinu:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Upravte příkazy prostředí PowerShell k vytvoření virtuální sítě, která je kompatibilní pro vaše prostředí.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```