---
title: Automatické zálohování hodnot klíčů z úložiště konfigurace aplikace Azure
description: Přečtěte si, jak nastavit automatické zálohování hodnot klíčů mezi obchody konfigurace aplikace.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: ef777f235c85c423ea126fb7249f8e6a11492d3a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528544"
---
# <a name="backup-app-configuration-stores-automatically"></a>Automatické zálohování úložišť konfigurací aplikace

V tomto článku se dozvíte, jak nastavit automatické zálohování hodnot klíče z primárního úložiště konfigurace aplikace do sekundárního úložiště. Využívá integraci Azure Event Grid s konfigurací aplikace. Po nastavení bude konfigurace aplikace publikovat události, aby se Event Grid všechny změny provedené v úložišti konfigurace na klíčových hodnotách. Event Grid podporuje celou řadu služeb Azure, ze kterých se uživatelé můžou přihlásit k odběru událostí vysílaných při vytváření, aktualizaci nebo odstraňování klíčových hodnot.

## <a name="overview"></a>Přehled

V tomto kurzu použijete Azure Storageovou frontu pro příjem událostí z Event Grid a k zpracování událostí ve frontě úložiště v dávkách použijete Trigger časovače Azure Functions. Když se aktivuje na základě událostí, funkce načte nejnovější hodnoty klíčů, které se změnily z primárního úložiště konfigurace aplikace, a odpovídajícím způsobem aktualizuje sekundární úložiště. Tato instalace pomáhá zkombinovat více změn, ke kterým dochází v krátké době v rámci jedné operace zálohování, a vyhněte se nadměrnému počtu požadavků na úložiště konfigurací vaší aplikace.

![Architektura zálohování úložiště konfigurace aplikace](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Zřizování prostředků

Motivace za zálohováním úložišť konfigurací aplikací je použití několika úložišť konfigurací v různých oblastech Azure ke zvýšení geografické odolnosti vaší aplikace. V takovém případě by vaše primární a sekundární úložiště měla být v různých oblastech Azure. Všechny ostatní prostředky vytvořené v tomto kurzu můžete zřídit v libovolné oblasti podle vašeho výběru. Důvodem je to, že pokud je primární oblast mimo provoz, nebude nic nového k zálohování, dokud nebude primární oblast opět přístupná.

V tomto kurzu vytvoříte sekundární úložiště v `centralus` oblasti a všech dalších prostředcích v `westus` oblasti.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/). Volitelně můžete použít Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou vývoje Azure.
- Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).
- Nejnovější verze rozhraní příkazového řádku Azure CLI (2.3.1 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI, musíte se nejdřív přihlásit pomocí `az login` . Volitelně můžete použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce, ve které se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group).

Následující příklad vytvoří skupinu prostředků s názvem `<resource_group_name>` v umístění `westus`.  Nahraďte `<resource_group_name>` jedinečným názvem vaší skupiny prostředků.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Vytvoření úložišť konfigurací aplikací

Vytvořte si úložiště konfigurací primární a sekundární aplikace v různých oblastech.
Nahraďte  `<primary_appconfig_name>` a `<secondary_appconfig_name>` jedinečnými názvy pro úložiště konfigurací. Název úložiště musí být jedinečný, protože se používá jako název DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Vytvořit frontu Azure Storage

Vytvořte účet úložiště a frontu úložiště pro příjem událostí publikovaných pomocí Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Přihlášení k odběru událostí úložiště konfigurace aplikace

Přihlásíte se k odběru těchto dvou událostí z úložiště konfigurace primární aplikace:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Následující příkaz vytvoří předplatné Event Grid pro dvě události odeslané do fronty úložiště, kde je nastaven typ koncového bodu `storagequeue` a koncový bod je nastaven na ID fronty. Nahraďte názvem, který `<event_subscription_name>` si zvolíte pro odběr události.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Vytvořit Azure Functions pro zpracování událostí z fronty úložiště

### <a name="setup-with-ready-to-use-azure-functions"></a>Nastavení pomocí Azure Functions připraveného k použití

V tomto kurzu budete pracovat s C# Azure Functions s následujícími vlastnostmi:
- Zásobník modulu runtime .NET Core 3,1
- Azure Functions runtime verze 3. x
- Funkce aktivované časovačem každých 10 minut

Abychom vám usnadnili zahájit zálohování dat, otestovali a publikovali [Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , které můžete použít, aniž byste museli provádět změny kódu. Stáhněte si soubory projektu a [publikujte ji na vlastní Function App Azure ze sady Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> V kódu, který jste stáhli, neprovádějte žádné změny proměnných prostředí. V další části budete vytvářet požadovaná nastavení aplikace.
>

### <a name="build-your-own-azure-functions"></a>Sestavení vlastního Azure Functions

Pokud vzorový kód uvedený výše nesplňuje vaše požadavky, můžete také vytvořit vlastní Azure Functions. Aby bylo možné dokončit zálohování, musí být funkce schopna provádět následující úlohy:
- Pravidelně si načtěte obsah vaší fronty úložiště, abyste viděli, jestli obsahuje oznámení z Event Grid. Podrobnosti o implementaci najdete v [sadě SDK pro frontu úložiště](/azure/storage/queues/storage-quickstart-queues-dotnet) .
- Pokud vaše fronta úložiště obsahuje [oznámení o událostech z Event Grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), extrahujte veškerý jedinečný <klíč, popisek> ze zpráv událostí. Kombinace klíčového a popisku je jedinečný identifikátor pro změny klíč-hodnota v primárním úložišti.
- Načte všechna nastavení z primárního úložiště. Aktualizuje jenom ta nastavení v sekundárním úložišti, která mají odpovídající událost ve frontě úložiště. Odstraní všechna nastavení ze sekundárního úložiště, která byla přítomna ve frontě úložiště, ale ne v primárním úložišti. Můžete využít [sadu App Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) pro přístup k úložištím konfigurace prostřednictvím kódu programu.
- Odstraní zprávy z fronty úložiště, pokud během zpracování nebyly žádné výjimky.
- Ujistěte se, že implementujete zpracování chyb podle vašich potřeb. Můžete se podívat na výše uvedený příklad kódu a zobrazit tak některé běžné výjimky, které můžete chtít zpracovat.

Další informace o vytváření Azure Functions najdete v tématu: [Vytvoření funkce v Azure aktivované časovačem](/azure/azure-functions/functions-create-scheduled-function) a [vývoj Azure Functions pomocí sady Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Podle toho, jak často provedete změny v primárním úložišti konfigurace, můžete zvolit plán časovače. Mějte na paměti, že při spuštění funkce příliš často může dokončit požadavky na úložiště.
>


## <a name="create-azure-function-app-settings"></a>Vytvoření nastavení Azure Function App

Pokud používáte Azure Functions, které jsme poskytli, budete v Azure Function App potřebovat následující nastavení aplikace:
- `PrimaryStoreEndpoint`: Koncový bod pro primární úložiště konfigurace aplikace. Například `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Koncový bod pro úložiště konfigurace sekundární aplikace. Například `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Identifikátor URI fronty úložiště. Například `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Následující příkaz vytvoří požadovaná nastavení aplikace ve službě Azure Function App. Nahraďte `<function_app_name>` názvem vaší služby Azure Function App.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Udělení přístupu ke spravované identitě Azure Function App

Použijte následující příkaz nebo [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) k přidání spravované identity přiřazené systémem pro Azure Function App.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Aby bylo možné provést požadované vytváření prostředků a správu rolí, váš účet potřebuje `'Owner'` oprávnění v příslušném oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si, [jak přidat nebo odebrat přiřazení rolí Azure pomocí Azure Portal](/azure/role-based-access-control/role-assignments-portal).

Pomocí následujících příkazů nebo [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) udělte spravované identitě Azure Function App přístup k vašim úložištím konfigurací vaší aplikace.
- Přiřaďte `App Configuration Data Reader` roli v primárním úložišti konfigurace aplikace.
- Přiřaďte `App Configuration Data Owner` roli v úložišti konfigurace sekundární aplikace.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Pomocí následujícího příkazu nebo [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) udělte spravované identitě Azure Function App přístup k vaší frontě úložiště. 
- Přiřaďte `Storage Queue Data Contributor` roli ve frontě úložiště.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Aktivace události konfigurace aplikace

Chcete-li otestovat, zda vše funguje, můžete vytvořit, aktualizovat nebo odstranit hodnotu klíče z primárního úložiště. Tato změna by se měla v sekundárním úložišti automaticky zobrazit několik sekund po Azure Functions spustí časovač.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Aktivovali jste událost a za chvíli Event Grid odešlete oznámení události do fronty Azure Storage. ***Po dalším naplánovaném spuštění Azure Functions***můžete zobrazit nastavení konfigurace v sekundárním úložišti, abyste viděli, jestli obsahuje aktualizovanou hodnotu klíče z primárního úložiště.

> [!NOTE]
> Během testování a odstraňování potíží můžete [aktivovat Azure Functions ručně](/azure/azure-functions/functions-manually-run-non-http) , aniž byste čekali na naplánovanou aktivační událost časovače.

Po ujištění, že se funkce zálohování úspěšně spustila, uvidíte, že se klíč nachází v sekundárním úložišti.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud se v sekundárním úložišti nezobrazuje nové nastavení:

- Ujistěte se, že se funkce zálohování aktivovala ***po*** vytvoření nastavení v primárním úložišti.
- Je možné, že Event Grid nedokázal odeslat oznámení o události do fronty úložiště v čase. Ověřte, jestli vaše fronta úložiště stále obsahuje oznámení o událostech z primárního úložiště, a pokud to funguje, spusťte znovu funkci zálohování.
- V [protokolech Azure Functions](/azure/azure-functions/functions-create-scheduled-function#test-the-function) najdete případné chyby nebo upozornění.
- Pomocí [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) zajistěte, aby Azure Function App obsahoval správné hodnoty pro nastavení aplikace, které se Azure Functions pokouší přečíst.
- Můžete také nastavit monitorování a upozorňování na Azure Functions pomocí [Azure Application Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto konfigurací aplikace a odběrem událostí, neprovádějte čištění prostředků vytvořených v tomto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak nastavit automatické zálohování hodnot klíčů, přečtěte si další informace o tom, jak můžete rozšířit geografickou odolnost vaší aplikace:

- [Odolnost a zotavení po havárii](concept-disaster-recovery.md)
