---
title: Schéma událostí předplatného Azure Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro odběr událostí pomocí služby Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 10/12/2018
ms.author: tomfitz
ms.openlocfilehash: ae6513c503b930d9c953f5245a9c98ea096109bb
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310229"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Schéma událostí Azure Event Grid pro předplatná

Tento článek obsahuje vlastnosti a schéma pro události předplatného Azure. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Skupiny prostředků a předplatná Azure generování stejné typy událostí. Typy událostí se vztahují na změny zdrojů nebo akce. Hlavní rozdíl je, že skupiny prostředků vysílat události pro prostředky v rámci skupiny prostředků a předplatných Azure vysílat události pro prostředky v rámci předplatného.

Zdroj události jsou vytvořeny pro metodu POST PUT, PATCH, operací a odstranění, které se pošlou `management.azure.com`. ZÍSKAT operace nevytvářet události. Operace odeslání k rovině dat (třeba `myaccount.blob.core.windows.net`) nevytvářet události. Akce události poskytují data události pro operace, jako je výpis klíčů pro prostředek.

Při vytvoření odběru událostí předplatného Azure, váš koncový bod přijímá všechny události pro toto předplatné. Události můžou zahrnovat události, kterou chcete zobrazit, jako je například aktualizaci virtuálního počítače, ale také událostí, které nejsou možná pro vás důležitá, jako je například zápis nová položka v historii nasazení. Můžete zobrazit všechny události na váš koncový bod a napsat kód, který zpracovává události, kterou chcete zpracovat. Nebo můžete nastavit filtr při vytváření odběru událostí.

Programově zpracování událostí, můžete seřadit události pohledem `operationName` hodnotu. Například může váš koncový bod události pouze zpracovávat události pro operace, které se rovnají `Microsoft.Compute/virtualMachines/write` nebo `Microsoft.Storage/storageAccounts/write`.

Předmět události je ID prostředku prostředků, které je cílem operaci. Chcete-li filtrovat události pro prostředek, zadejte tento prostředek ID při vytvoření odběru událostí. Chcete-li filtrovat podle typu prostředku, použijte hodnotu v následujícím formátu: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj událostí předplatného Azure](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Typy událostí k dispozici

Předplatná Azure vysílat události management z Azure Resource Manageru, například při vytvoření virtuálního počítače nebo účet úložiště je odstraněný.

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Vyvoláno, když se zruší akci u prostředku. |
| Microsoft.Resources.ResourceActionFailure | Vyvoláno, když se akce u prostředku se nezdaří. |
| Microsoft.Resources.ResourceActionSuccess | Vyvoláno, když se akce u prostředku proběhne úspěšně. |
| Microsoft.Resources.ResourceDeleteCancel | Vyvolá se, když odstranit operace se zrušila. Tato událost se stane, když se zruší nasazení šablony. |
| Microsoft.Resources.ResourceDeleteFailure | Vyvolá se, když se odstranění nezdaří operace. |
| Microsoft.Resources.ResourceDeleteSuccess | Vyvolá se, když operace odstranění úspěšná. |
| Microsoft.Resources.ResourceWriteCancel | Vyvolá se, když vytvoření nebo aktualizace operace se zrušila. |
| Microsoft.Resources.ResourceWriteFailure | Vyvolá se, když vytvořit nebo aktualizovat operace se nezdaří. |
| Microsoft.Resources.ResourceWriteSuccess | Vyvolá se, když vytvoření nebo aktualizace operace úspěšná. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu **ResourceWriteSuccess** událostí. Se používá stejné schéma pro **ResourceWriteFailure** a **ResourceWriteCancel** události s různými hodnotami parametru `eventType`.

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

Následující příklad ukazuje schématu **ResourceDeleteSuccess** událostí. Se používá stejné schéma pro **ResourceDeleteFailure** a **ResourceDeleteCancel** události s různými hodnotami parametru `eventType`.

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

Následující příklad ukazuje schématu **ResourceActionSuccess** událostí. Se používá stejné schéma pro **ResourceActionFailure** a **ResourceActionCancel** události s různými hodnotami parametru `eventType`.

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

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události předplatného. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Autorizace | objekt | Požadovaná oprávnění pro operaci. |
| deklarace identity | objekt | Vlastnosti deklarace identity. Další informace najdete v tématu [JWT specifikace](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | řetězec | ID operace odstraňování potíží. |
| httpRequest | objekt | Podrobnosti o operaci. Tento objekt je pouze zahrnuty při aktualizaci stávajícího prostředku nebo odstranění prostředku. |
| ResourceProvider | řetězec | Poskytovatel prostředků pro operaci. |
| resourceUri | řetězec | Identifikátor URI prostředku v operaci. |
| operationName | řetězec | Operace, která byla provedena. |
| status | řetězec | Stav operace. |
| subscriptionId | řetězec | ID předplatného prostředku. |
| ID Tenanta | řetězec | ID tenanta prostředku. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md).
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
