---
title: Použití identity spravované služby k přeposílání událostí Azure Event Grid předplatných
description: Tento článek popisuje, jak povolit identitu spravované služby pro téma Azure Event Grid. Použijte ji k přeposílání událostí do podporovaných cílů.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 0e1f46c0bd7ce22cae77db1a524336445fd3f795
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691023"
---
# <a name="enable-and-use-managed-service-identity-for-an-event-grid-topic-or-domain"></a>Povolení a použití identity spravované služby v tématu nebo doméně Event gridu 
Tento článek popisuje, jak povolit [identitu spravované služby](../active-directory/managed-identities-azure-resources/overview.md) pro téma nebo doménu služby Event Grid. Slouží k přeposílání událostí do podporovaných cílů, jako jsou Service Bus fronty a témata, centra událostí a účty úložiště.

Tady je postup, který je podrobně popsaný v tomto článku:
1. Vytvořte téma nebo doménu s identitou přiřazenou systémem (nebo) Aktualizace existujícího tématu nebo domény, aby bylo možné identitu povolit. 
2. Přidejte identitu do příslušné role (příklad: Service Bus odesílatel dat) v cíli (příklad: Service Bus Queue).
3. Při vytváření odběrů událostí povolte použití identity k doručování událostí do cíle. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Vytvoření tématu nebo domény s identitou
Nejdřív se podíváme na to, jak vytvořit téma nebo doménu s identitou spravovanou systémem.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Můžete povolit identitu přiřazenou systémem pro téma nebo doménu při jejím vytváření v Azure Portal. Následující obrázek ukazuje, jak v tématu Povolit identitu spravovanou systémem. V podstatě vyberte možnost **Povolit identitu přiřazenou systému** na stránce **Upřesnit** v Průvodci vytvořením tématu. Tato možnost se zobrazí také na stránce **Upřesnit** v Průvodci vytvořením domény. 

![Povolit identitu při vytváření tématu](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Pomocí Azure CLI můžete také vytvořit téma nebo doménu s identitou přiřazenou systémem. Použijte `az eventgrid topic create` příkaz s `--identity` parametrem nastaveným na `systemassigned` . Pokud hodnotu pro tento parametr nezadáte, použije se výchozí hodnota `noidentity` . 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Podobně můžete pomocí `az eventgrid domain create` příkazu vytvořit doménu s identitou spravovanou systémem.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Povolení identity pro existující téma nebo doménu
V poslední části jste zjistili, jak povolit identitu spravovanou systémem při vytváření tématu nebo domény. V této části se dozvíte, jak povolit identitu spravovanou systémem pro existující téma nebo doménu. 

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
1. Přejít na [Azure Portal](https://portal.azure.com)
2. Na panelu hledání vyhledejte **témata Event gridu** .
3. Vyberte **téma** , pro které chcete spravovanou identitu povolit. 
4. Přepněte na kartu **Identita** . 
5. Zapnutím přepínače povolte identitu. 

    Podobný postup můžete použít k povolení identity pro doménu služby Event Grid.

### <a name="using-azure-cli"></a>Použití Azure CLI
Pomocí `az eventgrid topic update` příkazu s `--identity` nastavením na `systemassigned` můžete pro existující téma povolit identitu přiřazenou systémem. Pokud chcete identitu zakázat, zadejte `noidentity` hodnotu. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Příkaz pro aktualizaci existující domény je podobný ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Podporované cíle a role kontroly přístupu na základě rolí (RBAC)
Po povolení identity pro téma nebo doménu služby Event Grid Azure automaticky vytvoří identitu v Azure Active Directory (Azure AD). Přidejte tuto identitu do příslušných rolí RBAC, aby téma nebo doména mohly přesílat události do podporovaných cílů. Přidejte například identitu do role **odesilatele dat Azure Event Hubs** pro obor názvů Event Hubs tak, aby téma Event Grid mohl předávané události do Center událostí v daném oboru názvů.  

V současné době Azure Event Grid podporuje témata nebo domény nakonfigurované pomocí spravované identity přiřazené systémem k přeposílání událostí do následujících cílů. Tato tabulka také poskytuje role, ve kterých by měla být identita, aby téma mohla přecházet mezi událostmi.

| Cíl | Role RBAC | 
| ----------- | --------- | 
| Service Bus fronty a témata | [Azure Service Bus odesílatel dat](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Centrum událostí | [Odesilatel dat Event Hubs Azure](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob Storage | [Přispěvatel dat objektu BLOB služby Storage](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Queue Storage |[Odesílatel zprávy s daty ve frontě úložiště](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Přidání identity do rolí RBAC v umístěních
Tato část popisuje, jak přidat identitu pro vaše téma nebo doménu do role RBAC. 

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Pomocí **Azure Portal** můžete přiřadit k příslušné roli identitu tématu nebo domény, aby bylo možné v tématu nebo doméně přesměrovat události do cílového umístění. 

Následující příklad přidá spravovanou identitu pro téma Event gridu s názvem **msitesttopic** do role **Azure Service Bus data Sender** pro **obor názvů** Service Bus, který obsahuje prostředek fronty nebo tématu. Když přidáte do role na úrovni oboru názvů, téma může přecházet události všem entitám v oboru názvů. 

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů Service Bus** . 
2. V levém podokně vyberte **Access Control** . 
3. V části **Přidání přiřazení role** vyberte **Přidat** . 
4. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte roli. V tomto případě je to **Azure Service Bus odesílatel dat**. 
    2. Vyberte **identitu** pro vaše téma nebo doménu. 
    3. Kliknutím na **Uložit** uložte konfiguraci.

Postup je podobný jako při přidání identity k jiným rolím uvedeným v tabulce. 

### <a name="using-azure-cli"></a>Použití Azure CLI
V příkladu v této části se dozvíte, jak pomocí **Azure CLI** přidat identitu do role RBAC. Ukázkové příkazy jsou k pro témata služby Event Grid. Příkazy pro domény služby Event Grid jsou podobné. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Získat ID objektu zabezpečení pro identitu systému tématu 
Nejprve získejte hlavní ID identity spravovaného systémem v tématu a přiřaďte identitu k příslušným rolím.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Vytvoření přiřazení role pro centra událostí v různých oborech 
Následující příklad rozhraní příkazového řádku ukazuje, jak přidat identitu tématu do role **odesilatele dat Azure Event Hubs** na úrovni oboru názvů nebo na úrovni centra událostí. Pokud vytvoříte přiřazení role v oboru názvů, může téma přeposlání události do všech Center událostí v daném oboru názvů. Pokud vytvoříte na úrovni centra událostí, může téma předává události pouze do konkrétního centra událostí. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Vytvoření přiřazení role pro Service Bus téma v různých oborech 
Následující příklad rozhraní příkazového řádku ukazuje, jak přidat identitu tématu do role **Azure Service Bus data Sender** na úrovni oboru názvů nebo na úrovni Service Bus tématu. Pokud vytvoříte přiřazení role v oboru názvů, téma Event gridu může v rámci tohoto oboru názvů přecházet události všech entit (Service Bus fronty nebo témata). Pokud vytvoříte na úrovni Service Bus fronty nebo tématu, může téma Event Grid přeposílání událostí pouze do této konkrétní Service Bus fronty nebo tématu. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Vytvoření odběrů událostí, které používají identitu
Jakmile budete mít téma nebo doménu s identitou spravovanou systémem a přidáte identitu do příslušné role v cíli, budete připraveni vytvořit odběry, které používají tuto identitu. 

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal
Při vytváření odběru událostí se zobrazí možnost povolit použití identity přiřazené systémem pro koncový bod v části **Podrobnosti o koncovém bodu** . 

![Při vytváření odběru událostí pro frontu Service Bus povolit identitu](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Můžete také povolit používání identity přiřazené systémem pro nedoručené odkládání na kartě **Další funkce** . 

![Povolit systémově přiřazenou identitu pro nedoručené dopisy](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Použití Azure CLI – Service Bus Queue 
V této části se dozvíte, jak pomocí **Azure CLI** povolit používání identity přiřazené systémem k doručování událostí do fronty Service Bus. Identita musí být členem role **Azure Service Bus data Sender** . Musí být také členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, který se používá pro nedoručené odkládání. 

#### <a name="define-variables"></a>Definování proměnných
Nejdřív zadejte hodnoty pro následující proměnné, které se použijí v příkazu CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 
Tento ukázkový příkaz vytvoří odběr událostí pro téma Event gridu s typem koncového bodu nastaveným na **Service Bus Queue**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>Vytvoření odběru událostí pomocí spravované identity pro doručení a nedoručená oznámení
Tento ukázkový příkaz vytvoří odběr událostí pro téma Event gridu s typem koncového bodu nastaveným na **Service Bus Queue**. Také určuje, že identita spravovaná systémem, která se má použít pro nedoručené zprávy. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI – Event Hubs 
V této části se dozvíte, jak pomocí **Azure CLI** povolit využití identity přiřazené systémem k doručování událostí do centra událostí. Identita musí být členem role **odesilatele dat Azure Event Hubs** . Musí být také členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, který se používá pro nedoručené odkládání. 

#### <a name="define-variables"></a>Definování proměnných
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 
Tento ukázkový příkaz vytvoří odběr události pro téma Event gridu s typem koncového bodu nastaveným na **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování a doručování zpráv 
Tento ukázkový příkaz vytvoří odběr události pro téma Event gridu s typem koncového bodu nastaveným na **Event Hubs**. Také určuje, že identita spravovaná systémem, která se má použít pro nedoručené zprávy. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI – Azure Storage fronta 
V této části se dozvíte, jak pomocí **Azure CLI** povolit používání identity přiřazené systémem k doručování událostí do fronty Azure Storage. Identita musí být členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště.

#### <a name="define-variables"></a>Definování proměnných  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování a doručování zpráv 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Další kroky
Další informace o identitách spravované služby najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 