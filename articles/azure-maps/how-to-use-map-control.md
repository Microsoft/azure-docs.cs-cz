---
title: Jak používat mapový ovládací prvek Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít knihovnu Javascript na straně klienta Azure Maps mapového ovládacího prvku.
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 228d2d3331b510a0f07dbd3ca278715466d747af
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988887"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Jak používat mapový ovládací prvek Azure Maps
Knihovna Javascript na straně klienta mapový ovládací prvek umožňuje vykreslit mapy a vložené funkce Azure Maps do vašich webových nebo mobilních aplikací. 

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapě můžete vložit na webové stránce pomocí knihovny Javascript na straně klienta mapový ovládací prvek.

1. Vytvořte nový soubor a pojmenujte ho MapSearch.html.

2. Azure Maps šablony stylů a skripty zdroje do přidejte odkazy `<head>` prvek souboru:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. K vykreslení v prohlížeči nové mapování, přidejte **#map** odkazovat v `<style>` elementu.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Abyste inicializovali mapový ovládací prvek, definujte nový oddíl v textu html a vytvořit skript. Ve skriptu použijte vlastní klíč účtu Azure Maps. Pokud potřebujete k vytvoření účtu služby nebo najít vaše klíče najdete v tématu [jak spravovat účet Azure Maps a klíčů](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Otevřete soubor ve webovém prohlížeči a zobrazení vykreslené mapy.

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak vytvořit základní mapu klíčem Azure Maps. Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 

* [Vytvoření mapy](map-create.md)
* [Přidání špendlíku](map-add-pin.md)
