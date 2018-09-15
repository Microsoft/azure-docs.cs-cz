---
title: Jak používat mapový ovládací prvek Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít knihovnu Javascript na straně klienta Azure Maps mapového ovládacího prvku.
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 45890b4bd474c010b2b086be0405b79d340aeebd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603155"
---
# <a name="use-the-azure-maps-map-control"></a>Použití mapového ovládacího prvku Azure Maps

Knihovna Javascript na straně klienta mapový ovládací prvek umožňuje vykreslit mapy a vložené funkce Azure Maps do vašich webových nebo mobilních aplikací.

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapě můžete vložit na webové stránce pomocí knihovny Javascript na straně klienta mapový ovládací prvek.

1. Vytvořte nový soubor s názvem **MapSearch.html**.

2. Azure Maps šablony stylů a skripty zdroje do přidejte odkazy `<head>` prvek souboru:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. K vykreslení v prohlížeči nové mapování, přidejte **#map** odkazovat v `<style>` element:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Abyste inicializovali mapový ovládací prvek, definujte nový oddíl v textu html a vytvořit skript. Ve skriptu použijte vlastní klíč účtu Azure Maps. Pokud potřebujete k vytvoření účtu služby nebo najít vaše klíče najdete v tématu [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md).

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

Další informace o vytvoření mapy pomocí Úplný příklad:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

Další informace o stylu mapy:

> [!div class="nextstepaction"]
> [Vyberte styl mapy](choose-map-style.md)
