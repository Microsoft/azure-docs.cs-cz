---
title: 'Kurz: odeslání vlastních událostí do hybridního připojení – Event Grid'
description: 'Kurz: k publikování tématu použijte Azure Event Grid a Azure CLI a přihlaste se k odběru této události. Hybridní připojení se používá pro koncový bod.'
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: c709d77827551860cc917c3c84c5a849d9fdc512
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566822"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Kurz: směrování vlastních událostí Azure Relay Hybrid Connections pomocí Azure CLI a Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. Azure Relay Hybrid Connections je jednou z podporovaných obslužných rutin událostí. Hybridní připojení použijete jako obslužnou rutinu události, když je potřeba zpracovat události z aplikací, které nemají veřejný koncový bod. Tyto aplikace se můžou nacházet ve vaší podnikové síti. V tomto článku vytvoříte pomocí Azure CLI vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do hybridního připojení.

## <a name="prerequisites"></a>Požadavky

- Tento článek předpokládá, že už máte hybridní připojení a aplikaci naslouchacího procesu. Pokud chcete začít používat hybridní připojení, přečtěte si téma [Začínáme s Relay Hybrid Connections – .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) nebo [Začínáme s Relay Hybrid Connections – Node](../azure-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.56 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Následující příklad vytvoří skupinu prostředků *gridResourceGroup* v umístění *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. Následující příklad vytvoří vlastní téma ve vaší skupině prostředků. Nahraďte `<topic_name>` jedinečným názvem vlastního tématu. Název tématu Event Gridu musí být jedinečný, protože ho reprezentuje položka DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Přihlášení k odběru vlastního tématu

Přihlásíte se k odběru tématu Event gridu, které informuje Event Grid události, které chcete sledovat. Následující příklad přihlásí vlastní téma, které jste vytvořili, a předá ID prostředku hybridního připojení pro koncový bod. ID hybridního připojení je ve formátu:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Následující skript načte ID prostředku oboru názvů přenosu. Vytvoří ID pro hybridní připojení a přihlásí se k odběru tématu Event Gridu. Skript nastaví typ koncového bodu na `hybridconnection` a použije ID hybridního připojení pro daný koncový bod.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Všimněte si, že je nastavené [datum vypršení platnosti](concepts.md#event-subscription-expiration) odběru.

## <a name="create-application-to-process-events"></a>Vytvoření aplikace pro zpracování událostí

Potřebujete aplikaci, která dokáže načítat události z hybridního připojení. Tuto operaci provádí [ukázka příjemce hybridního připojení Microsoft Azure Event Grid pro jazyk C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination). Už jste dokončili požadované kroky.

1. Ujistěte se, že máte Visual Studio 2019 nebo novější.

1. Naklonujte si úložiště na místní počítač.

1. Načtěte projekt HybridConnectionConsumer v sadě Visual Studio.

1. V souboru Program.cs nahraďte `<relayConnectionString>` a `<hybridConnectionName>` připojovacím řetězcem předávání a názvem hybridního připojení, které jste vytvořili.

1. V sadě Visual Studio zkompilujte a spusťte aplikaci.

## <a name="send-an-event-to-your-topic"></a>Odeslání události do tématu

Teď aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Tento článek ukazuje, jak aktivovat událost pomocí Azure CLI. Alternativně můžete použít [aplikaci vydavatele Event Grid](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher).

Nejprve získáme adresu URL a klíč vlastního tématu. Znovu místo položky `<topic_name>` použijte název vlastního tématu.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

V zájmu zjednodušení tohoto článku použijte k odeslání do vlastního tématu ukázková data události. Obvykle by aplikace nebo služba Azure odesílala data události. CURL je nástroj, který odesílá požadavky HTTP. V tomto článku používáme nástroj CURL k odeslání události do vlastního tématu.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Aplikace naslouchacího procesu by měla dostat zprávu o událostech.

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