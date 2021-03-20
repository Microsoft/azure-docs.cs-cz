---
title: Doručení události, identita spravované služby a privátní odkaz
description: Tento článek popisuje, jak povolit identitu spravované služby pro téma Azure Event Grid. Použijte ji k přeposílání událostí do podporovaných cílů.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007767"
---
# <a name="event-delivery-with-a-managed-identity"></a>Doručování událostí se spravovanou identitou
Tento článek popisuje, jak povolit [identitu spravované služby](../active-directory/managed-identities-azure-resources/overview.md) pro vlastní témata nebo domény služby Azure Event Grid. Slouží k přeposílání událostí do podporovaných cílů, jako jsou Service Bus fronty a témata, centra událostí a účty úložiště.

Tady je postup, který je podrobně popsaný v tomto článku:
1. Vytvořte vlastní téma nebo doménu s identitou přiřazenou systémem, nebo aktualizujte stávající vlastní téma nebo doménu, aby bylo možné identitu povolit. 
1. Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue).
1. Při vytváření odběrů událostí povolte použití identity k doručování událostí do cíle. 

> [!NOTE]
> V současné době není možné doručovat události pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). Další informace najdete v části [soukromé koncové body](#private-endpoints) na konci tohoto článku. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Vytvoření vlastního tématu nebo domény s identitou
Nejdřív se podíváme na to, jak vytvořit téma nebo doménu s identitou spravovanou systémem.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Identitu přiřazenou systémem můžete povolit pro vlastní téma nebo doménu při jejím vytváření v Azure Portal. Následující obrázek ukazuje, jak povolit identitu spravovanou systémem pro vlastní téma. V podstatě vyberte možnost **Povolit identitu přiřazenou systému** na stránce **Upřesnit** v Průvodci vytvořením tématu. Tato možnost se zobrazí také na stránce **Upřesnit** v Průvodci vytvořením domény. 

![Povolit identitu při vytváření vlastního tématu](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Použití Azure CLI
Pomocí rozhraní příkazového řádku Azure můžete také vytvořit vlastní téma nebo doménu s identitou přiřazenou systémem. Použijte `az eventgrid topic create` příkaz s `--identity` parametrem nastaveným na `systemassigned` . Pokud hodnotu pro tento parametr nezadáte, použije se výchozí hodnota `noidentity` . 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Podobně můžete pomocí `az eventgrid domain create` příkazu vytvořit doménu s identitou spravovanou systémem.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Povolení identity pro existující vlastní téma nebo doménu
V předchozí části jste zjistili, jak povolit systémově spravovanou identitu, zatímco jste vytvořili vlastní téma nebo doménu. V této části se dozvíte, jak povolit identitu spravovanou systémem pro existující vlastní téma nebo doménu. 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Následující postup ukazuje, jak povolit identitu spravovanou systémem pro vlastní téma. Postup pro povolení identity pro doménu je podobný. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V horní části panelu hledání vyhledejte **témata Event gridu** .
3. Vyberte **vlastní téma** , pro které chcete spravovanou identitu povolit. 
4. Přepněte na kartu **Identita** . 
5. **Zapnutím přepínače povolte identitu** . 
1. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Stránka identita pro vlastní téma"::: 

Podobný postup můžete použít k povolení identity pro doménu služby Event Grid.

### <a name="use-the-azure-cli"></a>Použití Azure CLI
Pomocí `az eventgrid topic update` příkazu s `--identity` nastavením na `systemassigned` Povolte identitu přiřazenou systémem pro existující vlastní téma. Pokud chcete identitu zakázat, zadejte `noidentity` hodnotu. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Příkaz pro aktualizaci existující domény je podobný ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-azure-roles"></a>Podporované cíle a role Azure
Po povolení identity pro vlastní téma nebo doménu služby Event Grid vytvoří Azure v Azure Active Directory automaticky identitu. Přidejte tuto identitu do odpovídajících rolí Azure, aby vlastní téma nebo doména mohly předávané události do podporovaných cílů. Přidejte například identitu do role **odesilatele dat azure Event Hubs** pro obor názvů Azure Event Hubs, aby vlastní téma Event Grid mohl předávané události do Center událostí v daném oboru názvů. 

V současné době Azure Event Grid podporuje vlastní témata nebo domény nakonfigurované pomocí spravované identity přiřazené systémem k přeposílání událostí do následujících cílů. Tato tabulka také poskytuje role, ve kterých by měla být identita, aby vlastní téma mohl tyto události předaných.

| Cíl | Role Azure | 
| ----------- | --------- | 
| Service Bus fronty a témata | [Azure Service Bus odesílatel dat](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Odesilatel dat Event Hubs Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Přispěvatel dat v objektech blob služby Storage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Odesílatel zprávy s daty ve frontě úložiště](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Přidání identity do rolí Azure v umístěních
Tato část popisuje, jak přidat identitu pro vlastní téma nebo doménu do role Azure. 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
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

### <a name="use-the-azure-cli"></a>Použití Azure CLI
V příkladu v této části se dozvíte, jak pomocí rozhraní příkazového řádku Azure přidat identitu do role Azure. Ukázkové příkazy jsou k pro vlastní témata služby Event Grid. Příkazy pro domény služby Event Grid jsou podobné. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Získat ID objektu zabezpečení pro identitu systému vlastního tématu 
Nejprve získejte hlavní ID identity spravované systémem vlastního tématu a přiřaďte identitu k příslušným rolím.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Vytvoření přiřazení role pro centra událostí v různých oborech 
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

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Vytvoření přiřazení role pro Service Bus téma v různých oborech 
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

## <a name="create-event-subscriptions-that-use-an-identity"></a>Vytvoření odběrů událostí, které používají identitu
Jakmile máte vlastní téma Event gridu nebo doménu s identitou spravovanou systémem a přidáte identitu do příslušné role v cíli, budete připraveni vytvořit odběry, které používají identitu. 

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal
Když vytváříte odběr událostí, zobrazí se možnost povolit použití identity přiřazené systémem pro koncový bod v části **Podrobnosti o koncovém bodu** . 

![Povolit identitu při vytváření odběru událostí pro frontu Service Bus](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Na kartě **Další funkce** můžete také povolit použití identity přiřazené systémem, která se má použít k distransakčnímu používání. 

![Povolit systémově přiřazenou identitu pro nedoručené dopisy](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Použití fronty Azure CLI-Service Bus 
V této části se dozvíte, jak pomocí Azure CLI povolit používání identity přiřazené systémem k doručování událostí do fronty Service Bus. Identita musí být členem role **Azure Service Bus data Sender** . Musí být také členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, který se používá pro nedoručené odkládání. 

#### <a name="define-variables"></a>Definování proměnných
Nejdřív zadejte hodnoty pro následující proměnné, které se použijí v příkazu CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 
Tento ukázkový příkaz vytvoří odběr události pro vlastní téma Event Grid s typem koncového bodu nastaveným na **Service Bus Queue**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Vytvoření odběru událostí pomocí spravované identity pro doručení a nedoručená oznámení
Tento ukázkový příkaz vytvoří odběr události pro vlastní téma Event Grid s typem koncového bodu nastaveným na **Service Bus Queue**. Také určuje, že systémově spravovaná identita má být použita pro nedoručené zprávy. 

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

### <a name="use-the-azure-cli---event-hubs"></a>Použití rozhraní příkazového řádku Azure CLI Event Hubs 
V této části se dozvíte, jak pomocí Azure CLI povolit používání identity přiřazené systémem k doručování událostí do centra událostí. Identita musí být členem role **odesilatele dat Azure Event Hubs** . Musí být také členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, který se používá pro nedoručené odkládání. 

#### <a name="define-variables"></a>Definování proměnných
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 
Tento ukázkový příkaz vytvoří odběr události pro vlastní téma Event Grid s typem koncového bodu nastaveným na **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování a doručování zpráv 
Tento ukázkový příkaz vytvoří odběr události pro vlastní téma Event Grid s typem koncového bodu nastaveným na **Event Hubs**. Také určuje, že systémově spravovaná identita má být použita pro nedoručené zprávy. 

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

### <a name="use-the-azure-cli---azure-storage-queue"></a>Použití fronty Azure CLI-Azure Storage 
V této části se dozvíte, jak pomocí Azure CLI povolit používání identity přiřazené systémem k doručování událostí do fronty Azure Storage. Identita musí být členem role **odesílatele zprávy dat fronty úložiště** v účtu úložiště. Musí být také členem role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, který se používá pro nedoručené odkládání.

#### <a name="define-variables"></a>Definování proměnných  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Vytvoření odběru událostí pomocí spravované identity pro doručování a doručování zpráv 

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

## <a name="private-endpoints"></a>Privátní koncové body
V současné době není možné doručovat události pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). To znamená, že pokud máte přísné požadavky na izolaci sítě, u kterých vaše doručováné události nesmí opustit privátní IP adresu, neexistuje žádná podpora. 

Pokud ale vaše požadavky zavolají zabezpečený způsob, jak odesílat události pomocí šifrovaného kanálu a známé identity odesilatele (v tomto případě Event Grid) pomocí veřejného prostoru IP adres, mohli byste události doručovat do Event Hubs, Service Bus nebo Azure Storage pomocí vlastního tématu v Azure Event gridu nebo domény s nakonfigurovanou systémem, který je nakonfigurovaný podle tohoto článku. Pak můžete pomocí privátního odkazu nakonfigurovaného v Azure Functions nebo Webhooku nasazeného ve virtuální síti vyžádat události. Viz Ukázka: [připojení k privátním koncovým bodům pomocí Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

V rámci této konfigurace přechází přenos přes veřejnou IP adresu nebo Internet z Event Grid na Event Hubs, Service Bus nebo Azure Storage, kanál ale může být zašifrovaný a používá se spravovaná identita Event Grid. Pokud nakonfigurujete Azure Functions nebo Webhook nasazený do virtuální sítě tak, aby používal Event Hubs, Service Bus nebo Azure Storage prostřednictvím privátního propojení, Tato část provozu bude v Azure zůstat v provozu.


## <a name="next-steps"></a>Další kroky
Další informace o identitách spravované služby najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 
