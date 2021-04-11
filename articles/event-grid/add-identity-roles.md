---
title: Přidat spravovanou identitu do role v Azure Event Grid cíli
description: Tento článek popisuje, jak přidat spravovanou identitu do rolí Azure v umístěních, jako jsou Azure Service Bus a Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280475"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Udělit spravované identitě přístup k Event Grid cíli
Tato část popisuje, jak přidat identitu pro vaše systémové téma, vlastní téma nebo doménu do role Azure. 

## <a name="prerequisites"></a>Požadavky
Přiřaďte spravovanou identitu přiřazenou systémem pomocí pokynů z následujících článků:

- [Vlastní témata nebo domény](enable-identity-custom-topics-domains.md)
- [Systémová témata](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Podporované cíle a role Azure
Po povolení identity pro vlastní téma nebo doménu služby Event Grid vytvoří Azure v Azure Active Directory automaticky identitu. Přidejte tuto identitu do odpovídajících rolí Azure, aby vlastní téma nebo doména mohly předávané události do podporovaných cílů. Přidejte například identitu do role **odesilatele dat azure Event Hubs** pro obor názvů Azure Event Hubs, aby vlastní téma Event Grid mohl předávané události do Center událostí v daném oboru názvů. 

V současné době Azure Event Grid podporuje vlastní témata nebo domény nakonfigurované pomocí spravované identity přiřazené systémem k přeposílání událostí do následujících cílů. Tato tabulka také poskytuje role, ve kterých by měla být identita, aby vlastní téma mohl tyto události předaných.

| Cíl | Role Azure | 
| ----------- | --------- | 
| Service Bus fronty a témata | [Azure Service Bus odesílatel dat](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Odesilatel dat Event Hubs Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Přispěvatel dat v objektech blob služby Storage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Odesílatel zprávy s daty ve frontě úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Pomocí Azure Portal můžete přiřadit vlastní téma nebo identitu domény příslušné roli, aby vlastní téma nebo doména mohly předávané události do cílového umístění. 

Následující příklad přidá spravovanou identitu pro vlastní téma Event Grid s názvem **msitesttopic** do role **Azure Service Bus data Sender** pro obor názvů Service Bus, který obsahuje prostředek fronty nebo tématu. Když přidáte do role na úrovni oboru názvů, vlastní téma Event Grid může přecházet události všem entitám v rámci oboru názvů. 

1. V [Azure Portal](https://portal.azure.com)přejít na **obor názvů Service Bus** . 
1. V levém podokně vyberte **Access Control** . 
1. V části **Přidání přiřazení role** vyberte **Přidat** . 
1. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte roli. V tomto případě je to **Azure Service Bus odesílatel dat**. 
    1. Vyberte **identitu** pro vlastní téma Event Grid nebo doménu. 
    1. Kliknutím na **Uložit** uložte konfiguraci.

Postup je podobný jako při přidání identity k jiným rolím uvedeným v tabulce. 

## <a name="use-the-azure-cli"></a>Použití Azure CLI
V příkladu v této části se dozvíte, jak pomocí rozhraní příkazového řádku Azure přidat identitu do role Azure. Ukázkové příkazy jsou k pro vlastní témata služby Event Grid. Příkazy pro domény služby Event Grid jsou podobné. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Získat ID objektu zabezpečení pro identitu systému vlastního tématu 
Nejprve získejte hlavní ID identity spravované systémem vlastního tématu a přiřaďte identitu k příslušným rolím.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Vytvoření přiřazení role pro centra událostí v různých oborech 
Následující příklad rozhraní příkazového řádku ukazuje, jak přidat identitu vlastního tématu do role **odesilatele dat Azure Event Hubs** na úrovni oboru názvů nebo na úrovni centra událostí. Pokud vytvoříte přiřazení role na úrovni oboru názvů, vlastní téma může předávané události do všech Center událostí v daném oboru názvů. Pokud vytvoříte přiřazení role na úrovni centra událostí, vlastní téma může předává události pouze do konkrétního centra událostí. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Vytvoření přiřazení role pro Service Bus téma v různých oborech 
Následující příklad rozhraní příkazového řádku ukazuje, jak přidat identitu vlastního tématu Event gridu do role **Azure Service Bus data Sender** na úrovni oboru názvů nebo na úrovni Service Bus tématu. Pokud vytvoříte přiřazení role na úrovni oboru názvů, téma Event gridu může přecházet události všem entitám (Service Bus fronty nebo témata) v rámci tohoto oboru názvů. Pokud vytvoříte přiřazení role na úrovni Service Bus fronty nebo tématu, může vlastní téma Event Grid přeslat události pouze do této konkrétní Service Bus fronty nebo tématu. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Další kroky
Teď, když jste přiřadili systémovou identitu k vašemu systémovému tématu, vlastnímu tématu nebo doméně a Přidali jste identitu do odpovídajících rolí v umístěních, přečtěte si téma [doručování událostí pomocí spravované identity](managed-service-identity.md) při doručování událostí do cílových umístění pomocí identity.


