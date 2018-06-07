---
title: Jak používat Azure mapy mapový ovládací prvek | Microsoft Docs
description: Další informace o použití knihovny Javascript klienta Azure mapy mapový ovládací prvek.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4ba4b9a9f2357d283ddc03a4723cb08b48d40a9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599207"
---
# <a name="how-to-use-the-azure-maps-map-control"></a>Jak používat Azure mapy mapový ovládací prvek
Knihovna Javascript na straně klienta mapový ovládací prvek umožňuje vykreslení mapy a vložené funkce Azure mapy do webové nebo mobilních aplikací. 

## <a name="create-a-new-map-in-a-web-page"></a>Vytvořit nové mapování na webové stránce

Mapu můžete vložit na webové stránce pomocí knihovny Javascript klienta mapový ovládací prvek.

1. Vytvořte nový soubor s názvem MapSearch.html.

2. Přidejte odkazy Azure mapy šablony stylů a skript zdroje do `<head>` element souboru:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. K vykreslení nové mapování v prohlížeči, přidejte **#map** odkaz v `<style>` elementu.

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. Chcete-li inicializovat mapový ovládací prvek, zadejte novou část v těle html a vytvořit skript. Použijte vlastní klíč účtu Azure mapy ve skriptu. Pokud potřebujete k vytvoření účtu nebo vyhledání vašeho klíče najdete v tématu [jak spravovat váš účet Azure mapy a klíče](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. Otevřete soubor ve webovém prohlížeči a zobrazit vykreslené mapy.

## <a name="next-steps"></a>Další postup

Tento článek ukázal, jak vytvořit základní mapu s klíč Azure mapy. Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 

* [Vytvoření mapy](map-create.md)
* [Přidání kódu pin](map-add-pin.md)