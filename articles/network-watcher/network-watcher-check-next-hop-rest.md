---
title: "Najít další segment s síť Azure sledovacích procesů další segment - REST | Microsoft Docs"
description: "Tento článek popisuje, jak můžete najít, co je typ dalšího směrování a ip adresu pomocí dalšího přechodu pomocí REST API služby Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Zjistěte, co typ dalšího směrování je pomocí funkce další směrování v sledovací proces sítě Azure pomocí rozhraní REST API Azure

> [!div class="op_single_selector"]
> - [portál Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-next-hop-rest.md)

Další směrování je funkce sledovací proces sítě, která poskytuje možnost get typ dalšího směrování a IP adresy, které jsou založené na zadaný virtuální počítač. Tato možnost je užitečná při určování, zda prochází odchozího provozu z virtuálního počítače brány, internet nebo virtuální sítě získat do cíle.

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey v [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku používá další směrování, funkce sledovací proces sítě, který vyhledá typ dalšího směrování a IP adresu pro prostředek. Další informace o další směrování, navštivte [další směrování přehled](network-watcher-next-hop-overview.md).

V tomto scénáři provedete následující:

* Načtěte další směrování pro virtuální počítač.

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

Přihlaste se k armclient pomocí svých přihlašovacích údajů Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Spusťte následující skript pro vrácení virtuálního počítače. Tyto informace budete potřebovat pro spuštění dalším místě směrování.

Následující kód potřebuje hodnoty pro následující proměnné:

- **ID předplatného** -Id použít předplatného.
- **Název skupiny prostředků** -název skupiny prostředků, která obsahuje virtuální počítače.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Z výstupu v následujícím id virtuálního počítače se používá v následujícím příkladu:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Získat další směrování

Po vytvoření hlavičku autorizace je možné načíst dalšího přechodu z virtuálního počítače. Pro tento příklad kódu pro práci se musí nahradit následující hodnoty.

> [!Important]
> Pro volání rozhraní API REST sledovací proces sítě, že název skupiny prostředků v identifikátoru URI požadavku je skupina prostředků, který obsahuje sledovací proces sítě ne prostředky provádíte diagnostiky akce na.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Další směrování vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

## <a name="results"></a>Výsledky

Následující fragment kódu je příklad výstupu přijata. Výsledky obsahují následující hodnoty:

* **nextHopType** – tato hodnota je jedním z následujících hodnot: Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway nebo žádný.
* **nextHopIpAddress** – IP adresa dalšího směrování.
* **routeTableId** – hodnota je buď identifikátor uri pro směrovací tabulka přiřazené k postupu, nebo když není uživatelem definované trasy je definována hodnota *systémová trasa* je vrácen.

Níže jsou výsledky ve formátu json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Další kroky

Jakmile jste moct zjistit další směrování pro virtuální počítač, můžete zobrazit zabezpečení síťových prostředků navštívíte [zobrazení zabezpečení – přehled](network-watcher-security-group-view-overview.md)














