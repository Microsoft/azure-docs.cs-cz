---
title: Připojení ke službě webové funkce (WFS) | Mapy Microsoft Azure
description: Přečtěte si, jak se připojit ke službě WFS, a pak pomocí Azure Maps Web SDK a modulu pro prostor v/v vytvořit dotaz na službu WFS.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891444"
---
# <a name="connect-to-a-wfs-service"></a>Připojení ke službě WFS

Služba webové funkce (WFS) je webová služba pro dotazování prostorových dat s standardizovaným rozhraním API, které je definováno Open Geospatial Consortium (OGC). `WfsClient`Třída v modulu pro prostorové vstupně-výstupní operace umožňuje vývojářům připojit se ke službě WFS a dotazovat se na data ze služby.

Třída podporuje následující funkce `WfsClient` :

- Podporované verze: `1.0.0` , `1.1.0` a `2.0.0`
- Podporované operátory filtru: binární porovnávání, Logic, Math, Value a `bbox` .
- Žádosti se provádějí `HTTP GET` jenom pomocí.
- Podporované operace:

    | Operace | Description |
    | :-- | :-- |
    | GetCapabilities | Vygeneruje dokument metadat s platnými operacemi a parametry WFS. |
    | Getfeature | Vrátí výběr funkcí ze zdroje dat. |
    | DescribeFeatureType | Vrátí podporované typy funkcí. |

## <a name="using-the-wfs-client"></a>Použití klienta WFS

`atlas.io.ogc.WfsClient`Třída v modulu pro prostorové vstupně-výstupní operace usnadňuje dotazování na službu WFS a převádění odpovědí na objekty třídyal JSON. Tento objekt pro objektiv JSON se pak dá použít pro jiné účely mapování.

Následující kód dotazuje službu WFS a vykresluje vrácené funkce na mapě.

<br/>

<iframe height='700' scrolling='no' title='Příklad jednoduchého WFSu' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>příklad jednoduchého WFS</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Podporované filtry

Specifikace pro WFS Standard využívá filtry OGC. Níže uvedené filtry jsou podporovány klientem WFS za předpokladu, že služba volána také podporuje tyto filtry. Vlastní řetězce filtru lze předat do `CustomFilter` třídy.

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

**Relační operátory**

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

Následující kód demonstruje použití různých filtrů s klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Příklady filtru WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu Azure Maps () na CodePen se podívejte na <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>Příklady filtru per WFS</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) <a href='https://codepen.io'></a>.
</iframe>

## <a name="wfs-service-explorer"></a>Průzkumník služby WFS

Následující kód používá klienta WFS k prozkoumání služeb WFS. Vyberte vrstvu typu vlastnosti v rámci služby a zobrazte přidruženou legendu.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Průzkumník služby WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>se podívejte na Azure Maps službu pera pro pero.
</iframe>

Pokud chcete získat přístup ke službám WFS hostovaným na koncových bodech, které nejsou povoleny pro CORS, můžete předat službu proxy s podporou CORS do `proxyService` Možnosti klienta WFS, jak je znázorněno níže. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaných formátech dat](spatial-io-supported-data-format-details.md)