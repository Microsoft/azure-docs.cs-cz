---
title: Načtení metrik pomocí rozhraní REST API
titleSuffix: Azure Load Balancer
description: V tomto článku můžete začít používat Azure REST API ke shromažďování metrik stavu a využití pro Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225246"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Získání metrik využití vyrovnávání zatížení pomocí rozhraní REST API

Shromážděte počet bajtů zpracovaných [standardním vyvyčovávatelem zatížení](/azure/load-balancer/load-balancer-standard-overview) po dobu pomocí [rozhraní Azure REST API](/rest/api/azure/).

Kompletní referenční dokumentace a další ukázky pro rozhraní REST API jsou k dispozici v [odkazu Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

Následující požadavek GET slouží ke shromažďování [metriky ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) ze standardního vykladače zatížení. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný  [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name (Název) | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si informace [o práci s více předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Název skupiny prostředků, která obsahuje prostředek. Tuto hodnotu můžete získat z rozhraní API Azure Resource Manager, CLI nebo portálu. |
| loadBalancerName | Název azure vyrovnávání zatížení. |
| názvy metrik | Seznam platných [metrik vyrovnávání zatížení oddělených čárkami](/azure/load-balancer/load-balancer-standard-diagnostics). |
| verze-api | Verze rozhraní API pro požadavek.<br /><br /> Tento dokument se `2018-01-01`vztahuje api-verze , které jsou zahrnuty ve výše uvedené URL.  |
| Timespan | Časový rozsah dotazu. Je to řetězec s následujícím `startDateTime_ISO/endDateTime_ISO`formátem . Tento volitelný parametr je nastaven tak, aby v příkladu vracel data za den. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není potřeba žádné tělo požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Stavový kód 200 je vrácena při úspěšném vrácení seznamu hodnot metriky. Úplný seznam kódů chyb je k dispozici v [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse).

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
