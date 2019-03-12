---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552973"
---
Pokud ještě nemáte virtuální síť, můžete si ji rychle vytvořit pomocí PowerShellu. Virtuální síť můžete vytvořit i pomocí webu Azure Portal.

* Nezapomeňte si ověřit, že se adresní prostor pro virtuální síť, kterou vytvoříte, nepřekrývá s žádným z rozsahů adres pro jiné virtuální sítě, ke kterým se chcete připojit, ani s adresními prostory místní sítě. 
* Pokud už virtuální síť máte, ověřte si, jestli splňuje povinná kritéria a nemá bránu virtuální sítě.

Virtuální síť můžete snadno vytvořit, když kliknutím na „Vyzkoušet“ v tomto článku otevřete konzolu PowerShellu. Upravte hodnoty, potom příkazy zkopírujte a vložte je do okna konzoly.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Upravte příkazy PowerShellu a potom vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Upravte příkazy PowerShellu a vytvořte virtuální síť kompatibilní s vaším prostředím.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```