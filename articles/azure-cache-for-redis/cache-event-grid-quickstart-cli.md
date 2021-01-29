---
title: 'Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure CLI'
description: Použijte Azure Event Grid k přihlášení k odběru mezipaměti Azure pro události Redis, odeslání událostí Webhooku a zpracování událostí ve webové aplikaci.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 55c4c6bb5352d70c9bc688b28c3e0ab6eccd34fd
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055516"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí Azure CLI

Azure Event Grid je služba zpracování událostí pro cloud. V tomto rychlém startu použijete rozhraní příkazového řádku Azure CLI k přihlášení k odběru mezipaměti Azure pro události Redis, aktivaci události a zobrazení výsledků.

Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto rychlého startu ale odešlete události do webové aplikace, která bude shromažďovat a zobrazovat zprávy. Po dokončení kroků popsaných v tomto rychlém startu uvidíte, že se data události odeslala do webové aplikace.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou nejnovější verzi rozhraní příkazového řádku Azure CLI (2.0.70 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

Pokud nepoužíváte Cloud Shell, musíte se nejprve přihlásit pomocí příkazu `az login`.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Event Grid témata se nasazují jako jednotlivé prostředky Azure a musí se zřídit v rámci skupiny prostředků Azure. Skupina prostředků je logická kolekce, do které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). 

Následující příklad vytvoří skupinu prostředků `<resource_group_name>` v umístění *westcentralus*.  Nahraďte `<resource_group_name>` jedinečným názvem vaší skupiny prostředků.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Vytvoření instance mezipaměti

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Nahraďte `<your-site-name>` jedinečným názvem vaší webové aplikace. Název webové aplikace musí být jedinečný, protože je součástí položky DNS.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Měli byste vidět web aktuálně bez zobrazených zpráv.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Přihlášení k odběru instance Azure cache pro instanci Redis

V tomto kroku se přihlásíte k odběru tématu, které informuje Event Grid události, které chcete sledovat, a kam se mají tyto události odesílat. V následujícím příkladu se přihlásí k odběru instance Azure cache pro Redis, kterou jste vytvořili, a předá adresu URL z vaší webové aplikace jako koncový bod pro oznamování událostí. Nahraďte řetězec `<event_subscription_name>` názvem odběru události. Místo `<resource_group_name>` a `<cache_name>` použijte hodnoty názvu skupiny prostředků a názvu účtu úložiště, které jste vytvořili dříve.

Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Aktivace události z mezipaměti Azure pro Redis

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. Pojďme exportovat data uložená v mezipaměti Azure pro instanci Redis. Znovu použijte hodnoty pro a, `{cache_name}` `{resource_group_name}` které jste vytvořili dříve.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Podívejte se na webovou aplikaci, abyste si zobrazili událost, kterou jste právě odeslali.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud máte v úmyslu pokračovat v práci s touto mezipamětí Azure pro instanci Redis a odběr událostí, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste vytvořili v rámci tohoto rychlého startu.

Nahraďte `<resource_group_name>` názvem skupiny prostředků, kterou jste vytvořili výše.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet témata a odběry událostí, přečtěte si další informace o službě Azure cache pro Redis události a o tom, co vám Event Grid můžou:

- [Reakce na události Redis v mezipaměti Azure](cache-event-grid.md)
- [Informace o službě Event Grid](../event-grid/overview.md)
