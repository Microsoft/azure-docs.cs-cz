---
title: Používání koncových bodů Machine Learning v Azure Stream Analytics
description: Tento článek popisuje, jak pomocí uživatelem definované funkce jazyka počítače v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426202"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integrace Azure Machine Learning Studio (Classic) v Stream Analytics (Preview)
Stream Analytics podporuje uživatelsky definované funkce, které volají koncové body Azure Machine Learning Studio (Classic). Podpora rozhraní REST API pro tuto funkci je podrobně popsaná v [knihovny rozhraní REST API pro Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Tento článek obsahuje doplňující informace potřebné pro úspěšné implementaci této funkce ve Stream Analytics. Kurz také se zveřejní a je k dispozici [tady](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Přehled: terminologie Azure Machine Learning Studio (Classic)
Microsoft Azure Machine Learning Studio (Classic) poskytuje nástroj pro spolupráci a přetahování, který můžete použít k sestavení, testování a nasazení prediktivních analytických řešení na vaše data. Tento nástroj se nazývá *Azure Machine Learning Studio (Classic)* . Sady studio se používá k interakci s prostředky Machine Learning a snadno vytvářet, testovat a iterovat návrh. Níže jsou tyto prostředky a jejich definice.

* **Pracovní prostor**: *pracovní prostor* je kontejner, který obsahuje všechny ostatní prostředky služby Machine Learning společně v kontejneru pro správu a řízení.
* **Experiment**: *experimenty* vytvářejí datovým vědcům využívat datových sad a jejich trénování modelu strojového učení.
* **Koncový bod**: *koncové body* jsou objekt Azure Machine Learning Studio (klasický), který slouží k převzetí funkcí jako vstup, použití zadaného modelu Machine Learning a vrácení výstupu vráceného skóre.
* **Webová služba vyhodnocování**: A *vyhodnocování služby typu webservice* je kolekce koncových bodů, jak je uvedeno výše.

Každý koncový bod má rozhraní API pro dávkové spouštění a synchronní zpracování. Stream Analytics používá synchronní zpracování. Konkrétní služba se nazývá [Služba požadavků a odpovědí](../machine-learning/studio/consume-web-services.md) v Azure Machine Learning Studio (Classic).

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
Příklad: Následující vzorový kód vytvoří skalární systém souborů UDF s názvem *newudf* , který se váže k koncovému bodu Azure Machine Learning Studio (Classic). Všimněte si, že *koncový bod* (identifikátor URI služby) najdete na stránce nápovědy rozhraní API pro zvolený služby a *apiKey* můžete najít na hlavní stránce služeb.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

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
Jakmile se vytvoří kostru UDF je potřeba dokončení definice UDF. Koncový bod RetrieveDefaultDefinition vám pomůže získat výchozí definici pro skalární funkci, která je svázána s koncovým bodem Azure Machine Learning Studio (Classic). Datová část níže vyžaduje, abyste k získání výchozí definice UDF pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Ji neurčuje skutečný koncový bod, protože již byl poskytnut během požadavek PUT. Stream Analytics volá zadaný v požadavku, pokud ji explicitně poskytuje koncový bod. V opačném případě použije předplatné původně odkazuje. Tady UDF přebírá jediný řetězec, parametr (věta) a vrátí výstup jednoho typu řetězec, což znamená "mínění" popisku pro tuto větu:.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

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

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

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


## <a name="get-help"></a>Získání nápovědy
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další kroky
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
