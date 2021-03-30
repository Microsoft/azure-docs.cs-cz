---
title: Vypnutí Microsoft Azure FXT Edge souborového Unit
description: Seznamte se s postupy pro spuštění a bezpečné vypnutí uzlu Azure FXT Edge souborového pomocí softwaru na ovládacím panelu clusteru.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218727"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpečně vypnout hardware Azure FXT Edge souborového

I když můžete použít tlačítko fyzického napájení k přepínání v jednotlivých uzlech, neměli byste ho používat k vypnutí jednotky za normálních okolností.

Po použití uzlu Azure FXT Edge souborového v rámci clusteru byste měli použít software na ovládacím panelu clusteru k vypnutí hardwaru.

> [!NOTE]
> Aby se předešlo možné ztrátě nebo poškození dat, vždy pomocí softwaru na ovládacích panelech vypněte Azure FXT Edge souborového. Nepoužívejte tlačítko fyzického napájení pro vypínání, pokud jste to neučinili prostřednictvím služby a podpory pro zákazníky společnosti Microsoft.
>
> V případě elektrického nouze odpojte napájecí šňůry nebo použijte mechanismus odpojování elektřiny datového centra.

## <a name="shut-down-a-node-from-the-control-panel"></a>Vypnutí uzlu z ovládacích panelů

Postupujte podle těchto pokynů a bezpečně vypněte uzel Azure FXT Edge souborového:

1. Přihlaste se k ovládacímu panelu clusteru. (Pokyny v [části otevření stránek nastavení](fxt-cluster-create.md#open-the-settings-pages))
1. Klikněte na kartu **Nastavení** a potom na stránce **cluster**  >  **FXT Nodes** (načíst uzly clusteru).
1. V seznamu uzlů clusteru Najděte ten, který chcete vypnout. Ve sloupci **Akce** klikněte **na tlačítko vypnout** .
1. Chvíli počkejte. Uzel se vypne a zapíná se.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o stavu diody LED a dalších indikátorech v [monitorování stavu hardwaru Azure FXT Edge souborového](fxt-monitor.md).
* Přečtěte si další informace o zdrojích napájení Azure FXT Edge souborového v tématu [připojení napájecích kabelů](fxt-network-power.md#connect-power-cables).
