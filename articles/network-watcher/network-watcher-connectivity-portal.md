---
title: Řešení potíží s připojení s sledovací proces sítě Azure - portálu Azure | Microsoft Docs
description: Naučte se používat připojení k řešení potíží s schopností sledovací proces sítě Azure pomocí portálu Azure.
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
ms.openlocfilehash: cf7b71a49b63a95ed535210125120c6b76d9de8f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Poradce při potížích připojení s sledovací proces sítě Azure pomocí portálu Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Rozhraní API Azure REST](network-watcher-connectivity-rest.md)

Další informace o použití připojení řešení Chcete-li ověřit, zda lze vytvořit přímé připojení TCP z virtuálního počítače do daného koncového bodu.

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že máte v následujících zdrojích informací:

* Instance sledovací proces sítě v oblasti, kterou chcete vyřešte potíže připojením.
* Virtuální počítače potíží s připojením s.

> [!IMPORTANT]
> Řešení potíží s připojení vyžaduje, aby řešení z virtuálního počítače `AzureNetworkWatcherExtension` nainstalovat rozšíření virtuálního počítače. Instalaci rozšíření na virtuální počítač s Windows najdete v článku [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a u virtuálního počítače s Linuxem, navštivte [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Na cílovém koncový bod není vyžadován rozšíření.

## <a name="check-connectivity-to-a-virtual-machine"></a>Zkontrolujte připojení k virtuálnímu počítači

Tento příklad zkontroluje připojení k cílovému virtuálnímu počítači přes port 80.

Přejděte do vaší sledovací proces sítě a klikněte na tlačítko **řešení potíží s připojení**. Vyberte virtuální počítač, zkontrolujte připojení z. V **cílové** vyberte **vyberte virtuální počítač** a vyberte správnou virtuálního počítače a port pro testování.

Po kliknutí na tlačítko **zkontrolujte**, je zaškrtnuta možnost připojení mezi virtuálními počítači na zadaný port. V příkladu se cílový počítač nedostupný, zobrazí se seznam všech segmentů směrování.

![Výsledky kontroly připojení pro virtuální počítač][1]

## <a name="check-remote-endpoint-connectivity"></a>Zkontrolujte připojení vzdáleného koncového bodu

Zkontrolujte připojení a latence pro vzdálený koncový bod, vyberte **ručně zadejte** přepínač v **cílové** části, zadejte adresu url a port a klikněte na tlačítko **zkontrolujte**.  Používá se pro vzdálené koncové body jako koncové body weby a úložiště.

![Výsledky kontroly připojení pro webovou stránku][2]

## <a name="next-steps"></a>Další postup

Informace o automatizaci paketu zachytává se virtuální počítač výstrahy zobrazením [vytvořit zaznamenání výstrahy spouštěná paketu](network-watcher-alert-triggered-packet-capture.md)

Najít, pokud určité provoz je povolený v nebo z virtuálního počítače navštivte stránky [zkontrolujte IP tok ověření](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png