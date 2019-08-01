---
title: Zpřístupnění dostupné aplikace s Azure Maps | Microsoft Docs
description: Postup vytvoření přístupné aplikace pomocí Azure Maps
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638784"
---
# <a name="building-an-accessible-application"></a>Vytvoření přístupné aplikace

V tomto článku se dozvíte, jak vytvořit aplikaci mapy, kterou může používat čtečka obrazovky.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Vytvoření přístupné aplikace' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero a <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>zpřístupněte aplikaci</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapa obsahuje předem sestavené funkce usnadnění. Uživatelé mohou procházet mapu pomocí klávesnice. Pokud je spuštěný čtečka obrazovky, mapa upozorní uživatele na změny jeho stavu.
Například uživatelé jsou upozorňováni na změny v mapě, když je mapa vyvážení nebo zvětšena. Všechny další informace, které jsou umístěny na základní mapě, by měly mít odpovídající textové informace pro uživatele čtečky obrazovky.

Použití [překryvného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) je jedním ze způsobů, jak toho dosáhnout. Ve výše uvedeném příkladu hledání se k mapě přidají automaticky otevírané okno s textovými informacemi pro každý kód PIN, který je umístěný na mapě. Pomocí metody [Attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) v [místní nabídce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) můžete překryvné okno zobrazit čtečkou obrazovky bez vizuálního zobrazení místní nabídky na mapě.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o třídě automaticky otevíraných oken a jejích metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Oken](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Podívejte se na další ukázky kódu:

> [!div class="nextstepaction"]
> [Stránka ukázka kódu](https://aka.ms/AzureMapsSamples)
