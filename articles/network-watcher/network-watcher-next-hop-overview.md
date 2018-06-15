---
title: Úvod do dalšího směrování v sledovací proces sítě Azure | Microsoft Docs
description: Tento článek obsahuje přehled sledovací proces sítě další funkce směrování.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bbb782e700781dcfedbbd340c7d10db53767b035
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32180381"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Použít další segment k diagnostikování problémů směrování virtuálního počítače

Přenosy z virtuálního počítače (VM) se odesílají do cílového umístění v závislosti na účinné postupy spojené s síťové rozhraní (NIC). Další směrování získá typ dalšího směrování a IP adresa z paketu z konkrétní virtuální počítač a síťový adaptér. Znalost dalšího směrování vám pomůže určit, pokud je provoz směrovat do určené cílové, nebo zda nikde odesílání přenosů. Nesprávná konfigurace trasy, kde je přenášená na místní umístění, nebo virtuální zařízení, může způsobit problémy s připojením. Další směrování také vrátí hodnotu směrovací tabulka přidružené k dalším místě směrování. Pokud trasa je definován jako trasy definované uživatelem, je vrácena danou trasu. Jinak vrátí dalšího směrování **systémová trasa**.

![dalšího směrování – přehled](./media/network-watcher-next-hop-overview/figure1.png)

Další směrování, které mohou být vráceny další směrování schopnosti jsou následující:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* VnetPeering
* Žádný

Další informace o každé další typ směrování, najdete v části [Přehled směrování](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Další postup

Další informace o použití další segment k diagnostikování problémů směrování sítě virtuálních počítačů, najdete v tématu [síť Virtuálních diagnostikovat problémy se směrováním](diagnose-vm-network-routing-problem.md).
