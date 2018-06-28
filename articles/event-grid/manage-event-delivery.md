---
title: Spravovat nastavení doručení pro předplatná Azure událostí mřížky
description: Popisuje, jak přizpůsobit možnosti doručení událostí pro událost mřížky.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035818"
---
# <a name="manage-event-grid-delivery-settings"></a>Spravovat nastavení doručení událostí mřížky

Když vytváříte odběr událostí, můžete upravit nastavení pro odeslání události. Můžete nastavit, jak dlouho pokusí doručit zprávu událostí mřížky. Můžete nastavit účet úložiště, který chcete použít pro ukládání událostí, které nelze doručit do koncového bodu.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Nastavit zásady opakovaných pokusů

Při vytváření předplatného služby událostí mřížky, můžete nastavit hodnoty pro jak dlouho se pokuste mřížky událostí doručit událost. Ve výchozím nastavení události mřížky v pokusech o 24 hodin (1 440 minut) a pokusí maximálně 30 časy. Můžete nastavit některé z těchto hodnot pro vaše předplatné mřížky událostí.

Pokud chcete nastavit událost time to live na jinou hodnotu než 1 440 minut, použijte:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Pokud chcete nastavit maximální počet opakování pokusů na jinou hodnotu než 30, použijte:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Nastavíte-li `event-ttl` a `max-deliver-attempts`, události mřížka použije první vyprší pro pokusy o opakování.

## <a name="set-dead-letter-location"></a>Nastavení umístění nedoručených zpráv

Když mřížky událostí doručit událost, kterou může odesílat nedoručených událostí na účet úložiště. Tento proces se označuje jako lettering zpráv. Ve výchozím nastavení není událostí mřížky zapnout lettering zpráv. Chcete-li ji povolit, musíte zadat účet úložiště do něho vešly nedoručených události při vytváření odběru událostí. Můžete pro vyžádání obsahu události z tohoto účtu úložiště k vyřešení doručení.

Pokud to nezkusí všechny jeho opakovaných pokusů, nebo pokud obdrží chybovou zprávu, která určuje, se nikdy úspěšné doručení událostí mřížky odešle událost do umístění nedoručených zpráv. Například pokud událost mřížky obdrží chybu nesprávný formát při doručování událost, okamžitě odešle tuto událost do umístění nedoručených zpráv.

Před nastavením nedoručených zpráv umístění, musí mít účet úložiště ke kontejneru. Zadejte koncový bod pro tento kontejner, při vytváření odběru událostí. Koncový bod je ve formátu: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Následující skript načte ID prostředku stávající účet úložiště a vytvoří odběr událostí, který používá kontejner v daném účtu úložiště pro koncový bod nedoručených zpráv.

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
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Reakce na nedoručených události pomocí událostí mřížky [vytvořit odběr událostí](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pro úložiště objektů blob nedoručených zpráv. Pokaždé, když obdrží úložiště objektů blob nedoručených zpráv nedoručených událost, upozorní událostí mřížky vaší obslužné rutiny. Obslužná rutina odpovídá s akcemi, které chcete vyjmout pro vyřešení nedoručených události. 

## <a name="next-steps"></a>Další postup

* Informace o události doručení a opakovaných pokusů [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).
