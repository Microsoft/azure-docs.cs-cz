---
title: Poradce při potížích s připojením – portál Azure
titleSuffix: Azure Network Watcher
description: Naučte se používat možnosti řešení potíží s připojením Azure Network Watcher pomocí portálu Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283235"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Poradce při potížích s připojením pomocí Azure Network Watcher pomocí portálu Azure

> [!div class="op_single_selector"]
> - [Portál](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Zjistěte, jak pomocí řešení potíží s připojením ověřit, zda lze navázat přímé připojení TCP z virtuálního počítače k danému koncovému bodu.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující zdroje:

* Instance sledovacího programu sítě v oblasti, ve které chcete vyřešit potíže s připojením.
* Virtuální počítače pro řešení potíží s připojením.

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, `AzureNetworkWatcherExtension` aby v ym, který řešíte z má rozšíření virtuálního zařízení nainstalován. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro virtuální počítač SIP na stránce Rozšíření [virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozšíření není vyžadováno v cílovém koncovém bodě.

## <a name="check-connectivity-to-a-virtual-machine"></a>Kontrola připojení k virtuálnímu počítači

Tento příklad kontroluje připojení k cílovému virtuálnímu počítači přes port 80.

Přejděte na objekt sledování sítě a klepněte na tlačítko **Poradce při potížích s připojením**. Vyberte virtuální počítač, ze který chcete zkontrolovat připojení. V části **Cíl** **zvolte Vyberte virtuální počítač** a zvolte správný virtuální počítač a port, který chcete otestovat.

Po klepnutí na tlačítko **Zkontrolovat**se zkontroluje připojení mezi virtuálními počítači na určeném portu. V příkladu je cílový virtuální virtuální měnový virtuální mohl být nedostupný, zobrazí se seznam směrování.

![Kontrola výsledků připojení pro virtuální počítač][1]

## <a name="check-remote-endpoint-connectivity"></a>Kontrola vzdáleného připojení koncového bodu

Chcete-li zkontrolovat připojení a latenci vzdáleného koncového bodu, zvolte **ručně přepínací** tlačítko Určit v části **Cíl,** zadejte adresu URL a port a klepněte na tlačítko **Zkontrolovat**.  Používá se pro vzdálené koncové body, jako jsou weby a koncové body úložiště.

![Kontrola výsledků připojení pro web][2]

## <a name="next-steps"></a>Další kroky

Zjistěte, jak automatizovat zachytávání paketů pomocí upozornění virtuálního počítače zobrazením [zobrazení vytvoření záznamu aktivovaného zachycení paketu](network-watcher-alert-triggered-packet-capture.md)

Zjistěte, jestli je určitý provoz povolený do nebo z vašeho virtuálního počítače, a to najdete na [stránce Kontrola toku IP](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png