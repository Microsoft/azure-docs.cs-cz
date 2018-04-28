---
title: Ověřte provoz s IP Adresou sledovací proces sítě Azure tok ověření – prostředí PowerShell | Microsoft Docs
description: Tento článek popisuje, jak zkontrolovat, pokud provoz nebo z virtuálního počítače povolený nebo zakázaný pomocí prostředí PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7c2d5e0811f7a5e1f865992be1d5a2c189f10374
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Zkontrolujte, jestli je povolené nebo zakázané do nebo z virtuálního počítače s tokem IP přenosy ověřte součást sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Tok IP ověřte, že je funkce sledovací proces sítě, která vám umožní ověřit, pokud provoz je povolený do nebo z virtuálního počítače. Tento scénář je vhodný pro zjištění aktuálního stavu o tom, jestli virtuální počítač může kontaktovat na externí prostředek nebo back-end. Ověřte toku IP umožňuje ověřit, pokud vaše skupina zabezpečení sítě (NSG) pravidla jsou správně nakonfigurovány a vyřešit toky, kteří jsou Blokovaní pravidla NSG. Dalším důvodem pro použití IP tok ověření, je potřeba zajistit provoz, který chcete blokovat, je správně blokován nastavením NSG.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě nebo máte existující instanci sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Tento scénář používá IP tok ověření k ověření, pokud virtuální počítač může kontaktovat známé Bing IP adresu. Pokud je odepřená provoz, vrátí pravidlo zabezpečení, které je odepřen, aby provoz. Další informace o toku IP ověřte najdete [IP tok ověření – přehled](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Načtení sledovací proces sítě

Prvním krokem je pro získání instance sledovací proces sítě. `$networkWatcher` Se předá proměnná IP tok ověření rutiny.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Získat virtuální počítač

Tok IP ověřte testy přenosy do nebo z IP adresy na virtuální počítač do nebo z vzdálený cíl. Id virtuálního počítače je vyžadováno pro rutinu. Pokud už znáte ID virtuálního počítače používat, můžete tento krok přeskočit.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Získat síťové karty

IP adresa síťového adaptéru na virtuálním počítači je potřeba. Načtěte síťové adaptéry připojené k virtuálnímu počítači pomocí příkazu, který následuje dále. Pokud už znáte IP adresu, která chcete testovat na virtuálním počítači, můžete tento krok přeskočit.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Ověření spuštění toku IP

Spustit `Test-AzureRmNetworkWatcherIPFlow` rutiny k otestování provozu. V tomto příkladu se používá první IP adresu na první síťový adaptér.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> Tok IP ověření vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

## <a name="review-results"></a>Zkontrolujte výsledky

Po spuštění `Test-AzureRmNetworkWatcherIPFlow` budou vráceny výsledky, v následujícím příkladu je výsledky vrácené z předchozího kroku.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Další postup

Pokud je blokován provoz a neměl by být, najdete v části [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které jsou definovány.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













