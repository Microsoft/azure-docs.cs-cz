---
title: Získání dat o využití virtuálních zařízení Azure pomocí rozhraní REST API
description: Pomocí api Azure REST ke shromažďování metrik využití virtuálního počítače.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944741"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Získání metrik využití virtuálního počítače pomocí rozhraní REST API

Tento příklad ukazuje, jak načíst využití procesoru pro [virtuální počítač S Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) pomocí [rozhraní AZURE REST API](/rest/api/azure/).

Kompletní referenční dokumentace a další ukázky pro rozhraní REST API jsou k dispozici v [odkazu Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Sestavení požadavku

Ke shromažďování [metriky procento procesoru](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) z virtuálního počítače použijte následující požadavek GET

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Název skupiny prostředků Azure přidružené k prostředku. Tuto hodnotu můžete získat z rozhraní API Azure Resource Manager, CLI nebo portálu. |
| vmname | Název virtuálního počítače Azure. |
| názvy metrik | Seznam platných [metrik vyrovnávání zatížení oddělených čárkami](/azure/load-balancer/load-balancer-standard-diagnostics). |
| verze-api | Verze rozhraní API pro požadavek.<br /><br /> Tento dokument se `2018-01-01`vztahuje api-verze , které jsou zahrnuty ve výše uvedené URL.  |
| Timespan | Řetězec s následujícím `startDateTime_ISO/endDateTime_ISO` formátem, který definuje časový rozsah vrácených metrik. Tento volitelný parametr je nastaven tak, aby v příkladu vracel data za den. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci není potřeba žádné tělo požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Stavový kód 200 je vrácena při úspěšném vrácení seznamu hodnot metriky. Úplný seznam kódů chyb je k dispozici v [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse).

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
