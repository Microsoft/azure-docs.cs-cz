---
title: Připojení ke službě webové funkce (WFS) | Mapy Microsoft Azure
description: Přečtěte si, jak se připojit ke službě WFS, a pak pomocí Azure Maps Web SDK a modulu pro prostor v/v vytvořit dotaz na službu WFS.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402750"
---
# <a name="connect-to-a-wfs-service"></a>Připojení ke službě WFS

Služba webové funkce (WFS) je webová služba pro dotazování prostorových dat s standardizovaným rozhraním API, které je definováno Open Geospatial Consortium (OGC). Třída `WfsClient` v modulu pro prostorové vstupně-výstupní operace umožňuje vývojářům připojit se ke službě WFS a dotazovat se na data ze služby.

Třída `WfsClient` podporuje následující funkce:

- Podporované verze: `1.0.0`, `1.1.0`a `2.0.0`
- Podporované operátory filtru: binární porovnávání, Logic, Math, Value a `bbox`.
- Žádosti se provádějí jenom pomocí `HTTP GET`.
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Vygeneruje dokument metadat s platnými operacemi a parametry WFS. |
    | Getfeature | Vrátí výběr funkcí ze zdroje dat. |
    | DescribeFeatureType | Vrátí podporované typy funkcí. |

## <a name="using-the-wfs-client"></a>Použití klienta WFS

Třída `atlas.io.ogc.WfsClient` v modulu pro prostorové vstupně-výstupní operace usnadňuje dotazování na službu WFS a převádění odpovědí na objekty třídyal JSON. Tento objekt pro objektiv JSON se pak dá použít pro jiné účely mapování.

Následující kód dotazuje službu WFS a vykresluje vrácené funkce na mapě.

<br/>

<iframe height='700' scrolling='no' title='Příklad jednoduchého WFSu' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Azure Maps <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Ukázka jednoduchého</a> pera v WFS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
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

Následující kód demonstruje použití různých filtrů s klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Příklady filtru WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>Příklady filtru pera WFS</a> .
</iframe>

## <a name="wfs-service-explorer"></a>Průzkumník služby WFS

Následující kód používá klienta WFS k prozkoumání služeb WFS. Vyberte vrstvu typu vlastnosti v rámci služby a zobrazte přidruženou legendu.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Průzkumník služby WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>WFS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>najdete v Azure Maps <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Průzkumníkovi služby</a> pero.
</iframe>

K načtení prostředků hostovaných v doménách, které nejsou povolené CORs, můžete taky použít proxy službu. Nejdřív byste definovali proměnnou pro uchování adresy URL proxy služby a nastavili možnost `proxyService` pro klienta WFS. Pokud chcete pro uživatele vykreslit možnost služby proxy, přidejte do uživatelského rozhraní uživatelský vstup. Načte adresu URL služby, když se klikne na vstup. Následující fragmenty kódu ukazují, jak používat službu proxy.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Fragment kódu HTML níže odpovídá výše uvedenému kódu JavaScriptu:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
