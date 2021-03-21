---
title: Řešení chyb AMQP v Azure Event Hubs | Microsoft Docs
description: Poskytuje seznam AMQPch chyb, které se mohou zobrazit při použití Azure Event Hubs a příčině těchto chyb.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466061"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP chyby v Azure Event Hubs
Tento článek popisuje některé chyby, které obdržíte při použití AMQP s Azure Event Hubs. Jsou to všechna standardní chování služby. Můžete se jim vyhnout tím, že v připojení nebo propojení vytvoříte volání pro odesílání a přijímání, které automaticky znovu vytvoří připojení nebo propojení.

## <a name="link-is-closed"></a>Odkaz je uzavřený. 
Při aktivním připojení AMQP a propojení se zobrazí následující chyba, ale při použití odkazu na 30 minut se neuskuteční žádná volání (například Send nebo Receive). Proto je odkaz uzavřený. Připojení je stále otevřené.

"AMQP: Link: Detach-vynuceno: odkaz ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 ' je vynuceně odpojen zprostředkovatelem, protože došlo k chybám v aplikaci Publisher (link164614). Odpojte počátek: AmqpMessagePublisher. IdleTimerExpired: časový limit nečinnosti: 00:30:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: MyNamespace: téma: MyTopic, timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Připojení je zavřené.
Na připojení AMQP se zobrazí následující chyba, když jsou všechna propojení v připojení zavřena, protože nebyla žádná aktivita (nečinný) a nové propojení nebylo vytvořeno během 5 minut.

"Error {Condition = AMQP: Connection: nucený, Description = ' připojení bylo neaktivní po více než povolené 300000 milisekund a je uzavřeno kontejnerem ' LinkTracker '. TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T17:32:00, info = null}

## <a name="link-isnt-created"></a>Odkaz není vytvořen. 
Tato chyba se zobrazí, když se vytvoří nové připojení AMQP, ale odkaz se nevytvoří během 1 minuty od vytvoření připojení AMQP.

"Error {Condition = AMQP: Connection: nucený, Description = ' připojení bylo neaktivní po více než povolené 60000 milisekund a je uzavřeno kontejnerem ' LinkTracker '. TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, timestamp: 2019-03-06T18:41:51 ', info = null} "

## <a name="next-steps"></a>Další kroky
Další informace o AMQP najdete v tématu [Průvodce protokolem AMQP 1,0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
