---
title: 'Kurz: Kontrola koncového bodu projevy-LUIS'
description: V tomto kurzu Vylepšete předpovědi aplikace tím, že ověříte nebo opravíte projevy, které jste obdrželi prostřednictvím koncového bodu HTTP LUIS, který LUIS nedrží. U některých promluv může být potřeba zkontrolovat záměr, zatímco u jiných entitu.
services: cognitive-services
ms.topic: tutorial
ms.date: 06/22/2020
ms.openlocfilehash: c2df8cdba3422c522aa4ccf1fe4138a510355d12
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445916"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Kurz: Opravte si nejistotu, že předpovědi zkontroluje projevy koncového bodu.
V tomto kurzu Vylepšete předpovědi aplikací tím, že ověříte nebo opravíte projevy, přijmete prostřednictvím koncového bodu LUIS HTTPS, který LUIS NEsI nedrží. Měli byste zkontrolovat projevy koncového bodu jako běžnou součást plánované údržby LUIS.

Tento proces revize umožňuje LUIS zjistit vaši doménu aplikace. LUIS vybere projevy, který se zobrazí v seznamu revizí. Tento seznam:

* Je specifický pro danou aplikaci.
* Má za cíl zlepšit přesnost předpovědí aplikace.
* Měl by se pravidelně kontrolovat.

Kontrolou projevů koncového bodu ověřujete nebo opravujete předpokládaný záměr promluvy.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Kontrola promluv koncového bodu
> * Výuka a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Stáhnout soubor JSON pro aplikaci

Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true) a uložte si ho.

## <a name="import-json-file-for-app"></a>Importovat soubor JSON pro aplikaci

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Výuka aplikace pro použití změn entit v aplikaci

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Přidat projevy na koncový bod

V této aplikaci máte záměry a entity, ale nemáte žádné použití koncového bodu. Toto použití koncového bodu je potřeba ke zlepšení aplikace pomocí utterance revize koncového bodu.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejít na konec adresy URL na adresním řádku a nahraďte _YOUR_QUERY_HERE_ projevyem v následující tabulce. Pro každou utterance odešlete utterance a získejte výsledek. Pak na konci nahraďte utterance dalším utterance.

    |Utterance koncového bodu|Zarovnaný záměr|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Je jen jeden fond promluv ke kontrole bez ohledu na to, jakou verzi právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě.

## <a name="review-endpoint-utterances"></a>Kontrola promluv koncového bodu

Prohlédněte si projevy koncového bodu pro správné zarovnání záměru. I když existuje jeden fond projevy pro kontrolu napříč všemi verzemi, proces správného zarovnání záměru přidá příklad utterance pouze k aktuálnímu _aktivnímu modelu_ .

1. V části **Build (sestavení** ) na portálu vyberte **zkontrolovat koncový bod projevy** z levé navigační oblasti. Seznam je filtrovaný pro záměr **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Tento utterance, není `I'm looking for a job with Natural Language Processing` ve správném záměru.

1.  Chcete-li tuto utterance zarovnat, vyberte na řádku utterance správný **záměr zarovnání** `GetJobInformation` . Přidejte změněné utterance do aplikace tak, že vyberete značku zaškrtnutí.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Prohlédněte si zbývající projevy tohoto záměru a podle potřeby opravte zarovnaný záměr. K zobrazení zarovnaného záměru použijte úvodní tabulku utterance v tomto kurzu.

    Seznam **projevya koncového bodu Endpoint** by již neměl mít opravenou projevy. Pokud se zobrazí více projevy, pokračujte v práci se seznamem a opravte zarovnané záměry, dokud není seznam prázdný.

    Jakákoli oprava označování entit se provádí po zarovnání záměru na stránce s podrobnostmi záměru.

1. Trénujte a publikujte aplikaci znovu.

## <a name="get-intent-prediction-from-endpoint"></a>Získat předpověď záměru z koncového bodu

Pokud chcete ověřit, že se správně zarovnaný příklad projevy vylepšuje předpověď aplikace, zkuste utterance zavřít na opravené utterance.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. V adresním řádku pokračujte na konec adresy URL a nahraďte _YOUR_QUERY_HERE_ `Are there any natural language processing jobs in my department right now?` .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Teď, když je projevy správně zarovnaný, byl správný záměr předpověď s **vysokým skóre**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Dá se kontrola nahradit přidáním dalších promluv?
Asi vás napadá, proč nepřidat další ukázkové promluvy. Jaký je účel kontrol promluv koncového bodu? V reálných aplikacích LUIS jsou promluvy koncového bodu od uživatelů a mají volbu slov a uspořádání, které jste ještě nepoužili. Pokud jste použili stejnou volbu slov a uspořádání, původní predikce by měla vyšší procentní hodnocení.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Proč je hlavní záměr uvedený v seznamu promluv?
Některé z promluv koncového bodu budou mít v seznamu kontrol vysoké skóre předpovědi. Tyto promluvy musíte přesto zkontrolovat a ověřit. Jsou v tomto seznamu proto, že další záměr má skóre příliš blízko skóre nejlepšího záměru. Mezi jednotlivými hlavními záměry je dobré mít asi 15% rozdíl.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zkontrolovali promluvy získané z koncového bodu, které nemusela mít služba LUIS správně naučené. Jakmile se takové promluvy ověří a přesunou jako ukázkové promluvy do správných záměrů, zlepší se ve službě LUIS přesnost předpovědí.

> [!div class="nextstepaction"]
> [Jak používat vzory](luis-tutorial-pattern.md)
