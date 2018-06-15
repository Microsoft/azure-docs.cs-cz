---
title: Vytvoření internetového nástroje pro vyrovnávání zatížení – Azure PowerShell Classic | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení v režimu Classic pomocí prostředí PowerShell
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 07d3658ff86a46875a57cb3359a60661911e0c8b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067584"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení (Classic) v prostředí PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Než začnete pracovat s prostředky Azure, je potřeba si uvědomit, že Azure má v současné době dva modely nasazení: Azure Resource Manager a klasický. Před zahájením práce s jakýmikoli prostředky Azure se ujistěte, že rozumíte [modelům nasazení a příslušným nástrojům](../azure-classic-rm.md). Dokumentaci k různým nástrojům můžete zobrazit kliknutím na karty v horní části tohoto článku. Tento článek se týká modelu nasazení Classic. Případně [zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Nastavení nástroje pro vyrovnávání zatížení pomocí prostředí PowerShell

Pokud chcete nastavit nástroj pro vyrovnávání zatížení pomocí PowerShellu, proveďte následující kroky:

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, přejděte na téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) a proveďte všechny pokyny, abyste se mohli přihlásit k Azure a vybrat své předplatné.
2. Po vytvoření virtuálního počítače můžete pomocí rutin prostředí PowerShell přidat nástroj pro vyrovnávání zatížení do virtuálního počítače v rámci stejné cloudové služby.

V následujícím příkladu přidáte sadu nástroje pro vyrovnávání zatížení s názvem webfarm do cloudové služby mytestcloud (nebo mytestcloud.cloudapp.net). Zároveň pro nástroj pro vyrovnávání zatížení přidáte koncové body do virtuálních počítačů s názvy web1 a web2. Nástroj pro vyrovnávání zatížení přijímá síťový provoz na portu 80 a vyrovnává zatížení mezi virtuální počítače definované místním koncovým bodem (v tomto případě portem 80) pomocí protokolu TCP.

### <a name="step-1"></a>Krok 1

Vytvořte koncový bod s vyrovnáváním zatížení pro první virtuální počítač web1.

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Krok 2

Vytvořte další koncový bod pro druhý virtuální počítač web2 a použijte k tomu stejný název sady nástroje pro vyrovnávání zatížení.

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Odebrání virtuálního počítače z nástroje pro vyrovnávání zatížení

K odebrání koncového bodu virtuálního počítače z nástroje pro vyrovnávání zatížení můžete použít příkaz Remove-AzureEndpoint.

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Další kroky

Můžete také [začít vytvářet interní nástroj pro vyrovnávání zatížení](load-balancer-get-started-ilb-classic-ps.md) a nakonfigurovat, jaký typ [distribučního režimu](load-balancer-distribution-mode.md) se má použít pro konkrétní chování nástroje pro vyrovnávání zatížení síťového provozu.

Pokud vaše aplikace potřebuje udržovat aktivní připojení serverů, které jsou za nástrojem pro vyrovnávání zatížení, můžete zjistit více o [nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md). Pomůže vám to pochopit chování nečinného připojení při používání služby Azure Load Balancer.
