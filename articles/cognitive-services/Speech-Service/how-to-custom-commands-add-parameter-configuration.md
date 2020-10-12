---
title: 'Postupy: Konfigurace parametru jako entity externích entit'
titleSuffix: Azure Cognitive Services
description: V tomto článku vyvysvětlíme, jak nakonfigurovat parametr řetězce, který bude odkazovat na zpřístupnění katalogu v rámci webového koncového bodu.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284179"
---
# <a name="add-configurations-to-commands-parameters"></a>Přidání konfigurací do parametrů příkazů

V tomto článku se dozvíte víc o rozšířené konfiguraci parametrů, včetně:

 - Jak hodnoty parametrů můžou patřit do množiny definované externě pro aplikace Custom Commands
 - Postup přidání klauzulí ověřování pro hodnotu parametrů

## <a name="prerequisites"></a>Požadavky

Je nutné provést kroky v následujících článcích:

> [!div class="checklist"]
> * [Postupy: vytváření aplikací pomocí jednoduchých příkazů](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Postupy: Přidání parametrů k příkazům](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurovat parametr jako entitu externího katalogu

V této části nakonfigurujete parametry typu String, aby odkazovaly na externí katalogy hostované přes webový koncový bod. Díky tomu můžete externí katalog aktualizovat nezávisle, aniž byste museli provádět úpravy aplikace Custom Commands. Tento přístup je užitečný v případech, kdy položky katalogu můžou být velké v čísle.

Znovu použijte parametr **SubjectDevice** z příkazu **TurnOnOff** . Aktuální konfigurace tohoto parametru **přijímá předdefinované vstupy z interního katalogu**. To odkazuje na statický seznam zařízení, jak je definováno v konfiguraci parametru. Chceme přesunout obsah tohoto obsahu do externího zdroje dat, který se dá aktualizovat nezávisle.

Pokud to chcete provést, Začněte přidáním nového webového koncového bodu. V levém podokně v části přejít na **koncové body webu** a přidejte nový webový koncový bod s následující konfigurací.

| Nastavení | Navrhovaná hodnota |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metoda | GET |


Pokud navrhovaná hodnota pro adresu URL nefunguje, je nutné nakonfigurovat a hostovat jednoduchý koncový bod webu, který vrátí kód JSON sestávající ze seznamu zařízení, která lze ovládat. Webový koncový bod by měl vracet formát JSON následujícím způsobem:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Potom přejděte na stránku nastavení parametrů **SubjectDevice** a změňte vlastnosti na následující.

| Nastavení | Navrhovaná hodnota |
| ----| ---- |
| Konfigurace | Přijmout předdefinované vstupy z externího katalogu |                               
| Koncový bod katalogu | getzařízení |
| Metoda | GET |

Pak vyberte **Uložit**.

> [!IMPORTANT]
> Možnost konfigurace parametru pro příjem vstupů z externího katalogu se nezobrazuje, pokud v levém podokně není nastaven koncový bod webu v části **koncový bod webu** .

### <a name="try-it-out"></a>Vyzkoušet

Vyberte možnost **výuka** a počkejte na dokončení školení. Po dokončení školení vyberte **test** a vyzkoušejte pár interakcí.

* Vstup: zapnout
* Výstup: které zařízení chcete ovládat?
* Vstup: světla
* Výstup: OK, zapnutí světla

> [!NOTE]
> Všimněte si, jak můžete teď řídit všechna zařízení hostovaná na webovém koncovém bodu. Pro otestování nových změn a opětovném publikování aplikace je stále nutné vyškolit aplikaci.

## <a name="add-validation-to-parameters"></a>Přidání ověření do parametrů

**Ověření** jsou určena pro určité typy parametrů, které vám umožní nakonfigurovat omezení hodnoty parametru a zobrazit výzvu k opravě, pokud hodnoty nespadají do omezení. Úplný seznam typů parametrů, které rozšiřují konstrukci ověřování, najdete v [odkazech](./custom-commands-references.md) .

Otestujte ověřování pomocí příkazu **SetTemperature** . Pomocí následujících kroků přidejte ověření pro parametr **teploty** .

1. V levém podokně vyberte příkaz **SetTemperature** .
1. V prostředním podokně vyberte možnost  **teplota** .
1. V pravém podokně vyberte **Přidat ověření** .
1. V okně **nové ověření** nakonfigurujte ověřování následujícím způsobem a vyberte **vytvořit**.


    | Konfigurace parametru | Navrhovaná hodnota | Popis |
    | ---- | ---- | ---- |
    | Min. hodnota | `60` | Pro parametry Number minimální hodnota, kterou tento parametr může předpokládat |
    | Max. hodnota | `80` | Pro číselné parametry maximální hodnoty, které tento parametr může předpokládat |
    | Neúspěšná reakce |  Jednoduchý editor > první variace > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Výzva k zadání nové hodnoty, pokud se ověření nepovede |

    > [!div class="mx-imgBorder"]
    > ![Přidat ověření rozsahu](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Vyzkoušet

1. Vyberte ikonu **výuky** v pravém horním rohu okna.

1. Po dokončení školení vyberte **test** a vyzkoušejte pár interakcí:

    - Vstup: Nastavte teplotu na 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů
    - Vstup: Nastavte teplotu na 45 stupňů.
    - Výstup: je nám líto, ale dá se nastavit jenom teplota mezi 60 a 80 stupnemi.
    - Vstup: nastavte místo toho 72 stupňů.
    - Výstup: OK, nastavení teploty na 72 stupňů

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: přidání pravidel interakce](./how-to-custom-commands-add-interaction-rules.md)
