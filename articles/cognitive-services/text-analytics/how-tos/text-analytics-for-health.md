---
title: Jak používat Analýza textu pro stav
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak extrahovat a označovat lékařské informace z nestrukturovaného klinického textu pomocí Analýza textu pro stav.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599424"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Postupy: použití Analýza textu pro stav (Preview)

> [!IMPORTANT] 
> Analýza textu pro stav je funkce ve verzi Preview, která je k dispozici, a se všemi CHYBAmi. V takovém případě **by analýza textu pro stav (Preview) neměly být implementovány ani nasazeny v jakémkoli provozním provozu.** Analýza textu pro stav není zamýšlené ani nedostupné pro použití jako lékařské zařízení, klinická podpora, diagnostický nástroj nebo jiná technologie určená k použití v oblasti diagnostiky, udělení, zmírnění nebo prevence nákazy a jiných podmínek a společnost Microsoft neuděluje licenci ani právo k používání této možnosti pro tyto účely. Tato funkce není navržená ani implementovaná jako náhrada pro profesionální lékařské poradenství nebo zdravotní stanovisko, diagnostiku, zpracování nebo klinické rozsudky odborníka na zdravotní péči a neměla by se používat jako takový. Zákazník je výhradně zodpovědný za jakékoliv použití Analýza textu pro stav. Společnost Microsoft neopravňuje k tomu, aby Analýza textu pro zdraví nebo materiály poskytované v souvislosti s schopností byly dostatečné pro jakékoli lékařské účely nebo jinak splňovaly zdravotní a lékařské požadavky jakékoli osoby. 


Analýza textu pro stav je funkce služby rozhraní API pro analýzu textu, která extrahuje a štítky relevantní lékařské informace z nestrukturovaných textů, jako jsou například poznámky lékaře, souhrny vypouštění, klinické dokumenty a elektronické záznamy o stavu.  Existují dva způsoby, jak tuto službu využít: 

* [Webové rozhraní API (asynchronní)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Kontejner Docker (synchronní)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funkce

Analýza textu pro stav provádí rozpoznávání pojmenovaných entit (NER), extrakci vztahů, negaci entit a odkazování entit na anglický text, aby bylo možné získat přehled o nestrukturovaných klinických a biolékařských textech.

### <a name="named-entity-recognition"></a>[Rozpoznávání pojmenovaných entit](#tab/ner)

Rozpoznávání pojmenovaných entit detekuje slova a fráze zmíněné v nestrukturovaném textu, které mohou být přidruženy k jednomu nebo více sémantickým typům, jako je například diagnostika, název léků, příznak/znaménko nebo stáří.

> [!div class="mx-imgBorder"]
> ![NER stavu](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Extrakce vztahu](#tab/relation-extraction)

Extrakce relace identifikuje smysluplná připojení mezi koncepty uvedenými v textu. Například relace "čas podmínky" je nalezena přidružením názvu podmínky k času nebo mezi zkratkou a úplným popisem.  

> [!div class="mx-imgBorder"]
> ![Stav znovu](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

Propojování entit neumožňuje určit jedinečné entity přidružením pojmenovaných entit uvedených v textu k konceptům, které se nacházejí v předdefinované databázi konceptů, včetně jednotného UMLS (Unified lékařský jazyk System). Lékařské pojmy se také přiřazují upřednostňované pojmenování, jako další forma normalizace.

> [!div class="mx-imgBorder"]
> ![Stav EL](../media/ta-for-health/health-entity-linking.png)

Analýza textu pro stav podporuje propojení se stavem a biozdravotnickými slovníky, které se nacházejí ve zdroji znalostí[Metathesaurus (Unified](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)lékařského jazyka).

### <a name="assertion-detection"></a>[Zjišťování kontrolního výrazu](#tab/assertion-detection) 

Význam lékařského obsahu je vysoce ovlivněn modifikátory, jako jsou negativní nebo podmíněné kontrolní výrazy, které mohou mít kritické důsledky, pokud je nepředstavuje. Analýza textu pro stav podporuje pro entity v textu tři kategorie detekce kontrolního výrazu: 

* jistotu
* podmíněného
* spojitost

> [!div class="mx-imgBorder"]
> ![Zdravotní záporné](../media/ta-for-health/assertions.png)

---

Úplný seznam podporovaných entit najdete v tématu [kategorie entit](../named-entity-types.md?tabs=health) vrácené funkcí analýza textu pro stav. Informace o hodnocení spolehlivosti najdete na stránce [Analýza textu transparentnosti poznámky](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Podporované jazyky a oblasti

Analýza textu pro stav podporuje pouze dokumenty v anglické jazykové verzi. 

Analýza textu pro webové rozhraní API hostovaného pro stav je v tuto chvíli dostupná jenom v těchto oblastech: Západní USA 2, Východní USA 2, Střed USA, Severní Evropa a Západní Evropa.

## <a name="request-access-to-the-public-preview"></a>Požádat o přístup do veřejné verze Preview

Vyplňte a odešlete [formulář žádosti o Cognitive Services](https://aka.ms/csgate) , abyste požádali o přístup k analýza textu ve verzi Public Preview. Nebudete se vám účtovat Analýza textu pro využívání stavu. 

Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a pošle vám e-mail s rozhodnutím.

> [!IMPORTANT]
> * Ve formuláři je nutné použít e-mailovou adresu přidruženou k ID předplatného Azure.
> * Prostředek Azure, který použijete, musí být vytvořený s schváleným ID předplatného Azure. 
> * Pokud chcete aktualizovat stav vaší aplikace od Microsoftu, Projděte si e-mail (Doručená pošta a složky nevyžádané pošty).

## <a name="using-the-docker-container"></a>Použití kontejneru Docker 

Pokud chcete spustit Analýza textu pro kontejner stavů ve vašem vlastním prostředí, postupujte podle těchto [pokynů ke stažení a instalaci kontejneru](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>S využitím klientské knihovny

Nejnovější předběžná verze klientské knihovny Analýza textu umožňuje volat Analýza textu pro stav pomocí objektu klienta. Přečtěte si referenční dokumentaci a podívejte se na příklady na GitHubu:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Odesílá se žádost o REST API. 

### <a name="preparation"></a>Příprava

Analýza textu pro stav přináší vyšší kvalitu, když dáte menší množství textu, na kterém chcete pracovat. Jedná se o opak některých dalších funkcí Analýza textu, jako je extrakce klíčových frází, která je lepší na větších blocích textu. Chcete-li získat nejlepší výsledky z těchto operací, zvažte odpovídajícím způsobem restrukturalizaci vstupů.

Je nutné mít dokumenty JSON v tomto formátu: ID, text a jazyk. 

Velikost dokumentu musí být v rozmezí 5 120 znaků na dokumentu. Maximální počet dokumentů povolených v kolekci najdete v článku o [omezeních dat](../concepts/data-limits.md?tabs=version-3) v tématu Koncepty. Kolekce se posílá v textu žádosti.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Struktura požadavku rozhraní API pro hostované asynchronní webové rozhraní API

V případě kontejneru i hostovaného webového rozhraní API musíte vytvořit žádost POST. K rychlému vytváření a odesílání požadavků POST do hostovaného webového rozhraní API v požadované oblasti můžete [použít post](text-analytics-how-to-call-api.md), příkaz nebo **konzolu testování api** na webu [Analýza textu for Health hostd](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) API. 

> [!NOTE]
> Asynchronní `/analyze` i `/health` koncové body jsou k dispozici pouze v následujících oblastech: Západní USA 2, Východní USA 2, Střed USA, Severní Evropa a západní Evropa.  Chcete-li provést úspěšné požadavky na tyto koncové body, zajistěte, aby byl prostředek vytvořen v jedné z těchto oblastí.

Níže je uveden příklad souboru JSON připojeného k Analýza textu pro tělo žádosti rozhraní API pro stav:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Odpověď hostovaného asynchronního webového rozhraní API 

Vzhledem k tomu, že se tento požadavek POST používá k odeslání úlohy pro asynchronní operaci, v objektu Response není žádný text.  Pokud ale chcete, aby požadavek GET kontroloval stav úlohy a výstupu, potřebujete hodnotu klíč umístění operace – v hlavičkách odpovědi.  Níže je uveden příklad hodnoty KLÍČového umístění operace v hlavičce odpovědi žádosti POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Chcete-li zkontrolovat stav úlohy, proveďte požadavek GET na adresu URL v hodnotě hlavičky klíče umístění operace po odpovědi.  Následující stavy se používají k vyjádření stavu úlohy: `NotStarted` , `running` , `succeeded` , `failed` , `rejected` , `cancelling` a `cancelled` .  

Můžete zrušit úlohu se `NotStarted` `running` stavem nebo s VOLÁNÍM Delete protokolu HTTP na stejnou adresu URL jako požadavek GET.  Další informace o volání metody DELETE jsou k dispozici v části [Analýza textu pro rozhraní API hostovaného pro stav](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Následuje příklad odpovědi na požadavek GET.  Výstup je k dispozici pro načtení až do `expirationDateTime` (24 hodin od okamžiku vytvoření úlohy), po jejímž uplynutí se výstup vyprázdní.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Struktura požadavku rozhraní API pro kontejner

K odeslání dotazu do kontejneru, který jste nasadili, můžete [použít post](text-analytics-how-to-call-api.md) nebo příklad níže uvedeného požadavku a nahradit `serverURL` proměnnou příslušnou hodnotou.  Všimněte si, že verze rozhraní API v adrese URL kontejneru se liší od hostovaného rozhraní API.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Následující JSON je příklad souboru JSON připojeného k Analýza textu pro tělo žádosti rozhraní API pro stav:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Tělo odpovědi kontejneru

Následující JSON je příkladem Analýza textu pro tělo odpovědi rozhraní API stavu z kontejnerového synchronního volání:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Výstup kontrolního výrazu

Analýza textu pro stav vrátí modifikátory kontrolního výrazu, které jsou informativní atributy přiřazené lékařským koncepcím, které poskytují hlubší porozumění kontextu konceptů v rámci textu. Tyto modifikátory jsou rozděleny do tří kategorií, každý se zaměřuje na jiný aspekt a obsahují sadu vzájemně se vylučujících hodnot. Každému entitě je přiřazena pouze jedna hodnota na kategorii. Nejběžnější hodnotou pro každou kategorii je výchozí hodnota. Výstupní odpověď služby obsahuje jenom modifikátory kontrolního výrazu, které se liší od výchozí hodnoty.

**Jistota**  – poskytuje informace o přítomnosti (přítomné vs. chybějící) konceptu a o tom, jak určitý text se týká jeho přítomnosti (přesně a případně).
*   **Kladná** [výchozí]: koncept existuje nebo se stalo.
* **Negativní**: koncept teď neexistuje, ale nikdy nenastal.
* **Positive_Possible**: koncept nejspíš existuje, ale dojde k nějaké nejistotě.
* **Negative_Possible**: existence konceptu je nepravděpodobná, ale dojde k nějaké nejistotě.
* **Neutral_Possible**: koncept může nebo nemusí existovat, aniž by došlo k žádnému boku.

**Podmínka** – poskytuje informace o tom, jestli existence konceptu závisí na určitých podmínkách. 
*   **Žádné** [výchozí]: koncept je fakt a není hypotetický a nezávisí na určitých podmínkách.
*   **Hypotetický**: koncept může být v budoucnu vyvinutý nebo v budoucnu.
*   **Podmíněný**: koncept existuje nebo se vyskytuje jenom za určitých podmínek.

**Asociace** – popisuje, zda je koncept přidružen k předmětu textu nebo někomu jinému.
*   **Subject** [výchozí]: koncept je přidružen k předmětu textu, obvykle pacientovi.
*   **Someone_Else**: koncept je přidružen k osobě, která není předmětem textu.


Detekce kontrolního výrazu představuje nestejné entity jako zápornou hodnotu pro kategorii jistoty, například:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Výstup extrakce vztahu

Analýza textu pro stav rozpoznává vztahy mezi různými koncepty, včetně vztahů mezi atributem a entitou (například směr struktury těla, dávkou léků) a mezi entitami (například rozpoznání zkratky).

**ZKRATKA**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * Relace odkazující na podmínku mohou odkazovat buď na typ entity typu, nebo na typ entity SYMPTOM_OR_SIGN.
> * Relace odkazující na léků můžou odkazovat buď na typ entity MEDICATION_NAME, nebo na typ entity MEDICATION_CLASS.
> * Relace odkazující na čas můžou odkazovat buď na typ entity čas, nebo na typ entity data.

Výstup extrakce relace obsahuje odkazy na identifikátory URI a přiřazené role entit typu vztahu. Například:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Kategorie pojmenovaných entit](../named-entity-types.md)
* [Co je nového](../whats-new.md)
