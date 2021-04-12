---
title: 'Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí PowerShellu'
description: Použijte Azure Event Grid k přihlášení k odběru mezipaměti Azure pro události Redis, odeslání událostí Webhooku a zpracování událostí ve webové aplikaci.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508159"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Rychlý Start: směrování mezipaměti Azure pro události Redis do webového koncového bodu pomocí PowerShellu

Azure Event Grid je služba zpracování událostí pro cloud. V tomto rychlém startu použijete Azure PowerShell k přihlášení k odběru mezipaměti Azure pro události Redis, aktivaci události a zobrazení výsledků. 

Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto rychlého startu ale odešlete události do webové aplikace, která bude shromažďovat a zobrazovat zprávy. Po dokončení kroků popsaných v tomto rychlém startu uvidíte, že se data události odeslala do webové aplikace.

## <a name="setup"></a>Nastavení

Tento rychlý Start vyžaduje, abyste spustili nejnovější verzi Azure PowerShell. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkazu a podle pokynů na obrazovce proveďte ověření.

```powershell
Connect-AzAccount
```

Tento příklad používá **westus2** a ukládá výběr do proměnné pro použití v celém.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Event Grid témata se nasazují jako jednotlivé prostředky Azure a musí se zřídit v rámci skupiny prostředků Azure. Skupina prostředků je logická kolekce, do které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

Následující příklad vytvoří skupinu prostředků **gridResourceGroup** v umístění **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Vytvoření mezipaměti Azure pro instanci Redis 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Další informace o vytvoření instance mezipaměti v PowerShellu najdete v [referenčních](/powershell/module/az.rediscache/new-azrediscache)informacích o Azure PowerShell. 

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Nahraďte `<your-site-name>` jedinečným názvem vaší webové aplikace. Název webové aplikace musí být jedinečný, protože je součástí položky DNS.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Měli byste vidět web aktuálně bez zobrazených zpráv.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Prázdný web nástroje Event Grid Viewer":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Přihlášení k odběru události Azure cache pro Redis

V tomto kroku se přihlásíte k odběru tématu, které informuje Event Grid události, které chcete sledovat. V následujícím příkladu se přihlásí k odběru instance mezipaměti, kterou jste vytvořili, a předá adresu URL z vaší webové aplikace jako koncový bod pro oznamování událostí. Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Aktivace události z mezipaměti Azure pro Redis

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Další informace o importování v PowerShellu najdete v [referenčních](/powershell/module/az.rediscache/import-azrediscache)informacích o Azure PowerShell. 

Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Podívejte se na webovou aplikaci, abyste si zobrazili událost, kterou jste právě odeslali.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud máte v úmyslu pokračovat v práci s touto mezipamětí Azure pro instanci Redis a odběr událostí, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste vytvořili v rámci tohoto rychlého startu.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet témata a odběry událostí, přečtěte si další informace o službě Azure cache pro Redis události a o tom, co vám Event Grid můžou:

- [Reakce na události Redis v mezipaměti Azure](cache-event-grid.md)
- [Informace o službě Event Grid](../event-grid/overview.md)