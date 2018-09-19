---
title: Provádění dostupné aplikace s Azure Maps | Dokumentace Microsoftu
description: Postup vytvoření přístupné aplikace využívající Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 59e4226d7abb1d2692c531f146685feb203ab423
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129433"
---
# <a name="building-an-accessible-application"></a>Vytvoření přístupné aplikace

Tento článek ukazuje, jak sestavit aplikaci mapu, která mohou být využívána čtečky obrazovky.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Dostupné aplikace' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>přístupné aplikace</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapa obsahuje předem sestavených pomocí funkce pro usnadnění přístupu. Uživatelé mohou přejít na mapě pomocí klávesnice. Pokud běží se čtečkou obrazovky, na mapě se upozornění pro uživatele změny stavu.
Například uživatelé se zobrazí oznámení o změny provedené v mapování mapy je přesunuty nebo možnosti zvětšit. Další informace, je umístěn na podkladovou mapu by měl mít odpovídající textové informace pro uživatelům čtečky obrazovky.

Pomocí [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) je jedním ze způsobů, jak tohoto dosáhnout. Ve výše uvedeném příkladu vyhledávání automaticky otevíraného okna s textové informace se přidá do mapy pro každou PIN kód, který je umístěn na mapě. Použití [automaticky otevíraného okna na](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [připojit](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) metoda umožňuje automaticky otevírané okno má být zobrazeno bez vizuální zobrazení na mapě automaticky otevírané okno čtečky obrazovky.

## <a name="next-steps"></a>Další postup

Další informace o třídě automaticky otevíraného okna a jeho metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Přečtěte si další ukázky kódu:

> [!div class="nextstepaction"]
> [Znaková stránka vzorku](http://aka.ms/AzureMapsSamples)
