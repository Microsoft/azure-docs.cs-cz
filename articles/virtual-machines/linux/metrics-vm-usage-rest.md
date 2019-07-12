---
title: Získání dat o používání virtuálních počítačů Azure pomocí rozhraní REST API | Dokumentace Microsoftu
description: Použijte rozhraní Azure REST API pro shromažďování metrik využití pro virtuální počítač.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 53e93d20f4a8c4877374d8242521ca97d481e15d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667459"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Získat metriky využití virtuálního počítače pomocí rozhraní REST API

Tento příklad ukazuje, jak načíst využití procesoru pro [virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) pomocí [rozhraní Azure REST API](/rest/api/azure/).

Úplnou referenční dokumentaci a další ukázky pro rozhraní REST API jsou k dispozici v [Reference k rozhraní REST pro monitorování Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Žádost o sestavení

Můžete shromažďovat následující požadavek GET [metriky využití CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) z virtuálního počítače

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Hlavičky požadavku

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinný parametr. Nastavte na `application/json`.|  
|*Authorization:*|Povinný parametr. Nastaven na platné `Bearer` [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parametry identifikátoru URI

| Name | Popis |
| :--- | :---------- |
| subscriptionId | ID předplatného, který identifikuje předplatné Azure. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Název skupiny prostředků Azure, které jsou přidružené k prostředku. Tuto hodnotu lze získat z rozhraní API Azure Resource Manageru, rozhraní příkazového řádku nebo portálu. |
| vmname | Název virtuálního počítače Azure. |
| metricnames | Čárkami oddělený seznam platných [metriky Load balanceru úrovně](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Verze rozhraní API, která se má použít pro daný požadavek.<br /><br /> Tento dokument popisuje verzi api-version `2018-01-01`, který je obsažen v adrese URL výše.  |
| TimeSpan | Řetězec v následujícím formátu `startDateTime_ISO/endDateTime_ISO` , který definuje časový rozsah vrácené metrik. Tento nepovinný parametr je nastaven na vrátit data za jeden den v příkladu. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Text požadavku

Pro tuto operaci je potřeba není datová část požadavku.

## <a name="handle-the-response"></a>Zpracování odpovědi

Pokud seznam hodnot metriky byla úspěšně vrácena se vrátí stavový kód 200. Úplný seznam kódů chyb je k dispozici v [referenční dokumentaci](/rest/api/monitor/metrics/list#errorresponse).

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
