---
title: Přidat automaticky otevírané okno s Azure Maps | Microsoft Docs
description: Postup přidání automaticky otevíraného okna do mapy JavaScriptu
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639001"
---
# <a name="add-a-popup-to-the-map"></a>Přidat místní nabídku na mapu

V tomto článku se dozvíte, jak přidat místní nabídku do bodu na mapě.

## <a name="understand-the-code"></a>Vysvětlení kódu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Přidat automaticky otevírané okno pomocí Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Přidání otevřeného okna pomocí Azure Maps</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) . Také vytvoří obsah HTML, který se zobrazí v překryvném okně.

Druhý blok kódu vytvoří objekt zdroje dat pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Bod je [funkcí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) třídy [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Objekt Point s vlastnostmi název a popis je pak vytvořen a přidán do zdroje dat.

[Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  V třetím bloku kódu se vytvoří Symbolová vrstva. Zdroj dat se přidá do vrstvy symbolů, která se pak přidá do mapy.

Čtvrtý blok kódu vytvoří [Překryvný objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Popup()`. Místní vlastnosti, jako je například Position a pixelOffset, jsou součástí [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions lze definovat v automaticky otevíraném konstruktoru nebo prostřednictvím funkce [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) třídy překryvného objektu. Vytvoří se naslouchací proces událostiprovrstvusymbolů.`mouseover`

Poslední blok kódu vytvoří funkci, která je aktivována `mouseover` naslouchací proces události. Nastaví obsah a vlastnosti místní nabídky a přidá objekt místní nabídky na mapu.

## <a name="reusing-a-popup-with-multiple-points"></a>Opakované použití automaticky otevíraného okna s více body

Když máte spoustu bodů a chcete zobrazit jenom jedno automaticky otevírané okno najednou, nejlepším řešením je vytvořit jedno automaticky otevírané okno a znovu ho použít místo vytvoření automaticky otevírané okno pro každou funkci bodu. Tím se výrazně sníží počet prvků modelu DOM vytvořených aplikací, což může poskytovat lepší výkon. Tato ukázka vytvoří funkce 3 body. Pokud kliknete na kterýkoli z nich, zobrazí se automaticky otevírané okno s obsahem této funkce Point.

<br/>

<iframe height='500' scrolling='no' title='Opakované použití automaticky otevíraného okna s více PIN kódy' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>automaticky otevírané okno s více kolíky</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Oken](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Úplné ukázky kódu najdete v následujících skvělých článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat značku HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Přidat tvar](./map-add-shape.md)