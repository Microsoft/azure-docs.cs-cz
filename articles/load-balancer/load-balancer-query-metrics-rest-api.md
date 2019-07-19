---
title: Načtení metrik pomocí REST API
titlesuffix: Azure Load Balancer
description: Pomocí rozhraní Azure REST API můžete shromažďovat metriky stavu a využití pro Load Balancer pro určitý rozsah času a data.
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274524"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Získat metriky využití Load Balancer pomocí REST API

Tento postup ukazuje, jak shromáždit počet bajtů zpracovaných [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) po dobu v časovém intervalu pomocí [REST API Azure](/rest/api/azure/).

Kompletní Referenční dokumentace a další ukázky pro REST API jsou k dispozici v [Referenční příručce Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Žádost o sestavení

K shromáždění [metriky byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) z Standard Load Balancer použijte následující požadavek GET. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Jsou vyžadovány následující hlavičky: 

|Hlavička žádosti|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|  
|*Authorization:*|Povinný parametr. Nastavte na platný `Bearer` [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s více](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)předplatnými. |
| resourceGroupName | Název skupiny prostředků, která obsahuje prostředek. Tuto hodnotu můžete získat z rozhraní Azure Resource Manager API, CLI nebo na portálu. |
| loadBalancerName | Název Azure Load Balancer. |
| metricnames | Čárkami oddělený seznam platných [metrik Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Verze rozhraní API, která se má použít pro požadavek<br /><br /> Tento dokument popisuje verzi `2018-01-01`rozhraní API, která je součástí výše uvedené adresy URL.  |
| TimeSpan | Časové rozpětí dotazu. Jedná se o řetězec s následujícím formátem `startDateTime_ISO/endDateTime_ISO`. Tento volitelný parametr je nastaven tak, aby v příkladu vrátil data o hodnotě dne. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není nutný žádný text žádosti.

## <a name="handle-the-response"></a>Zpracování odpovědi

Po úspěšném vrácení seznamu hodnot metriky se vrátí stavový kód 200. V [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse)je dostupný úplný seznam chybových kódů.

## <a name="example-response"></a>Příklad odpovědi 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
