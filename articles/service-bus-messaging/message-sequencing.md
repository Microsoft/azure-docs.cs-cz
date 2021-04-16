---
title: Azure Service Bus sekvencování a časová razítka zpráv | Microsoft Docs
description: Tento článek vysvětluje, jak zachovat sekvencování a řazení (s časovými razítky) Azure Service Busch zpráv.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497093"
---
# <a name="message-sequencing-and-timestamps"></a>Určování pořadí zpráv a časová razítka

Sekvence a časová razítka jsou dvě funkce, které jsou vždycky povolené na všech Service Bus entit a povrchu `SequenceNumber` prostřednictvím `EnqueuedTimeUtc` vlastností a přijatých nebo procházených zpráv.

V případech, kdy je absolutní pořadí zpráv významné a/nebo kdy spotřebitel potřebuje důvěryhodný jedinečný identifikátor pro zprávy, zprostředkovatel označí zprávy s mezerou a zvyšuje pořadové číslo vzhledem k frontě nebo tématu. Pro dělené entity je číslo sekvence vystaveno relativně k oddílu.

Hodnota **SequenceNumber** je jedinečné 64 celé číslo, které je přiřazeno ke zprávě, protože je přijata a ukládána zprostředkovatelem a funguje jako jeho vnitřní identifikátor. U dělených entit se v rámci 16 bitů odráží identifikátor oddílu. Pořadová čísla se převádějí na nulu při vyčerpání rozsahu 48/64.

Pořadové číslo může být důvěryhodné jako jedinečný identifikátor, protože je přiřazený centrální a neutrální autoritou a nikoli klienty. Představuje taky skutečný způsob doručení a je přesnější než časové razítko jako kritérium objednávky, protože časová razítka nemusí mít dostatečně vysokou přesnost na extrémních sazbách zpráv a můžou se řídit (ale minimální) časovým posunem v situacích, kdy zprostředkovatel prochází přechody mezi uzly.

Absolutní pořadí doručení, například v obchodních scénářích, ve kterých se v případě, že je v rámci dodávky poskytováno omezené množství nabízených zboží, se zadává na základě prvního přidávaného a koncového nabídky. Příkladem je prodej lístku ve vzájemné shodě.

Schopnost časová razítka funguje jako neutrální a důvěryhodná autorita, která přesně zachycuje dobu UTC doručení zprávy, která se odráží ve vlastnosti **EnqueuedTimeUtc** . Hodnota je užitečná v případě, že obchodní scénář závisí na konečných termínech, například zda byla pracovní položka odeslána v určitém datu před půlnocí, ale zpracování je daleko za nevyřízenými položkami fronty.

## <a name="scheduled-messages"></a>Plánované zprávy

Zprávy můžete odeslat do fronty nebo tématu pro zpožděné zpracování. Můžete třeba naplánovat úlohu, která bude systému pro zpracování dostupná v konkrétní čas. Tato schopnost má za sebou spolehlivý distribuovaný Plánovač založený na čase.

Naplánované zprávy se ve frontě nevyhodnotit, dokud není definovaná doba zařazení do fronty. Před uplynutím této doby lze naplánované zprávy zrušit. Zrušení odstraní zprávu.

Pomocí kteréhokoli z našich klientů můžete naplánovat zprávy dvěma způsoby:
- Použijte běžné rozhraní API pro odesílání, ale `ScheduledEnqueueTimeUtc` před odesláním nastavte vlastnost ve zprávě.
- Použijte rozhraní API pro naplánování zpráv, předejte normální zprávu i naplánovaný čas. Tato akce vrátí **SequenceNumber** naplánované zprávy, kterou můžete později použít k zrušení naplánované zprávy v případě potřeby. 

Naplánované zprávy a jejich pořadová čísla lze také zjistit pomocí [procházení zpráv](message-browsing.md).

**SequenceNumber** pro naplánovanou zprávu je platná pouze v případě, že je zpráva v tomto stavu. Při přechodu na aktivní stav zprávy se zpráva připojí do fronty, jako kdyby byla zařazená do fronty v aktuálním okamžitém stavu, což zahrnuje přiřazení nové **SequenceNumber**.

Vzhledem k tomu, že je tato funkce ukotvena u jednotlivých zpráv a zprávy lze zařadit do fronty pouze jednou, Service Bus nepodporuje opakované plány pro zprávy.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
