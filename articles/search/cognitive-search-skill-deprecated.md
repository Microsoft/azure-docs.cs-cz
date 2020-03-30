---
title: Zastaralé kognitivní dovednosti
titleSuffix: Azure Cognitive Search
description: Tato stránka obsahuje seznam kognitivních dovedností, které jsou považovány za zastaralé a nebudou podporovány v blízké budoucnosti v dovednostech Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792021"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Zastaralé kognitivní dovednosti v Azure Cognitive Search

Tento dokument popisuje kognitivní dovednosti, které jsou považovány za zastaralé. Pro obsah použijte následující příručku:

* Název dovednosti: Název dovednosti, která bude zastaralá, se @odata.type mapuje na atribut.
* Poslední dostupná verze rozhraní API: Poslední verze veřejného rozhraní API Azure Cognitive Search, jehož prostřednictvím lze vytvořit nebo aktualizovat dovednosti obsahující odpovídající zastaralé dovednosti.
* Konec podpory: Poslední den, po kterém je odpovídající dovednost považována za nepodporovanou. Dříve vytvořené dovednosti by měly i nadále fungovat, ale uživatelům se doporučuje migrovat z zastaralé dovednosti.
* Doporučení: Cesta k migraci vpřed použít podporovanou dovednost. Uživatelům se doporučuje dodržovat doporučení, aby i nadále dostávat podporu.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Poslední dostupná verze rozhraní API

2017-11-11-Preview

### <a name="end-of-support"></a>Ukončení podpory

15. února 2019

### <a name="recommendations"></a>Doporučení 

Místo toho použijte [Microsoft.Skills.Text.EntityRecognitionSkill.](cognitive-search-skill-entity-recognition.md) Poskytuje většinu funkcí NamedEntityRecognitionSkill ve vyšší kvalitě. Má také bohatší informace ve svých složitých výstupních polích.

Chcete-li migrovat na [dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md), budete muset provést jednu nebo více z následujících změn definice dovedností. Definici dovedností můžete aktualizovat pomocí rozhraní [Update Skillset API](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> V současné době není podporováno skóre spolehlivosti jako koncept. Parametr `minimumPrecision` existuje pro `EntityRecognitionSkill` budoucí použití a pro zpětnou kompatibilitu.

1. *(Povinné)* Změňte `@odata.type` `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` z `"#Microsoft.Skills.Text.EntityRecognitionSkill"`na .

2. *(Nepovinné)* Pokud používáte `entities` výstup, použijte výstup `namedEntities` komplexní kolekce `EntityRecognitionSkill` z místo. Definici `targetName` dovednosti můžete použít k mapování na poznámku `entities`nazvanou .

3. *(Nepovinné)* Pokud nezadáte explicitně `categories`, `EntityRecognitionSkill` může vrátit jiný typ kategorií kromě `NamedEntityRecognitionSkill`těch, které byly podporovány . Pokud je toto chování nežádoucí, nezapomeňte `categories` explicitně nastavit parametr na `["Person", "Location", "Organization"]`.

    _Ukázkové definice migrace_

    * Jednoduchá migrace

        _(Dříve) Definice dovednosti NamedEntityRecognition_
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
        _(Po) Definice dovednosti EntityRecognition_
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
    
    * Mírně komplikovaná migrace

        _(Dříve) Definice dovednosti NamedEntityRecognition_
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
        _(Po) Definice dovednosti EntityRecognition_
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
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md)
