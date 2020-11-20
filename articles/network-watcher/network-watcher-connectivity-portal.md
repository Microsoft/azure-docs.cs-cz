---
title: Řešení potíží s připojením – Azure Portal
titleSuffix: Azure Network Watcher
description: Naučte se používat funkce řešení potíží s připojením v Azure Network Watcher pomocí Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: 115adb7a71d820a75261837f4c14b1b84adb98da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965523"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Řešení potíží s připojením k Azure Network Watcher pomocí Azure Portal

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Naučte se používat řešení potíží s připojením k ověření, jestli je možné navázat přímé připojení TCP z virtuálního počítače do daného koncového bodu.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte následující zdroje:

* Instance Network Watcher v oblasti, ve které chcete řešit potíže s připojením.
* Virtuální počítače pro řešení potíží s připojeními.

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, aby virtuální počítač, ze kterého řešení řešíte, byl `AzureNetworkWatcherExtension` nainstalován rozšíření virtuálního počítače. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%252fazure%252fnetwork-watcher%252ftoc.json). V cílovém koncovém bodě není rozšíření vyžadováno.

## <a name="check-connectivity-to-a-virtual-machine"></a>Ověřte připojení k virtuálnímu počítači.

Tento příklad zkontroluje připojení k cílovému virtuálnímu počítači přes port 80.

Přejděte do Network Watcher a klikněte na **řešení potíží s připojením**. Vyberte virtuální počítač, ze kterého se má kontrolovat připojení. V části **cíl** zvolte **Vybrat virtuální počítač** a zvolte správný virtuální počítač a port, který chcete otestovat.

Po kliknutí na **zkontrolovat** se zkontroluje připojení mezi virtuálními počítači na daném portu. V tomto příkladu je cílový virtuální počítač nedosažitelný, zobrazí se seznam směrování.

![Podívejte se na výsledky připojení pro virtuální počítač.][1]

## <a name="check-remote-endpoint-connectivity"></a>Kontrolovat připojení vzdáleného koncového bodu

Pokud chcete zjistit konektivitu a latenci pro vzdálený koncový bod, zvolte v části **cíl** možnost **zadat ručně** přepínací tlačítko, zadejte adresu URL a port a klikněte na tlačítko **ověřit**.  Používá se pro vzdálené koncové body, jako jsou weby a koncové body úložiště.

![Kontroluje výsledky připojení pro web.][2]

## <a name="next-steps"></a>Další kroky

Podívejte se, jak automatizovat zachycení paketů s výstrahami virtuálních počítačů zobrazením [Vytvoření výstrahy aktivované zachytávání paketů](network-watcher-alert-triggered-packet-capture.md) .

Zjistěte, jestli je na VIRTUÁLNÍm počítači povolený určitý provoz, a to tak, že navštíví [kontrolu toku IP](diagnose-vm-network-traffic-filtering-problem.md) .

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png