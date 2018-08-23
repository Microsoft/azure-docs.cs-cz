---
title: Asynchronní operace Azure | Dokumentace Microsoftu
description: Popisuje, jak sledovat asynchronních operací v Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: 601f4a899393d8ddd5ea698d4d01ade7141ee91f
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054062"
---
# <a name="track-asynchronous-azure-operations"></a>Sledování asynchronních operací v Azure
Některé operace Azure REST spustit asynchronně, protože operaci nelze dokončit, rychle. Tento článek popisuje, jak sledovat stav asynchronní operace, ať už v odpovědi vrácené hodnoty.  

## <a name="status-codes-for-asynchronous-operations"></a>Stavové kódy pro asynchronní operace
Asynchronní operace zpočátku vrátí stavový kód HTTP buď:

* 201 (vytvořeno)
* 202 (přijato) 

Po úspěšném dokončení operace vrátí buď:

* 200 (OK)
* 204 (žádný obsah) 

Odkazovat [dokumentace k rozhraní REST API](/rest/api/) zobrazíte odpovědi pro operaci provedete.

## <a name="monitor-status-of-operation"></a>Sledování stavu operace
Asynchronní operace REST návratové hodnoty hlavičky, které můžete použít ke zjištění stavu operace. Existují potenciálně tři hodnoty hlavičky k prozkoumání:

* `Azure-AsyncOperation` – Adresa URL pro kontrolu stavu probíhající operace. Pokud operace vrátí tuto hodnotu, vždycky jeho použití (a nikoli umístění) ke sledování stavu operace.
* `Location` – Adresa URL pro určení, kdy je operace dokončená. Tuto hodnotu použijte jenom v případě, že Azure-AsyncOperation nevrátí.
* `Retry-After` -Počet sekund čekání před kontroluje se stav asynchronní operace.

Ale ne každé asynchronní operace vrátí všechny tyto hodnoty. Například budete muset vyhodnotit hodnotu hlavičky Azure-AsyncOperation pro jednu operaci a hodnota hlavičky umístění pro jiná operace. 

Jak by načíst libovolnou hodnotu záhlaví pro žádost o načtení hodnoty hlavičky. Například v jazyce C#, načtete hodnota hlavičky ze `HttpWebResponse` objekt s názvem `response` následujícím kódem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation žádostí a odpovědí

Pokud chcete získat stav asynchronní operace, odeslat požadavek GET na adresu URL v Azure-AsyncOperation hodnota hlavičky.

Text odpovědi z této operace obsahuje informace o operaci. Následující příklad ukazuje vrácená z operace možné hodnoty:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Pouze `status` se vrací pro všechny odpovědi. Objekt error je vrácena, pokud je stav selhalo nebo zrušeno. Všechny ostatní hodnoty jsou volitelné. Proto může vypadat odpověď, který jste dostali lišit od příkladu.

## <a name="provisioningstate-values"></a>Stav zřizování hodnoty

Operace vytvoření, aktualizace nebo odstranění (PUT, PATCH, DELETE) prostředku obvykle vrací `provisioningState` hodnotu. Po dokončení operace se vrátí jednu z následujících tří hodnot: 

* Úspěch
* Selhalo
* Zrušeno

Všechny ostatní hodnoty označují, že operace je stále spuštěn. Poskytovatel prostředků může vrátit vlastní hodnotu, která indikuje její stav. Například můžete obdržet **přijato** při požadavku je přijatá a spuštěné.

## <a name="example-requests-and-responses"></a>Příklad žádosti a odpovědi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Spustit virtuální počítač (s Azure-AsyncOperation 202)
Tento příklad ukazuje, jak zjistit stav **start** operace pro virtuální počítače. V prvotní žádosti je v následujícím formátu:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Vrátí stavový kód 202. Mezi hodnoty hlavičky se zobrazí:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Chcete-li zkontrolovat stav asynchronní operace odeslání dalšího požadavku na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Text odpovědi obsahuje stav operace:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Nasazení prostředků (201 s Azure-AsyncOperation)

Tento příklad ukazuje, jak zjistit stav **nasazení** operace nasazení prostředků do Azure. V prvotní žádosti je v následujícím formátu:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Vrátí stavový kód 201. Tělo odpovědi obsahuje:

```json
"provisioningState":"Accepted",
```

Mezi hodnoty hlavičky se zobrazí:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Chcete-li zkontrolovat stav asynchronní operace odeslání dalšího požadavku na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Text odpovědi obsahuje stav operace:

```json
{"status":"Running"}
```

Po dokončení nasazení se odpověď obsahuje:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Vytvoření účtu úložiště (umístění a zkuste to znovu po 202)

Tento příklad ukazuje, jak zjistit stav **vytvořit** operace pro účty úložiště. V prvotní žádosti je v následujícím formátu:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

A text žádosti obsahuje vlastnosti pro účet úložiště:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Vrátí stavový kód 202. Mezi hodnoty hlavičky najdete v následujících dvou hodnot:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po čekání na počet sekund zadané v poli Retry-After, zkontrolujte stav asynchronní operace odesláním další požadavek na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Pokud požadavek pořád běží, zobrazí se stavovým kódem 202. Pokud se žádost dokončí, váš zobrazit stavový kód 200 a text odpovědi obsahuje vlastnosti tohoto účtu úložiště, který byl vytvořen.

## <a name="next-steps"></a>Další postup

* Dokumentaci ke službě jednotlivých operací REST, naleznete v tématu [dokumentace k rozhraní REST API](/rest/api/).
* informace o nasazení šablony pomocí rozhraní REST API Resource Manageru najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a rozhraní REST API Resource Manageru](resource-group-template-deploy-rest.md).