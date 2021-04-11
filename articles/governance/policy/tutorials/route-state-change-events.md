---
title: 'Kurz: události změny stavu zásad směrování pro Event Grid s využitím Azure CLI'
description: V tomto kurzu nakonfigurujete Event Grid k naslouchání událostem změny stavu zásad a volání Webhooku.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735534"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Kurz: události změny stavu zásad směrování pro Event Grid s využitím Azure CLI

V tomto článku se dozvíte, jak nastavit Azure Policy odběry událostí pro odesílání událostí změny stavu zásad do webového koncového bodu. Azure Policy uživatelé se můžou přihlásit k odběru událostí vysílaných při změně stavu zásad u prostředků. Tyto události mohou aktivovat Webhooky, [Azure Functions](../../../azure-functions/index.yml), [Azure Storage fronty](../../../storage/queues/index.yml)nebo jakékoli jiné obslužné rutiny událostí, které [Azure Event Grid](../../../event-grid/index.yml)podporuje. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto kurzu však odešlete události do webové aplikace, která bude zprávy shromažďovat a zobrazovat.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Tento rychlý Start vyžaduje, abyste spustili Azure CLI verze 2.0.76 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

- I v případě, že jste již dříve použili Azure Policy nebo Event Grid, znovu zaregistrujte příslušné poskytovatele prostředků:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Témata služby Event Grid jsou prostředky Azure a musí být umístěné ve skupině prostředků Azure. Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). 

Následující příklad vytvoří skupinu prostředků s názvem `<resource_group_name>` v umístění _westus_ . Nahraďte `<resource_group_name>` jedinečným názvem vaší skupiny prostředků.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Vytvoření Event Grid systémových témat

Teď, když máme skupinu prostředků, vytvoříme [systémové téma](../../../event-grid/system-topics.md). Systémové téma v Event Grid představuje jednu nebo více událostí publikovaných službami Azure, jako jsou Azure Policy a Azure Event Hubs. Toto systémové téma používá `Microsoft.PolicyInsights.PolicyStates` typ tématu pro Azure Policy změny stavu. `<SubscriptionID>`V parametru **Scope** nahraďte ID vašeho předplatného a `<resource_group_name>` parametr **skupiny prostředků** s dříve vytvořenou skupinou prostředků.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru tématu vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

Nahraďte `<your-site-name>` jedinečným názvem vaší webové aplikace. Název webové aplikace musí být jedinečný, protože je součástí položky DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

Měli byste vidět web aktuálně bez zobrazených zpráv.

## <a name="subscribe-to-the-system-topic"></a>Přihlášení k odběru systémového tématu

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat. V následujícím příkladu se přihlásí k odběru systémového tématu, které jste vytvořili, a předá adresu URL z vaší webové aplikace jako koncový bod pro příjem oznámení události. Nahraďte řetězec `<event_subscription_name>` názvem odběru události. Místo `<resource_group_name>` a `<your-site-name>` použijte hodnoty názvu skupiny prostředků a názvu účtu úložiště, které jste vytvořili dříve.

Koncový bod pro webovou aplikaci musí obsahovat příponu `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Snímek obrazovky Event Grid události ověření předplatného v předem připravené webové aplikaci":::

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici **vyžadovat značku na definici skupin prostředků** . Tato definice zásady identifikuje skupiny prostředků, ve kterých chybí značka nakonfigurovaná během přiřazování zásad.

Spuštěním následujícího příkazu vytvořte přiřazení zásady v oboru pro skupinu prostředků, kterou jste vytvořili pro uložení tématu Event gridu:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Předchozí příkaz používá následující informace:

- **Name** – skutečný název přiřazení. V tomto příkladu byly použity _události requiredtags-_ .
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě budete pomocí _RG vyžadovat označení_.
- **Scope** – Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Může sahat od předplatného až po skupiny prostředků. Nezapomeňte nahradit &lt;scope&gt; názvem vaší skupiny prostředků. Formát pro rozsah skupiny prostředků je `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Policy** – ID definice zásady, kterou používáte k vytvoření tohoto přiřazení. V tomto případě se jedná o ID definice zásady, která _vyžaduje značku pro skupiny prostředků_. ID definice zásady získáte spuštěním příkazu `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`.

Po vytvoření přiřazení zásady počkejte, než se ve webové aplikaci zobrazí oznámení o události **Microsoft. PolicyInsights. PolicyStateCreated** . Skupina prostředků, kterou jsme vytvořili, zobrazuje `data.complianceState` hodnotu _nekompatibilní_ se začátkem.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Snímek obrazovky události vytvoření stavu zásad předplatného Event Grid pro skupinu prostředků v předem připravené webové aplikaci":::

> [!NOTE]
> Pokud skupina prostředků zdědí další přiřazení zásad z hierarchie předplatného nebo skupiny pro správu, zobrazí se také události pro každou z nich. Potvrďte tuto událost pro přiřazení v tomto kurzu vyhodnocením `data.policyDefinitionId` Vlastnosti.

## <a name="trigger-a-change-on-the-resource-group"></a>Aktivace změny skupiny prostředků

Aby vyhovovala skupina prostředků, je nutné zadat značku s názvem **EventTest** . Přidejte značku do skupiny prostředků s následujícím příkazem nahraďte `<SubscriptionID>` ID předplatného a `<ResourceGroup>` názvem skupiny prostředků:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Po přidání požadované značky do skupiny prostředků počkejte, než se ve webové aplikaci zobrazí oznámení o události **Microsoft. PolicyInsights. PolicyStateChanged** . Rozbalte událost a `data.complianceState` hodnota nyní zobrazuje _vyhovující_.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s touto webovou aplikací a Azure Policy odběrem událostí, neprovádějte čištění prostředků vytvořených v tomto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste vytvořili v tomto článku.

Nahraďte `<resource_group_name>` názvem skupiny prostředků, kterou jste vytvořili výše.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Další kroky

Teď, když víte, jak vytvářet témata a odběry událostí pro Azure Policy, přečtěte si další informace o událostech změny stavu zásad a o tom, co Event Grid vám může pomáhat:

- [Reakce na události změny stavu Azure Policy](../concepts/event-overview.md)
- [Podrobnosti Azure Policy schématu pro Event Grid](../../../event-grid/event-schema-policy.md)
- [Informace o službě Event Grid](../../../event-grid/overview.md)