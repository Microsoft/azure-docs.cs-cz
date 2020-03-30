---
title: Marketplace měření služby API - nejčastější dotazy | Azure Marketplace
description: Vyzařovat využití nabídky SaaS na Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275777"
---
# <a name="marketplace-metering-service-apis---faq"></a>Rozhraní API služeb měření na marketplace – nejčastější dotazy

Jakmile se uživatel Azure přihlásí k odběru služby SaaS, která zahrnuje fakturaci účtované podle objemu dat, budete sledovat spotřebu pro každou fakturační dimenzi, kterou zákazník používá. Pokud spotřeba překročí zahrnutá množství nastavená pro termín vybraný zákazníkem, bude vaše služba vypouštět události využití společnosti Microsoft.

## <a name="emit-usage-events"></a>Vyzařovat události využití

>[!Note]
>Tato část je použitelná pouze pro nabídky SaaS, kde alespoň jeden z plánů má rozměry měřicí služby definované v době zveřejnění nabídky.

![Vyzařovat události využití](media/isv-emits-usage-event.png)

Informace o kontraktu rozhraní API pro vyzařování událostí využití rozhraní API pro vyzařování událostí využití najdete v tématu Rozhraní API pro dávkové využití [SaaS.](./marketplace-metering-service-apis.md#batch-usage-event)

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak často se očekává, že vyzařuje využití?

V ideálním případě se očekává, že vyzařují využití každou hodinu za poslední hodinu, pouze v případě, že je využití v předchozí hodině.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jaká je maximální prodleva mezi časem výskytu události a časem, kdy je událost využití vyzařována společnosti Microsoft?

V ideálním případě je událost využití vyzařována každou hodinu pro události, ke kterým došlo v poslední hodině. Očekává se však zpoždění. Maximální povolené zpoždění je 24 hodin, po kterém nebudou akceptovány události využití.

Například pokud dojde k události využití v 1pm na den, máte do 1 PM na další den vyzařovat událost využití přidružené k této události. To znamená, že v případě, že systém emitující využití má prostoje, může obnovit a potom odeslat událost využití pro hodinový interval, ve kterém došlo k použití, bez ztráty věrnosti.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Co se stane, když odešlete více než jednu událost využití ve stejnou hodinu?

Pro hodinový interval je přijata pouze jedna událost použití. Hodinový interval začíná v minutě 0 a končí v minutě 59.  Pokud je pro stejný hodinový interval vyzařováno více než jedna událost použití, všechny následné události použití jsou vynechány jako duplicitní.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co se stane, když vyzařujete využití předplatného SaaS, které již bylo odhlášeno?

Po odstranění předplatného SaaS nebudou přijaty žádné události využití vyzařované platformě Marketplace.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Můžete získat seznam všech odběrů SaaS, včetně aktivních a odhlasovaných předplatných?

Ano, když zavoláte `GET /saas/subscriptions` rozhraní API, obsahuje seznam všech předplatných SaaS. Pole stavu v odpovědi pro každé předplatné SaaS zachycuje, zda je odběr aktivní nebo odhlášen. Volání do seznamu Odběry vrátí maximálně 100 odběry v době.

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Marketplace metering service API.](./marketplace-metering-service-apis.md)
