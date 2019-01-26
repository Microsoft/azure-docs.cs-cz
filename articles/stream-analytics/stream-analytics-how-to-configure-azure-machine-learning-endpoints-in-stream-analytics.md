---
title: Používání koncových bodů Machine Learning v Azure Stream Analytics
description: Tento článek popisuje, jak pomocí uživatelem definované funkce jazyka počítače v Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 8d5e3060d31a260ddba2e7b23d468568ea9569c0
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078028"
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Machine Learning integrace ve službě Stream Analytics
Stream Analytics podporuje uživatelem definované funkce, které vyžadují ke koncovým bodům Azure Machine Learning. Podpora rozhraní REST API pro tuto funkci je podrobně popsaná v [knihovny rozhraní REST API pro Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Tento článek obsahuje doplňující informace potřebné pro úspěšné implementaci této funkce ve Stream Analytics. Kurz také se zveřejní a je k dispozici [tady](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Přehled: Terminologie služby Azure Machine Learning
Microsoft Azure Machine Learning poskytuje nástroj pro spolupráci, přetáhněte myší, které můžete použít k sestavení, testovat a nasazovat řešení prediktivní analýzy s vašimi daty. Tento nástroj je volána *Azure Machine Learning Studio*. Sady studio se používá k interakci s prostředky Machine Learning a snadno vytvářet, testovat a iterovat návrh. Níže jsou tyto prostředky a jejich definice.

* **Pracovní prostor**: *Pracovní prostor* je kontejner, který obsahuje všechny ostatní prostředky služby Machine Learning společně v kontejneru pro správu a řízení.
* **Experiment**: *Experimenty* vytvářejí datovým vědcům využívat datových sad a jejich trénování modelu strojového učení.
* **Koncový bod**: *Koncové body* se používá jako vstup přijímat funkce, používá model zadaný strojového učení a vrací Vyhodnocená výstup objektu Azure Machine Learning.
* **Webová služba vyhodnocování**: A *vyhodnocování služby typu webservice* je kolekce koncových bodů, jak je uvedeno výše.

Každý koncový bod má rozhraní API pro dávkové spouštění a synchronní zpracování. Stream Analytics používá synchronní zpracování. Název konkrétní služby [požadavku nebo odpovědi služby](../machine-learning/studio/consume-web-services.md) v aplikaci Azure Machine Learning studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning prostředky potřebné pro úlohy Stream Analytics
Pro účely služby Stream Analytics úlohy zpracování, koncový bod požadavku nebo odpovědi [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), a definice swaggeru jsou všechny potřebné pro úspěšné provedení. Stream Analytics je další koncový bod, který vytvoří adresu url pro koncový bod swaggeru, vyhledá rozhraní a vrátí výchozí definice UDF uživateli.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurace Stream Analytics a Machine learningu UDF přes rozhraní REST API
Rozhraní REST API můžete nakonfigurovat úlohy pro volání funkce Azure strojového jazyka. Kroky jsou následující:

1. Vytvoření úlohy Stream Analytics
2. Definujte vstupní hodnota
3. Definování výstup
4. Vytvoření uživatelem definované funkce (UDF)
5. Zápis transformace Stream Analytics, která volá UDF
6. Spuštění úlohy

## <a name="creating-a-udf-with-basic-properties"></a>Vytváření uživatelem definovanou FUNKCI s základní vlastnosti
Například následující ukázkový kód vytvoří skalární UDF s názvem *newudf* , která vytvoří vazbu na koncový bod Azure Machine Learning. Všimněte si, že *koncový bod* (identifikátor URI služby) najdete na stránce nápovědy rozhraní API pro zvolený služby a *apiKey* můžete najít na hlavní stránce služeb.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Text požadavku příkladu:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Volání koncového bodu RetrieveDefaultDefinition pro výchozí hodnotu UDF
Jakmile se vytvoří kostru UDF je potřeba dokončení definice UDF. Koncový bod RetrieveDefaultDefinition vám pomůže využít vaše výchozí definici pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Datová část níže vyžaduje, abyste k získání výchozí definice UDF pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Ji neurčuje skutečný koncový bod, protože již byl poskytnut během požadavek PUT. Stream Analytics volá zadaný v požadavku, pokud ji explicitně poskytuje koncový bod. V opačném případě použije předplatné původně odkazuje. Tady UDF přebírá jediný řetězec, parametr (věta) a vrátí výstup jednoho typu řetězec, což znamená "mínění" popisku pro tuto větu:.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Text požadavku příkladu:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Ukázku výstup tohoto by hledejte něco jako níže.

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

## <a name="patch-udf-with-the-response"></a>Oprava UDF s odpovědí
Nyní UDF musí opravit s předchozí odpověď, jak je znázorněno níže.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Text požadavku (výstup z RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementace transformace Stream Analytics k volání UDF
Nyní dotazování UDF (tady s názvem scoreTweet) pro každý vstupní události a zápisu odpovědi pro tuto událost do výstupu.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
