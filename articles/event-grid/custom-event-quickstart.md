---
title: Vlastní události pro Azure Event Grid promocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Pomocí Azure Event Gridu a Azure CLI můžete publikovat téma a přihlásit se k odběru příslušné události.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: d68df064614c262bd9755be0688841fdb64af762
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301797"
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Vytvoření a směrování vlastních událostí pomocí Azure CLI a Event Gridu

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí Azure CLI vytvoříte vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Obvykle odesíláte události do koncového bodu, který na událost reaguje například webhookem nebo funkcí Azure Functions. Pro zjednodušení tohoto článku však budete události odesílat na adresu URL, která jenom shromažďuje zprávy. Tuto adresu URL vytvoříte pomocí nástroje třetí strany z webu [Hookbin](https://hookbin.com/).

>[!NOTE]
>**Hookbin** není určený pro použití s vysokou propustností. Použití tohoto nástroje je čistě demonstrativní. Pokud najednou nabídnete více než jednu událost, možná se v nástroji nezobrazí všechny.

Až budete hotovi, uvidíte, že se data události odeslala do koncového bodu.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, pro účely tohoto článku musíte používat nejnovější verzi Azure CLI (2.0.24 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

Pokud nepoužíváte Cloud Shell, musíte se nejprve přihlásit pomocí příkazu `az login`.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). 

Následující příklad vytvoří skupinu prostředků *gridResourceGroup* v umístění *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. Následující příklad vytvoří vlastní téma ve vaší skupině prostředků. Nahraďte `<topic_name>` jedinečným názvem vašeho tématu. Název tématu musí být jedinečný, protože je reprezentován položkou DNS.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Místo psaní kódu, který by na událost reagoval, vytvoříme koncový bod, který bude shromažďovat zprávy, abyste je mohli zobrazit. Hookbin je nástroj třetí strany, který umožňuje vytvořit koncový bod a zobrazit požadavky, které se do něj odesílají. Přejděte na web [Hookbin](https://hookbin.com/) a klikněte na **Create New Endpoint** (Vytvořit nový koncový bod).  Zkopírujte adresu URL přihrádky, protože ji budete potřebovat při přihlašování k odběru vlastního tématu.

## <a name="subscribe-to-a-topic"></a>Přihlášení k odběru tématu

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat. Následující příklad se přihlásí k odběru tématu, které jste vytvořili, a předá adresu URL z webu Hookbin jako koncový bod pro oznámení události. Nahraďte `<event_subscription_name>` jedinečným názvem vašeho odběru a `<endpoint_URL>` hodnotou adresy URL z nástroje RequestBin z předchozí části. Díky zadání koncového bodu při přihlašování k odběru bude služba Event Grid zpracovávat směrování událostí do tohoto koncového bodu. Místo `<topic_name>` použijte hodnotu názvu tématu, který jste vytvořili dříve. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Odeslání události do tématu

Teď aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Nejprve získáme adresu URL a klíč vlastního tématu. Znovu místo `<topic_name>` použijte název vašeho tématu.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Pro zjednodušení tohoto článku použijte k odeslání do tématu ukázková data události. Obvykle by aplikace nebo služba Azure odesílala data události. Následující příklad získá data události:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Pokud chcete zobrazit úplnou událost, použijte `echo "$body"`. Element JSON `data` je datová část vaší události. V tomto poli může být libovolný JSON ve správném formátu. Můžete také použít pole subject (předmět) pro pokročilé směrování a filtrování.

CURL je nástroj, který odesílá požadavky HTTP. V tomto článku používáme nástroj CURL k odeslání události do tématu. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte na adresu URL koncového bodu, kterou jste vytvořili dříve. Nebo v otevřeném prohlížeči klikněte na tlačítko pro obnovení. Zobrazí se událost, kterou jste právě odeslali. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

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
