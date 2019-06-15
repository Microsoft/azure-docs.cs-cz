---
title: Zastaralé kognitivní dovednosti – Azure Search
description: Tato stránka obsahuje seznam kognitivního vyhledávání dovednosti, které se považují za zastaralé a aplikace nebude podporována v blízké budoucnosti.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a73c7e381cb6001b773251a1812466b3c82373f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541745"
---
# <a name="deprecated-cognitive-search-skills"></a>Dovednosti nepoužívané kognitivního vyhledávání

Tento dokument popisuje kognitivní dovednosti, které se považují za zastaralé. Použijte následující pokyny pro obsah:

* Název dovedností: Název dovednosti, které se nepoužívá, mapuje se na @odata.type atribut.
* Poslední dostupná verze rozhraní api: Poslední verze Azure vyhledejte veřejné rozhraní API, pomocí které dovednosti obsahující odpovídající dovednosti nepoužívané může být vytvořené nebo aktualizované.
* Konec podpory: Poslední den po jejímž uplynutí je považován za odpovídající dovednosti není podporován. Dříve vytvořili dovednosti musí i nadále fungovat, ale uživatelé jsou doporučena pro opustit nepoužívané dovedností.
* Doporučení: Cesty migrace vpřed na použití podporované dovedností. Uživatelé by měli postupovat podle doporučení nadále využívat podporu.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Poslední dostupná verze rozhraní api

2019-05-06-Preview

### <a name="end-of-support"></a>Ukončení podpory

15\. února 2019

### <a name="recommendations"></a>Doporučení 

Use [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) instead. Poskytuje většinu funkcí NamedEntityRecognitionSkill ve větší kvalitě. Má také bohatší informace v jeho komplexní výstupních polí.

Migrace na [dovednosti rozpoznávání entit](cognitive-search-skill-entity-recognition.md), budete muset provést jeden nebo více z následujících změn do definice dovedností. Můžete aktualizovat definici dovednosti pomocí [aktualizovat rozhraní API dovednosti](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> Skóre spolehlivosti jako koncept v současné době se nepodporuje. `minimumPrecision` Parametr existuje na `EntityRecognitionSkill` pro budoucí použití a zpětné kompatibility.

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
