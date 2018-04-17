---
title: Ověřte provoz s tokem IP sledovací proces sítě Azure ověřit - REST | Microsoft Docs
description: Tento článek popisuje, jak zkontrolovat, pokud je povolené nebo zakázané přenosy do nebo z virtuálního počítače
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c3d38f776db63e777174b7dca8b09a0d19c387e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Zkontrolujte, jestli je povolené nebo zakázané s tokem IP přenosy ověřte součást sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Tok IP ověřte, že je funkce sledovací proces sítě, která vám umožní ověřit, pokud provoz je povolený do nebo z virtuálního počítače. Ověření lze spustit pro příchozí nebo odchozí provoz. Tento scénář je vhodný pro zjištění aktuálního stavu o tom, jestli virtuální počítač může kontaktovat na externí prostředek nebo back-end. Ověřte toku IP umožňuje ověřit, pokud vaše skupina zabezpečení sítě (NSG) pravidla jsou správně nakonfigurovány a vyřešit toky, kteří jsou Blokovaní pravidla NSG. Dalším důvodem pro použití IP tok ověření, je potřeba zajistit provoz, který chcete blokovat, je správně blokován nastavením NSG.

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey v [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

## <a name="scenario"></a>Scénář

Tento scénář používá IP toku ověřte, zda chcete-li ověřit, pokud virtuální počítač může kontaktovat k jinému počítači přes port 443. Pokud je odepřená provoz, vrátí pravidlo zabezpečení, které je odepřen, aby provoz. Další informace o toku IP ověřte naleznete [IP tok ověření – přehled](network-watcher-ip-flow-verify-overview.md)

V tomto scénáři můžete:

* Načíst virtuální počítač
* Ověření volání IP toku
* Zkontrolujte výsledky

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Načíst virtuální počítač

Spusťte následující skript pro vrácení virtuálního počítače. Následující kód potřebuje hodnoty pro proměnné:

* **ID předplatného** -Id použít předplatného.
* **Název skupiny prostředků** -název skupiny prostředků, která obsahuje virtuální počítače.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Informace, které je potřeba je id v části typ `Microsoft.Compute/virtualMachines`. Výsledky by měl vypadat podobně jako následující ukázka kódu:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Volání IP toku ověřte

Následující příklad vytvoří požadavek na ověření přenosy dat pro zadaný virtuální počítač. Odpověď se vrátí, pokud je povolen přenos nebo pokud provoz byl odepřen. Pokud je provoz odepřela také vrátí hodnotu co pravidlo blokuje provoz.

> [!NOTE]
> Tok IP ověření vyžaduje, aby prostředků virtuálního počítače je přidělená.

Skript vyžaduje Id virtuálního počítače a síťový adaptér na virtuálním počítači prostředku. Tyto hodnoty jsou poskytovány tento výstup.

> [!Important]
> Pro všechna volání REST sledovací proces sítě název skupiny prostředků v identifikátoru URI požadavku je ten, který obsahuje sledovací proces sítě instanci, není diagnostiky akce se provádí na prostředky.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Seznámení s výsledky

Odpověď, kterou jste se vrátit poznáte, jestli provoz povolený nebo zakázaný. Odpověď vypadá jako jednu z následujících příkladech:

**Povoleno**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Odepřen**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Další postup

Pokud je blokován provoz a neměl by být, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) Další informace o skupinách zabezpečení sítě.












