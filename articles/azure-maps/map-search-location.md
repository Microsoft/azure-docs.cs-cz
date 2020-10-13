---
title: Zobrazit výsledky hledání na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak provést požadavek hledání pomocí nástroje Microsoft Azure Maps Web SDK a zobrazit výsledky na mapě.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be6d508da15b7c403259bd66c86c3b3e72ff2f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089269"
---
# <a name="show-search-results-on-the-map"></a>Zobrazit výsledky hledání na mapě

V tomto článku se dozvíte, jak vyhledat umístění zájmu a zobrazit výsledky hledání na mapě.

Existují dva způsoby, jak vyhledat umístění zájmu. Jedním ze způsobů, jak použít modul služby k vytvoření žádosti o vyhledávání. Druhým způsobem je učinit požadavek hledání [Azure Maps rozhraní API pro hledání přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) prostřednictvím [rozhraní API pro načtení](https://fetch.spec.whatwg.org/). Oba způsoby jsou popsány níže.

## <a name="make-a-search-request-via-service-module"></a>Vytvoření žádosti o vyhledávání přes modul služby

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě (modul služby)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na téma <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>zobrazení výsledků hledání na mapě (modulu služby)</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu vytvoří první blok objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří `TokenCredential` pro ověření požadavků HTTP pro Azure Maps pomocí přístupového tokenu. Pak předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) . `searchURL`Představuje adresu URL pro Azure Maps [vyhledávacích](https://docs.microsoft.com/rest/api/maps/search) operací.

Třetí blok kódu vytvoří objekt zdroje dat pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) a přidá do něj výsledky hledání. [Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) jako symboly na mapě.  Pak se vytvoří vrstva symbolů. Zdroj dat se přidá do vrstvy symbolů, která se pak přidá do mapy.

Čtvrtý blok kódu používá metodu [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) v [modulu služby](how-to-use-services-module.md). Umožňuje provést bezplatné hledání textu formuláře prostřednictvím [rozhraní Get REST API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Požadavky GET na rozhraní API pro hledání přibližných hodnot můžou zpracovat jakoukoli kombinaci neostrých vstupů. Kolekce funkcí pro injson z odpovědi je pak extrahována pomocí `geojson.getFeatures()` metody a přidána do zdroje dat, což automaticky vede k vykreslování dat na mapě prostřednictvím vrstvy symbolů.

Poslední blok kódu upraví meze kamery pro mapu pomocí vlastnosti [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Požadavek hledání, zdroj dat, vrstva symbolů a hranice kamery jsou uvnitř [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) mapy. Chceme zajistit, aby se výsledky zobrazovaly po úplném načtení mapy.


## <a name="make-a-search-request-via-fetch-api"></a>Vytvoření žádosti o vyhledávání prostřednictvím rozhraní API pro načtení

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>zobrazení výsledků hledání na mapě</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Nastaví mechanismus ověřování pro použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří adresu URL, na kterou se má vytvořit žádost o vyhledávání. Vytvoří také dvě pole pro uložení hranic a kódů PIN pro výsledky hledání.

Třetí blok kódu používá [rozhraní API pro načtení](https://fetch.spec.whatwg.org/). [Rozhraní API pro načtení](https://fetch.spec.whatwg.org/) se používá k vytvoření požadavku na [Azure Maps rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , ve kterém se budou hledat body zájmu. Rozhraní API pro přibližné vyhledávání může zpracovat jakoukoli kombinaci přibližných vstupů. Pak zpracovává a analyzuje odpověď na hledání a přidá do pole searchPins výsledné kódy PIN.

Čtvrtý blok kódu vytvoří objekt zdroje dat pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) . Do kódu přidáme výsledky hledání do zdrojového objektu. [Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) jako symboly na mapě. Pak se vytvoří vrstva symbolů. Zdroj dat se přidá do vrstvy symbolů, která se pak přidá do mapy.

Poslední blok kódu vytvoří objekt [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox) . Používá pole výsledků a poté upravuje meze kamery pro mapu pomocí [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)mapy. Následně vykreslí výsledné kódy PIN.

Požadavek hledání, zdroj dat, vrstva symbolů a hranice kamery jsou nastaveny v rámci [naslouchacího procesu mapování událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) , aby se zajistilo, že se výsledky zobrazí po úplném načtení mapy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

Další informace o **přibližném vyhledávání**:

> [!div class="nextstepaction"]
> [Azure Maps rozhraní API pro přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](map-route.md)
