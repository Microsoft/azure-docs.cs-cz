---
title: Získejte připojovací řetězec – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje pokyny pro získání připojovacířetězec, který klienti můžete použít pro připojení k Centru událostí Azure.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264931"
---
# <a name="get-an-event-hubs-connection-string"></a>Získání připojovacího řetězce Centra událostí

Chcete-li používat centra událostí, musíte vytvořit obor názvů Event Hubs. Obor názvů je kontejner oboru pro více center událostí nebo témata Kafka. Tento obor názvů poskytuje jedinečný [soubor FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po vytvoření oboru názvů můžete získat připojovací řetězec potřebný ke komunikaci s event huby.

Připojovací řetězec pro Centra událostí Azure má v něm vložené následující součásti,

* FQDN = fQDN oboru názvů EventHubs, který jste vytvořili (obsahuje název oboru názvů EventHubs následovaný servicebus.windows.net)
* SharedAccessKeyName = název, který jste zvolili pro klíče SAS vaší aplikace
* SharedAccessKey = vygenerovaná hodnota klíče.

Šablona připojovacího řetězce vypadá
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Ukázkový připojovací řetězec může vypadat`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Tento článek vás provede různými způsoby získání připojovacího řetězce.

## <a name="get-connection-string-from-the-portal"></a>Získání připojovacího řetězce z portálu
1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. V levé navigační nabídce vyberte **Všechny služby.** 
3. V části **Analytics** vyberte **Centra událostí.** 
4. V seznamu center událostí vyberte centrum událostí.
6. Na stránce **Obor názvů centra eventhubů** vyberte v levé nabídce **zásady sdíleného přístupu.**

    ![Položka nabídky Zásady sdíleného přístupu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. V seznamu zásad vyberte **zásady sdíleného přístupu.** Výchozí má název: **RootManageSharedAccessPolicy**. Můžete přidat zásadu s příslušnými oprávněními (čtení, zápis) a použít tuto zásadu. 

    ![Zásady sdíleného přístupu centra event hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Vyberte tlačítko **kopírování** vedle pole **Připojovací řetězec primárníklíč.** 

    ![Rozbočovače událostí – získat připojovací řetězec](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Získání připojovacího řetězce pomocí Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Pomocí klávesy Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) můžete získat připojovací řetězec pro konkrétní název zásady/pravidla, jak je znázorněno níže:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Získání připojovacího řetězce pomocí azure cli
K získání připojovacího řetězce pro obor názvů můžete použít následující:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Nebo můžete použít následující získat připojovací řetězec pro eventhub entity:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Další informace o příkazech Azure CLI pro centra událostí najdete v [tématu Azure CLI pro centra událostí](/cli/azure/eventhubs).

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
