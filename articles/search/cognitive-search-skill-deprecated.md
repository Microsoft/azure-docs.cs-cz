---
title: Zastaralé dovednosti při rozpoznávání
titleSuffix: Azure Cognitive Search
description: Tato stránka obsahuje seznam dovedností rozpoznávání, které se považují za zastaralé a v blízké budoucnosti se v Azure Kognitivní hledání dovednosti nepodporují.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936091"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Zastaralé dovednosti při rozpoznávání v Azure Kognitivní hledání

Tento dokument popisuje vnímání dovedností, které se považují za zastaralé. Pro obsah použijte následující příručku:

* Dovednost název: název dovednosti, která bude zastaralá, bude mapována na @odata.type atribut.
* Poslední dostupná verze rozhraní API: poslední verze veřejného rozhraní API Azure Kognitivní hledání, přes který se dá vytvořit nebo aktualizovat dovednosti obsahující odpovídající vystaralou dovednost.
* Konec podpory: poslední den, po kterém se odpovídající dovednost považuje za nepodporovanou. Dříve vytvořená dovednosti by měla dál fungovat, ale uživatelé se doporučují migrovat z nepoužívané dovednosti.
* Doporučení: předejte cestu k migraci, aby používala podporovanou dovednost. Uživatelům se doporučuje postupovat podle doporučení, aby mohli nadále přijímat podporu.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. dovednosti. text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Poslední dostupná verze rozhraní API

2017-11-11-Preview

### <a name="end-of-support"></a>Konec podpory

15. února 2019

### <a name="recommendations"></a>Doporučení 

Místo toho použijte [Microsoft. dovednosti. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Poskytuje většinu funkcí NamedEntityRecognitionSkill s vyšší kvalitou. Obsahuje také bohatší informace ve svých komplexních výstupních polích.

Chcete-li provést migraci na [dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md), budete muset provést jednu nebo více následujících změn definice dovednosti. Definici dovedností můžete aktualizovat pomocí [rozhraní Update dovednosti API](/rest/api/searchservice/update-skillset).

> [!NOTE]
> V současné době není hodnocení spolehlivosti v rámci konceptu podporováno. `minimumPrecision`Parametr existuje v `EntityRecognitionSkill` pro budoucí použití a pro zpětnou kompatibilitu.

1. *(Povinné)* Změňte `@odata.type` z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` na `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Volitelné)* Pokud používáte `entities` výstup, použijte `namedEntities` místo toho komplexní výstup z kolekce `EntityRecognitionSkill` . Můžete použít `targetName` v definici dovedností k namapování na poznámku nazvanou `entities` .

3. *(Volitelné)* Pokud explicitně neurčíte `categories` , `EntityRecognitionSkill` může vracet jiný typ kategorií kromě těch, které byly podporovány v `NamedEntityRecognitionSkill` . Pokud je toto chování nežádoucí, nezapomeňte explicitně nastavit `categories` parametr na `["Person", "Location", "Organization"]` .

    _Ukázkové definice migrace_

    * Jednoduchá migrace

        _Uložení Definice dovednosti NamedEntityRecognition_
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
        _Konci Definice dovednosti EntityRecognition_
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
    
    * Mírně složitá migrace

        _Uložení Definice dovednosti NamedEntityRecognition_
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
        _Konci Definice dovednosti EntityRecognition_
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

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Dovednost pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md)