---
title: "Najít další segment s Azure sítě sledovacích procesů další segment - portálu Azure | Microsoft Docs"
description: "Tento článek popisuje, jak můžete najít, co je typ dalšího směrování a ip adresu pomocí dalšího přechodu pomocí portálu Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Zjistěte, co typ dalšího směrování je pomocí funkce další směrování v sledovací proces sítě Azure pomocí portálu

> [!div class="op_single_selector"]
> - [portál Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-next-hop-rest.md)

Další směrování je funkce sledovací proces sítě, která poskytuje možnost get typ dalšího směrování a IP adresy, které jsou založené na zadaný virtuální počítač. Tato funkce je užitečná při určování, zda prochází odchozího provozu z virtuálního počítače brány, internet nebo virtuální sítě získat do cíle.

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku používá další segment Pokud chcete zjistit typ dalšího směrování a IP adresu pro prostředek. Další informace o další směrování, navštivte [další směrování přehled](network-watcher-next-hop-overview.md).

V tomto scénáři provedete následující:

* Další směrování načíst z virtuálního počítače.

## <a name="get-next-hop"></a>Získat další směrování

### <a name="step-1"></a>Krok 1

Přejděte do prostředku sledovací proces sítě na portálu Azure.

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **dalšího směrování** v navigačním podokně vyberte virtuální počítač a síťové rozhraní, vyplňte zdrojové a cílové IP a klikněte na **dalšího směrování** tlačítko.

> [!NOTE]
> Další směrování vyžaduje, aby prostředků virtuálního počítače je přidělená ke spuštění.

![získat další směrování – přehled][1]

### <a name="step-3"></a>Krok 3

Po dokončení úlohy jsou k dispozici výsledky. IP adresa a typ zařízení, které další směrování je, se zobrazí. V následující tabulce jsou uvedeny dostupné vrácené hodnoty na portálu.

**Typ dalšího směrování**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Žádný

Pokud vlastní trasy byla použita pro směrování tento provoz, trasy definované uživatelem (UDR) se také zobrazí ve výsledcích.

![získat další směrování výsledky][2]

## <a name="next-steps"></a>Další kroky

Zjistěte, jak zkontrolovat nastavení skupiny zabezpečení sítě prostřednictvím kódu programu, navštivte stránky [NSG auditování s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














