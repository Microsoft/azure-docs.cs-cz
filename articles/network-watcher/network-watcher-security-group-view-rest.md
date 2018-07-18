---
title: Analýza zabezpečení sítě v Azure Network Watcher zobrazení skupin zabezpečení – rozhraní REST API | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí prostředí PowerShell pro analýzu zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 0fd96f9bd3027568e81e9c56ddb095297c699683
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089770"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analyzovat zabezpečení vašich virtuálních počítačů se zobrazením skupin zabezpečení pomocí rozhraní REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Zobrazení skupin zabezpečení vrátí pravidla zabezpečení sítě nakonfigurovaná a efektivní, které se použijí k virtuálnímu počítači. Tato možnost je užitečná k auditování a diagnostice skupiny zabezpečení sítě a pravidel, které jsou nakonfigurované na virtuálním počítači k zajištění provozu se správně povolený nebo zakázaný. V tomto článku ukážeme, jak načíst pravidla zabezpečení efektivní a použité k virtuálnímu počítači pomocí rozhraní REST API

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři volání Rest API sledovací proces sítě získat zobrazení skupin zabezpečení pro virtuální počítač. ARMclient slouží k volání rozhraní REST API pomocí Powershellu. ARMClient se nachází na chocolatey na [ARMClient v Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénáře popsané v tomto článku načte efektivní a použité zabezpečení pravidla pro daný virtuální počítač.

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Následující kód, spusťte následující skript, který vrátí virtuální machineThe potřebuje proměnné:

- **subscriptionId** – id předplatného můžete získat také pomocí **Get-AzureRMSubscription** rutiny.
- **Název skupiny prostředků** – název skupiny prostředků obsahující virtuální počítače.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informace, které je potřeba je **id** jako typ `Microsoft.Compute/virtualMachines` v odpovědi, jak je znázorněno v následujícím příkladu:

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

## <a name="get-security-group-view-for-virtual-machine"></a>Získat zobrazení skupin zabezpečení pro virtuální počítač

Následující příklad požádá o zobrazení skupin zabezpečení cílových virtuálních počítačů. Výsledky z tohoto příkladu slouží k porovnání s pravidly a zabezpečení určené vzniku hledat odchylky od konfigurace.

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

## <a name="view-the-response"></a>Zobrazení odpovědi

Následující příklad je odpovědi vrácené ve výstupu předchozího příkazu. Ve výsledcích zobrazí všechna pravidla zabezpečení efektivní a použité na virtuálním počítači rozdělené ve skupinách **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, a  **EffectiveSecurityRules**.

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

## <a name="next-steps"></a>Další postup

Navštivte [auditování skupiny zabezpečení sítě (NSG) pomocí služby Network Watcher](network-watcher-security-group-view-powershell.md) postup automatizace ověření skupiny zabezpečení sítě.


