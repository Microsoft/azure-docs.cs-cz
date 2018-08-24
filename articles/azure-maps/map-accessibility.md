---
title: Provádění dostupné aplikace s Azure Maps | Dokumentace Microsoftu
description: Postup vytvoření přístupné aplikace využívající Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 3fe0ba47e2e3529352ca8386dc7531a96a2689af
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746208"
---
# <a name="building-an-accessible-application"></a>Vytvoření přístupné aplikace

Tento článek ukazuje, jak sestavit aplikaci mapu, která mohou být využívána čtečky obrazovky.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Dostupné aplikace' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>přístupné aplikace</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapa je předem sestavených s některé funkce pro usnadnění přístupu. Uživatele můžete přejít na mapě pomocí klávesnice a pokud běží se čtečkou obrazovky, na mapě upozorní uživatele na změny stavu. Uživatel budete například informováni o na mapě nového, šířky, Lupa a umístění, když se na mapě přesunuty nebo možnosti zvětšit. Další informace, je umístěn na podkladovou mapu by měl mít odpovídající textové informace pro uživatelům čtečky obrazovky. Pomocí [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) je jedním ze způsobů, jak tohoto dosáhnout. Ve výše uvedeném příkladu vyhledávání automaticky otevíraného okna s textové informace se přidá do mapy pro každou PIN kód, který je umístěn na mapě. Použití [automaticky otevíraného okna na](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) připojit metoda umožňuje automaticky otevírané okno má být zobrazeno bez vizuální zobrazení na mapě automaticky otevírané okno čtečky obrazovky.

## <a name="next-steps"></a>Další postup

Další informace o třídě automaticky otevíraného okna a jeho metody používané v tomto článku:

* [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [Připojení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach)
    * [odebrat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#remove)
    * [Otevřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zavřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Podívejte se na naše [znaková stránka ukázka](http://aka.ms/AzureMapsSamples) pro další scénáře mapování.
