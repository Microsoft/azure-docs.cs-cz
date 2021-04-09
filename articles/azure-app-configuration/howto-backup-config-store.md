---
title: Automatické zálohování hodnot klíč-hodnota z úložišť konfigurace aplikací Azure
description: Přečtěte si, jak nastavit automatické zálohování hodnot klíče mezi obchody konfigurace aplikace.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454239"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Zálohování úložišť konfigurací aplikací automaticky

V tomto článku se dozvíte, jak nastavit automatickou zálohu klíčových hodnot z primárního úložiště konfigurace aplikace Azure do sekundárního úložiště. Automatické zálohování používá integraci Azure Event Grid s konfigurací aplikace. 

Po nastavení automatického zálohování bude konfigurace aplikace publikovat události, aby se Azure Event Grid všechny změny provedené v úložišti konfigurace na klíčových hodnotách. Event Grid podporuje celou řadu služeb Azure, ze kterých se uživatelé můžou přihlásit k odběru událostí vysílaných při vytváření, aktualizaci nebo odstraňování klíčových hodnot.

## <a name="overview"></a>Přehled

V tomto článku budete používat službu Azure Queue Storage pro příjem událostí z Event Grid a k zpracování událostí ve frontě v dávkách použít aktivační událost časovače Azure Functions. 

Když se aktivuje funkce na základě událostí, načte nejnovější hodnoty klíčů, které se změnily z primárního úložiště konfigurace aplikace, a odpovídajícím způsobem aktualizuje sekundární úložiště. Tato instalace usnadňuje kombinování více změn, ke kterým dochází v krátké době v rámci jedné operace zálohování, což zabraňuje nadměrným požadavkům na úložiště konfigurací vaší aplikace.

![Diagram znázorňující architekturu zálohování úložiště konfigurace aplikace.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Zřizování prostředků

Motivace za zálohováním úložišť konfigurací aplikací je použití několika úložišť konfigurací v různých oblastech Azure ke zvýšení geografické odolnosti vaší aplikace. V takovém případě by vaše primární a sekundární úložiště měla být v různých oblastech Azure. Všechny ostatní prostředky vytvořené v tomto kurzu můžete zřídit v libovolné oblasti podle vašeho výběru. Důvodem je to, že pokud je primární oblast mimo provoz, nebude už nic nového k zálohování, dokud nebude primární region přístupný znovu.

V tomto kurzu vytvoříte sekundární úložiště v `centralus` oblasti a všech dalších prostředcích v `westus` oblasti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Požadavky 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou vývoje Azure.

- [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento kurz vyžaduje verzi rozhraní příkazového řádku Azure (2.3.1 nebo novější). Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

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
Nahraďte `<primary_appconfig_name>` a `<secondary_appconfig_name>` jedinečnými názvy pro úložiště konfigurací. Každý název úložiště musí být jedinečný, protože se používá jako název DNS.

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

## <a name="create-a-queue"></a>Vytvoření fronty

Vytvořte účet úložiště a frontu pro příjem událostí publikovaných pomocí Event Grid.

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

Následující příkaz vytvoří předplatné Event Grid pro dvě události odeslané do fronty. Typ koncového bodu je nastaven na hodnotu `storagequeue` a koncový bod je nastaven na ID fronty. Nahraďte názvem, který `<event_subscription_name>` si zvolíte pro odběr události.

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

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Vytvoření funkcí pro zpracování událostí z úložiště Queue

### <a name="set-up-with-ready-to-use-functions"></a>Nastavení s využitím připravených funkcí

V tomto článku budete pracovat s funkcemi jazyka C#, které mají následující vlastnosti:
- Zásobník modulu runtime .NET Core 3,1
- Azure Functions runtime verze 3. x
- Funkce aktivované časovačem každých 10 minut

Aby bylo snazší začít zálohovat vaše data, [otestovali a publikovali jsme funkci](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , kterou můžete použít, aniž byste museli provádět změny kódu. Stáhněte si soubory projektu a [publikujte je do vlastní aplikace Azure Functions ze sady Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> V kódu, který jste stáhli, neprovádějte žádné změny proměnných prostředí. V další části vytvoříte požadovaná nastavení aplikace.
>

### <a name="build-your-own-function"></a>Sestavení vlastní funkce

Pokud ukázkový kód uvedený dříve nesplňuje vaše požadavky, můžete také vytvořit vlastní funkci. Aby bylo možné dokončit zálohování, musí být funkce schopna provádět následující úlohy:
- Pravidelně si načtěte obsah vaší fronty, abyste viděli, jestli obsahují oznámení z Event Grid. Podrobnosti o implementaci najdete v [sadě SDK pro frontu úložiště](../storage/queues/storage-quickstart-queues-dotnet.md) .
- Pokud vaše fronta obsahuje [oznámení o událostech z Event Grid](./concept-app-configuration-event.md#event-schema), Extrahujte všechny jedinečné `<key, label>` informace ze zpráv událostí. Kombinací klíčového a popisku je jedinečný identifikátor pro změny klíč-hodnota v primárním úložišti.
- Načte všechna nastavení z primárního úložiště. Aktualizuje jenom ta nastavení v sekundárním úložišti, která mají odpovídající událost ve frontě. Odstraňte všechna nastavení ze sekundárního úložiště, která byla přítomna ve frontě, ale ne v primárním úložišti. [Sadu App Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) můžete použít pro přístup k úložištím konfigurace prostřednictvím kódu programu.
- Odstraní zprávy z fronty, pokud během zpracování nebyly žádné výjimky.
- Implementujte zpracování chyb podle vašich potřeb. Další informace o běžných výjimkách, které byste mohli chtít zpracovat, najdete v předchozím příkladu kódu.

Další informace o vytváření funkcí najdete v tématu [Vytvoření funkce v Azure aktivované časovačem](../azure-functions/functions-create-scheduled-function.md) a [vývoj Azure Functions pomocí sady Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> K výběru plánu časovače na základě toho, jak často provádíte změny v primárním úložišti konfigurace, použijte své nejlepší rozhodnutí. Spuštění funkce příliš často může mít za zpracování požadavky na omezení pro vaše úložiště.
>


## <a name="create-function-app-settings"></a>Vytvořit nastavení aplikace Function App

Pokud používáte funkci, kterou jsme poskytli, budete ve své aplikaci Function App potřebovat následující nastavení aplikace:
- `PrimaryStoreEndpoint`: Koncový bod pro primární úložiště konfigurace aplikace. Příklad: `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Koncový bod pro úložiště konfigurace sekundární aplikace. Příklad: `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Identifikátor URI fronty. Příklad: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Následující příkaz vytvoří požadovaná nastavení aplikace ve vaší aplikaci Function App. Nahraďte `<function_app_name>` názvem vaší aplikace Function App.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Udělení přístupu spravované identitě aplikace Function App

Pomocí následujícího příkazu nebo [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) Přidejte spravovanou identitu přiřazenou systémem aplikace Function App.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Aby bylo možné provést požadované vytváření prostředků a správu rolí, váš účet potřebuje `Owner` oprávnění v příslušném oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si, [jak přiřadit role Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

Pomocí následujících příkazů nebo [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) udělte spravované identitě aplikace Function App přístup k vašim úložištím konfigurace vaší aplikace. Použijte tyto role:
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

Pomocí následujícího příkazu nebo [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) udělte spravované identitě aplikace Function App přístup k vaší frontě. Přiřaďte `Storage Queue Data Contributor` roli ve frontě.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Aktivace události konfigurace aplikace

Chcete-li otestovat, zda vše funguje, můžete vytvořit, aktualizovat nebo odstranit klíčovou hodnotu z primárního úložiště. Tato změna se má v sekundárním úložišti automaticky zobrazit několik sekund po Azure Functions triggeru časovače.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Aktivovali jste událost. Za chvíli Event Grid odešle oznámení události do fronty. *Po dalším naplánovaném spuštění funkce* zobrazte nastavení konfigurace v sekundárním úložišti, abyste viděli, jestli obsahuje aktualizovanou hodnotu klíče z primárního úložiště.

> [!NOTE]
> [Svou funkci můžete aktivovat ručně](../azure-functions/functions-manually-run-non-http.md) během testování a řešení potíží bez čekání na naplánovanou aktivační událost časovače.

Po zajistěte, aby byla funkce zálohování úspěšně spuštěná, vidíte, že se klíč nachází v sekundárním úložišti.

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

- Ujistěte se, že se funkce zálohování aktivovala *po* vytvoření nastavení v primárním úložišti.
- Je možné, že Event Grid nemohl odeslat oznámení události do fronty v čase. Ověřte, jestli fronta stále obsahuje oznámení o událostech z primárního úložiště. V takovém případě znovu spusťte funkci zálohování.
- V [protokolech Azure Functions](../azure-functions/functions-create-scheduled-function.md#test-the-function) najdete případné chyby nebo upozornění.
- Pomocí [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) zajistěte, aby aplikace funkce Azure obsahovala správné hodnoty pro nastavení aplikace, které se Azure Functions pokouší přečíst.
- Můžete také nastavit monitorování a upozorňování na Azure Functions pomocí [Azure Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto konfigurací aplikace a odběrem událostí, neprovádějte čištění prostředků vytvořených v tomto článku. Pokud pokračovat nechcete, pomocí následujícího příkazu odstraňte prostředky vytvořené v tomto článku.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak nastavit automatické zálohování klíčových hodnot, přečtěte si další informace o tom, jak můžete rozšířit geografickou odolnost vaší aplikace:

- [Odolnost a zotavení po havárii](concept-disaster-recovery.md)
