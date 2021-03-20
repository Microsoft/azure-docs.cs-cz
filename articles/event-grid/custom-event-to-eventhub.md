---
title: 'Rychlý Start: odesílání vlastních událostí do Event Hubs-Event Grid, Azure CLI'
description: 'Rychlý Start: pomocí Azure Event Grid a Azure CLI můžete publikovat téma a přihlásit se k odběru této události. Centrum událostí se používá pro koncový bod.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c236025f31e10c00b324e5ff3374bd6504b2b7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324106"
---
# <a name="quickstart-route-custom-events-to-azure-event-hubs-with-azure-cli-and-event-grid"></a>Rychlý Start: směrování vlastních událostí do Azure Event Hubs pomocí Azure CLI a Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. Služba Azure Event Hubs je jednou z podporovaných obslužných rutin události. V tomto článku vytvoříte pomocí Azure CLI vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do centra událostí.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Následující příklad vytvoří skupinu prostředků *gridResourceGroup* v umístění *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. Následující příklad vytvoří vlastní téma ve vaší skupině prostředků. Nahraďte `<your-topic-name>` jedinečným názvem vlastního tématu. Název vlastního tématu musí být jedinečný, protože ho reprezentuje položka DNS.

```azurecli-interactive
topicname=<your-topic-name>
az eventgrid topic create --name $topicname -l westus2 -g gridResourceGroup
```

## <a name="create-event-hub"></a>Vytvoření centra událostí

Před přihlášením k odběru vlastního tématu vytvoříme koncový bod pro zprávy události. Vytvoříte centrum událostí pro shromažďování událostí.

```azurecli-interactive
namespace=<unique-namespace-name>
hubname=demohub

az eventhubs namespace create --name $namespace --resource-group gridResourceGroup
az eventhubs eventhub create --name $hubname --namespace-name $namespace --resource-group gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Přihlášení k odběru vlastního tématu

Přihlásíte se k odběru tématu Event gridu, které informuje Event Grid události, které chcete sledovat. Následující příklad se přihlásí k odběru vlastního tématu, které jste vytvořili, a předá ID prostředku centra událostí pro koncový bod. Koncový bod je ve formátu:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.EventHub/namespaces/<namespace-name>/eventhubs/<hub-name>`

Následující skript načte ID prostředku pro centrum událostí a přihlásí se k odběru tématu Event Gridu. Nastaví typ koncového bodu na `eventhub` a použije ID centra událostí pro daný koncový bod.

```azurecli-interactive
hubid=$(az eventhubs eventhub show --name $hubname --namespace-name $namespace --resource-group gridResourceGroup --query id --output tsv)
topicid=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name subtoeventhub \
  --endpoint-type eventhub \
  --endpoint $hubid
```

Účet, který vytváří odběr události, musí mít přístup k zápisu do služby centra událostí.

## <a name="send-an-event-to-your-custom-topic"></a>Odeslání události do vlastního tématu

Teď aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejprve získáme adresu URL a klíč vlastního tématu.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name $topicname -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicname -g gridResourceGroup --query "key1" --output tsv)
```

V zájmu zjednodušení tohoto článku použijte k odeslání do vlastního tématu ukázková data události. Obvykle by aplikace nebo služba Azure odesílala data události. CURL je nástroj, který odesílá požadavky HTTP. V tomto článku používáme nástroj CURL k odeslání události do vlastního tématu.  Následující příklad odešle tři události do tématu Event Gridu:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Přejděte na portálu do centra událostí a všimněte si, že služba Event Grid odeslala tyto tři události do centra událostí.

![Zobrazit zprávy](./media/custom-event-to-eventhub/show-result.png)

Obvykle vytvoříte aplikaci, která načte události z centra událostí. Pokud chcete vytvořit aplikaci, která získá zprávy z centra událostí, přečtěte si témata:

* [Začínáme s příjmem zpráv pomocí třídy Event Processor Host v .NET Standard](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* [Příjem událostí ze služby Azure Event Hubs pomocí Javy](../event-hubs/event-hubs-java-get-started-send.md)
* [Příjem událostí ze služby Event Hubs pomocí Apache Storm](../event-hubs/event-hubs-storm-getstarted-receive.md)

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
