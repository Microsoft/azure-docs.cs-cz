---
title: Použití koncových bodů Azure Machine Learning Studio (Classic) v Azure Stream Analytics
description: Tento článek popisuje, jak používat funkce uživatelsky definovaného jazyka počítače v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 236191710dac19a08db0e8ce94dc695d393009a7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127123"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integrace Azure Machine Learning Studio (Classic) v Stream Analytics (Preview)
Stream Analytics podporuje uživatelsky definované funkce, které volají koncové body Azure Machine Learning Studio (Classic). Podpora REST API pro tuto funkci je podrobně popsána v [knihovně Stream Analytics REST API](/rest/api/streamanalytics/). Tento článek poskytuje dodatečné informace potřebné k úspěšné implementaci této funkce v Stream Analytics. V [tomto](stream-analytics-machine-learning-integration-tutorial.md)kurzu je také publikovaný a dostupný.

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Přehled: terminologie Azure Machine Learning Studio (Classic)
Microsoft Azure Machine Learning Studio (Classic) poskytuje nástroj pro spolupráci a přetahování, který můžete použít k sestavení, testování a nasazení prediktivních analytických řešení na vaše data. Tento nástroj se nazývá *Azure Machine Learning Studio (Classic)* . Studio (Classic) slouží k interakci s prostředky strojového učení a k snadnému sestavování, testování a iteraci v návrhu. Níže jsou uvedené prostředky a jejich definice.

* **Pracovní prostor** : *pracovní prostor* je kontejner, který obsahuje všechny ostatní prostředky strojového učení společně v kontejneru pro správu a řízení.
* **Experiment** : *experimenty* vytvářejí datové odborníky za účelem využití datových sad a výukového modelu strojového učení.
* **Koncový bod** : *koncové body* jsou objekt Studio (Classic), který slouží k převzetí funkcí jako vstup, použití zadaného modelu Machine Learning a vrácení výstupu vraceného skóre.
* Služba **bodování** : *Webová služba bodování* je kolekce koncových bodů, jak je uvedeno výše.

Každý koncový bod obsahuje rozhraní API pro spuštění dávky a synchronní spuštění. Stream Analytics používá synchronní spuštění. Konkrétní služba se nazývá [Služba požadavků a odpovědí](../machine-learning/classic/consume-web-services.md) v Azure Machine Learning Studio (Classic).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Prostředky studia (Classic) potřebné pro úlohy Stream Analytics
Pro účely Stream Analytics zpracování úloh je pro úspěšné provedení nutné provést i koncový bod požadavek/odpověď, [apikey](../machine-learning/classic/consume-web-services.md)a definici Swagger. Stream Analytics má další koncový bod, který vytvoří adresu URL koncového bodu Swagger, vyhledá rozhraní a vrátí výchozí definici UDF pro uživatele.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Konfigurace Stream Analytics a studia (Classic) UDF přes REST API
Pomocí rozhraní REST API můžete nakonfigurovat úlohu, aby volala funkce studia (Classic). Kroky jsou následující:

1. Vytvoření úlohy Stream Analytics
2. Definování vstupu
3. Definování výstupu
4. Vytvořit uživatelsky definovanou funkci (UDF)
5. Zapsat Stream Analytics transformaci, která volá systém souborů UDF
6. Spuštění úlohy

## <a name="creating-a-udf-with-basic-properties"></a>Vytvoření systému souborů UDF se základními vlastnostmi
Příklad: Následující vzorový kód vytvoří skalární systém souborů UDF s názvem *newudf* , který se váže k koncovému bodu Azure Machine Learning Studio (Classic). Všimněte si, že *koncový bod* (identifikátor URI služby) najdete na stránce s nápovědu k rozhraní API pro zvolenou službu a na hlavní stránce služby najdete *apiKey* .

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Příklad textu žádosti:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Volat koncový bod RetrieveDefaultDefinition pro výchozí systém souborů UDF
Jakmile se vytvoří kostra systému souborů UDF, je potřeba, aby byla úplná definice UDF. Koncový bod RetrieveDefaultDefinition vám pomůže získat výchozí definici pro skalární funkci, která je svázána s koncovým bodem Azure Machine Learning Studio (Classic). Datová část níže vyžaduje, abyste získali výchozí definici UDF pro skalární funkci, která je vázaná na koncový bod studia (Classic). Neurčuje skutečný koncový bod, protože již byl poskytnut během žádosti o vložení. Stream Analytics volá koncový bod poskytnutý v žádosti, pokud je k dispozici explicitně. V opačném případě používá původně odkazovaný. V tomto případě systém souborů UDF převezme parametr s jedním řetězcem (větu) a vrátí jediný výstup typu String, který označuje popisek "mínění" pro tuto větu.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Příklad textu žádosti:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Ukázkový výstup této položky by vypadal podobně jako v následujícím příkladu.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Oprava systému souborů UDF s odpovědí
Systém souborů UDF se teď musí opravit s předchozí odpovědí, jak je znázorněno níže.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Text žádosti (výstup z RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementace Stream Analytics transformace pro volání systému souborů UDF
Nyní se v případě každé události vstupu do systému souborů UDF (zde nazvaný scoreTweet) zaznamenejte a zapište odpověď pro tuto událost na výstup.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Získání pomoci
Pokud potřebujete další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)