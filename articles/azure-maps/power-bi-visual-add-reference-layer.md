---
title: Přidání referenční vrstvy do Azure Maps Power BI vizuálu | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak používat referenční vrstvu v vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261756"
---
# <a name="add-a-reference-layer"></a>Přidání referenční vrstvy

Funkce referenční vrstvy umožňuje odeslat sekundární prostorovou datovou sadu do vizuálu a překrytí na mapě, aby poskytovala kontext sčítání. Tato datová sada je hostovaná pomocí Power BI a musí se jednat o [soubor v souboru. JSON](https://wikipedia.org/wiki/GeoJSON) s `.json` `.geojson` příponou nebo.

Chcete-li přidat soubor. **JSON** jako referenční vrstvu, přejděte do podokna **Formát** , rozbalte část **referenční vrstva** a stiskněte tlačítko **+ Přidat místní soubor** .

Po přidání souboru. JSON do referenční vrstvy se název souboru zobrazí místo tlačítka **+ Přidat místní soubor** s **X** vedle něho. Kliknutím na tlačítko **X** odeberte data z vizuálu a odstraňte soubor. json z Power BI.

Následující Mapa zobrazuje [2016 sčítání Coloradu](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), vybarvení podle populace.

> [!div class="mx-imgBorder"]
> ![Mapa, která zobrazuje 2016 sčítání pro Coloradu, vybarvení podle populace jako referenční vrstva](media/power-bi-visual/reference-layer-CO-census-tract.png)

Níže jsou všechna nastavení v podokně **Formát** , která jsou k dispozici v části **referenční vrstva** .

| Nastavení              | Popis   |
|----------------------|---------------|
| Data referenční vrstvy | Soubor. JSON dat, který se má nahrát do vizuálu, jako další vrstva v mapě Kliknutím na tlačítko **+ Přidat místní soubor** otevřete dialog souboru, který uživatel může použít k výběru souboru. JSON, který má `.json` `.geojson` příponu souboru nebo. |

> [!NOTE]
> V této verzi Preview Azure Mapsho vizuálu bude referenční vrstva načíst pouze prvních 5 000 funkcí tvaru na mapě. Toto omezení se zvýší v budoucí aktualizaci.

## <a name="styling-data-in-a-reference-layer"></a>Stylování dat v referenční vrstvě

Vlastnosti lze přidat do každé funkce v rámci souboru. JSON, aby bylo možné přizpůsobit, jak je na mapě naformátovaná. Tato funkce používá funkci jednoduché datové vrstvy v sadě Azure Maps Web SDK. Další informace najdete v tomto dokumentu u [podporovaných vlastností stylu](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Vlastní obrázky ikon nejsou v rámci Azure Maps vizuálu podporovány jako bezpečnostní opatření.

Níže je uveden příklad funkce pro zobrazení bodových JSON, která nastaví barvu zobrazenou na červenou.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Další kroky

Přidat k mapě další kontext:

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu v reálném čase](power-bi-visual-show-real-time-traffic.md)
