---
title: Detekce duplicitních zpráv ve službě Azure Service Bus | Dokumentace Microsoftu
description: Zjišťování duplicitních zpráv služby Service Bus
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: 7402fcf01078ea3934d1b6794a9190947fe339c2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696621"
---
# <a name="duplicate-detection"></a>Vyhledávání duplicit

Pokud aplikace zjistí závažná chyba ihned po odešle zprávu a instance aplikace restartovala chybně domnívá, že předchozí zprávu doručování nedošlo k, následné odeslat způsobí, že stejná zpráva se zobrazí v systému dvakrát.

Je také možné chyby na úrovni klienta nebo síti dojde k chvíli předtím a odeslané zprávy, která potvrdí do fronty se potvrzení úspěšně vrácen do klienta. Tento scénář ponechá klienta v výsledek operace odeslání.

Zjišťování duplicit přebírá nejisté z těchto situací tím, že odesílatel znovu odeslat stejné zprávy a fronty nebo tématu zahodí všechny duplicitní kopie.

Když se povolí zjišťování duplicit pomáhá udržovat přehled o řízené aplikací *MessageId* všech zpráv odeslaných do fronty nebo tématu během zadaného časového intervalu. Pokud žádné nové zprávy provádění *MessageId* , která již byla zaznamenána během časového intervalu, zpráva se oznámilo, přijato (úspěšné operaci odeslání), ale nově odeslané zprávě okamžitě ignorována a vyřadit. Žádné jiné části zprávy jiné než *MessageId* jsou považovány za.

Řízení aplikací v identifikátoru je nezbytné, protože pouze to, že umožňuje, aby aplikace a jejich zapojení *MessageId* k obchodním procesu kontextu, ze kterého ho můžete předvídatelně znovu vytvořena v případě selhání.

Pro obchodní proces, ve kterém jsou odesílány více zpráv v průběhu zpracování některých kontext aplikace *MessageId* může být složené identifikátor kontextu úrovni aplikace, jako je například čísla nákupních objednávek a předmět zprávy. například **12345.2017/platební**.

*MessageId* může vždy být některé identifikátor GUID, ale ukotvení identifikátor obchodního procesu poskytuje předvídatelný opakovatelnosti, který je požadován pro efektivní využití funkce vyhledávání duplicit.

## <a name="enable-duplicate-detection"></a>Povolit vyhledávání duplicit

Na portálu, tato funkce je zapnutá během vytváření entit s **povolit vyhledávání duplicit** zaškrtávací políčko, které je ve výchozím nastavení vypnuté. Je ekvivalentní nastavení pro vytvoření nových témat.

![][1]

Prostřednictvím kódu programu, nastavte příznak s [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) vlastnost na úplné rozhraní .NET API. Pomocí rozhraní API Azure Resource Manageru, je hodnota nastavena s [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnost.

Historie vyhledávání duplicit čas výchozí hodnota je 30 sekund u front a témat, přičemž maximální hodnota 7 dní. Můžete změnit toto nastavení v okně Vlastnosti frontu a téma na webu Azure Portal.

![][2]

Prostřednictvím kódu programu, můžete nakonfigurovat velikost okna zjišťování duplicit, během kterého se zachovají identifikátory zpráv, pomocí [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) vlastnost s úplné rozhraní .NET Framework API . Pomocí rozhraní API Azure Resource Manageru, je hodnota nastavena s [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnost.

Všimněte si, že povolení zjišťování duplicit a velikost okna přímo mít dopad na propustnost fronty (a téma), protože všechny zaznamenané ID zprávy musí být hledána identifikátor nově odeslané zprávy.

Udržování okno malé znamená, že méně identifikátory zpráv musí být zachovány a odpovídající a propustnost je ovlivněný menší. Vysoká propustnost entit, které vyžadují vyhledávání duplicit byste měli mít v okně co nejmenší.

## <a name="next-steps"></a>Další postup

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
