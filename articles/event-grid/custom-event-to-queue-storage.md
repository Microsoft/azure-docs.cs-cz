---
title: Odesílání vlastních události do fronty úložiště – Event Grid, Azure CLI
description: Pomocí Azure Event Gridu a Azure CLI můžete publikovat téma a přihlásit se k odběru příslušné události. Pro koncový bod se používá fronta úložiště.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 01/07/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 9d1d9caccac0fcdeb4e1d2dae40e677f36de40ea
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728041"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Rychlý start: Směrování vlastních událostí do Azure Queue Storage pomocí Azure CLI a Event Gridu

Azure Event Grid je služba zpracování událostí pro cloud. Azure Queue Storage je jednou z podporovaných obslužných rutin události. V tomto článku vytvoříte pomocí Azure CLI vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do Queue Storage.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure v místním počítači namísto použití Cloud Shell na webu Azure Portal, ujistěte se, že máte následující verze rozhraní příkazového řádku Azure a Azure Powershellu. 

- Azure CLI verze 2.0.56 nebo vyšší. Pokyny k instalaci nejnovější verze Azure CLI najdete v tématu [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). 
- Azure PowerShell verze 1.1.0 nebo vyšší. Stáhněte si nejnovější verzi Azure Powershellu na svém počítači s Windows z [stažení Azure – nástroje pro příkazový řádek](https://azure.microsoft.com/downloads/). 

Tento článek obsahuje příkazy pro pomocí Azure CLI. 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Následující příklad vytvoří skupinu prostředků *gridResourceGroup* v umístění *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. Následující příklad vytvoří vlastní téma ve vaší skupině prostředků. Nahraďte `<topic_name>` jedinečným názvem vlastního tématu. Název tématu Event Gridu musí být jedinečný, protože ho reprezentuje položka DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Vytvoření Queue Storage

Před přihlášením k odběru vlastního tématu vytvoříme koncový bod pro zprávy události. Pro shromažďování událostí můžete vytvořit Queue Storage.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Přihlášení k odběru vlastního tématu

K odběru vlastního tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. Následující příklad se přihlásí k odběru vlastního tématu, které jste vytvořili, a předá ID prostředku služby Queue Storage pro koncový bod. Pomocí Azure CLI předáte ID Queue Storage jako koncový bod. Koncový bod je ve formátu:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

Následující skript načte ID prostředku účtu úložiště pro danou frontu. Vytvoří ID pro Queue Storage a přihlásí se k odběru tématu Event Gridu. Nastaví typ koncového bodu na `storagequeue` a použije ID fronty pro daný koncový bod.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

Účet, který vytváří odběr události, musí mít přístup k zápisu do služby Queue Storage. Všimněte si, že je nastavené [datum vypršení platnosti](concepts.md#event-subscription-expiration) odběru.

Pokud k vytvoření odběru používáte rozhraní REST API, předáte ID účtu úložiště a název fronty jako samostatný parametr.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Odeslání události do vlastního tématu

Teď aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejprve získáme adresu URL a klíč vlastního tématu. Znovu místo položky `<topic_name>` použijte název vlastního tématu.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

V zájmu zjednodušení tohoto článku použijte k odeslání do vlastního tématu ukázková data události. Obvykle by aplikace nebo služba Azure odesílala data události. CURL je nástroj, který odesílá požadavky HTTP. V tomto článku používáme nástroj CURL k odeslání události do vlastního tématu.  Následující příklad odešle tři události do tématu Event Gridu:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Přejděte na portálu na Queue Storage a všimněte si, že služba Event Grid odeslala tyto tři události do fronty.

![Zobrazit zprávy](./media/custom-event-to-queue-storage/messages.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Jinak pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Další postup

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
