---
title: Získání připojovacího řetězce - Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje pokyny, jak získat připojovací řetězec, který můžou klienti použít k připojení k Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158918"
---
# <a name="get-an-event-hubs-connection-string"></a>Získání připojovacího řetězce služby Event Hubs

Pokud chcete používat Event Hubs, je potřeba vytvořit obor názvů služby Event Hubs. Obor názvů je kontejner oboru pro více služby event hubs nebo témat Kafka. Tento obor názvů poskytuje jedinečnou [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Po vytvoření oboru názvů můžete získat připojovací řetězec potřebný ke komunikaci se službou Event Hubs.

Připojovací řetězec pro službu Azure Event Hubs má následující komponenty vložit v ní,

* Plně kvalifikovaný název domény = plně kvalifikovaný název domény EventHubs oboru názvů, který jste vytvořili (zahrnuje název oboru názvů EventHubs, za nímž následuje servicebus.windows.net)
* SharedAccessKeyName = název, který jste zvolili pro vaši aplikaci klíče SAS
* SharedAccessKey = vygenerovanou hodnotu klíče.

Šablona připojovací řetězec vypadá
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Příklad připojovacího řetězce vypadat `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Tento článek vás provede vás provedou různými způsoby se dá získat připojovací řetězec.

## <a name="get-connection-string-from-the-portal"></a>Získání připojovacího řetězce z portálu
1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
2. Vyberte **všechny služby** v navigační nabídce vlevo. 
3. Vyberte **Event Hubs** v **Analytics** oddílu. 
4. V seznamu event hubs vyberte Centrum událostí.
6. Na **Event Hubs Namespace** stránce **sdílené zásady přístupu** v nabídce vlevo.

    ![Sdílené zásady přístupu položky nabídky](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Vyberte **sdílené zásady přístupu** v seznamu zásad. Výchozí jednu s názvem: **RootManageSharedAccessPolicy**. Můžete přidat zásadu s příslušnými oprávněními (čtení, zápisu) a použít tuto zásadu. 

    ![Event Hubs sdílené zásady přístupu](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Vyberte **kopírování** vedle **připojovací řetězec – primární klíč** pole. 

    ![Event Hubs – získání připojovacího řetězce](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Získávání připojovacího řetězce pomocí Azure Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete použít [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) k získání připojovacího řetězce pro název konkrétní pravidlo, jak je znázorněno níže:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Získávání připojovacího řetězce pomocí Azure CLI
Chcete-li získat připojovací řetězec pro obor názvů můžete použít následující:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Další informace o příkazech rozhraní příkazového řádku Azure pro službu Event Hubs najdete v tématu [Azure CLI pro službu Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
