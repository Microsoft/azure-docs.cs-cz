---
title: Analyzovat zabezpečení sítě s Azure sítě sledovacích procesů zabezpečení skupiny zobrazení – 1.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje postup použití Azure CLI 1.0 analyzovat zabezpečení virtuálních počítačů s zobrazení skupiny zabezpečení.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23862828"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analýza zabezpečení vašeho virtuálního počítače s zobrazení skupiny zabezpečení pomocí Azure CLI 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Zobrazení skupiny zabezpečení vrátí pravidla zabezpečení sítě nakonfigurované a efektivní, která se použijí k virtuálnímu počítači. Tato možnost je užitečná k auditování a diagnostice skupin zabezpečení sítě a pravidel, které jsou nakonfigurované na virtuálním počítači zajistit provoz se správně povolený nebo zakázaný. V tomto článku jsme ukazují, jak načíst pravidla zabezpečení nakonfigurované a efektivní k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure

Tento článek používá 1.0 rozhraní příkazového řádku Azure a platformy, která je dostupná pro Windows, Mac a Linux. Sledovací proces sítě aktuálně používá pro podporu rozhraní příkazového řádku Azure CLI 1.0.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku načte pravidla nakonfigurované a efektivní zabezpečení pro daný virtuální počítač.

## <a name="get-a-vm"></a>Získat virtuální počítač

Virtuální počítač je potřeba spustit `vm list` rutiny. Následující příkaz zobrazí virtuální machinese ve skupině prostředků:

```azurecli
azure vm list -g resourceGroupName
```

Jakmile znáte virtuální počítač, můžete použít `vm show` rutiny jeho Id prostředku:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Načtení zobrazení skupiny zabezpečení

Dalším krokem je načíst výsledky zobrazení skupiny zabezpečení. Přidávání "--json" příznak naformátuje výsledky ve formátu json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Zobrazení výsledků

V následujícím příkladu je zkrácení odpověď výsledky vrácené. Výsledky zobrazit všechna pravidla zabezpečení efektivní a použitých na virtuálním počítači rozdělení v skupiny **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, a **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Navštivte [auditování zabezpečení sítě skupiny (NSG) s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md) se dozvíte, jak automatizovat ověření skupin zabezpečení sítě.

Další informace o zabezpečení pravidla, která se použijí k síťovým prostředkům, navštivte stránky [přehled zobrazení skupiny zabezpečení](network-watcher-security-group-view-overview.md)
