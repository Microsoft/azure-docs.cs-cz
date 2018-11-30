---
title: Zastaralé kognitivní dovednosti (Azure Search) | Dokumentace Microsoftu
description: Tato stránka obsahuje seznam kognitivního vyhledávání dovednosti, které se považují za zastaralé a aplikace nebude podporována v blízké budoucnosti.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.openlocfilehash: 6bd364ea9923b1c8cdd7c96fc29cb91dff88ec95
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52449907"
---
#    <a name="deprecated-cognitive-seach-skills"></a>Nepoužívané Cognitive vyhledávací dovednosti

Tento dokument popisuje kognitivní dovednosti, které se považují za zastaralé. Použijte následující pokyny pro obsah:

* Název dovedností: Název dovednosti, které se nepoužívá, mapuje se na @odata.type atribut.
* Poslední dostupná verze rozhraní api: poslední verze služby Azure search veřejné rozhraní API, prostřednictvím které dovednosti obsahující odpovídající zastaralé dovednosti je možné vytvořit/aktualizovat.
* Konec podpory: poslední den po jejímž uplynutí je považován za odpovídající dovednosti není podporován. Dříve vytvořili dovednosti musí i nadále fungovat, ale uživatelé jsou doporučena pro opustit nepoužívané dovedností.
* Doporučení: Migrace cesta vpřed pro podporované dovedností. Uživatelé by měli postupovat podle doporučení nadále využívat podporu.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Poslední dostupná verze rozhraní api

2017-11-11-preview

### <a name="end-of-support"></a>Ukončení podpory

15. února 2019

### <a name="recommendations"></a>Doporučení 

Použití [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) místo. Poskytuje většinu funkcí NamedEntityRecognitionSkill ve větší kvalitě. Má také bohatší informace v jeho komplexní výstupních polí.

Migrace na [dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md), budete muset provést jeden nebo více z následujících změn do definice dovedností. Můžete aktualizovat definici dovednosti pomocí [aktualizovat rozhraní API dovednosti](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

_Poznámka:_: v současné době nepodporuje skóre spolehlivosti jako koncept. Bude podporovat v blízké budoucnosti. `minimumPrecision` Parametr existuje na `EntityRecognitionSkill` pro budoucí použití a zpětné kompatibility.

1. *(Povinné)*  Změnit `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` k `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Volitelné)*  Pokud provádíte využívání `entities` výstup, použijte `namedEntities` komplexní kolekci výstup `EntityRecognitionSkill` místo. Můžete použít `targetName` ve dovednosti s názvem definice pro mapování na poznámku `entities`.

3. *(Volitelné)*  Pokud explicitně neurčíte `categories`, `EntityRecognitionSkill` může vrátit jiný typ kategorie kromě těch, které se nepodporuje `NamedEntityRecognitionSkill`. Pokud toto chování nežádoucí, ujistěte se, že explicitně nastavit `categories` parametr `["Person", "Location", "Organization"]`.

    _Ukázka migrace definice_

    * Jednoduchá migrace

        _(Před) Definice NamedEntityRecognition dovedností_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Po) Definice EntityRecognition dovedností_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * O něco složitější migrace

        _(Před) Definice NamedEntityRecognition dovedností_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Po) Definice EntityRecognition dovedností_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Další informace najdete v tématech

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md)
