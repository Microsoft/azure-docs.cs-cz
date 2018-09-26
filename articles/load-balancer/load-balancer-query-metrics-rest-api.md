---
title: Načíst metriky Azure Load Balancer pomocí rozhraní REST API | Dokumentace Microsoftu
description: Pomocí rozhraní Azure REST API pro shromažďování metrik stavu a využití pro nástroj pro vyrovnávání zatížení pro zadaný rozsah datum a čas.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 1fac461c3af4ea0a2e1f2257256969c47bc3d134
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094457"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Získat metriky využití pro vyrovnávání zatížení pomocí rozhraní REST API

Tento návod ukazuje, jak shromažďovat počet bajtů, které jsou zpracovány [Load balanceru úrovně Standard](/azure/load-balancer/load-balancer-standard-overview) interval pomocí [rozhraní Azure REST API](/rest/api/azure/).

Úplnou referenční dokumentaci a další ukázky pro rozhraní REST API jsou k dispozici v [Reference k rozhraní REST pro monitorování Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Žádost o sestavení

Můžete shromažďovat následující požadavek GET [ByteCount metrika](/azure/load-balancer/load-balancer-standard-diagnostics#a-name--multidimensionalmetricsamulti-dimensional-metrics) z Load balanceru úrovně Standard. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Autorizace:*|Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Název | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, který identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). |
| resourceGroupName | Název skupiny prostředků, který obsahuje prostředek. Tuto hodnotu můžete získat z rozhraní API Azure Resource Manageru, rozhraní příkazového řádku nebo portálu. |
| loadBalancerName | Název nástroje pro vyrovnávání zatížení Azure. |
| metricnames | Čárkami oddělený seznam platných [metriky Load balanceru úrovně](/azure/load-balancer/load-balancer-standard-diagnostics). |
| verze API-version | Verze rozhraní API, která se má použít pro daný požadavek.<br /><br /> Tento dokument popisuje verzi api-version `2018-01-01`, který je obsažen v adrese URL výše.  |
| Časový interval | Časový interval dotazu. Jedná se o řetězec v následujícím formátu `startDateTime_ISO/endDateTime_ISO`. Tento nepovinný parametr je nastaven na vrátit data za jeden den v příkladu. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Tělo požadavku

Pro tuto operaci je potřeba není datová část požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Pokud seznam hodnot metriky byla úspěšně vrácena se vrátí stavový kód 200. Úplný seznam kódů chyb je k dispozici v [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse).

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