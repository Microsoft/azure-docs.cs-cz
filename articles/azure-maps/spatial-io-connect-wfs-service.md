---
title: Připojení ke službě webové funkce (WFS) | Mapy Microsoft Azure
description: Zjistěte, jak se připojit ke službě WFS, a pak se na službu WFS zadejte pomocí webové sady Azure Maps SDK a modulu Prostorové iO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334203"
---
# <a name="connect-to-a-wfs-service"></a>Připojení ke službě WFS

Webfeature Service (WFS) je webová služba pro dotazování prostorových dat, která má standardizované rozhraní API, které je definováno open geoprostorové konsorcium (OGC). Třída `WfsClient` v modulu prostorových IO umožňuje vývojářům připojit se ke službě WFS a dotazovat data ze služby.

`WfsClient` Třída podporuje následující funkce:

- Podporované verze: `1.0.0` `1.1.0`, , a`2.0.0`
- Podporované operátory filtrů: binární porovnání, logika, matematika, hodnota a `bbox`.
- Žádosti jsou prováděny `HTTP GET` pouze pomocí.
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Generuje dokument metadat s platnými operacemi a parametry WFS. |
    | Funkce GetFeature | Vrátí výběr funkcí ze zdroje dat. |
    | Typ describefeature | Vrátí podporované typy funkcí. |

## <a name="using-the-wfs-client"></a>Použití klienta WFS

Třída `atlas.io.ogc.WfsClient` v modulu prostorových IO usnadňuje dotazování na službu WFS a převést odpovědi na objekty GeoJSON. Tento objekt GeoJSON pak lze použít pro jiné účely mapování.

Následující kód se dotazuje služby WFS a vykresluje vrácené funkce na mapě.

<br/>

<iframe height='700' scrolling='no' title='Jednoduchý příklad WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>příklad jednoduchého</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pera WFS podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Podporované filtry

Specifikace standardu WFS využívá filtry OGC. Níže uvedené filtry jsou podporovány klientem WFS za předpokladu, že volaná služba tyto filtry také podporuje. Vlastní řetězce filtru mohou být `CustomFilter` předány do třídy.

**Logické operátory**

- `And`
- `Or`
- `Not`

**Operátory hodnoty**

- `GmlObjectId`
- `ResourceId`

**Matematické operátory**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operátory porovnání**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Následující kód ukazuje použití různých filtrů s klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Příklady filtrů WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>příklady filtru</a> Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>WFS podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Průzkumník služeb WFS

Následující kód používá klienta WFS k prozkoumání služeb WFS. Vyberte vrstvu typu vlastnosti v rámci služby a podívejte se na přidruženou legendu.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Průzkumník služeb WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>průzkumník služby</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen WFS podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pro přístup ke službám WFS hostovaným na koncových bodech, které nejsou `proxyService` povoleny cors, může být proxy služba s podporou CORS předána do možnosti klienta WFS, jak je znázorněno níže. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
