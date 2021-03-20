---
title: Analýza zabezpečení sítě pomocí zobrazení skupiny zabezpečení – Azure CLI
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí Azure CLI analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupin zabezpečení.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94948642"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Rozhraní API pro zobrazení skupiny zabezpečení již není udržováno a bude brzy zastaralé. Použijte prosím [funkci platná pravidla zabezpečení](./network-watcher-security-group-view-overview.md) , která poskytuje stejné funkce.


Zobrazení skupiny zabezpečení vrací nakonfigurovaná a efektivní pravidla zabezpečení sítě, která se používají pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel nakonfigurovaných na virtuálním počítači, aby se zajistilo správné povolení nebo odepření provozu. V tomto článku vám ukážeme, jak načíst nakonfigurovaná a platná pravidla zabezpečení pro virtuální počítač pomocí Azure CLI.

K provedení kroků v tomto článku potřebujete [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

## <a name="scenario"></a>Scenario

Scénář popsaný v tomto článku načte nakonfigurovaná a platná pravidla zabezpečení pro daný virtuální počítač.

## <a name="get-a-vm"></a>Získání virtuálního počítače

Ke spuštění rutiny je nutný virtuální počítač `vm list` . Následující příkaz vypíše virtuální počítače ve skupině prostředků:

```azurecli
az vm list -resource-group resourceGroupName
```

Jakmile znáte virtuální počítač, můžete `vm show` k získání ID prostředku použít rutinu:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácená odezva vrácených výsledků. Ve výsledcích se zobrazí všechna platná a použitá pravidla zabezpečení na virtuálním počítači rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** a **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak automatizovat ověřování skupin zabezpečení sítě, najdete [v tématu auditování skupin zabezpečení sítě (NSG) s Network Watcher](network-watcher-nsg-auditing-powershell.md) .

Další informace o pravidlech zabezpečení, která se vztahují na vaše síťové prostředky, najdete v tématu [Přehled zobrazení skupiny zabezpečení.](network-watcher-security-group-view-overview.md)