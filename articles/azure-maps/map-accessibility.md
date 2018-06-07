---
title: Provedení dostupné aplikace s Azure mapy | Microsoft Docs
description: Jak sestavit aplikaci přístupné pomocí Azure mapy
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
ms.openlocfilehash: 2523287669628b8c58028cae9887743c20b6bc5e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654793"
---
# <a name="building-an-accessible-application"></a>Sestavení dostupné aplikace

Tento článek ukazuje, jak sestavit aplikaci mapu, která mohou být využívána čtečky obrazovky.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zkontrolujte dostupné aplikace' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>zkontrolujte dostupné aplikace</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Mapy je předem sestavené s některé funkce pro usnadnění přístupu. Uživatele můžete přejít pomocí klávesnice mapy a pokud běží čtečky obrazovky, mapy upozorní uživatele změny stavu. Například uživatel budou informováni o mapy na nové, šířky, přiblížení a polohu, když se posouvá v okně nebo možnosti mapy. Žádné další informace, které je umístěn na základní mapě by měl mít odpovídající textové informace pro uživatele čtečky obrazovky. Pomocí [místní](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) je jedním ze způsobů jak toho docílit. Ve výše uvedeném příkladu vyhledávání se místní okno s textové informace přidá do mapy pro každou kód pin, který je umístěn na mapě. Pomocí [na místní](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) attach – metoda umožňuje překryvném okně má být zobrazeno čtečky obrazovky bez vizuální zobrazení automaticky otevřeném okně na mapě.

## <a name="next-steps"></a>Další postup

Další informace o třídě místní a její metody používané v tomto článku:

* [Místní nabídka](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [Připojení](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [Odebrat](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Otevřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zavřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Podívejte se na naše [znaková stránka ukázka](http://aka.ms/AzureMapsSamples) pro více scénářů mapování.
