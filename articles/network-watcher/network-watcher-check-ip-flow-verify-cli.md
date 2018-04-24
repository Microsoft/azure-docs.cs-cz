---
title: Ověřte provozu pomocí Azure sítě sledovacích procesů IP toku ověřit - rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje, jak zkontrolovat, pokud provoz nebo z virtuálního počítače povolený nebo zakázaný pomocí rozhraní příkazového řádku Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b7122b632dca99eba6fae058beb644f945f67872
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Zkontrolujte, jestli je provoz povolen nebo odepřen do nebo z virtuálního počítače s tok ověření IP součást sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Tok IP ověřte je funkce sledovací proces sítě, která vám umožní ověřit, pokud provoz je povolený do nebo z virtuálního počítače. Tento scénář je vhodný pro zjištění aktuálního stavu o tom, jestli virtuální počítač může kontaktovat na externí prostředek nebo back-end. Ověřte toku IP umožňuje ověřit, pokud vaše skupina zabezpečení sítě (NSG) pravidla jsou správně nakonfigurovány a vyřešit toky, kteří jsou Blokovaní pravidla NSG. Dalším důvodem pro použití IP tok ověření, je potřeba zajistit provoz, který chcete blokovat, je správně blokován nastavením NSG.

Tento článek používá naší nové generace rozhraní příkazového řádku pro model nasazení prostředků management, Azure CLI 2.0, která je dostupná pro Windows, Mac a Linux.

Chcete-li provést kroky v tomto článku, je potřeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě nebo máte existující instanci sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Tento scénář používá k ověření, pokud virtuální počítač může kontaktovat známé Bing IP adresu IP tok ověření. Pokud je odepřená provoz, vrátí pravidlo zabezpečení, které je odepřen, aby provoz. Další informace o toku ověřte IP, navštivte [přehled tok ověření IP](network-watcher-ip-flow-verify-overview.md)

## <a name="get-a-vm"></a>Získat virtuální počítač

Tok IP ověřte testy přenosy do nebo z IP adresy na virtuální počítač do nebo z vzdálený cíl. Id virtuálního počítače je vyžadováno pro rutinu. Pokud už znáte ID virtuálního počítače používat, můžete tento krok přeskočit.

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>Získat síťové karty

IP adresa síťového adaptéru na virtuálním počítači je potřeba. Načtěte síťové adaptéry připojené k virtuálnímu počítači pomocí příkazu, který následuje dále. Pokud už znáte IP adresu, která chcete testovat na virtuálním počítači, můžete tento krok přeskočit.

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>Ověření spuštění toku IP

Spustit `az network watcher test-ip-flow` rutiny k otestování provozu. V tomto příkladu se používá první IP adresu prvního síťového adaptéru.

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> Tok IP ověření vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

## <a name="review-results"></a>Zkontrolujte výsledky

Po spuštění `az network watcher test-ip-flow` budou vráceny výsledky, v následujícím příkladu je výsledky vrácené z předchozího kroku.

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>Další postup

Pokud je blokován provoz a neměl by být, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou definovány.

Naučte se audit nastavení NSG navštivte stránky [auditování zabezpečení sítě skupiny (NSG) s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
