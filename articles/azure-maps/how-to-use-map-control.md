---
title: Jak používat mapový ovládací prvek Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít knihovnu Javascript na straně klienta Azure Maps mapového ovládacího prvku.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166017"
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

3. Pokud chcete zobrazit mapu s novým v prohlížeči, přidejte **#map** odkazovat v `<style>` element:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Inicializace mapový ovládací prvek, definujte nový oddíl v textu html a vytvořit skript. Ve skriptu použijte vlastní klíč účtu Azure Maps. Pokud potřebujete k vytvoření účtu služby nebo najít vaše klíče najdete v tématu [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md). **Nastavení jazyka** metody Určuje jazyk, který chcete použít pro popisky mapy a ovládací prvky. Další informace o podporovaných jazyků najdete v tématu [podporované jazyky](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
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
