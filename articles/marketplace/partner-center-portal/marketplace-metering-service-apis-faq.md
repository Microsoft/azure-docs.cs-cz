---
title: Rozhraní API služby měření – Nejčastější dotazy – komerční tržiště Microsoftu
description: Nejčastější dotazy týkající se rozhraní API služby měření pro SaaS nabízí v Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87319996"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Rozhraní API pro fakturaci měření na webu Marketplace – Nejčastější dotazy

Jakmile se zákazník přihlásí k odběru služby SaaS nebo aplikace Azure s plánem spravovaných aplikací s měřením fakturace, budete sledovat spotřebu pro každou použitou fakturační dimenzi.  Pokud spotřeba překročí zahrnuté množství nastavené pro termín vybraný zákazníkem, služba vygeneruje události využití společnosti Microsoft.

## <a name="for-both-saas-offers-and-managed-apps"></a>Jak pro SaaS nabídky, tak pro spravované aplikace

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak často se očekává, že vygenerujete využití?

V ideálním případě se očekává, že vygenerujete využití každou hodinu za poslední hodinu, a to jenom v případě, že je využití během předchozí hodiny.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Existuje maximální období mezi jednou emisní a další?

Toto omezení neexistuje. Vygenerujte využití pouze při jejich výskytu. Například pokud potřebujete odeslat jenom jednu jednotku využití na dobu platnosti předplatného, můžete to udělat.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jaká je maximální prodleva mezi okamžikem výskytu události a časem, kdy je událost využití vyvolána společnosti Microsoft?

V ideálním případě se událost využití generuje každou hodinu pro události, ke kterým došlo během poslední hodiny. Očekává se ale zpoždění. Maximální povolené zpoždění je 24 hodin, po kterém nebudou události využití přijaty. Osvědčeným postupem je shromažďovat hodinové využití a vysílat je jako jedna událost na konci hodiny.

Pokud například událost využití nastane 1. den, budete k vygenerování události využití přidružené k této události mít až 1 ODP.  V případě, že systém emituje využití mimo provoz, může obnovit a následně odeslat událost využití v intervalu hodin, ve kterém se využití stalo, a to bez ztráty přesnosti.

Pokud se po aktuálním využití předali 24 hodin, můžete využívané jednotky i nadále vysílat s pozdějšími událostmi použití.  Tento postup však může snížit věrohodnosti sestav událostí fakturace pro koncového zákazníka.  Doporučujeme, abyste se vyhnuli posílání měřiče měření jednou za den, týden nebo měsíc.  Bude obtížnější pochopit skutečné využití zákazníka a vyřešit problémy nebo otázky, které mohou být vyvolány týkající se událostí využití.

Dalším důvodem k odeslání využití každou hodinu je zabránit situacím, kdy uživatel předplatné zruší, než pošle událost pro denní/týdenní/měsíční emise.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Co se stane, když do jedné hodiny odešlete více než jednu událost použití?

Po dobu jednoho hodiny je přijímána pouze jedna událost použití. Interval hodin začíná na minuty 0 a končí v minutách 59.  Pokud je za stejnou hodinu vygenerována více než jedna událost použití, budou všechny následné události použití vyřazeny jako duplicitní.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Co se stane, když zákazník zruší nákup v čase povoleném zásadami zrušení?

Částka paušální sazby se nebude účtovat, ale využívání překročení bude platit.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Můžou se vlastní plány měření použít pro jednorázové platby?

Ano, můžete definovat vlastní dimenzi jako jednu jednotku jednorázové platby a pro každého zákazníka ji vygenerovat jenom jednou.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Můžou se vlastní plány měření použít k vrstvenému cenovému modelu?

Ano, dá se implementovat s každou vlastní dimenzí reprezentující jednu cenovou úroveň.

Společnost Contoso například chce účtovat $0,5 na e-mail za prvních 1000 e-mailů, $0,4 za e-mail mezi 1000 a e-maily a na e-maily nad 5000 rámec e-mailů. Můžou definovat tři vlastní dimenze, které odpovídají třem cenovým úrovním e-mailu. Vygeneruje jednotky prvního rozměru, dokud počet e-mailů zůstane nižší než 1000, potom jednotky druhé dimenze, pokud je počet e-mailů v rozsahu 1000 až 5000, a nakonec jednotky třetího rozměru nad rámec 5000 e-maily.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Co se stane, když dojde k výpadku služby měření na webu Marketplace?

Pokud nezávislý výrobce softwaru pošle vlastní měřič a obdrží chybu, může to být způsobeno problémem na straně Microsoftu (obvykle v případě, že se podobné události přijaly před chybou), ISV by měl počkat a opakovat emise.

Pokud s tím budou dál problémy, odešlete tento vlastní měřič znovu o příští hodinu (celkové množství). Pokračovat v tomto procesu, dokud nebude přijata odpověď bez chyby.

## <a name="for-saas-offers-only"></a>Jenom pro SaaS nabídky

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co se stane, když vygenerujete využití u předplatného SaaS, které se už odhlásilo?

Jakákoli událost využití vygenerovaná na webu Marketplace nebude po odstranění předplatného SaaS přijata.

Využití se dá vysílat jenom pro předplatná ve stavu předplatného (a ne pro předplatná v `PendingFulfillmentStart` , `Suspended` nebo ve `Unsubscribed` stavu).

Jedinou výjimkou je použití vykazování pro čas, který byl zrušen před zrušením předplatného SaaS.

Například zákazník zrušil předplatné SaaS ještě dnes ve 3 ODP. Nyní je 5 odp. Vydavatel stále může vysílat využití po dobu mezi 6 hodinami a 3 pm v dnešním SaaS předplatném.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Můžete získat seznam všech předplatných SaaS, včetně aktivních a odhlásilých předplatných?

Ano, když zavoláte [rozhraní API pro získání seznamu předplatných](pc-saas-fulfillment-api-v2.md#subscription-apis) , protože obsahuje seznam všech předplatných SaaS. Pole stav v reakci pro každé předplatné SaaS zachycuje, jestli je předplatné aktivní, nebo se odhlásí.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>Jsou počáteční a koncové datum termínu předplatného SaaS a s využitím emisí nadlimitního využití?

U stávajícího předplatného *SaaS ve stavu* předplatného se dají vysílat události nadlimitního využití v jakémkoli okamžiku. Je zodpovědný Vydavatel, aby vygeneroval události využití na základě zásad definovaných v plánu fakturace. Nadlimitní využití se musí vypočítat na základě dat definovaných v období předplatného SaaS. 

Pokud například Vydavatel definuje plán SaaS, který zahrnuje 1000 e-mailů pro $100 v měsíční paušální sazbě, každý e-mail nad 1000 se účtuje $1 prostřednictvím vlastní dimenze.

Když zákazník koupí a aktivuje předplatné z 6. ledna, bude se v tomto dni počítat e-mail 1000, který je zahrnutý v paušální sazbě. Takže pokud do 5. února (konce prvního měsíce předplatného) přijdete jenom o 900 e-mailů, zákazník bude platit pevnou sazbu jenom za první měsíc tohoto předplatného a vydavatel neodstraní žádné události nadlimitního využití vysílané vydavatelem od 6. ledna. února. 6. února se předplatné automaticky obnoví a počet se spustí znovu. Pokud od 15. února 1000 odeslaných e-mailů, zbývající e-maily odeslané do 5. března se budou účtovat jako nadlimitní využití vycházejících z vydaných vydavatelů za překročení limitu ($1 na e-mail).

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rozhraní API služby pro měření z Marketplace](./marketplace-metering-service-apis.md).
