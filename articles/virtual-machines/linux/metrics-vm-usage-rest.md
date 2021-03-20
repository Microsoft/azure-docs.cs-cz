---
title: Získat data o využití virtuálních počítačů Azure pomocí REST API
description: K shromažďování metrik využití pro virtuální počítač použijte rozhraní Azure REST API.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: a7237bfc82a932b774b4b6ef293c242a84fd75af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591206"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Získat metriky využití virtuálních počítačů pomocí REST API

Tento příklad ukazuje, jak načíst využití CPU pro virtuální počítač se systémem Linux pomocí [REST API Azure](/rest/api/azure/).

Kompletní Referenční dokumentace a další ukázky pro REST API jsou k dispozici v [Referenční příručce Azure monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

Pomocí následující žádosti GET Shromážděte [procento metriky procesoru](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) z virtuálního počítače.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Jsou vyžadovány následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte na `application/json`.|  
|*Authorization:*|Povinná hodnota. Nastavte na platný [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Název | Description |
| :--- | :---------- |
| subscriptionId | ID předplatného, které identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s více předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli). |
| resourceGroupName | Název skupiny prostředků Azure přidružené k prostředku. Tuto hodnotu můžete získat z rozhraní Azure Resource Manager API, CLI nebo na portálu. |
| VMName | Název virtuálního počítače Azure. |
| metricnames | Čárkami oddělený seznam platných  [metrik Load Balancer](../../load-balancer/load-balancer-standard-diagnostics.md). |
| verze-api | Verze rozhraní API, která se má použít pro požadavek<br /><br /> Tento dokument popisuje verzi rozhraní API `2018-01-01` , která je součástí výše uvedené adresy URL.  |
| timespan | Řetězec s následujícím formátem `startDateTime_ISO/endDateTime_ISO` , který definuje časový rozsah vrácených metrik. Tento volitelný parametr je nastaven tak, aby v příkladu vrátil data o hodnotě dne. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není nutný žádný text žádosti.

## <a name="handle-the-response"></a>Zpracování odpovědi

Po úspěšném vrácení seznamu hodnot metriky se vrátí stavový kód 200. V [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse)je dostupný úplný seznam chybových kódů.

## <a name="example-response"></a>Příklad odpovědi 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
