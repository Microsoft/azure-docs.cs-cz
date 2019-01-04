---
title: Získání připojovacího řetězce - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje pokyny, jak získat připojovací řetězec, který můžou klienti použít k připojení k Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 31220002f8529fd31407470e7650a4c97b62f2b4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535264"
---
# <a name="get-an-event-hubs-connection-string"></a>Získání připojovacího řetězce služby Event Hubs

Pokud chcete používat Event Hubs, je potřeba vytvořit obor názvů služby Event Hubs. Obor názvů je kontejner oboru, který může zastřešovat i více Event Hubs / témat Kafka. Tento obor názvů poskytuje jedinečnou [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po vytvoření oboru názvů můžete získat připojovací řetězec potřebný ke komunikaci se službou Event Hubs.

Připojovací řetězec pro službu Azure Event Hubs má následující komponenty vložit v ní,

* Plně kvalifikovaný název domény = plně kvalifikovaný název domény EventHubs oboru názvů, který jste vytvořili (to bude zahrnovat název oboru názvů EventHubs, za nímž následuje servicebus.windows.net)
* SharedAccessKeyName = název, který jste zvolili pro vaši aplikaci klíče SAS
* SharedAccessKey = vygenerovanou hodnotu klíče.

Šablona připojovací řetězec vypadá
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Příklad připojovacího řetězce vypadat `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Tento článek vás provede vás provedou různými způsoby se dá získat připojovací řetězec.

## <a name="get-connection-string-from-the-portal"></a>Získání připojovacího řetězce z portálu

Jakmile máte obor názvů Event Hubs, oddílu přehled portálu vám může poskytnout připojovací řetězec jak je znázorněno níže:

![Připojovací řetězec centra událostí](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Po kliknutí na odkaz řetězec připojení v oddílu přehled se otevře karta Zásady SAS, jak je znázorněno na následujícím obrázku:

![Zásady SAS služby Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Můžete přidat nové zásady SAS a získání připojovacího řetězce nebo použít výchozí zásada, která je už pro vás vytvořili. Po otevření zásady připojovací řetězec se získá jak je znázorněno následující obrázek:

![Získání připojovacího řetězce služby Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Získávání připojovacího řetězce pomocí Azure Powershellu
Get-AzureRmEventHubNamespaceKey můžete použít k získání připojovacího řetězce pro název konkrétní pravidlo, jak je znázorněno níže:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Odkazovat na [modulu Powershellu pro Azure Event Hubs](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) další podrobnosti.

## <a name="getting-the-connection-string-with-azure-cli"></a>Získávání připojovacího řetězce pomocí Azure CLI
Chcete-li získat připojovací řetězec pro obor názvů můžete použít následující:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Odkazovat na [Azure CLI pro službu Event Hubs](https://docs.microsoft.com/cli/azure/eventhubs) Další informace.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
