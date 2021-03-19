---
title: Stav asynchronních operací
description: Popisuje, jak sledovat asynchronní operace v Azure. Zobrazuje hodnoty, které použijete k získání stavu dlouhotrvající operace.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: e2c5ba137d5277466cf1b382d2b0b1bc02259f00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88723448"
---
# <a name="track-asynchronous-azure-operations"></a>Sledování asynchronních operací Azure

Některé operace Azure REST běží asynchronně, protože operaci nejde dokončit rychle. Tento článek popisuje, jak sledovat stav asynchronních operací prostřednictvím hodnot vrácených v odpovědi.  

## <a name="status-codes-for-asynchronous-operations"></a>Stavové kódy pro asynchronní operace

Asynchronní operace zpočátku vrátí stavový kód HTTP buď:

* 201 (vytvořeno)
* 202 (přijato)

Po úspěšném dokončení operace vrátí buď:

* 200 (OK)
* 204 (žádný obsah)

Odpovědi na operaci, kterou provádíte, najdete v [dokumentaci k REST API](/rest/api/azure/) .

Po získání kódu odpovědi 201 nebo 202 jste připraveni monitorovat stav operace.

## <a name="url-to-monitor-status"></a>Adresa URL pro sledování stavu

Stav asynchronní operace můžete monitorovat dvěma různými způsoby. Správný přístup určíte tak, že prozkoumáte hodnoty hlaviček, které se vrátí z původní žádosti. Nejdřív vyhledejte:

* `Azure-AsyncOperation` -Adresa URL pro kontrolu průběžného stavu operace. Pokud vaše operace vrátí tuto hodnotu, použijte ji ke sledování stavu operace.
* `Retry-After` – Počet sekund, po které se má počkat, než se zkontroluje stav asynchronní operace.

Pokud `Azure-AsyncOperation` není jedna z hodnot záhlaví, vyhledejte:

* `Location` -Adresa URL pro určení, kdy byla operace dokončena. Tuto hodnotu použijte pouze v případě, že se nevrátí Azure-AsyncOperation.
* `Retry-After` – Počet sekund, po které se má počkat, než se zkontroluje stav asynchronní operace.

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation žádosti a odpovědi

Pokud máte adresu URL z hodnoty v `Azure-AsyncOperation` hlavičce, pošlete požadavek GET na tuto adresu URL. Pomocí hodnoty z `Retry-After` můžete naplánovat, jak často se má stav kontrolovat. Dostanete objekt Response, který indikuje stav operace. Při kontrole stavu operace s adresou URL se vrátí jiná odpověď `Location` . Další informace o odpovědi z adresy URL umístění najdete v tématu [Vytvoření účtu úložiště (202 s umístěním a opakování)](#create-storage-account-202-with-location-and-retry-after).

Vlastnosti odpovědi se mohou lišit, ale vždy obsahují stav asynchronní operace.

```json
{
    "status": "{status-value}"
}
```

Následující příklad ukazuje další hodnoty, které mohou být vráceny z operace:

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

Objekt Error se vrátí, když se stav nezdařil nebo byl zrušen. Všechny ostatní hodnoty jsou volitelné. Odpověď, kterou dostanete, může vypadat jinak než v příkladu.

## <a name="provisioningstate-values"></a>hodnoty provisioningState

Operace, které vytvářejí, aktualizují nebo odstraňují (PUT, PATCH, DELETE) prostředek, obvykle vracejí `provisioningState` hodnotu. Po dokončení operace se vrátí jedna z následujících tří hodnot:

* Úspěšný
* Neúspěšný
* Zrušeno

Všechny ostatní hodnoty označují, že operace stále běží. Poskytovatel prostředků může vrátit přizpůsobenou hodnotu, která označuje její stav. Můžete například obdržet přijetí žádosti, když je **žádost přijata a** je spuštěná.

## <a name="example-requests-and-responses"></a>Příklady požadavků a odpovědí

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Spuštění virtuálního počítače (202 s Azure-AsyncOperation)

Tento příklad ukazuje, jak určit stav [operace spuštění pro virtuální počítače](/rest/api/compute/virtualmachines/start). Počáteční žádost má následující formát:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Vrátí stavový kód 202. Mezi hodnoty hlaviček vidíte:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Chcete-li zjistit stav asynchronní operace, odešlete do této adresy URL další požadavek.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
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

Tento příklad ukazuje, jak určit stav [operace nasazení pro nasazení prostředků](/rest/api/resources/deployments/createorupdate) do Azure. Počáteční žádost má následující formát:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Vrátí stavový kód 201. Text odpovědi zahrnuje:

```json
"provisioningState":"Accepted",
```

Mezi hodnoty hlaviček vidíte:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Chcete-li zjistit stav asynchronní operace, odešlete do této adresy URL další požadavek.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Tělo odpovědi obsahuje stav operace:

```json
{
    "status": "Running"
}
```

Po dokončení nasazení odpověď obsahuje:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Vytvořit účet úložiště (202 s umístěním a opakovat po)

Tento příklad ukazuje, jak určit stav [operace vytvoření pro účty úložiště](/rest/api/storagerp/storageaccounts/create). Počáteční žádost má následující formát:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

A tělo požadavku obsahuje vlastnosti pro účet úložiště:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Vrátí stavový kód 202. Mezi hodnotami hlaviček vidíte následující dvě hodnoty:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Po čekání na počet sekund zadaných v operaci opakovat – ověřte stav asynchronní operace odesláním další žádosti na tuto adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Pokud je požadavek pořád spuštěný, zobrazí se stavový kód 202. Pokud se žádost dokončí, dostanete stavový kód 200 a text odpovědi obsahuje vlastnosti účtu úložiště, který byl vytvořen.

## <a name="next-steps"></a>Další kroky

* Dokumentaci ke každé operaci REST najdete v [dokumentaci REST API](/rest/api/azure/).
* Informace o nasazení šablon prostřednictvím Správce prostředků REST API najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Správce prostředků REST API](../templates/deploy-rest.md).