---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386535"
---
Tento rychlý Start založený na držiteli vás provede tím, že získáte odpověď ze znalostní báze.

## <a name="prerequisites"></a>Předpoklady

* Nejnovější [**příspěvek**](https://www.getpostman.com/).
* Musíte mít
    * [Služba QnA maker](../How-To/set-up-qnamaker-service-azure.md)
    * Vyškolená a publikovaná [znalostní báze s dotazy a odpověďmi](../Quickstarts/add-question-metadata-portal.md) vytvořenými v rychlém startu je nakonfigurována pomocí funkce konverzace s metadaty a funkce CHITEST.

> [!NOTE]
> Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze Knowledge Base, musíte [vyškolit](../Quickstarts/create-publish-knowledge-base.md#save-and-train) a [publikovat](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) znalostní bázi. Když je vaše znalostní báze publikována, zobrazí se na stránce **publikovat** nastavení požadavku HTTP, které vygeneruje odpověď. Karta **po odeslání** zobrazuje nastavení potřebná k vygenerování odpovědi.

## <a name="set-up-postman-for-requests"></a>Nastavení metody post pro žádosti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

V tomto rychlém startu se používá stejné nastavení pro **odeslání požadavku POST** a potom se nakonfiguruje tak, aby se kód JSON odeslal na službu na základě toho, na co se pokoušíte zadat dotaz.

Pomocí tohoto postupu nakonfigurujete příkaz post a potom si přečtěte jednotlivé další části, kde můžete nakonfigurovat JSON pro tělo příspěvku.

1. Na stránce **Nastavení** znalostní báze vyberte kartu **publikovat** a zobrazte tak konfiguraci používanou k vygenerování odpovědi ze znalostní báze. Zkopírujte následující informace, které se použijí v poli post.

    |Name (Název)|Nastavení|Účel a hodnota|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Toto je metoda HTTP a trasa pro adresu URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Toto je hostitel adresy URL. Po zřetězení hodnoty host a post získáte úplnou adresu URL generateAnswer.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Hodnota hlavičky pro autorizaci vaší žádosti do Azure |
    |`Content-type`|`application/json`|Hodnota hlavičky vašeho obsahu|
    ||`{"question":"<Your question>"}`|Tělo žádosti POST jako objekt JSON Tato hodnota se v každé následující části změní v závislosti na tom, co má dotaz udělat.|

1. Otevřete post a vytvořte novou základní žádost **post** s publikovaným nastavením znalostní báze. V následujících částech změňte formát JSON těla zprávy tak, aby se změnil dotaz na znalostní bázi.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V tomto rychlém startu se používá stejné nastavení pro **odeslání požadavku POST** a potom se nakonfiguruje tak, aby se kód JSON odeslal na službu na základě toho, na co se pokoušíte zadat dotaz.

Pomocí tohoto postupu nakonfigurujete příkaz post a potom si přečtěte jednotlivé další části, kde můžete nakonfigurovat JSON pro tělo příspěvku.

1. Na stránce **Nastavení** znalostní báze vyberte kartu **publikovat** a zobrazte tak konfiguraci používanou k vygenerování odpovědi ze znalostní báze. Zkopírujte následující informace, které se použijí v poli post.

    |Name (Název)|Nastavení|Účel a hodnota|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Toto je metoda HTTP a trasa pro adresu URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Toto je hostitel adresy URL. Po zřetězení hodnoty host a post získáte úplnou adresu URL generateAnswer.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Hodnota hlavičky pro autorizaci vaší žádosti. |
    |`Content-type`|`application/json`|Hodnota hlavičky vašeho obsahu|
    ||`{"question":"<Your question>"}`|Tělo žádosti POST jako objekt JSON Tato hodnota se v každé následující části změní v závislosti na tom, co má dotaz udělat.|

1. Otevřete post a vytvořte novou základní žádost **post** s publikovaným nastavením znalostní báze. V následujících částech změňte formát JSON těla zprávy tak, aby se změnil dotaz na znalostní bázi.
---

## <a name="use-metadata-to-filter-answer"></a>Filtrovat odpověď pomocí metadat

V předchozím rychlém startu se metadata přidaly do dvou párů QnA a rozlišily se mezi dvěma různými otázkami. Přidejte metadata do dotazu, abyste omezili filtr jenom na příslušný pár QnA.

1. V poli post změňte pouze JSON dotazu tak, že přidáte `strictFilters` vlastnost s dvojicí název-hodnota `service:qna_maker` . KÓD JSON pro tělo by měl být:

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

    Otázka je jenom jedno slovo, `size` které může vracet jednu ze dvou párů otázek a odpovědí. `strictFilters`Pole oznamuje odpověď, aby se snížila pouze na `qna_maker` odpovědi.

1. Odpověď obsahuje pouze odpověď, která splňuje kritéria filtru.

    Byla naformátována následující odpověď pro čitelnost:

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

    Pokud existuje pár otázek a odpovědí, které nevyhověly hledanému termínu, ale nesplnila tento filtr, nebude vrácen. Místo toho se vrátí obecná odpověď `No good match found in KB.` .

## <a name="use-debug-query-property"></a>Použít vlastnost dotazu pro ladění

> [!NOTE]
>Pro žádnou závislost nedoporučujeme používat vlastnost ladění. Tato vlastnost byla přidána pro pomoc s produktovým týmem při řešení potíží.

Informace o ladění vám pomůžou pochopit, jak byla zjištěna odpověď. I když je to užitečné, není nutné. Pokud chcete vygenerovat odpověď s ladicími informacemi, přidejte `debug` vlastnost:

1. V poli post změňte pouze JSON těla přidáním `debug` Vlastnosti. JSON by měl být:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Odpověď obsahuje relevantní informace o odpovědi. V následujícím výstupu JSON byly některé detaily ladění nahrazeny třemi tečkami.

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

## <a name="use-test-knowledge-base"></a>Použít testovací znalostní bázi

Pokud chcete získat odpověď ze zkušební znalostní báze, použijte `isTest` vlastnost text.

V poli post změňte pouze JSON těla přidáním `isTest` Vlastnosti. JSON by měl být:

```json
{
    'question':'size',
    'isTest': true
}
```

Odpověď JSON používá stejné schéma jako publikovaný dotaz znalostní báze.

> [!NOTE]
> Pokud jsou testovací a zveřejněné znalostní báze přesně stejné, může se stát, že je stále trochu proměnlivá variace, protože index testu se sdílí mezi všemi bázemi znalostí v prostředku.

## <a name="query-for-a-chit-chat-answer"></a>Dotaz na příkaz CHITEST-chat Answer

1. V poli post změňte pouze JSON těla na příkaz ukončení konverzace od uživatele. JSON by měl být:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Odpověď zahrnuje skóre a odpověď.

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

    Vzhledem k tomu, že otázka `Thank you` (Děkuji) přesně odpovídá konverzační otázce, služba QnA Maker udává naprostou jistotu se skóre 100. QnA Maker také vrátil všechny související otázky a vlastnost metadata obsahující informace značky metadat CHITEST-chat.

## <a name="use-threshold-and-default-answer"></a>Použít prahovou hodnotu a výchozí odpověď

Pro odpověď můžete požádat o minimální prahovou hodnotu. Pokud není prahová hodnota splněna, je vrácena výchozí odpověď.

1. V poli post změňte pouze JSON těla na příkaz ukončení konverzace od uživatele. JSON by měl být:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Znalostní báze by neměl tuto odpověď najít, protože skóre otázky je 71% a místo toho vrátí výchozí odpověď, kterou jste zadali při vytváření znalostní báze.

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

    QnA Maker vrátilo skóre `0` , což znamená bez obav. Vrátila se také výchozí odpověď.

1. Změňte prahovou hodnotu na 60% a požádejte o dotaz znovu:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Vrácený kód JSON nalezl odpověď.

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