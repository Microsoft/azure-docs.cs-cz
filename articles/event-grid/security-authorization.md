---
title: Azure Event Grid zabezpečení a ověřování
description: Popisuje Azure Event Grid a jeho koncepty.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 5a1e4af17c2f4335ed26490bfc2408c66f4aee6b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328721"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizace přístupu k prostředkům Event Grid
Azure Event Grid umožňuje řídit úroveň přístupu daným různým uživatelům a provádět různé **operace správy** , jako jsou odběry událostí seznamu, vytváření nových a generování klíčů. Event Grid používá řízení přístupu na základě role v Azure (Azure RBAC).

> [!NOTE]
> EventGrid nepodporuje službu Azure RBAC pro publikování událostí pro Event Grid témat nebo domén. Použijte klíč sdíleného přístupového podpisu (SAS) nebo token k ověřování klientů, kteří publikují události. Další informace najdete v tématu [ověřování klientů publikování](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Typy operací

Event Grid podporuje následující akce:

* Microsoft. EventGrid/*/Read
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/DELETE
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/témata/klíče listkey/Action
* Microsoft. EventGrid/témata/regenerateKey/Action

Poslední tři operace vrátí potenciálně tajné informace, které se vyfiltrují z běžných operací čtení. Doporučuje se omezit přístup k těmto operacím. 

## <a name="built-in-roles"></a>Vestavěné role

Event Grid poskytuje dvě předdefinované role pro správu odběrů událostí. Jsou důležité při implementaci [domén událostí](event-domains.md) , protože uživatelům poskytují oprávnění, která potřebují k přihlášení k odběru témat v doméně události. Tyto role jsou zaměřené na odběry událostí a neudělují přístup k akcím, jako je vytváření témat.

[Tyto role můžete přiřadit uživateli nebo skupině](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Přispěvatel**: Správa operací předplatného Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: číst Event Grid předplatná

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Vlastní role

Pokud potřebujete zadat oprávnění, která se liší od předdefinovaných rolí, můžete vytvořit vlastní role.

Níže jsou uvedené ukázkové Event Grid definice rolí, které umožňují uživatelům provádět různé akce. Tyto vlastní role se liší od předdefinovaných rolí, protože udělují širší přístup než jenom odběry událostí.

**EventGridReadOnlyRole.js**: povoluje pouze operace jen pro čtení.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.jszapnuto**: Povolit omezené akce příspěvku, ale zakázat akce odstranění.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.jszapnuto**: umožňuje všechny akce v mřížce událostí.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Můžete vytvářet vlastní role pomocí [PowerShellu](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md)a [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

Všechny události nebo data zapsaná na disk službou Event Grid službou jsou šifrovány klíčem spravovaným společností Microsoft, který zajišťuje, aby byl zašifrovaný v klidovém stavu. Kromě toho maximální doba, po kterou jsou události nebo data uchovávány, je 24 hodin v dodržování [zásad Event Grid opakování](delivery-and-retry.md). Event Grid budou automaticky odstraňovat všechny události nebo data po 24 hodinách nebo na hodnotu TTL (Time to Live), podle toho, co je méně.

## <a name="permissions-for-event-subscriptions"></a>Oprávnění pro odběry událostí
Pokud používáte obslužnou rutinu události, která není webhookem (například centrum událostí nebo úložiště front), budete k tomuto prostředku potřebovat přístup pro zápis. Tato oprávnění znemožňují neoprávněnému uživateli odesílat události do vašeho prostředku.

Musíte mít oprávnění **Microsoft. EventGrid/EventSubscriptions/Write** pro prostředek, který je zdrojem událostí. Toto oprávnění budete potřebovat, protože píšete nové předplatné v oboru prostředku. Požadovaný prostředek se liší v závislosti na tom, jestli jste se přihlásili k odběru systémového tématu nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Systémová témata (vydavatelé služeb Azure)
Pro systémová témata potřebujete oprávnění k zápisu nového odběru události v oboru prostředku, který publikuje událost. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například pro přihlášení k odběru události v účtu úložiště s názvem **ucet**budete potřebovat oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata
Pro vlastní témata potřebujete oprávnění zapsat nové předplatné události v oboru tématu Event Grid. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic**potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Další kroky

* Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)
