---
title: Azure Event Grid zabezpečení a ověřování
description: Popisuje Azure Event Grid a jeho koncepty.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601036"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autorizace přístupu k prostředkům Event Grid
Azure Event Grid umožňuje řídit úroveň přístupu daným různým uživatelům a provádět různé **operace správy** , jako jsou odběry událostí seznamu, vytváření nových a generování klíčů. Event Grid používá řízení přístupu na základě role v Azure (Azure RBAC).

> [!NOTE]
> EventGrid nepodporuje službu Azure RBAC pro publikování událostí pro Event Grid témat nebo domén. Použijte klíč sdíleného přístupového podpisu (SAS) nebo token k ověřování klientů, kteří publikují události. Další informace najdete v tématu [ověřování klientů publikování](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Typy operací
Pokud chcete zobrazit seznam operací podporovaných nástrojem Azure Event Grid, spusťte následující příkaz rozhraní příkazového řádku Azure: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Následující operace vracejí potenciálně tajné informace, které se vyfiltrují z běžných operací čtení. Doporučuje se omezit přístup k těmto operacím. 

* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/témata/klíče listkey/Action
* Microsoft. EventGrid/témata/regenerateKey/Action


## <a name="built-in-roles"></a>Vestavěné role
Event Grid poskytuje následující tři předdefinované role. 

Role přispěvatele pro čtečku předplatného Event Grid a Event Grid předplatného jsou pro správu odběrů událostí. Jsou důležité při implementaci [domén událostí](event-domains.md) , protože uživatelům poskytují oprávnění, která potřebují k přihlášení k odběru témat v doméně události. Tyto role jsou zaměřené na odběry událostí a neudělují přístup k akcím, jako je vytváření témat.

Role Přispěvatel Event Grid umožňuje vytvářet a spravovat prostředky Event Grid. 


| Role | Popis |
| ---- | ----------- | 
| [Čtečka předplatných Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Umožňuje správu Event Gridch operací odběru událostí. |
| [Přispěvatel předplatného Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Umožňuje číst odběry událostí Event Grid. |
| [Přispěvatel Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Umožňuje vytvářet a spravovat Event Grid prostředky. |


> [!NOTE]
> Vyberte odkazy v prvním sloupci a přejděte k článku, který obsahuje další podrobnosti o této roli. Pokyny k přiřazení uživatelů nebo skupin k rolím RBAC najdete v [tomto článku](../role-based-access-control/quickstart-assign-role-user-portal.md).


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
Pro systémová témata, pokud nejste vlastníkem nebo přispěvatelem zdrojového prostředku, potřebujete oprávnění zapsat nové předplatné události v oboru prostředku, který publikuje událost. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například pro přihlášení k odběru události v účtu úložiště s názvem **ucet** budete potřebovat oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata
Pro vlastní témata potřebujete oprávnění zapsat nové předplatné události v oboru tématu Event Grid. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic** potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Další kroky

* Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)
