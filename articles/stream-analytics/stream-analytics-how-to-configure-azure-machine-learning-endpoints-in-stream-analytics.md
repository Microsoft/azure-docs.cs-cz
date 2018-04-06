---
title: Pomocí Azure Machine Learning koncové body v Stream Analytics | Microsoft Docs
description: Funkce definované uživatelem počítače jazyk v Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: a7d76d6015f8e9f08d3493b1c1e237858c341592
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Počítač integrace učení v Stream Analytics
Stream Analytics podporuje uživatelsky definované funkce, které volají na koncové body Azure Machine Learning. Podpora rozhraní REST API pro tuto funkci je podrobně popsaná v [Stream Analytics REST API knihovny](https://msdn.microsoft.com/library/azure/dn835031.aspx). Tento článek obsahuje doplňující informace potřebné pro úspěšné dokončení implementace tuto funkci v Stream Analytics. Kurz také byl odeslán a je k dispozici [zde](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Přehled: Terminologie služby Azure Machine Learning
Microsoft Azure Machine Learning poskytuje nástroj spolupráci, přetahování myší, které můžete použít k vytvoření, testování a nasazovat řešení prediktivní analýzy na vaše data. Tento nástroj je volána *Azure Machine Learning Studio*. Nástroje studio umožňuje pracovat s prostředky Machine Learning a snadno vytvářet, testovat a iterovat návrhu. Níže jsou tyto prostředky a jejich definice.

* **Pracovní prostor**: *prostoru* je kontejner, který obsahuje všechny ostatní prostředky Machine Learning společně v kontejneru pro správu a řízení.
* **Experiment**: *experimenty* jsou vytvořené pomocí datových vědců využívat datové sady a cvičení modelu strojového učení.
* **Koncový bod**: *koncové body* jsou Azure Machine Learning objekt použitý trvat funkce jako vstup, používá model zadaný strojového učení a vrátit scored výstup.
* **Vyhodnocování Webservice**: A *vyhodnocování webservice* je kolekcí koncových bodů, jak je uvedeno výše.

Každý koncový bod má rozhraní API pro spuštění dávky a synchronní zpracování. Stream Analytics používá synchronní zpracování. Název konkrétní službu [požadavků a odpovědí služby](../machine-learning/studio/consume-web-services.md) v AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Strojového učení prostředky potřebné pro úlohy Stream Analytics
Pro účely Stream Analytics úlohy zpracování, koncový bod žádosti a odpovědi [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), a všechny potřebné pro úspěšné provedení definici swaggeru. Stream Analytics obsahuje další koncový bod, který vytvoří adresu url pro koncový bod swagger, vyhledá rozhraní a vrátí výchozí UDF definici pro uživatele.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurace služby Stream Analytics a strojového učení UDF přes REST API
Pomocí rozhraní REST API můžete nakonfigurovat úlohu pro volání funkcí jazyka počítače Azure. Kroky jsou následující:

1. Vytvoření úlohy Stream Analytics
2. Zadejte vstup
3. Definování výstup
4. Vytvořit uživatelem definované funkce (UDF)
5. Zápis transformace Stream Analytics, která volá UDF
6. Spustit úlohu

## <a name="creating-a-udf-with-basic-properties"></a>Vytváření uživatelem definovanou FUNKCI s základní vlastnosti
Jako příklad následující vzorový kód vytvoří skalární uživatelem definovanou FUNKCI s názvem *newudf* která sváže koncový bod Azure Machine Learning. Všimněte si, že *koncový bod* (URI) se dá služba najít na stránce nápovědy rozhraní API pro službu zvolené a *apiKey* naleznete na hlavní stránce služeb.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Příklad textu žádosti:  

````
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
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Koncový bod RetrieveDefaultDefinition volání pro výchozí UDF
Po vytvoření je UDF kostru je potřeba dokončení definice UDF. Koncový bod RetreiveDefaultDefinition umožňuje získání výchozí definice pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Datové části níže, musíte získat výchozí definici UDF pro skalární funkci, která je vázána na koncový bod Azure Machine Learning. Skutečný koncový bod neurčuje jak již bylo zadáno během žádosti PUT. Stream Analytics volá zadaný v požadavku, pokud je explicitně zadaná koncový bod. V opačném případě se použije, původně odkazuje. Zde trvá UDF jednoho řetězce parametr (věta) a vrátí výstup jednoho typu řetězec, který určuje popisek "postojích" pro tuto větu.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Příklad textu žádosti:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Ukázka výstupu tohoto vyhledejte něco chtěli níže.  

````
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
````

## <a name="patch-udf-with-the-response"></a>Oprava UDF s odpovědí
Teď UDF musí opravit s předchozí odpověď, a to, jak je uvedeno níže.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Text žádosti (výstup z RetrieveDefaultDefinition):

````
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
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementace služby Stream Analytics transformace volat UDF
Nyní dotaz UDF (zde s názvem scoreTweet) pro každý vstupní událost a zápisu odpovědi pro tuto událost do výstupu.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Podpora
Další podporu naleznete v našem [fóru služby Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
