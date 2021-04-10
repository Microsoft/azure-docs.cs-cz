---
title: Doručení události, identita spravované služby a privátní odkaz
description: Tento článek popisuje, jak povolit identitu spravované služby pro téma Azure Event Grid. Použijte ji k přeposílání událostí do podporovaných cílů.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625267"
---
# <a name="event-delivery-with-a-managed-identity"></a>Doručování událostí se spravovanou identitou
Tento článek popisuje, jak používat [identitu spravované služby](../active-directory/managed-identities-azure-resources/overview.md) pro systémové téma Azure Event Grid, vlastní téma nebo doménu. Slouží k přeposílání událostí do podporovaných cílů, jako jsou Service Bus fronty a témata, centra událostí a účty úložiště.



## <a name="prerequisites"></a>Požadavky
1. Přiřazení identity přiřazené systému k systémovému tématu, vlastnímu tématu nebo doméně. 
    - Vlastní témata a domény najdete v tématu [Povolení spravované identity pro vlastní témata a domény](enable-identity-custom-topics-domains.md). 
    - Systémová témata najdete v tématu [Povolení spravované identity pro systémová témata](enable-identity-system-topics.md) .
1. Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue). Podrobný postup najdete v tématu [Přidání identity do rolí Azure v umístěních](add-identity-roles.md) .

    > [!NOTE]
    > V současné době není možné doručovat události pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). Další informace najdete v části [soukromé koncové body](#private-endpoints) na konci tohoto článku. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Vytvoření odběrů událostí, které používají identitu
Až budete mít vlastní téma Event Grid nebo systémové téma nebo doménu s identitou spravovanou systémem a přidáte identitu do příslušné role v cíli, budete připraveni vytvořit odběry, které používají tuto identitu. 

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
Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).
