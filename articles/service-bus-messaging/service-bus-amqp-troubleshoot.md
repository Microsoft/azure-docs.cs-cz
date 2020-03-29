---
title: Poradce při potížích s chybami AMQP v Azure Service Bus | Dokumenty společnosti Microsoft
description: Obsahuje seznam chyb AMQP, které se mohou zobrazit při použití služby Azure Service Bus, a příčinu těchto chyb.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402781"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Chyby AMQP v Azure Service Bus
Tento článek obsahuje některé chyby, které se zobrazí při použití AMQP s Azure Service Bus. Jedná se o standardní chování služby. Můžete se jim vyhnout tím, že odesílat / přijímat hovory na připojení / odkaz, který automaticky obnoví připojení / odkaz.

## <a name="link-is-closed"></a>Propojení je uzavřeno. 
Zobrazí se následující chyba, pokud amqp připojení a propojení jsou aktivní, ale žádné volání (například odeslat nebo přijmout) jsou prováděny pomocí odkazu po dobu 10 minut. Takže odkaz je uzavřen. Připojení je stále otevřené.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Připojení je uzavřeno.
Zobrazí se následující chyba na připojení AMQP, pokud byly uzavřeny všechny odkazy v připojení, protože nedošlo k žádné aktivitě (nečinnosti) a nové propojení nebylo vytvořeno za 5 minut.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Propojení není vytvořeno. 
Tato chyba se zobrazí při vytvoření nového připojení AMQP, ale propojení není vytvořeno do 1 minuty od vytvoření připojení AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Další kroky

Další informace o službách AMQP a Service Bus naleznete na následujících odkazech:

* [Přehled služby AMQP sběrnice]
* [Průvodce protokolem AMQP 1.0]
* [AMQP v service bus pro Windows Server]

[Přehled služby AMQP sběrnice]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP v service bus pro Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
