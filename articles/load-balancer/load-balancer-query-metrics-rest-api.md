---
title: Načtení metrik pomocí REST API
titleSuffix: Azure Load Balancer
description: V tomto článku se naučíte, jak pomocí rozhraní Azure REST API shromažďovat metriky stavu a využití pro Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 3b5aedb20bc7a8d2aa6f3aa3d8691a71af4cd3a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808371"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Získat Load Balancer metriky využití pomocí REST API

Shromáždí počet bajtů zpracovaných [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) po dobu v časovém intervalu pomocí [REST API Azure](/rest/api/azure/).

Kompletní Referenční dokumentace a další ukázky pro REST API jsou k dispozici v [Referenční příručce Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

K shromáždění [metriky byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) z Standard Load Balancer použijte následující požadavek GET. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Název | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s více předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Název skupiny prostředků, která obsahuje prostředek. Tuto hodnotu můžete získat z rozhraní Azure Resource Manager API, CLI nebo na portálu. |
| loadBalancerName | Název Azure Load Balancer. |
| názvy metrik | Čárkami oddělený seznam platných  [metrik Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics). |
| verze-api | Verze rozhraní API, která se má použít pro požadavek<br /><br /> Tento dokument popisuje verzi rozhraní API `2018-01-01` , která je součástí výše uvedené adresy URL.  |
| timespan | Časové rozpětí dotazu. Jedná se o řetězec s následujícím formátem `startDateTime_ISO/endDateTime_ISO` . Tento volitelný parametr je nastaven tak, aby v příkladu vrátil data o hodnotě dne. |
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
