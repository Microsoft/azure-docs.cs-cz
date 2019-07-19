---
title: Rozhraní API služby měření softwaru Marketplace – Nejčastější dotazy | Azure Marketplace
description: Vygeneruje použití nabídky SaaS v Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccab7e59eaa925df4ba46447cef458111dc7e60a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869570"
---
# <a name="marketplace-metering-service-apis---faq"></a>Rozhraní API služeb měření na marketplace – nejčastější dotazy

Jakmile se uživatel Azure přihlásí k odběru služby SaaS, která zahrnuje účtované podle objemu dat, budete sledovat spotřebu pro každou fakturační dimenzi, kterou zákazník používá. Pokud spotřeba překročí zahrnuté množství nastavené pro termín vybraný zákazníkem, služba vygeneruje události využití společnosti Microsoft.

## <a name="emit-usage-events"></a>Generování událostí využití

>[!Note]
>Tato část platí jenom pro nabídky SaaS, kde aspoň jeden z plánů má dimenze služby měření, které jsou definované v době publikování nabídky.

![Generování událostí využití](media/isv-emits-usage-event.png)

Informace o kontraktu rozhraní API pro vygenerování událostí využití najdete v tématu [rozhraní API pro události SaaS Batch](./marketplace-metering-service-apis.md#batch-usage-event) .

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak často se očekává, že vygenerujete využití?

V ideálním případě se očekává, že vygenerujete využití každou hodinu za poslední hodinu, a to jenom v případě, že je využití během předchozí hodiny.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jaká je maximální prodleva mezi okamžikem výskytu události a časem, kdy je událost využití vyvolána společnosti Microsoft?

V ideálním případě se událost využití generuje každou hodinu pro události, ke kterým došlo během poslední hodiny. Očekává se ale zpoždění. Maximální povolené zpoždění je 24 hodin, po kterém nebudou události využití přijaty.

Pokud například událost využití nastane 1. den, budete k vygenerování události využití přidružené k této události mít až 1 ODP. To znamená, že v případě, že využití systému vygeneruje čas mimo provoz, může dojít k obnovení a odeslání události využití v intervalu hodin, ve kterém bylo použití provedeno, bez ztráty přesnosti.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Co se stane, když do jedné hodiny odešlete více než jednu událost použití?

Pro hodinový interval je přijímána pouze jedna událost použití. Interval hodin začíná na minuty 0 a končí v minutách 59.  Pokud je pro stejný interval hodin vygenerována více než jedna událost použití, budou všechny následné události použití vyřazeny jako duplicitní.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co se stane, když vygenerujete využití u předplatného SaaS, které se už odhlásilo?

Jakákoli událost využití vygenerovaná na platformu Marketplace nebude po odstranění předplatného SaaS přijata.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Můžete získat seznam všech předplatných SaaS, včetně aktivních a odhlásilých předplatných?

Ano, při volání `GET /saas/subscriptions` rozhraní API obsahuje seznam všech předplatných SaaS. Pole stav v reakci pro každé předplatné SaaS zachycuje, jestli je předplatné aktivní, nebo se odhlásí. Volání funkce list Subscriptions vrátí maximálně 100 odběrů v čase.

## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu [rozhraní API služby měření softwaru Marketplace](./marketplace-metering-service-apis.md) .
