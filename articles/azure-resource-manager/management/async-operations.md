---
title: Stav asynchronních operací
description: Popisuje, jak sledovat asynchronní operace v Azure. Zobrazuje hodnoty, které používáte k získání stavu dlouhotrvající operace.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485439"
---
# <a name="track-asynchronous-azure-operations"></a>Sledování asynchronních operací Azure
Některé operace Azure REST spustit asynchronně, protože operace nelze dokončit rychle. Tento článek popisuje, jak sledovat stav asynchronních operací prostřednictvím hodnot vrácených v odpovědi.  

## <a name="status-codes-for-asynchronous-operations"></a>Stavové kódy pro asynchronní operace
Asynchronní operace zpočátku vrátí stavový kód HTTP buď:

* 201 (Vytvořeno)
* 202 (Přijato) 

Po úspěšném dokončení operace vrátí buď:

* 200 (OK)
* 204 (bez obsahu) 

Odpovědi na operaci, kterou provádíte, naleznete v dokumentaci k [rozhraní REST API.](/rest/api/)

## <a name="monitor-status-of-operation"></a>Sledování stavu provozu
Asynchronní operace REST vrátí hodnoty záhlaví, které slouží k určení stavu operace. Existují potenciálně tři hodnoty záhlaví, které je třeba zkontrolovat:

* `Azure-AsyncOperation`- URL pro kontrolu průběžného stavu operace. Pokud vaše operace vrátí tuto hodnotu, vždy ji použijte (namísto umístění) ke sledování stavu operace.
* `Location`- ADRESA URL pro určení po dokončení operace. Tuto hodnotu použijte pouze v případě, že azure-AsyncOperation není vrácena.
* `Retry-After`- Počet sekund čekání před kontrolou stavu asynchronní operace.

Však ne každá asynchronní operace vrátí všechny tyto hodnoty. Například budete muset vyhodnotit hodnotu hlavičky Azure-AsyncOperation pro jednu operaci a hodnotu hlavičky Umístění pro jinou operaci. 

Hodnoty záhlaví načtete tak, jako byste načetli libovolnou hodnotu záhlaví pro požadavek. Například v C# načtete hodnotu `HttpWebResponse` záhlaví `response` z objektu s názvem s následujícím kódem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Požadavek a odpověď Azure-AsyncOperation

Chcete-li získat stav asynchronní operace, odešlete požadavek GET na adresu URL v hodnotě hlavičky Azure-AsyncOperation.

Tělo odpovědi z této operace obsahuje informace o operaci. Následující příklad ukazuje možné hodnoty vrácené z operace:

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

Pouze `status` je vrácena pro všechny odpovědi. Objekt chyby je vrácen, pokud je stav Neúspěšný nebo Zrušený. Všechny ostatní hodnoty jsou volitelné; proto odpověď, kterou obdržíte může vypadat jinak než v příkladu.

## <a name="provisioningstate-values"></a>zřizováníStátní hodnoty

Operace, které vytvářejí, aktualizují nebo odstraňují (PUT, `provisioningState` PATCH, DELETE) prostředku obvykle vrátí hodnotu. Po dokončení operace je vrácena jedna z následujících tří hodnot: 

* Úspěch
* Failed
* Zrušeno

Všechny ostatní hodnoty označují, že operace je stále spuštěna. Zprostředkovatel prostředků může vrátit vlastní hodnotu, která označuje jeho stav. Můžete například obdržet **Přijato** při přijetí požadavku a spuštění.

## <a name="example-requests-and-responses"></a>Příklady požadavků a odpovědí

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Spuštění virtuálního počítače (202 s Azure-AsyncOperation)
Tento příklad ukazuje, jak určit stav **spuštění** operace pro virtuální počítače. Počáteční požadavek je v následujícím formátu:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Vrátí stavový kód 202. Mezi hodnotami záhlaví se zobrazí:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Chcete-li zkontrolovat stav asynchronní operace, odesláním jiného požadavku na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Tělo odpovědi obsahuje stav operace:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Nasazení prostředků (201 s Azure-AsyncOperation)

Tento příklad ukazuje, jak určit stav operace **nasazení** pro nasazení prostředků do Azure. Počáteční požadavek je v následujícím formátu:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Vrátí stavový kód 201. Tělo odpovědi zahrnuje:

```json
"provisioningState":"Accepted",
```

Mezi hodnotami záhlaví se zobrazí:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Chcete-li zkontrolovat stav asynchronní operace, odesláním jiného požadavku na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Tělo odpovědi obsahuje stav operace:

```json
{"status":"Running"}
```

Po dokončení nasazení odpověď obsahuje:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Vytvořit účet úložiště (202 s umístěním a opakováním po)

Tento příklad ukazuje, jak určit stav **operace vytvoření** pro účty úložiště. Počáteční požadavek je v následujícím formátu:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

A tělo požadavku obsahuje vlastnosti pro účet úložiště:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Vrátí stavový kód 202. Mezi hodnotami záhlaví se zobrazí následující dvě hodnoty:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po čekání na počet sekund zadaný v opakování po zkontrolujte stav asynchronní operace odesláním jiného požadavku na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Pokud je požadavek stále spuštěn, obdržíte stavový kód 202. Pokud byl požadavek dokončen, obdržíte stavový kód 200 a tělo odpovědi obsahuje vlastnosti účtu úložiště, který byl vytvořen.

## <a name="next-steps"></a>Další kroky

* Dokumentaci ke každé operaci REST naleznete v [dokumentaci k rozhraní REST API](/rest/api/).
* Informace o nasazování šablon prostřednictvím rozhraní REST API správce prostředků naleznete v [tématu Nasazení prostředků pomocí šablon Správce prostředků a rozhraní REST API správce prostředků](../templates/deploy-rest.md).