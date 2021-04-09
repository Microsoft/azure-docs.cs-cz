---
title: 'Rychlý Start: odesílání vlastních událostí do fronty úložiště – Event Grid, Azure CLI'
description: 'Rychlý Start: pomocí Azure Event Grid a Azure CLI můžete publikovat téma a přihlásit se k odběru této události. Pro koncový bod se používá fronta úložiště.'
ms.date: 02/02/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00808e7eca13824833673ef820d39b70bf618dd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493256"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Rychlý Start: směrování vlastních událostí do Azure Queue Storage pomocí rozhraní příkazového řádku Azure a Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. Azure Queue Storage je jednou z podporovaných obslužných rutin události. V tomto článku vytvoříte pomocí Azure CLI vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do Queue Storage.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.56 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

- Pokud používáte Azure PowerShell v místním počítači místo použití Cloud Shell v Azure Portal, ujistěte se, že máte Azure PowerShell verze 1.1.0 nebo novější. Stáhněte si nejnovější verzi Azure PowerShell na počítači s Windows ze složky [Azure downloads – nástroje příkazového řádku](https://azure.microsoft.com/downloads/). 

Tento článek obsahuje příkazy pro použití rozhraní příkazového řádku Azure CLI. 

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

Přihlásíte se k odběru vlastního tématu, které informuje Event Grid události, které chcete sledovat. Následující příklad přihlásí k odběru vlastního tématu, které jste vytvořili, a předá ID prostředku úložiště fronty pro koncový bod. Pomocí Azure CLI předáte ID Queue Storage jako koncový bod. Koncový bod je ve formátu:

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

> [!NOTE]
> Pokud pro Azure Functions pro frontu, která přijímá zprávy od Event Grid, používáte [Trigger služby Azure Queue Storage](../azure-functions/functions-bindings-storage-queue-trigger.md) , může se při provádění funkce zobrazit následující chybová zpráva: `The input is not a valid Base-64 string as it contains a non-base 64 character, more than two padding characters, or an illegal character among the padding characters.`
> 
> Důvodem je to, že když použijete [Trigger úložiště front Azure](../azure-functions/functions-bindings-storage-queue-trigger.md), Azure Functions očekávat **řetězec kódovaný v kódování base64**, ale Event Grid posílá zprávy do fronty úložiště v textovém formátu v prostém textu. V současné době není možné nakonfigurovat Trigger fronty pro Azure Functions pro příjem prostého textu. 


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. Jinak pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
