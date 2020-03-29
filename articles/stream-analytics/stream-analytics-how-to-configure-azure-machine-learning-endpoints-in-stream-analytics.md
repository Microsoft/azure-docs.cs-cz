---
title: Použití koncových bodů Machine Learning u Azure Stream Analytics
description: Tento článek popisuje, jak používat uživatelem definované funkce strojového jazyka v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426202"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integrace Azure Machine Learning Studio (klasická) v Stream Analytics (Preview)
Stream Analytics podporuje uživatelem definované funkce, které volají do koncových bodů Azure Machine Learning Studio (klasické). Podpora rozhraní REST API pro tuto funkci je podrobně popsána v [knihovně rozhraní API STREAM Analytics REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx). Tento článek obsahuje doplňující informace potřebné pro úspěšnou implementaci této funkce v Stream Analytics. Výukový program byl také vyslán a je k dispozici [zde](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Přehled: Terminologie Azure Machine Learning Studio (klasická)
Microsoft Azure Machine Learning Studio (classic) poskytuje kolaborativní nástroj pro přetahování, který můžete použít k vytváření, testování a nasazování prediktivních analytických řešení na vašich datech. Tento nástroj se nazývá *Azure Machine Learning Studio (klasické)*. Studio se používá k interakci s prostředky Machine Learning a snadno vytvářet, testovat a iterate na váš návrh. Tyto zdroje a jejich definice jsou uvedeny níže.

* **Pracovní prostor**: *Pracovní prostor* je kontejner, který obsahuje všechny ostatní prostředky Machine Learning společně v kontejneru pro správu a řízení.
* **Experiment**: *Experimenty* jsou vytvářeny datovými vědci, aby využili datové sady a trénovali model strojového učení.
* **Koncový bod**: *Koncové body* jsou Objekt Azure Machine Learning Studio (klasický) používaný k převzetí funkcí jako vstupu, použití zadaného modelu strojového učení a vrácení výstupu s skóre.
* **Vyhodnocování webová služba**: Vyhodnocování *webová služba* je kolekce koncových bodů, jak je uvedeno výše.

Každý koncový bod má apis pro dávkové spuštění a synchronní spuštění. Stream Analytics používá synchronní spuštění. Konkrétní služba se nazývá [služba požadavku a odpovědi](../machine-learning/studio/consume-web-services.md) v Azure Machine Learning Studio (klasické).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Materiály pro strojové učení potřebné pro úlohy Stream Analytics
Pro účely zpracování úloh YZD je pro úspěšné spuštění nezbytné koncového bodu požadavku a odpovědi, [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)a definice swagger. Stream Analytics má další koncový bod, který vytvoří adresu URL pro koncový bod swagger, vyhledá rozhraní a vrátí uživateli výchozí definici UDF.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurace analýzy datového proudu a strojového učení UDF pomocí rozhraní REST API
Pomocí rozhraní REST API můžete nakonfigurovat úlohu pro volání funkcí Azure Machine Language. Kroky jsou následující:

1. Vytvoření úlohy Stream Analytics
2. Definování vstupu
3. Definování výstupu
4. Vytvoření uživatelem definované funkce (UDF)
5. Napište transformaci Stream Analytics, která volá UDF
6. Spuštění úlohy

## <a name="creating-a-udf-with-basic-properties"></a>Vytvoření UDF se základními vlastnostmi
Následující ukázkový kód například vytvoří skalární UDF s názvem *newudf,* který se váže na koncový bod Azure Machine Learning Studio (klasický). Všimněte si, že *koncový bod* (identifikátor URI služby) najdete na stránce nápovědy rozhraní API pro vybranou službu a *apiKey* najdete na hlavní stránce služby.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Příklad textu požadavku:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Volání koncového bodu RetrieveDefaultDefinition pro výchozí udf
Po vytvoření kostry UDF je potřeba kompletní definice UDF. Koncový bod RetrieveDefaultDefinition vám pomůže získat výchozí definici skalární funkce, která je vázána na koncový bod Azure Machine Learning Studio (classic). Datová část níže vyžaduje, abyste získali výchozí definici UDF pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Neurčuje skutečný koncový bod, protože již byl poskytnut během požadavku PUT. Stream Analytics volá koncový bod uvedený v požadavku, pokud je k dispozici explicitně. V opačném případě použije původně odkazovaný. Zde UDF trvá jeden parametr řetězce (věta) a vrátí jeden výstup typu řetězce, který označuje popisek "mínění" pro tuto větu.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Příklad textu požadavku:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Ukázkový výstup by vypadal nějak níže.

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
Nyní musí být UDF opraveno předchozí odpovědí, jak je znázorněno níže.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Tělo požadavku (výstup z RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementace transformace Stream Analytics pro volání UDF
Nyní dotaz UDF (zde s názvem scoreTweet) pro každou vstupní událost a zapsat odpověď pro tuto událost do výstupu.

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

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
