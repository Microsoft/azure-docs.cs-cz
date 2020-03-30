---
title: Zásady nedoručených a opakování – Azure Event Grid
description: Popisuje, jak přizpůsobit možnosti doručení událostí pro Event Grid. Nastavte cíl nedoručených písmen a určete, jak dlouho se má doručení opakovat.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794039"
---
# <a name="dead-letter-and-retry-policies"></a>Zásady nedoručených adres a opakování

Při vytváření odběru událostí můžete přizpůsobit nastavení pro doručování událostí. Tento článek ukazuje, jak nastavit umístění nedoručených písmen a přizpůsobit nastavení opakování. Informace o těchto funkcích naleznete v [tématu Message Message Message Event Grid and retry](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Nastavení umístění nedoručených písmen

Chcete-li nastavit umístění nedoručených písmen, potřebujete účet úložiště pro držení událostí, které nelze doručit do koncového bodu. Příklady získat ID prostředku existujícího účtu úložiště. Vytvářejí odběr událostí, který používá kontejner v tomto účtu úložiště pro koncový bod nedoručených písmen.

> [!NOTE]
> - Vytvořte účet úložiště a kontejner objektů blob v úložišti před spuštěním příkazů v tomto článku.
> - Služba Event Grid vytváří objekty BLOB v tomto kontejneru. Názvy objektů BLOB budou mít název předplatného event gridu se všemi velkými písmeny. Například pokud název předplatného je My-Blob-Subscription, názvy objektů BLOB nedoručených písmen bude mít MY-BLOB-SUBSCRIPTION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-1111-1111111111111111.json). Toto chování je chránit před rozdíly v případu zpracování mezi službami Azure.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Chcete-li vypnout nedoručené písmo, znovu spusťte příkaz k `deadletter-endpoint`vytvoření odběru událostí, ale nezadejte hodnotu pro . Odběr události není nutné odstraňovat.

> [!NOTE]
> Pokud používáte Azure CLI na místním počítači, použijte Azure CLI verze 2.0.56 nebo vyšší. Pokyny k instalaci nejnovější verze příkazového příkazového příkazu k Azure najdete [v tématu Instalace příkazového příkazového příkazu k azure](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Chcete-li vypnout nedoručené písmo, znovu spusťte příkaz k `DeadLetterEndpoint`vytvoření odběru událostí, ale nezadejte hodnotu pro . Odběr události není nutné odstraňovat.

> [!NOTE]
> Pokud používáte Azure Poweshell v místním počítači, použijte Azure PowerShell verze 1.1.0 nebo vyšší. Stáhněte a nainstalujte nejnovější Azure PowerShell z [Azure ke stažení](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Nastavit zásady opakování

Při vytváření předplatného event gridu můžete nastavit hodnoty, jak dlouho se má Event Grid pokusit doručit událost. Ve výchozím nastavení se event grid pokusí 24 hodin (1440 minut) nebo 30krát. Můžete nastavit některou z těchto hodnot pro odběr mřížky událostí. Hodnota pro čas události pro spuštění musí být celé číslo od 1 do 1440. Hodnota pro maximální opakování musí být celé číslo od 1 do 30.

Plán [opakování](delivery-and-retry.md#retry-schedule-and-duration)nelze nakonfigurovat.

### <a name="azure-cli"></a>Azure CLI

Chcete-li nastavit aktuální hodnotu události na hodnotu jinou než 1440 minut, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Chcete-li nastavit maximální opakování na jinou hodnotu než 30, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Pokud nastavíte obě `event-ttl` aplikace a `max-deliver-attempts`, aplikace Event Grid použije první, v jehož uplynutí vyprší platnost, a určuje, kdy má být poskytování událostí ukončeno.

### <a name="powershell"></a>PowerShell

Chcete-li nastavit aktuální hodnotu události na hodnotu jinou než 1440 minut, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Chcete-li nastavit maximální opakování na jinou hodnotu než 30, použijte:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Pokud nastavíte obě `EventTtl` aplikace a `MaxDeliveryAttempt`, aplikace Event Grid použije první, v jehož uplynutí vyprší platnost, a určuje, kdy má být poskytování událostí ukončeno.

## <a name="next-steps"></a>Další kroky

* Ukázková aplikace, která používá aplikaci Azure Function ke zpracování událostí nedoručených událostí, najdete v [tématu Azure Event Grid Ukázněné vzorky pro .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Informace o doručení události a opakování zpráv [event grid u zpráv a opakování](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
