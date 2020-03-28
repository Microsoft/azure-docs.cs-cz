---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 46947579ea72e2199af116442472eec330b38009
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112314"
---
Tento pošťák-založené rychlý start vás provede získáním odpovědi z vaší znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Poslední [**pošťák**](https://www.getpostman.com/).
* Musíte mít
    * [Služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Vyškolená a publikovaná [znalostní báze s otázkami a odpověďmi](../Quickstarts/add-question-metadata-portal.md) vytvořenými z rychlého startu je nakonfigurována s metadaty a chatem Chit.

> [!NOTE]
> Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze, musíte tuto znalostní báze [trénovat](../Quickstarts/create-publish-knowledge-base.md#save-and-train) a [publikovat.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) Po publikování znalostní báze se na stránce **Publikování** zobrazí nastavení požadavku HTTP pro generování odpovědi. Karta **Pošťák** zobrazuje nastavení potřebná ke generování odpovědi.

## <a name="set-up-postman-for-requests"></a>Nastavit pošťáka pro požadavky

Tento rychlý start používá stejné nastavení pro požadavek Postman **POST** pak konfiguruje post tělo JSON odeslané do služby na základě toho, co se pokoušíte dotaz.

Tento postup slouží ke konfiguraci Postman, pak si přečtěte každý následující oddíl pro konfiguraci těla POST JSON.

1. Na stránce **Nastavení** znalostní báze vyberte kartu **Pošťák** a zobrazte konfiguraci použitou ke generování odpovědi ze znalostní báze. Zkopírujte následující informace, které chcete použít v Postman.

    |Name (Název)|Nastavení|Účel a hodnota|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Toto je metoda HTTP a trasa adresy URL.|
    |`Host`|`https://diberry-qna-s0-s.azurewebsites.net/qnamaker`|Toto je hostitel adresy URL. Zřetězit hodnoty Host a Post získat kompletní generateAnswer URL.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Hodnota záhlaví pro autorizaci vašeho požadavku do Azure. |
    |`Content-type`|`application/json`|Hodnota záhlaví pro váš obsah.|
    ||`{"question":"<Your question>"}`|Tělo požadavku POST jako objekt JSON. Tato hodnota se změní v každé následující části v závislosti na tom, co dotaz má dělat.|

1. Otevřete Pošťák a vytvořte novou základní žádost **POST** s publikovaným nastavením znalostní báze. V následujících částech změňte tělo post JSON změnit dotaz na znalostní bázi.

## <a name="use-metadata-to-filter-answer"></a>Použití metadat k filtrování odpovědi

V předchozím rychlém startu metadata byla přidána do dvou sad QnA rozlišovat mezi dvěma různými otázkami. Přidejte metadata do dotazu, abyste omezili filtr pouze na příslušnou sadu QnA.

1. V Pošťáku změňte pouze dotaz `strictFilters` JSON přidáním vlastnosti s dvojicí název/hodnota . `service:qna_maker` Tělo JSON by mělo být:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Otázka je jen jedno `size`slovo, , které může vrátit jednu ze dvou otázek a odpovědí sad. Pole `strictFilters` říká odpověď snížit pouze `qna_maker` odpovědi.

1. Odpověď obsahuje pouze odpověď, která splňuje kritéria filtru.

    Následující odpověď byla naformátována pro čitelnost:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Pokud existuje sada otázek a odpovědí, která nesplňuje hledaný výraz, ale splnila filtr, nebude vrácena. Místo toho je `No good match found in KB.` vrácena obecná odpověď.

## <a name="use-debug-query-property"></a>Použít vlastnost dotazu ladění

Informace o ladění vám pomohou pochopit, jak byla určena vrácená odpověď. I když je to užitečné, není to nutné. Chcete-li vygenerovat odpověď s `debug` informacemi o ladění, přidejte vlastnost:

1. V Pošťák, změnit pouze tělo JSON přidáním vlastnosti. `debug` JSON by měl být:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Odpověď obsahuje příslušné informace o odpovědi. V následujícím výstupu JSON byly některé podrobnosti ladění nahrazeny třemi tečkami.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Použití znalostní báze test

Pokud chcete získat odpověď z testovací znalostní `isTest` báze, použijte vlastnost body.

V Pošťák, změnit pouze tělo JSON přidáním vlastnosti. `isTest` JSON by měl být:

```json
{
    'question':'size',
    'isTest': true
}
```

Odpověď JSON používá stejné schéma jako publikovaný dotaz znalostní báze.

> [!NOTE]
> Pokud jsou testovací a publikované znalostní báze přesně stejné, může stále existovat určitá nepatrná odchylka, protože index testu je sdílen mezi všechny znalostní báze v prostředku.

## <a name="query-for-a-chit-chat-answer"></a>Dotaz na odpověď chit-chat

1. V Pošťák, změnit pouze tělo JSON na konverzační příkaz od uživatele. JSON by měl být:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Odpověď obsahuje skóre a odpověď.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Vzhledem k tomu, že otázka `Thank you` (Děkuji) přesně odpovídá konverzační otázce, služba QnA Maker udává naprostou jistotu se skóre 100. QnA Maker také vrátil všechny související otázky, stejně jako vlastnost metadat a obsahující informace o značce metadat Chit-chat.

## <a name="use-threshold-and-default-answer"></a>Použít prahovou hodnotu a výchozí odpověď

Můžete požádat o minimální prahovou hodnotu pro odpověď. Pokud prahová hodnota není splněna, je vrácena výchozí odpověď.

1. V Pošťák, změnit pouze tělo JSON na konverzační příkaz od uživatele. JSON by měl být:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Znalostní báze by neměla najít tuto odpověď, protože skóre otázky je 71 %, a místo toho vrátit výchozí odpověď, kterou jste zadali při vytváření znalostní báze.

    Vrácená odpověď JSON, včetně skóre a odpovědi, je:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker vrátil `0`skóre , což znamená žádnou důvěru. Také vrátil výchozí odpověď.

1. Změňte prahovou hodnotu na 60 % a požádejte o dotaz znovu:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Vrácená Odpověď našel JSON.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```