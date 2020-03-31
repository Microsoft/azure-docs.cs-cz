---
title: Analýza zabezpečení sítě pomocí zobrazení skupiny zabezpečení – Azure CLI
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak pomocí azure cli analyzovat zabezpečení virtuálních počítačů pomocí zobrazení skupiny zabezpečení.
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
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840771"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analýza zabezpečení virtuálního počítače pomocí zobrazení skupiny zabezpečení pomocí azure cli

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-security-group-view-rest.md)

Zobrazení skupiny zabezpečení vrátí nakonfigurovaná a efektivní pravidla zabezpečení sítě, která jsou použita pro virtuální počítač. Tato funkce je užitečná pro audit a diagnostiku skupin zabezpečení sítě a pravidel, která jsou nakonfigurována na virtuálním počítači, aby bylo zajištěno, že provoz je správně povolen nebo odepřen. V tomto článku vám ukážeme, jak načíst nakonfigurovaná a efektivní pravidla zabezpečení do virtuálního počítače pomocí azure CLI

Chcete-li provést kroky v tomto článku, je třeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě.

## <a name="scenario"></a>Scénář

Scénář, který je popsán v tomto článku načte nakonfigurovaná a efektivní pravidla zabezpečení pro daný virtuální počítač.

## <a name="get-a-vm"></a>Získání virtuálního virtuálního mě

Ke spuštění rutiny `vm list` je nutný virtuální počítač. Následující příkaz uvádí virtuální počítače ve skupině prostředků:

```azurecli
az vm list -resource-group resourceGroupName
```

Jakmile znáte virtuální počítač, můžete `vm show` pomocí rutiny získat jeho ID prostředků:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Načíst zobrazení skupiny zabezpečení

Dalším krokem je načtení výsledku zobrazení skupiny zabezpečení.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

Následující příklad je zkrácená odpověď na vrácené výsledky. Výsledky ukazují všechna platná a použitá pravidla zabezpečení virtuálního počítače rozdělená do skupin **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**a **EffectiveSecurityRules**.

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

Navštivte [auditování skupin zabezpečení sítě (NSG) s sledovacím procesem sítě,](network-watcher-nsg-auditing-powershell.md) kde se dozvíte, jak automatizovat ověřování skupin zabezpečení sítě.

Další informace o pravidlech zabezpečení, která jsou použita pro síťové prostředky, najdete v přehledu [zobrazení skupiny zabezpečení.](network-watcher-security-group-view-overview.md)
