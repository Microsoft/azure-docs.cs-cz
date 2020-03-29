---
title: Zabezpečení a ověřování služby Azure Event Grid
description: Popisuje Azure Event Grid a jeho koncepty.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899279"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizace přístupu k prostředkům sítě událostí
Azure Event Grid umožňuje řídit úroveň přístupu udělené různým uživatelům k různým operacím správy, jako je seznam odběrů událostí, vytváření nových a generování klíčů. Event Grid používá řízení přístupu na základě rolí Azure (RBAC).

## <a name="operation-types"></a>Typy operací

Event Grid podporuje následující akce:

* Microsoft.EventGrid/*/čtení
* Microsoft.EventGrid/*/zápis
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Poslední tři operace vrátí potenciálně tajné informace, které získá odfiltrovány z normální operace čtení. Doporučujeme omezit přístup k těmto operacím. 

## <a name="built-in-roles"></a>Vestavěné role

Event Grid poskytuje dvě předdefinované role pro správu odběrů událostí. Jsou důležité při implementaci [domén událostí,](event-domains.md) protože dávají uživatelům oprávnění, která potřebují k odběru témat ve vaší doméně událostí. Tyto role jsou zaměřeny na odběry událostí a neudělují přístup k akcím, jako je vytváření témat.

Tyto role můžete [přiřadit uživateli nebo skupině](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Přispěvatel eventgrid eventsubscription:** správa operací předplatného event gridu

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

**EventGrid EventSubscription Reader**: přečtěte si odběry Event Grid

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

Následují ukázkové definice rolí Event Grid, které uživatelům umožňují provést různé akce. Tyto vlastní role se liší od předdefinovaných rolí, protože poskytují širší přístup než pouze odběry událostí.

**EventGridReadOnlyRole.json**: Povolit pouze operace jen pro čtení.

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

**EventGridNoDeleteListKeysRole.json**: Povolit akce příspěvku s omezeným přístupem, ale zakázat akce odstranění.

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

**EventGridContributorRole.json**: Umožňuje všechny akce mřížky událostí.

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

Vlastní role můžete vytvářet pomocí [PowerShellu](../role-based-access-control/custom-roles-powershell.md), [Rozhraní příkazového příkazu Kazíru Azure](../role-based-access-control/custom-roles-cli.md)a [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny události nebo data zapsaná na disk službou Event Grid jsou šifrována klíčem spravovaným společností Microsoft, který zajišťuje, že je šifrován v klidovém stavu. Maximální doba, po kterou jsou události nebo data uchována, je navíc 24 hodin v souladu se [zásadami opakování mřížky událostí](delivery-and-retry.md). Event Grid automaticky odstraní všechny události nebo data po 24 hodinách, nebo čas události-k-live, podle toho, co je méně.

## <a name="next-steps"></a>Další kroky

* Úvod do mřížky událostí najdete v tématu [O mřížce událostí](overview.md)
