---
title: Tom, jak vypnout Microsoft Azure FXT hrany Filer jednotka
description: Postupy pro spuštění a bezpečné vypnutí uzlu Azure FXT hrany vyfiltrovat
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542861"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpečně vypnout Azure FXT hrany Filer hardwaru

I když používáte fyzické vypínače přepnout na jednotlivých uzlů byste neměli používat pro vypnutí jednotek za normálních okolností.

Po uzlu Azure FXT hrany Filer se používá jako součást clusteru, měli byste použít software clusteru ovládací prvek panel vypnout hardwaru. 

> [!NOTE] 
> Aby nedošlo ke ztrátě nebo poškození, vždy používejte software ovládací panely pro vypnutí Azure FXT hrany vyfiltrovat. Nepoužívejte na tlačítku napájení fyzické pro vypnutí, pokud jsou k tomu vyzváni Microsoft zákaznických služeb a podpory.
> 
> Ve stavu nouze elektrické odpojit napájecích kabelů nebo používat vaše data v centru elektřiny odpojit mechanismus.

## <a name="shut-down-a-node-from-the-control-panel"></a>Vypnutí uzlu z ovládacího panelu

Postupujte podle těchto pokynů můžete bezpečně vypnutí uzlu Azure FXT hrany filtr:

1. Přihlaste se ke clusteru ovládacích panelech. (Pokyny uvedené v [otevření stránky nastavení](fxt-cluster-create.md#open-the-settings-pages))
1. Klikněte na tlačítko **nastavení** kartu a pak nahrajte **clusteru** > **FXT uzly** stránky.
1. V seznamu uzlů clusteru vyhledejte umístění, které chcete vypnout. Klikněte na tlačítko **vypněte** tlačítko v jeho **akce** sloupce. 
1. Chvíli počkejte. Uzel vypne a samotné vypne.

## <a name="next-steps"></a>Další postup

* Další informace o stavu LED a další indikátory v [stav hardwaru monitorování Azure FXT hrany Filer](fxt-monitor.md).
* Další informace o Azure FXT hrany Filer výkonu poskytuje v [připojení napájecích kabelů](fxt-network-power.md#connect-power-cables).
