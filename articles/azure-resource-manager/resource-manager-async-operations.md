---
title: Asynchronní operace v Azure | Microsoft Docs
description: Popisuje, jak sledovat asynchronních operací v Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: f62212f0488e4d1be49b419615b3a16b80033fd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358706"
---
# <a name="track-asynchronous-azure-operations"></a>Sledování asynchronní operace v Azure
Některé operace Azure REST spustit asynchronně, protože operaci nelze dokončit rychle. Toto téma popisuje, jak sledovat stav asynchronní operace prostřednictvím hodnot vrácených v odpovědi.  

## <a name="status-codes-for-asynchronous-operations"></a>Stavové kódy pro asynchronní operace
Asynchronní operace původně vrátí kód stavu HTTP buď:

* 201 (vytvořeno)
* 202 (platných) 

Po úspěšném dokončení operace, vrátí buď:

* 200 (OK)
* 204 (žádný obsah) 

Odkazovat [dokumentace k REST API](/rest/api/) zobrazíte odpovědi pro operaci jsou prováděny. 

## <a name="monitor-status-of-operation"></a>Sledujte stav operace
Asynchronní operace REST návratové hodnoty hlavičky, které můžete použít k určení stavu operace. Existují potenciálně tři hodnoty hlavičky k zkontrolujte:

* `Azure-AsyncOperation` -Adresa URL pro kontrolu stavu probíhající operace. Pokud vaše operace vrací hodnotu této, vždy použijte ho (namísto umístění) sledovat stav operace.
* `Location` -Adresa URL pro určení po dokončení operace. Tato hodnota se používá jenom v případě, že Azure AsyncOperation nevrátí.
* `Retry-After` -Počet sekund čekání před kontroluje stav asynchronní operace.

Všechny tyto hodnoty se ale vrátí se nemusí být vždy asynchronní operaci. Potřebujete například vyhodnotit hodnotu hlavičky Azure AsyncOperation pro jednu operaci a hodnota hlavičky umístění pro jiná operace. 

Hodnoty hlavičky načíst, protože by načíst všechny hodnoty v záhlaví požadavku. Například v jazyce C#, načtete hodnota hlavičky ze `HttpWebResponse` objekt s názvem `response` následujícím kódem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure AsyncOperation žádosti a odpovědi

Pokud chcete získat stav asynchronní operace, odeslat požadavek GET na adresu URL v Azure AsyncOperation hodnota hlavičky.

Text odpovědi z této operace obsahuje informace o operaci. Následující příklad ukazuje možné hodnoty vrácená z operace:

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

Pouze `status` se vrátí pro všechny odpovědi. Objekt chyba se vrátí, když se stav se nezdařilo nebo zrušení. Všechny ostatní hodnoty jsou volitelné. proto bude vypadat odpovědi, které obdržíte lišit od příkladu.

## <a name="provisioningstate-values"></a>hodnoty stavu zřizování

Operace, které slouží k vytvoření, aktualizace nebo odstranění (DELETE PUT, PATCH,) prostředku obvykle vrací `provisioningState` hodnotu. Po dokončení operace se vrátí jednu z následujících tří hodnot: 

* Úspěch
* Selhalo
* Zrušeno

Všechny ostatní hodnoty označují, že operace je stále spuštěná. Poskytovatel prostředku může vrátit vlastní hodnotu, která určuje jeho stav. Například můžete obdržet **platných** po žádosti přijaté a spuštěná.

## <a name="example-requests-and-responses"></a>Příklad požadavky a odpovědi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Spustit virtuální počítač (202 s Azure AsyncOperation)
Tento příklad ukazuje, jak určit stav **spustit** operace pro virtuální počítače. Počáteční žádosti je v následujícím formátu:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Vrátí stavový kód 202. Mezi hodnoty hlavičky zobrazí:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Chcete-li zkontrolovat stav asynchronní operace odesílání další požadavek na tuto adresu URL.

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

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Nasadit prostředky (201 s Azure AsyncOperation)

Tento příklad ukazuje, jak určit stav **nasazení** operace nasazení prostředků do Azure. Počáteční žádosti je v následujícím formátu:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Vrátí stavový kód 201. Text odpovědi obsahuje:

```json
"provisioningState":"Accepted",
```

Mezi hodnoty hlavičky zobrazí:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Chcete-li zkontrolovat stav asynchronní operace odesílání další požadavek na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Text odpovědi obsahuje stav operace:

```json
{"status":"Running"}
```

Po dokončení nasazení obsahuje odpovědi:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Vytvořit účet úložiště (202 umístění a zkuste to znovu za)

Tento příklad ukazuje, jak určit stav **vytvořit** operace pro účty úložiště. Počáteční žádosti je v následujícím formátu:

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

Po čekání počet sekund zadaný v opakovat po, zkontrolujte stav asynchronní operace odesláním další požadavek na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Pokud požadavek je stále spuštěná, obdržíte kód stavu 202. Pokud je požadavek dokončen, vaše přijímat stavovým kódem 200 a text odpovědi obsahuje vlastnosti účtu úložiště, který byl vytvořen.

## <a name="next-steps"></a>Další postup

* Dokumentaci o každé operace REST najdete v tématu [dokumentace k REST API](/rest/api/).
* informace o nasazení šablony přes rozhraní REST API Resource Manager najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a REST API Resource Manageru](resource-group-template-deploy-rest.md).