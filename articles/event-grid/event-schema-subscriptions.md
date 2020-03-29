---
title: Schéma události předplatného služby Azure Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události předplatného s Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561672"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Schéma událostí Azure Event Grid pro předplatná

Tento článek obsahuje vlastnosti a schéma pro události předplatného Azure.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

Předplatná Azure a skupiny prostředků vyzařují stejné typy událostí. Typy událostí se vztahují ke změnám nebo akcím zdrojů. Hlavní rozdíl je, že skupiny prostředků vyzařují události pro prostředky v rámci skupiny prostředků a předplatná Azure vyzařují události pro prostředky v rámci předplatného.

Události prostředků jsou vytvářeny pro operace PUT, PATCH, `management.azure.com`POST a DELETE, které jsou odesílány do aplikace . Operace GET nevytvářejí události. Operace odeslané do roviny dat (například) `myaccount.blob.core.windows.net`nevytvářejí události. Události akce poskytují data událostí pro operace, jako je výpis klíčů pro prostředek.

Když se přihlásíte k odběru událostí pro předplatné Azure, váš koncový bod obdrží všechny události pro toto předplatné. Události mohou zahrnovat událost, kterou chcete zobrazit, jako je například aktualizace virtuálního počítače, ale také události, které pro vás možná nejsou důležité, jako je například zápis nové položky v historii nasazení. Můžete přijímat všechny události v koncovém bodě a napsat kód, který zpracovává události, které chcete zpracovat. Nebo můžete nastavit filtr při vytváření předplatného události.

Chcete-li programově zpracovávat události, můžete `operationName` události seřadit podle hodnoty. Koncový bod události může například zpracovávat `Microsoft.Compute/virtualMachines/write` události pouze `Microsoft.Storage/storageAccounts/write`pro operace, které jsou stejné nebo .

Předmět události je ID prostředku, který je cílem operace. Chcete-li filtrovat události pro prostředek, zadejte toto ID prostředku při vytváření předplatného události. Chcete-li filtrovat podle typu prostředku, použijte hodnotu v následujícím formátu:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Seznam ukázkových skriptů a kurzů najdete v tématu [Zdroj událostí předplatného Azure](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Dostupné typy událostí

Předplatná Azure vyzařují události správy ze Správce prostředků Azure, například když se vytvoří virtuální počítač nebo se odstraní účet úložiště.

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Vyvolána při zrušení akce na prostředku. |
| Microsoft.Resources.ResourceActionFailure | Je aktivována, když se akce na prostředek nezdaří. |
| Microsoft.Resources.ResourceActionSuccess | Vyvolána, když je akce na prostředek úspěšná. |
| Microsoft.Resources.ResourcesDeleteCancel | Je aktivována při odstranění operace je zrušena. K této události dojde, když je nasazení šablony zrušeno. |
| Microsoft.Resources.ResourcesDeleteSelhání | Je aktivována při odstranění operace se nezdaří. |
| Microsoft.Resources.ResourcesDeleteÚspěch | Je aktivována při úspěšném odstranění operace. |
| Microsoft.Resources.ResourceWriteCancel | Je aktivována při zrušení operace vytvoření nebo aktualizace. |
| Microsoft.Resources.ResourceWriteFailure | Je aktivována při vytvoření nebo aktualizaci operace se nezdaří. |
| Microsoft.Resources.ResourceWriteSuccess | Je aktivována při úspěšném vytvoření nebo aktualizaci operace. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma události **ResourceWriteSuccess.** Stejné schéma se používá pro **události ResourceWriteFailure** a **ResourceWriteCancel** s různými hodnotami pro `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Následující příklad ukazuje schéma události **ResourceDeleteSuccess.** Stejné schéma se používá pro **události ResourceDeleteFailure** a **ResourceDeleteCancel** s různými hodnotami pro `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Následující příklad ukazuje schéma události **ResourceActionSuccess.** Stejné schéma se používá pro **události ResourceActionFailure** a **ResourceActionCancel** s různými hodnotami pro `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí předplatného. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| autorizace | objekt | Požadované povolení pro operaci. |
| Nároky | objekt | Vlastnosti deklarací. Další informace naleznete v [tématu Specifikace JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | řetězec | ID operace pro řešení potíží. |
| httpRequest | objekt | Podrobnosti o operaci. Tento objekt je zahrnut pouze při aktualizaci existujícího prostředku nebo odstranění prostředku. |
| resourceProvider | řetězec | Zprostředkovatel prostředků pro operaci. |
| resourceUri | řetězec | Identifikátor URI prostředku v operaci. |
| operationName | řetězec | Operace, která byla provedena. |
| status | řetězec | Stav operace. |
| subscriptionId | řetězec | ID předplatného prostředku. |
| tenantId | řetězec | ID klienta prostředku. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md).
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
