---
title: Ověřte provozu pomocí Azure sítě sledovacích procesů IP toku ověřit - rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje, jak zkontrolovat, pokud provoz nebo z virtuálního počítače povolený nebo zakázaný pomocí rozhraní příkazového řádku Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8681118e55d8ddddf17ebac3bae63486446c70e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Zkontrolujte, jestli je provoz povolen nebo odepřen do nebo z virtuálního počítače s tok ověření IP součást sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Tok IP ověřte je funkce sledovací proces sítě, která vám umožní ověřit, pokud provoz je povolený do nebo z virtuálního počítače. Tento scénář je vhodný pro zjištění aktuálního stavu o tom, jestli virtuální počítač může kontaktovat na externí prostředek nebo back-end. Ověřte toku IP umožňuje ověřit, pokud vaše skupina zabezpečení sítě (NSG) pravidla jsou správně nakonfigurovány a vyřešit toky, kteří jsou Blokovaní pravidla NSG. Dalším důvodem pro použití IP tok ověření, je potřeba zajistit provoz, který chcete blokovat, je správně blokován nastavením NSG.

Tento článek používá 1.0 rozhraní příkazového řádku Azure a platformy, která je dostupná pro Windows, Mac a Linux.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě nebo máte existující instanci sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Tento scénář používá k ověření, pokud virtuální počítač může kontaktovat známé Bing IP adresu IP tok ověření. Pokud je odepřená provoz, vrátí pravidlo zabezpečení, které je odepřen, aby provoz. Další informace o toku ověřte IP, navštivte [přehled tok ověření IP](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Získat virtuální počítač

Tok IP ověřte testy přenosy do nebo z IP adresy na virtuální počítač do nebo z vzdálený cíl. Id virtuálního počítače je vyžadováno pro rutinu. Pokud už znáte ID virtuálního počítače používat, můžete tento krok přeskočit.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Získat síťové karty

IP adresa síťového adaptéru na virtuálním počítači je potřeba. Načtěte síťové adaptéry pro virtuální počítač pomocí příkazu, který následuje dále. Pokud už znáte IP adresu, která chcete testovat na virtuálním počítači, můžete tento krok přeskočit.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Ověření spuštění toku IP

Spustit `network watcher ip-flow-verify` rutiny k otestování provozu. V tomto příkladu se používá první IP adresu prvního síťového adaptéru:

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> Tok IP ověření vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

## <a name="review-results"></a>Zkontrolujte výsledky

Po spuštění `network watcher ip-flow-verify` budou vráceny výsledky, v následujícím příkladu je výsledky vrácené z předchozího kroku.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Další postup

Pokud je blokován provoz a neměl by být, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou definovány.

Naučte se audit nastavení NSG navštivte stránky [auditování zabezpečení sítě skupiny (NSG) s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
