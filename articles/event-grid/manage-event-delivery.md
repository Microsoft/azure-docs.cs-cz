---
title: Spravovat nastavení doručení pro odběry služby Azure Event Grid
description: Popisuje, jak přizpůsobit možnosti doručování událostí služby Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 0e575d668e28be52ee4ca61226693122304c7ea0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441353"
---
# <a name="dead-letter-and-retry-policies"></a>Zásady opakování a nedoručené zprávy

Při vytváření odběru událostí, můžete přizpůsobit nastavení pro doručování událostí. Můžete nastavit, jak dlouho pokusí doručit zprávu služby Event Grid. Můžete nastavit účet úložiště, který chcete použít pro ukládání událostí, které nelze doručit do koncového bodu.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Nastavit umístění onta nedoručených zpráv

Když Event Grid doručit událost, kterou může odesílat nedoručené událostí do účtu úložiště. Tento proces se označuje jako dead-lettering. Ve výchozím nastavení služby Event Grid nebude zapnout dead-lettering. Ho Pokud chcete povolit, musíte zadat účet úložiště pro uložení nedoručené události při vytváření odběru událostí. O přijetí změn události z tohoto účtu úložiště, chcete-li vyřešit doručení.

Pokud se všechny jeho opakované pokusy, nebo pokud obdrží chybovou zprávu, která určuje doručování uspějí nikdy služby Event Grid odešle událost dead-letter umístění. Například pokud Event Grid přijme chybu nesprávný formát při doručování událostí, ihned odešle tuto událost do umístění onta nedoručených zpráv.

Před nastavením umístění nedoručených zpráv, musíte mít účet úložiště s kontejnerem. Zadejte koncový bod pro tento kontejner, při vytváření odběru událostí. Koncový bod je ve formátu: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Následující skript načte ID prostředku pro existující účet úložiště a vytvoří odběr událostí, který používá kontejner v účtu úložiště pro koncový bod onta nedoručených zpráv.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Použití služby Event Grid pro reakci na nedoručené události [vytvoření odběru událostí](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pro úložiště objektů blob onta nedoručených zpráv. Pokaždé, když vaše úložiště objektů blob onta nedoručených zpráv přijme nedoručené událost, Event Grid oznámí obslužnou rutinu. Obslužné rutiny jsou reaguje s akcemi, které chcete provést pro vyřešení nedoručené události. 

Chcete-li vypnout dead-lettering, spusťte znovu příkaz pro vytvoření odběru událostí, ale neposkytují hodnotu `deadletter-endpoint`. Není nutné odstranit odběr události.

## <a name="set-retry-policy"></a>Nastavit zásady opakování

Při vytváření odběr Event gridu, můžete nastavit hodnoty pro jak dlouho by měl zkuste doručování událostí služby Event Grid. Ve výchozím nastavení služby Event Grid pokusí za 24 hodin (1 440 minut) a pokusí až 30krát. Pro váš odběr služby event grid můžete nastavit některé z těchto hodnot.

Pokud chcete nastavit událost time-to-live na jinou hodnotu než 1 440 minut, použijte:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Pokud chcete nastavit maximální počet opakovaných pokusů na jinou hodnotu než 30, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Nastavíte-li `event-ttl` a `max-deliver-attempts`, služby Event Grid použije první vypršení platnosti pro opakovaných pokusů.

## <a name="next-steps"></a>Další postup

* Ukázková aplikace, která používá aplikaci funkcí Azure ke zpracování událostí nedoručených zpráv, najdete v části [ukázky služby Event Grid nedoručených zpráv Azure pro .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Informace o doručování událostí a opakovaných pokusů [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).
