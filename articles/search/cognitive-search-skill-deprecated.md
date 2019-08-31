---
title: Nepoužívané vnímání zkušeností – Azure Search
description: Tato stránka obsahuje seznam dovedností pro hledání vnímání, které se považují za zastaralé a v blízké budoucnosti se nepodporují.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: e149edbf89ae68364439876ee59dd0605d33b882
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183426"
---
# <a name="deprecated-cognitive-search-skills"></a>Nepoužívané dovednosti při hledání rozpoznávání

Tento dokument popisuje vnímání dovedností, které se považují za zastaralé. Pro obsah použijte následující příručku:

* Název dovednosti: Název dovednosti, která bude zastaralá, bude mapována na @odata.type atribut.
* Poslední dostupná verze rozhraní API: Poslední verze veřejného rozhraní API služby Azure Search, přes který je možné vytvořit nebo aktualizovat dovednosti obsahující odpovídající zastaralou dovednost
* Konec podpory: Poslední den, po kterém se odpovídající dovednost považuje za nepodporovanou Dříve vytvořená dovednosti by měla dál fungovat, ale uživatelé se doporučují migrovat z nepoužívané dovednosti.
* Doporučit Cesta pro migraci k používání podporované dovednosti. Uživatelům se doporučuje postupovat podle doporučení, aby mohli nadále přijímat podporu.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Poslední dostupná verze rozhraní API

2017-11-11-Preview

### <a name="end-of-support"></a>Konec podpory

15. února 2019

### <a name="recommendations"></a>Doporučení 

Místo toho použijte [Microsoft. dovednosti. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Poskytuje většinu funkcí NamedEntityRecognitionSkill s vyšší kvalitou. Obsahuje také bohatší informace ve svých komplexních výstupních polích.

Chcete-li provést migraci na [dovednost rozpoznávání entit](cognitive-search-skill-entity-recognition.md), budete muset provést jednu nebo více následujících změn definice dovednosti. Definici dovedností můžete aktualizovat pomocí [rozhraní Update dovednosti API](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

> [!NOTE]
> V současné době není hodnocení spolehlivosti v rámci konceptu podporováno. `minimumPrecision` Parametr existuje`EntityRecognitionSkill` v pro budoucí použití a pro zpětnou kompatibilitu.

1. *(Povinné)* `@odata.type` Změňte z `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` na. `"#Microsoft.Skills.Text.EntityRecognitionSkill"`

2. *(Volitelné)* Pokud používáte `namedEntities`výstup, použijte `EntityRecognitionSkill` místo toho komplexní výstup z kolekce. `entities` Můžete použít `targetName` v definici dovedností k namapování na poznámku nazvanou `entities`.

3. *(Volitelné)* Pokud explicitně neurčíte `categories` `EntityRecognitionSkill` , může vracet jiný typ kategorií kromě těch, které byly podporovány v `NamedEntityRecognitionSkill`. Pokud je toto chování nežádoucí, nezapomeňte explicitně nastavit `categories` parametr na. `["Person", "Location", "Organization"]`

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

## <a name="see-also"></a>Viz také:

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Dovednost pro rozpoznávání entit](cognitive-search-skill-entity-recognition.md)
