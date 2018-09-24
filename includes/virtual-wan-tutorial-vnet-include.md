---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004137"
---
Pokud ještě nemáte virtuální síť, můžete si ji rychle vytvořit pomocí PowerShellu. Virtuální síť můžete vytvořit i pomocí webu Azure Portal.

* Nezapomeňte si ověřit, že se adresní prostor pro virtuální síť, kterou vytvoříte, nepřekrývá s žádným z rozsahů adres pro jiné virtuální sítě, ke kterým se chcete připojit, ani s adresními prostory místní sítě. 
* Pokud už virtuální síť máte, ověřte si, jestli splňuje povinná kritéria a nemá bránu virtuální sítě.

Virtuální síť můžete snadno vytvořit, když kliknutím na „Vyzkoušet“ v tomto článku otevřete konzolu PowerShellu. Upravte hodnoty, potom příkazy zkopírujte a vložte je do okna konzoly.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Upravte příkazy PowerShellu a potom vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Upravte příkazy PowerShellu a vytvořte virtuální síť kompatibilní s vaším prostředím.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```