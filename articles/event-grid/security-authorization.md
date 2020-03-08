---
title: Azure Event Grid zabezpečení a ověřování
description: Popisuje Azure Event Grid a jeho koncepty.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899279"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizace přístupu k prostředkům Event Grid
Azure Event Grid umožňuje řídit úroveň přístupu k různým uživatelům provádět různé operace správy, jako je například seznam odběrů událostí, vytvářet nové a vygenerujte klíče. Event Grid pomocí řízení přístupu na základě rolí Azure (RBAC).

## <a name="operation-types"></a>Typy operací

Podporuje Služba Event Grid následující akce:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Poslední tři operace vracejí potenciálně tajné informace, které získá z běžných operací čtení odfiltrována. Doporučujeme omezit přístup k těmto operacím. 

## <a name="built-in-roles"></a>Vestavěné role

Event Grid obsahuje dvě předdefinované role pro správu odběry událostí. Jsou důležité při implementaci [domén událostí](event-domains.md) , protože uživatelům poskytují oprávnění, která potřebují k přihlášení k odběru témat v doméně události. Tyto role jsou zaměřené na odběry událostí a není udělit přístup pro akce, jako je vytváření témat.

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

Pokud je třeba zadat oprávnění, která se liší od předdefinované role, můžete vytvořit vlastní role.

Následují definice rolí služby Event Grid ukázky, které umožňují uživatelům provádět různé akce. Tyto vlastní role se liší od předdefinované role, protože udělují širší přístup než jenom odběry událostí.

**EventGridReadOnlyRole. JSON**: povoluje jenom operace jen pro čtení.

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

**EventGridNoDeleteListKeysRole. JSON**: povolí omezené akce příspěvku, ale zakáže akce odstranění.

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

**EventGridContributorRole. JSON**: umožňuje všechny akce v mřížce událostí.

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



### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny události nebo data zapsaná na disk službou Event Grid službou jsou šifrovány klíčem spravovaným společností Microsoft, který zajišťuje, aby byl zašifrovaný v klidovém stavu. Kromě toho maximální doba, po kterou jsou události nebo data uchovávány, je 24 hodin v dodržování [zásad Event Grid opakování](delivery-and-retry.md). Event Grid budou automaticky odstraňovat všechny události nebo data po 24 hodinách nebo na hodnotu TTL (Time to Live), podle toho, co je méně.

## <a name="next-steps"></a>Další kroky

* Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)
