---
title: Analýza zabezpečení sítě – zobrazení skupiny zabezpečení – rozhraní AZURE REST API
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí prostředí PowerShell analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupiny zabezpečení.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840736"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí rozhraní REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-security-group-view-rest.md)

Zobrazení skupiny zabezpečení vrátí nakonfigurovaná a efektivní pravidla zabezpečení sítě, která jsou použita pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel, která jsou nakonfigurována na virtuálním počítači, aby bylo zajištěno, že provoz je správně povolen nebo odepřen. V tomto článku vám ukážeme, jak načíst platná a aplikovaná pravidla zabezpečení do virtuálního počítače pomocí rozhraní REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři zavoláte rozhraní NETWORK Watcher Rest API pro získání zobrazení skupiny zabezpečení pro virtuální počítač. ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě. Scénář také předpokládá, že skupina prostředků s platným virtuálním počítačem existuje pro použití.

## <a name="scenario"></a>Scénář

Scénář, na který se vztahuje tento článek načte efektivní a použitá pravidla zabezpečení pro daný virtuální počítač.

## <a name="log-in-with-armclient"></a>Přihlášení pomocí klienta ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načtení virtuálního počítače

Spusťte následující skript a vraťte virtuální počítačNásledující kód potřebuje proměnné:

- **subscriptionID** – id předplatného lze také načíst pomocí rutiny **Get-AzSubscription.**
- **resourceGroupName** - Název skupiny prostředků, která obsahuje virtuální počítače.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informace, které jsou potřebné, je `Microsoft.Compute/virtualMachines` **id** pod typem v odpovědi, jak je vidět v následujícím příkladu:

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

## <a name="get-security-group-view-for-virtual-machine"></a>Získání zobrazení skupiny zabezpečení pro virtuální počítač

Následující příklad požaduje zobrazení skupiny zabezpečení cílového virtuálního počítače. Výsledky z tohoto příkladu lze porovnat s pravidly a zabezpečení definované původ hledat posun konfigurace.

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

Následující ukázka je odpověď vrácená z předchozího příkazu. Výsledky ukazují všechna platná a použitá pravidla zabezpečení virtuálního počítače rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**a **EffectiveSecurityRules**.

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

Navštivte [auditování skupin zabezpečení sítě (NSG) s sledovacím procesem sítě,](network-watcher-security-group-view-powershell.md) kde se dozvíte, jak automatizovat ověřování skupin zabezpečení sítě.


