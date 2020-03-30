---
title: Jak vypnout jednotku Microsoft Azure FXT Edge Filer
description: Postupy pro spuštění a bezpečné vypnutí uzlu Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255998"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpečně vypnout hardware Azure FXT Edge Filer

I když můžete použít fyzické tlačítko napájení k zapnutí jednotlivého uzlu, neměli byste jej používat k vypnutí jednotky za normálních okolností.

Po Azure FXT Edge Filer uzel se používá jako součást clusteru, měli byste použít software ovládacího panelu clusteru vypnout hardware. 

> [!NOTE] 
> Chcete-li se vyhnout možné ztrátě nebo poškození dat, vždy použijte software Ovládací panely k vypnutí Azure FXT Edge Filer. Nepoužívejte tlačítko fyzického napájení pro vypnutí, pokud k tomu nedostanete pokyn oddělení služeb zákazníkům a podpory společnosti Microsoft.
> 
> V případě úrazu elektrickým proudem odpojte napájecí kabely nebo použijte mechanismus odpojení elektrické energie datového centra.

## <a name="shut-down-a-node-from-the-control-panel"></a>Vypnutí uzlu z Ovládacích panelů

Podle těchto pokynů můžete bezpečně vypnout uzel Azure FXT Edge Filer:

1. Přihlaste se k ovládacímu panelu clusteru. (Pokyny v [otevřete nastavení stránky](fxt-cluster-create.md#open-the-settings-pages))
1. Klikněte na kartu **Nastavení** a načtěte stránku**Uzlů FXT** **clusteru.** > 
1. V seznamu uzlů clusteru najděte ten, který chcete vypnout. Klikněte na tlačítko **Vypnout** ve sloupci **Akce.** 
1. Chvíli počkejte. Uzel se vypne a sám se vypne.

## <a name="next-steps"></a>Další kroky

* Informace o stavových LED diodách a dalších indikátorech v [části Monitorování stavu hardwaru Azure FXT Edge Filer](fxt-monitor.md).
* Přečtěte si další informace o napájecích zdrojích Azure FXT Edge Filer v [napájecích kabelech Connect](fxt-network-power.md#connect-power-cables).
