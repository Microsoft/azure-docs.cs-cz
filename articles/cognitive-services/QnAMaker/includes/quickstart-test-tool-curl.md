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
ms.openlocfilehash: 4bd483e40e3a85a2934e58abdf46d09b17a33ed4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758869"
---
Tento rychlý start založený na cURL vás provede získáním odpovědi z vaší znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Poslední [**cURL**](https://curl.haxx.se/).
* Musíte mít
    * [Služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Vyškolená a publikovaná znalostní báze s otázkami a odpověďmi z předchozího [rychlého startu](../Quickstarts/add-question-metadata-portal.md)nakonfigurovaná pomocí metadat a chatu Chit.

> [!NOTE]
> Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze, musíte tuto znalostní báze [trénovat](../Quickstarts/create-publish-knowledge-base.md#save-and-train) a [publikovat.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) Po publikování znalostní báze se na stránce **Publikování** zobrazí nastavení požadavku HTTP pro generování odpovědi. Karta **cURL** zobrazuje nastavení potřebná ke generování odpovědi z nástroje příkazového řádku.

## <a name="use-metadata-to-filter-answer"></a>Použití metadat k filtrování odpovědi

Pomocí znalostní báze z předchozího rychlého dotazu na odpověď na základě metadat.

1. Na stránce **Nastavení** znalostní báze vyberte kartu **CURL,** abyste viděli ukázkový příkaz cURL, který slouží ke generování odpovědi ze znalostní báze.
1. Zkopírujte příkaz do upravitelného prostředí (například textového souboru), abyste ho mohli upravit. Upravte hodnotu otázky takto, aby `service:qna_maker` metadata z se používá jako filtr pro dvojice QnA.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Otázkou je jen jedno `size`slovo, , které může vrátit jeden ze dvou párů QnA. Pole `strictFilters` říká odpověď snížit pouze `qna_maker` odpovědi.

1. Odpověď obsahuje pouze odpověď, která splňuje kritéria filtru. Následující odpověď cURL byla naformátována pro čitelnost:

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

```json
Debug: {Enable:true}
```

1. Chcete-li zobrazit další informace, upravte příkaz cURL tak, aby obsahoval vlastnost ladění.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```

1. Odpověď obsahuje příslušné informace o odpovědi. V následujícím výstupu JSON byly některé podrobnosti ladění nahrazeny třemi tečkami pro stručnost.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
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

Vlastnost je logická hodnota.

```json
isTest:true
```

Příkaz cURL vypadá takto:

```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

Odpověď JSON používá stejné schéma jako publikovaný dotaz znalostní báze.

> [!NOTE]
> Pokud jsou testovací a publikované znalostní báze přesně stejné, může stále existovat určitá nepatrná odchylka, protože index testu je sdílen mezi všechny znalostní báze v prostředku.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Použití cURL k dotazování na odpověď chit-chatu

1. V terminálu s podporou cURL použijte příkaz, který ukončí `Thank you` konverzaci robota od uživatele, například otázku. Nejsou k dispozici žádné další vlastnosti, které by bylo třeba nastavit.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```

1. Spusťte příkaz cURL a přijměte odpověď JSON, včetně skóre a odpovědi.

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

## <a name="use-curl-with-threshold-and-default-answer"></a>Použití cURL s prahovou hodnotou a výchozí odpovědí

Můžete požádat o minimální prahovou hodnotu pro odpověď. Pokud prahová hodnota není splněna, je vrácena výchozí odpověď.

1. Pomocí následujícího příkazu cURL, který nahradí vlastní název prostředku, ID znalostní báze a klíč koncového bodu, požádejte o `size` odpověď s prahovou hodnotou 80 % nebo lepší. Znalostní báze by neměla najít tuto odpověď, protože skóre otázky je 71 %, a místo toho vrátit výchozí odpověď, kterou jste zadali při vytváření znalostní báze.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Spusťte příkaz cURL a přijměte odpověď JSON, včetně skóre a odpovědi.

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

## <a name="use-curl-with-threshold-and-default-answer"></a>Použití cURL s prahovou hodnotou a výchozí odpovědí

Můžete požádat o minimální prahovou hodnotu pro odpověď. Pokud prahová hodnota není splněna, je vrácena výchozí odpověď.

1. Přidejte `threshold` vlastnost a požádejte `size` o odpověď s prahem 80% nebo lepším. Znalostní báze by neměla najít tuto odpověď, protože skóre otázky je 71%. Výsledek vrátí výchozí odpověď, kterou jste zadali při vytváření znalostní báze.

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```

1. Spusťte příkaz cURL a přijměte odpověď JSON.

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

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
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
