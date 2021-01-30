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
ms.date: 01/21/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: d7f71afd1cf47ab1a94d984090e8fb52ee69c6b4
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088934"
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

Extrakce relace identifikuje smysluplná připojení mezi koncepty uvedenými v textu. Například relace "čas podmínky" je nalezena přidružením názvu podmínky k času. 

> [!div class="mx-imgBorder"]
> ![Stav znovu](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

Odkaz na entitu nejednoznačně liší jedinečné entity přidružením pojmenovaných entit uvedených v textu k konceptům, které se našly v předdefinované databázi konceptů. Například UMLS (Unified lékařské jazyky System).

> [!div class="mx-imgBorder"]
> ![Stav EL](../media/ta-for-health/health-entity-linking.png)

Analýza textu pro stav podporuje propojení se stavem a biozdravotnickými slovníky, které se nacházejí ve zdroji znalostí[Metathesaurus (Unified](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)lékařského jazyka).

### <a name="negation-detection"></a>[Detekce negace](#tab/negation-detection) 

Význam lékařského obsahu je vysoce ovlivněn modifikátory, jako je například negace, což může mít kritické nevýznamy, pokud je chyba zjištěna chybně. Analýza textu pro stav podporuje detekci negace pro různé entity uvedené v textu. 

> [!div class="mx-imgBorder"]
> ![Zdravotní záporné](../media/ta-for-health/health-negation.png)

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

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Chcete-li zkontrolovat stav úlohy, proveďte požadavek GET na adresu URL v hodnotě hlavičky klíče umístění operace po odpovědi.  Následující stavy se používají k vyjádření stavu úlohy: `NotStarted` , `running` , `succeeded` , `failed` , `rejected` , `cancelling` a `cancelled` .  

Můžete zrušit úlohu se `NotStarted` `running` stavem nebo s VOLÁNÍM Delete protokolu HTTP na stejnou adresu URL jako požadavek GET.  Další informace o volání metody DELETE jsou k dispozici v části [Analýza textu pro rozhraní API hostovaného pro stav](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Následuje příklad odpovědi na požadavek GET.  Všimněte si, že výstup je k dispozici pro načtení až do doby `expirationDateTime` (24 hodin od okamžiku vytvoření úlohy), po jejímž uplynutí se výstup vyprázdní.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
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
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
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
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Výstup detekce negace

Při použití detekce negace může v některých případech jedna negace vybývat několika termíny najednou. Negace rozpoznané entity je ve výstupu JSON reprezentována logickou hodnotou `isNegated` příznaku, například:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Výstup extrakce vztahu

Výstup extrakce vztahu obsahuje odkazy identifikátoru URI na *zdroj* vztahu a jeho *cíl*. Entity s rolí vztahu role `ENTITY` jsou přiřazeny k `target` poli. Entity s rolí vztahu role `ATTRIBUTE` jsou přiřazeny k `source` poli. Relace zkratek obsahují obousměrný `source` a `target` pole a `bidirectional` budou nastaveny na `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Kategorie pojmenovaných entit](../named-entity-types.md)
* [Co je nového](../whats-new.md)
