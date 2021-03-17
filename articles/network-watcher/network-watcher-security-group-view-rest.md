---
title: Analýza zabezpečení sítě – zobrazení skupiny zabezpečení – Azure REST API
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak REST API Azure analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960576"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Rozhraní API pro zobrazení skupiny zabezpečení již není udržováno a bude brzy zastaralé. Použijte prosím [funkci platná pravidla zabezpečení](./network-watcher-security-group-view-overview.md) , která poskytuje stejné funkce. 

Zobrazení skupiny zabezpečení vrací nakonfigurovaná a efektivní pravidla zabezpečení sítě, která se používají pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel nakonfigurovaných na virtuálním počítači, aby se zajistilo správné povolení nebo odepření provozu. V tomto článku vám ukážeme, jak na virtuálním počítači načíst platná a použitá pravidla zabezpečení pomocí REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři zavoláte rozhraní Network Watcher REST API, abyste získali zobrazení skupiny zabezpečení pro virtuální počítač. ARMclient se používá k volání REST API s využitím PowerShellu. ARMClient se nachází v čokoládě na [ARMClient při čokoládě](https://chocolatey.org/packages/ARMClient) .

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher. Scénář také předpokládá, že je k dispozici skupina prostředků s platným virtuálním počítačem.

## <a name="scenario"></a>Scénář

Scénář popsaný v tomto článku načte platná a použitá pravidla zabezpečení pro daný virtuální počítač.

## <a name="log-in-with-armclient"></a>Přihlášení pomocí ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Spusťte následující skript, který vrátí virtuální machineThe, pro který je nutné použít následující kód:

- **SubscriptionId** – ID předplatného se dá načíst taky pomocí rutiny **Get-AzSubscription** .
- **resourceGroupName** – název skupiny prostředků, která obsahuje virtuální počítače.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Požadované informace jsou **ID** pod typem `Microsoft.Compute/virtualMachines` v odpovědi, jak je vidět v následujícím příkladu:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Získat zobrazení skupiny zabezpečení pro virtuální počítač

Následující příklad požádá o zobrazení skupiny zabezpečení cílového virtuálního počítače. Výsledky z tohoto příkladu můžete použít k porovnání s pravidly a zabezpečením definovaným zdrojem, aby se vyhledala možnost posun konfigurace.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Zobrazit odpověď

Následující ukázka je odpověď vrácená z předchozího příkazu. Ve výsledcích se zobrazí všechna platná a použitá pravidla zabezpečení na virtuálním počítači rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** a **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak automatizovat ověřování skupin zabezpečení sítě, najdete [v tématu auditování skupin zabezpečení sítě (NSG) s Network Watcher](network-watcher-security-group-view-powershell.md) .