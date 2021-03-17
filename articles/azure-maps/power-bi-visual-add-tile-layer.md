---
title: Přidat vrstvu dlaždice do Azure Maps Power BI vizuál | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak používat vrstvu dlaždice ve vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896204"
---
# <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

Funkce vrstvy dlaždice, jako je funkce referenční vrstvy, umožňuje překrytí dalších dat na mapě, aby bylo možné poskytnout více kontextu. Vrstvy dlaždic vám umožňují superimpose obrázky na dlaždicích základních map Azure Maps. Jedná se o skvělý způsob překrytí velkých nebo složitých datových sad, jako jsou například soubory s DRONY zachraňují životy nebo miliony řádků dat.

> [!div class="mx-imgBorder"]
> ![Mapa zobrazující bublinovou vrstvu nad dlaždicovou vrstvou, která zobrazuje aktuální infračervený počasí data z Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být buď předem vykresleny, nebo dynamicky vykresleny. Předem vykreslené obrázky jsou uloženy stejně jako jakékoli jiné obrázky na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Dynamicky vykreslené image používají službu k načtení imagí blízko v reálném čase. Vrstvy dlaždic představují skvělý způsob, jak vizualizovat velké datové sady na mapě. Pouze z obrázku lze vytvořit pouze dlaždicovou vrstvu, vektorová data lze také vykreslovat jako dlaždicovou vrstvu.

Ohraničovací rámeček a rozsah přiblížení, kde je k dispozici služba dlaždic, se dají předat jako nastavení, které omezí, kde se vyžadují dlaždice. Toto je vylepšení výkonu pro vizuál i pro službu dlaždic. Níže je uveden přehled všech nastavení dostupných v podokně **Formát** , které je k dispozici v části **vrstva dlaždice** .

| Nastavení        | Popis   |
|----------------|---------------|
| URL            | Naformátovaná adresa URL odkazující na službu dlaždic.  |
| Velikost dlaždice      | Celočíselná hodnota, která určuje rozměry dlaždic na šířku i na výšku.   |
| Severově vázaný    | Severní zeměpis ohraničovacího rámečku, kde jsou k dispozici dlaždice. |
| Jih – hranice    | Jižní Zeměpisná šířka ohraničovacího rámečku, kde jsou k dispozici dlaždice. |
| Vazba na východ     | Východní Zeměpisná délka ohraničovacího rámečku, kde jsou k dispozici dlaždice.  |
| Vázané na západ     | Západní Zeměpisná délka ohraničovacího rámečku, kde jsou k dispozici dlaždice   |
| Transparentnost   | Průhlednost vrstvy dlaždice   |
| Je organizace TMS         | Dlaždice mapové služby – specifikace, která vrátí osu souřadnic Y vrstvy dlaždice. |
| Minimální přiblížení       | Minimální dlaždice úrovně přiblížení jsou k dispozici. |
| Maximální přiblížení       | K dispozici jsou tyto dlaždice s maximální úrovní přiblížení.  |
| Pozice vrstvy | Určuje umístění vrstvy vzhledem k ostatním vrstvám mapy. |

## <a name="tile-url-formatting"></a>Formátování adresy URL dlaždice

Existují tři různé zásady vytváření názvů dlaždic, které podporuje Azure Maps vizuál:

-   **X, Y, zoom Notation** -X je sloupec, Y je umístěním dlaždice v mřížce dlaždice a přiblížením je hodnota na základě úrovně přiblížení.
-   **Quadkey Notation** – kombinuje informace x, y a přiblížení do jediné řetězcové hodnoty. Tato řetězcová hodnota se pro jednu dlaždici stala jedinečným identifikátorem.
-   **Ohraničovací rámeček** – určete obrázek v souřadnicích ohraničovacího rámečku: `{west},{south},{east},{north}` . Tento formát se běžně používá ve [službě mapování webu (WMS)](https://www.opengeospatial.org/standards/wms).

Adresa URL dlaždice obsahuje adresu URL HTTPS pro šablonu adresy URL dlaždice, která používá následující parametry:

-   `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
-   `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
-   `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
-   `{quadkey}` – `quadkey` Identifikátor dlaždice založený na konvenci pojmenování systému dlaždice mapy Bing
-   `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.

Následuje příklad, ve kterém je naformátovaná adresa URL dlaždice pro [službu počasí paprsků](/rest/api/maps/renderv2/getmaptilepreview) v Azure Maps. Všimněte si, že `[subscription-key]` je zástupný symbol pro váš Azure Maps klíč předplatného.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Další informace o Azure Maps systému skládání naleznete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Další kroky

Přidat k mapě další kontext:

> [!div class="nextstepaction"]
> [Zobrazení provozu v reálném čase](power-bi-visual-show-real-time-traffic.md)