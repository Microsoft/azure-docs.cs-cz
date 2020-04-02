---
title: 'Kurz: Kontrola promluv koncového bodu – LUIS'
description: V tomto kurzu zlepšit předpovědi aplikací ověřením nebo opravou projevy přijaté prostřednictvím koncového bodu LUIS HTTP, který LUIS není jistý. U některých promluv může být potřeba zkontrolovat záměr, zatímco u jiných entitu.
services: cognitive-services
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 307c18d3326cb1a64b884463a571985a015834ed
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548724"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Kurz: Oprava nejistých předpovědí kontrolou promluv koncového bodu
V tomto kurzu zlepšit předpovědi aplikací ověřením nebo opravou projevy, přijaté prostřednictvím koncového bodu LUIS HTTPS, že LUIS není jistý. Měli byste zkontrolovat projevy koncového bodu jako pravidelnou součást naplánované údržby LUIS.

Tento proces kontroly umožňuje LUIS naučit doménu aplikace. Služba LUIS vybere projevy, které se zobrazí v seznamu recenzí. Tento seznam:

* Je specifický pro danou aplikaci.
* Má za cíl zlepšit přesnost předpovědí aplikace.
* Měl by se pravidelně kontrolovat.

Kontrolou projevů koncového bodu ověřujete nebo opravujete předpokládaný záměr promluvy.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkové aplikace
> * Kontrola promluv koncového bodu
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkové aplikace

K importu aplikace použijte následující kroky.

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json?raw=true) a uložte si ho.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Trénování aplikace k použití změn entity v aplikaci

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikování aplikace pro přístup z koncového bodu HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Přidání promluv v koncovém bodě

V této aplikaci máte záměry a entity, ale nemáte žádné využití koncového bodu. Toto použití koncového bodu je nutné zlepšit aplikaci s kontrolou utterance koncového bodu.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adresním řádku a nahraďte _YOUR_QUERY_HERE_ projevy v následující tabulce. Pro každý utterance, odešle projev a získat výsledek. Pak nahradit utterance na konci další utterance.

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

Zkontrolujte projevy koncového bodu pro správně zarovnaný záměr. Zatímco je jeden fond projevy ke kontrole ve všech verzích, proces správně zarovnání záměru přidá ukázkový utterance pouze aktuální _aktivní model._

1. V části **Sestavení** portálu vyberte **zkontrolovat projevy koncového bodu** z levé navigace. Seznam je filtrovaný pro záměr **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Tento utterance, `I'm looking for a job with Natural Language Processing`, není ve správném záměru.

1.  Chcete-li zarovnat tuto utterance, na utterance řádku, vyberte správný **zarovnaný záměr** `GetJobInformation`. Přidejte změněnou promluvu do aplikace zaškrtnutím políčka zaškrtnutí.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Zkontrolujte zbývající projevy v tomto záměru a podle potřeby opravte zarovnaný záměr. Pomocí počáteční utterance tabulka v tomto kurzu zobrazit zarovnaný záměr.

    Seznam **projevy koncového bodu revize** by již neměly mít opravené projevy. Pokud se zobrazí další projevy, pokračujte v práci prostřednictvím seznamu, opravovat zarovnané záměry, dokud seznam je prázdný.

    Jakákoli oprava označení entity se provádí po zarovnání záměru ze stránky Podrobnosti záměru.

1. Trénujte a publikujte aplikaci znovu.

## <a name="get-intent-prediction-from-endpoint"></a>Získat předpověď záměru z koncového bodu

Chcete-li ověřit správně zarovnané příklad projevy lepší předpověď aplikace, zkuste utterance v blízkosti opravené utterance.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v _YOUR_QUERY_HERE_ adresním `Are there any natural language processing jobs in my department right now?`řádku a nahraďte YOUR_QUERY_HERE .

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

   Nyní, když jsou nejistý projevy správně zarovnány, byl správný záměr předpovězen s **vysokým skóre**.

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
