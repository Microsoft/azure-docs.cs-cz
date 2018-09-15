---
title: Zásady opakování pro odběry služby Azure Event Grid a nedoručené zprávy
description: Popisuje, jak přizpůsobit možnosti doručování událostí služby Event Grid. Nastavte cíl onta nedoručených zpráv a určit, jak dlouho to chcete zkusit znovu doručování.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: tomfitz
ms.openlocfilehash: 5db53567b1df9e726fb0c507f0302536ea45b7f4
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603766"
---
# <a name="dead-letter-and-retry-policies"></a>Zásady opakování a nedoručené zprávy

Při vytváření odběru událostí, můžete přizpůsobit nastavení pro doručování událostí. V tomto článku se dozvíte, jak nastavit umístění nedoručených zpráv a přizpůsobit nastavení opakování. Informace o těchto funkcích najdete v tématu [doručování zpráv služby Event Grid a zkuste to znovu](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Nastavit umístění onta nedoručených zpráv

Pokud chcete nastavit umístění nedoručených zpráv, potřebujete účet úložiště pro uchování událostí, které nelze doručit do koncového bodu. Následující skript načte ID prostředku pro existující účet úložiště a vytvoří odběr událostí, který používá kontejner v účtu úložiště pro koncový bod onta nedoručených zpráv.

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

Chcete-li vypnout dead-lettering, spusťte znovu příkaz pro vytvoření odběru událostí, ale neposkytují hodnotu `deadletter-endpoint`. Není nutné odstranit odběr události.

## <a name="set-retry-policy"></a>Nastavit zásady opakování

Při vytváření odběr Event gridu, můžete nastavit hodnoty pro jak dlouho by měl zkuste doručování událostí služby Event Grid. Ve výchozím nastavení služby Event Grid pokusí za 24 hodin (1 440 minut) a pokusí až 30krát. Pro váš odběr služby event grid můžete nastavit některé z těchto hodnot. Hodnota time to live události musí být celé číslo od 1 do 1440. Hodnota pro maximální doručování pokusů musí být celé číslo od 1 do 30.

Nejde nakonfigurovat [interval opakování](delivery-and-retry.md#retry-intervals-and-duration).

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
