---
title: Funkce stylů mapy v Azure Maps | Microsoft Docs
description: Přečtěte si o funkcích souvisejících s Azure Maps stylu.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 52936b14264bd4fe1846ae365e1de447d594b612
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639059"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Zvolit styl mapy v Azure Maps

Azure Maps má čtyři různé styly mapy, ze kterých si můžete vybrat. Další informace o stylech mapy najdete v tématu [podporované styly mapy v Azure Maps](./supported-map-styles.md). Tento článek ukazuje, jak použít funkce související se styly k nastavení stylu při načítání mapy, nastavení nového stylu a použití ovládacího prvku pro výběr stylu.

## <a name="set-style-on-map-load"></a>Nastavit styl při načítání mapy

<iframe height='500' scrolling='no' title='Nastavení stylu při načítání map' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Nastavení stylu při načtení mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Blok kódu výše nastaví klíč předplatného a vytvoří objekt mapy se stylem nastaveným na grayscale_dark. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

## <a name="update-the-style"></a>Aktualizace stylu

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizuje styl</a> o Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Blok kódu výše nastaví klíč předplatného a vytvoří objekt mapy bez předběžného nastavení stylu. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu používá metodu [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) mapy k nastavení stylu mapy na satelit.

## <a name="add-the-style-picker"></a>Přidat výběr stylu

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Přidání výběru stylu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu nastaví klíč předplatného a vytvoří objekt mapy. styl mapy je předem nastaven na grayscale_dark. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří selektor stylu pomocí konstruktoru StyleControla nástroje Atlas [](/javascript/api/azure-maps-control/atlas.control.stylecontrol) .

Výběr stylu umožňuje výběr stylu pro mapu. Třetí blok kódu přidá k mapě výběr stylu pomocí metody mapové [ovládací prvky. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Selektor stylu je v rámci **naslouchacího procesu mapování událostí** , aby bylo zajištěno, že se načte po úplném načtení mapy.

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Přidejte řízení k vašim mapám:

> [!div class="nextstepaction"]
> [Přidat mapové ovládací prvky](./map-add-controls.md)

Přidat mapový PIN kód:

> [!div class="nextstepaction"]
> [Přidat PIN kód](./map-add-pin.md)
