---
title: Použití metadata v znalostní báze společně s rozhraní API GenerateAnswer | Microsoft Docs
description: Pomocí rozhraní API GenerateAnswer metadat
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "35343908"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>Pomocí metadat a rozhraní API GenerateAnswer

QnA Maker můžete přidat metadata, ve formě páry klíč/hodnota do vaší sady otázek a odpovědí. Tyto informace můžete použít různé způsoby, například filtrování výsledků na dotazy, uživatele, zvýšit určité výsledky a ukládání Další informace, které lze použít v následných konverzace. Další informace najdete v tématu [znalostní báze Knowledge base](../Concepts/knowledge-base.md).

## <a name="qna-entity"></a>QnA Entity

Nejprve je důležité pochopit, jak QnA Maker ukládá data otázek a odpovědí. Následující obrázek znázorňuje QnA entity:

![QnA Entity](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Každá entita QnA má jedinečný a trvalé ID. ID můžete použít k nastavení aktualizace pro konkrétní QnA entity.

## <a name="generateanswer-api"></a>GenerateAnswer rozhraní API

Můžete použít rozhraní API GenerateAnswer na robota nebo v aplikaci k dotazování znalostní báze s otázkou uživatele k získání nejlepší shodu z otázek a odpovědí sady.

### <a name="generateanswer-endpoint"></a>Koncový bod GenerateAnswer

Po publikování znalostní báze, buď z [QnA Maker portál](https://www.qnamaker.ai), nebo pomocí [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff), můžete získat podrobnosti o GenerateAnswer koncový bod.

Pokud chcete získat koncový bod podrobnosti:
1. Přihlaste se k [ https://www.qnamaker.ai ](https://www.qnamaker.ai).
2. V **Moje znalostních bází**, klikněte na **kód zobrazení** pro vaše znalostní báze knowledge base.
![Moje znalostních bází](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. Získání podrobností o GenerateAnswer koncový bod.

![koncový bod – podrobnosti](../media/qnamaker-how-to-metadata-usage/view-code.png)

Můžete získat z podrobností o koncový bod **nastavení** kartě znalostní báze.

### <a name="generateanswer-request"></a>Žádost o GenerateAnswer

Zavoláte GenerateAnswer s požadavek HTTP POST. Ukázkový kód, který ukazuje způsob volání GenerateAnswer, najdete [– elementy QuickStart](../quickstarts/csharp.md).

- **Adresa URL požadavku**: https://{QnA Maker endpoint} /knowledgebases/ {ID znalostní báze knowledge base} / generateAnswer

- **Parametry žádosti**: 
    - **Znalostní báze Knowledge base ID** (řetězec): GUID pro vaše znalostní báze knowledge base.
    - **Koncový bod QnAMaker** (řetězec): název hostitele koncového bodu nasadit ve vašem předplatném Azure.
- **Hlavičky požadavku**
    - **Content-Type** (řetězec): typ média obsahu odeslaných do rozhraní API.
    - **Autorizace** (řetězec): klíč koncového bodu.
- **Text žádosti**
    - **Otázka** (řetězec): otázku uživatele Chcete-li být dotaz proti znalostní báze.
    - **horní** (volitelné, celé číslo): počet seřazený výsledků, které chcete zahrnout do výstupu. Výchozí hodnota je 1.
    - **ID uživatele** (volitelný, řetězec): jedinečné ID pro identifikaci uživatele. Toto ID se zaznamená v protokolech konverzace.
    - **strictFilters** (volitelný, řetězec):-li zadána, informuje QnA Maker vrátit pouze odpovědi, které mají Zadaná metadata. Další informace najdete níže.
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer odpovědi

- **Odpovědi 200** – úspěšné volání vrátí výsledek na otázku. Odpověď obsahuje následující pole:
    - **odpovědi** – seznam odpovědi na dotaz uživatele, seřazené v sestupném pořadí podle klasifikace skóre.
        - **skóre**: hodnocení skóre mezi 0 a 100.
        - **otázky**: otázky zadané uživatelem.
        - **Zdroj**: název zdroje, ze kterého byla odpověď vydání nebo uložit ve znalostní bázi knowledge base.
        - **metadata**: metadata související s odpovědí.
            - Název: název metadat. (string, maximální délka: 100, požadované)
            - hodnota: hodnota metadat. (string, maximální délka: 100, požadované)
        - **ID**: jedinečné ID přiřazené k odpovědi.
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>Příklad metadat

Vezměte v úvahu nižší než nejčastější dotazy týkající se data pro restaurace v Hyderabad. Kliknutím na ikonu ozubené kolečko přidáte metadata do znalostní báze.

![Přidání metadat](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>Filtrování výsledků s strictFilters

Zvažte otázku uživatele "Pokud nemá tento hotelů zavřít?" kde je záměr implicitní pro restaurace "Paradise."

Vzhledem k tomu, že výsledky se vyžaduje jenom pro restaurace "Paradise", můžete nastavit filtr ve volání GenerateAnswer na metadata "restaurace" název, následujícím způsobem.

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>Zachovat kontextu
Odpověď GenerateAnswer obsahuje odpovídající informace metadat sady odpovídající otázek a odpovědí, následujícím způsobem.

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

Tyto informace slouží k zaznamenání kontextu předchozí konverzace pro použití v novější konverzace. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](./create-knowledge-base.md)
