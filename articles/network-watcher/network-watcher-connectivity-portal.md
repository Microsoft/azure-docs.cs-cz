---
title: Řešení potíží s připojením pomocí služby Azure Network Watcher – Azure portal | Dokumentace Microsoftu
description: Další informace o použití připojení k řešení potíží s funkce služby Azure Network Watcher pomocí webu Azure portal.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 21e004e12a5111eb0e5fc7764c1e07fcb68c447d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986196"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Řešení potíží s připojením pomocí služby Azure Network Watcher pomocí webu Azure portal

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Rozhraní Azure REST API](network-watcher-connectivity-rest.md)

Zjistěte, jak používat připojení řešení Chcete-li ověřit, zda lze navázat přímé připojení TCP z virtuálního počítače do daného koncového bodu.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte následující prostředky:

* Instance služby Network Watcher v oblasti, kterou chcete k řešení potíží s připojení.
* Virtuální počítače k řešení problémů s připojením s.

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, aby měl řešit z virtuálního počítače `AzureNetworkWatcherExtension` nainstalované rozšíření virtuálního počítače. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Na cílový koncový bod není vyžadován rozšíření.

## <a name="check-connectivity-to-a-virtual-machine"></a>Zkontrolujte připojení k virtuálnímu počítači

Tento příklad kontroluje připojení k cílovému virtuálnímu počítači přes port 80.

Přejděte na Network Watcheru a klikněte na tlačítko **řešení potíží s připojením**. Vyberte virtuální počítač, aby se ověřilo připojení z. V **cílové** zvolte **vyberte virtuální počítač** a zvolte správné virtuálního počítače a port k testování.

Po kliknutí na **zkontrolujte**, připojení mezi virtuálními počítači na zadaný port je zaškrtnuté políčko. V příkladu cílový virtuální počítač nedosažitelný, se zobrazí výpis segmenty směrování.

![Výsledky kontroly připojení u virtuálního počítače][1]

## <a name="check-remote-endpoint-connectivity"></a>Zkontrolujte připojení vzdáleného koncového bodu

Pokud chcete zkontrolovat připojení a latenci na vzdálený koncový bod, vyberte **zadat ručně** přepínač v **cílové** části, zadejte adresu url a port a klikněte na tlačítko **zkontrolujte**.  Používá se pro vzdálené koncové body, jako jsou koncové body pro websites a storage.

![Výsledky kontroly připojení pro webovou stránku][2]

## <a name="next-steps"></a>Další postup

Zjistěte, jak automatizovat zachytávání paketů pomocí virtuálního počítače výstrahy zobrazením [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)

Najít, jestli některé je povolený provoz do nebo ze svého virtuálního počítače návštěvou [ověření toku protokolu IP zkontrolujte](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png