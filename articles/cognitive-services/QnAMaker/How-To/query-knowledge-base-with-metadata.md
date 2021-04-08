---
title: Kontextové filtrování pomocí metadat
titleSuffix: Azure Cognitive Services
description: QnA Maker filtruje páry QnA podle metadat.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022074"
---
# <a name="filter-responses-with-metadata"></a>Filtrování odpovědí s metadaty

QnA Maker umožňuje přidávat metadata ve formě párů klíč-hodnota na vaše páry otázek a odpovědí. Tyto informace pak můžete použít k filtrování výsledků do uživatelských dotazů a k ukládání dalších informací, které je možné použít v následných konverzacích.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Uložení otázek a odpovědí s entitou QnA

Je důležité porozumět tomu, jak QnA Maker ukládá data otázky a odpovědi. Následující ilustrace znázorňuje QnA entitu:

![Ilustrace entity QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečné a trvalé ID. Pomocí ID můžete provádět aktualizace konkrétní entity QnA.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Filtrování odpovědí podle vlastních značek metadat pomocí metadat

Přidání metadat umožňuje filtrovat odpovědi pomocí těchto značek metadat. Přidejte sloupec metadata z nabídky **Možnosti zobrazení** . Přidejte metadata do znalostní báze tak, že vyberete **+** ikonu metadat a přidáte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Snímek obrazovky s přidáním metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrovat výsledky pomocí strictFilters pro značky metadat

Vezměte v úvahu otázku uživatele "kdy se tento hotel blíží?", kde je záměr předpokládaný pro restauraci "Paradise".

Vzhledem k tomu, že se výsledky vyžadují jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer v metadatech "název restaurace". Následující příklad ukazuje:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logický operátor AND ve výchozím nastavení

Chcete-li v dotazu zkombinovat několik filtrů metadat, přidejte do pole vlastnosti další filtry metadat `strictFilters` . Ve výchozím nastavení jsou hodnoty logicky kombinovány (a). Logická kombinace vyžaduje, aby všechny filtry odpovídaly dvojicím QnA, aby se dvojice vracela v odpovědi.

To je ekvivalentní použití `strictFiltersCompoundOperationType` vlastnosti s hodnotou `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logická nebo použití vlastnosti strictFiltersCompoundOperationType

Pokud se zkombinujete s několika filtry metadat a chcete použít jenom jeden nebo několik filtrů, které odpovídají, použijte `strictFiltersCompoundOperationType` vlastnost s hodnotou `OR` .

Vaše znalostní báze umožňuje vracet odpovědi, pokud libovolný filtr odpovídá, ale nevrátí odpovědi, které nemají metadata.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Příklady metadat v rychlých startech

Další informace o metadatech v rychlém startu portálu QnA Maker pro metadata:
* [Vytváření – přidání metadat do páru QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prediktivní dotazování – filtrování odpovědí podle metadat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Použití výsledků otázek a odpovědí k zachování kontextu konverzace

Odpověď na GenerateAnswer obsahuje odpovídající informace o metadatech spárované dvojice otázek a odpovědí. Tyto informace můžete v klientské aplikaci použít k uložení kontextu předchozí konverzace pro použití v pozdějších konverzacích.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza znalostní báze](../How-to/get-analytics-knowledge-base.md)
