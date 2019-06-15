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
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077522"
---
Rychle vytvořit virtuální síť, že můžete kliknete na tlačítko "Vyzkoušet" v tomto článku, otevřete konzolu Powershellu ve službě Azure Cloud Shell. Upravte hodnoty, potom příkazy zkopírujte a vložte je do okna konzoly. 

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
