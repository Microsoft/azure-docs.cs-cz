---
title: Skupina prostředků Azure jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události skupiny prostředků s Azure Event Grid
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 4c1990909dc555e9e2a6d09538b807ba7e07ce83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363215"
---
# <a name="azure-resource-group-as-an-event-grid-source"></a>Skupina prostředků Azure jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události skupiny prostředků. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

Předplatná Azure a skupiny prostředků emitují stejné typy událostí. Typy událostí se vztahují ke změnám prostředků nebo akcím. Hlavním rozdílem je, že skupiny prostředků generují události pro prostředky v rámci skupiny prostředků a předplatné Azure generuje události pro prostředky v rámci předplatného.

Události prostředků se vytvářejí pro operace PUT, PATCH, POST a DELETE, které se odesílají do `management.azure.com` . Operace GET nevytváří události. Operace odeslané do roviny dat (například `myaccount.blob.core.windows.net` ) nevytvářejí události. Události akce poskytují data události pro operace, jako je například výpis klíčů pro určitý prostředek.

Když se přihlásíte k odběru událostí pro skupinu prostředků, koncový bod obdrží všechny události pro tuto skupinu prostředků. Události mohou zahrnovat událost, kterou chcete zobrazit, jako je například aktualizace virtuálního počítače, ale také události, které nemusí být pro vás důležité, například zápis nové položky v historii nasazení. Můžete přijmout všechny události v koncovém bodu a napsat kód, který zpracovává události, které chcete zpracovat. Případně můžete nastavit filtr při vytváření odběru události.

Chcete-li události programově zpracovávat, můžete události řadit podle `operationName` hodnoty. Například váš koncový bod události může zpracovávat pouze události pro operace, které jsou rovny `Microsoft.Compute/virtualMachines/write` nebo `Microsoft.Storage/storageAccounts/write` .

Předmět události je ID prostředku prostředku, který je cílem operace. Chcete-li filtrovat události pro určitý prostředek, zadejte toto ID prostředku při vytváření odběru události.  Chcete-li filtrovat podle typu prostředku, použijte hodnotu v následujícím formátu: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`


## <a name="available-event-types"></a>Dostupné typy událostí

Skupiny prostředků generují události správy z Azure Resource Manager, například když se vytvoří virtuální počítač nebo se odstraní účet úložiště.

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. Resources. ResourceActionCancel | Je aktivována při zrušení akce u prostředku. |
| Microsoft. Resources. ResourceActionFailure | Vyvolá se v případě, že akce u prostředku není úspěšná. |
| Microsoft. Resources. ResourceActionSuccess | Vyvolá se, když je akce prostředku úspěšná. |
| Microsoft. Resources. ResourceDeleteCancel | Vyvolá se při zrušení operace delete. Tato událost nastane, když se zruší nasazení šablony. |
| Microsoft. Resources. ResourceDeleteFailure | Vyvolá se v případě, že operace odstranění není úspěšná. |
| Microsoft. Resources. ResourceDeleteSuccess | Vyvolá se, když je operace delete úspěšná. |
| Microsoft. Resources. ResourceWriteCancel | Vyvolá se při zrušení operace CREATE nebo Update. |
| Microsoft. Resources. ResourceWriteFailure | Vyvolá se v případě, že operace vytvoření nebo aktualizace není úspěšná. |
| Microsoft. Resources. ResourceWriteSuccess | Vyvolá se, když je operace vytvořit nebo aktualizovat úspěšná. |

## <a name="example-event"></a>Příklad události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Následující příklad ukazuje schéma pro událost **ResourceWriteSuccess** . Stejné schéma se používá pro události **ResourceWriteFailure** a **ResourceWriteCancel** s různými hodnotami pro `eventType` .

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

Následující příklad ukazuje schéma pro událost **ResourceDeleteSuccess** . Stejné schéma se používá pro události **ResourceDeleteFailure** a **ResourceDeleteCancel** s různými hodnotami pro `eventType` .

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

Následující příklad ukazuje schéma pro událost **ResourceActionSuccess** . Stejné schéma se používá pro události **ResourceActionFailure** a **ResourceActionCancel** s různými hodnotami pro `eventType` .

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
}]
```

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma pro událost **ResourceWriteSuccess** . Stejné schéma se používá pro události **ResourceWriteFailure** a **ResourceWriteCancel** s různými hodnotami pro `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "type": "Microsoft.Resources.ResourceWriteSuccess",
  "time": "2018-07-19T18:38:04.6117357Z",
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

  "specversion": "1.0"
}]
```

Následující příklad ukazuje schéma pro událost **ResourceDeleteSuccess** . Stejné schéma se používá pro události **ResourceDeleteFailure** a **ResourceDeleteCancel** s různými hodnotami pro `eventType` .

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "type": "Microsoft.Resources.ResourceDeleteSuccess",
  "time": "2018-07-19T19:24:12.763881Z",
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
  "specversion": "1.0"
}]
```

Následující příklad ukazuje schéma pro událost **ResourceActionSuccess** . Stejné schéma se používá pro události **ResourceActionFailure** a **ResourceActionCancel** s různými hodnotami pro `eventType` .

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
  "type": "Microsoft.Resources.ResourceActionSuccess",
  "time": "2018-10-08T22:46:22.6022559Z",
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
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>Vlastnosti události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události skupiny prostředků. |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `source` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `type` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `time` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události skupiny prostředků. |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `authorization` | object | Požadovaná autorizace pro operaci. |
| `claims` | object | Vlastnosti deklarací identity. Další informace najdete v tématu [specifikace JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| `correlationId` | řetězec | ID operace pro řešení potíží. |
| `httpRequest` | object | Podrobnosti operace Tento objekt je zahrnutý pouze při aktualizaci stávajícího prostředku nebo při odstraňování prostředku. |
| `resourceProvider` | řetězec | Poskytovatel prostředků pro operaci. |
| `resourceUri` | řetězec | Identifikátor URI prostředku v operaci. |
| `operationName` | řetězec | Operace, kterou jste provedli. |
| `status` | řetězec | Stav operace. |
| `subscriptionId` | řetězec | ID předplatného prostředku |
| `tenantId` | řetězec | ID tenanta prostředku |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Aplikace logiky monitoruje změny virtuálního počítače a odesílá e-maily o těchto změnách. |
| [Azure CLI: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-cli-resource-group.md)| Ukázkový skript, který se přihlašuje k odběru událostí pro skupinu prostředků. Odesílá události do Webhooku. |
| [Azure CLI: přihlášení k odběru událostí pro skupinu prostředků a filtrování prostředku](./scripts/event-grid-cli-resource-group-filter.md) | Ukázkový skript, který přihlašuje události pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [PowerShell: přihlášení k odběru událostí pro skupinu prostředků](./scripts/event-grid-powershell-resource-group.md) | Ukázkový skript, který se přihlašuje k odběru událostí pro skupinu prostředků. Odesílá události do Webhooku. |
| [PowerShell: přihlášení k odběru událostí pro skupinu prostředků a filtrování prostředku](./scripts/event-grid-powershell-resource-group-filter.md) | Ukázkový skript, který přihlašuje události pro skupinu prostředků a filtruje události pro jeden prostředek. |
| [Správce prostředků Šablona: odběr prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Přihlašuje se k odběru událostí předplatného Azure nebo skupiny prostředků. Odesílá události do Webhooku. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
