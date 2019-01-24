---
title: Služby Service Bus, ceny a fakturace | Dokumentace Microsoftu
description: Přehled služby Service Bus Cenová struktura.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 5b9aae979a25a1f175b3d5a5e24960d6f392b9b4
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852928"
---
# <a name="service-bus-pricing-and-billing"></a>Služby Service Bus, ceny a fakturace

Azure Service Bus nabízíme v Standard a [Premium](service-bus-premium-messaging.md) úrovně. Můžete zvolit úroveň služby pro každý obor názvů služby Service Bus služby, které vytvoříte, a tento výběr úrovně vztahuje na všechny entity vytvořené v daném oboru názvů.

> [!NOTE]
> Podrobné informace o aktuální ceny služby Service Bus, najdete v článku [stránce s cenami služby Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/)a [nejčastějších dotazech ke službě Service Bus](service-bus-faq.md#pricing).
>
>

Service Bus pomocí následujících měřičů 2 pro fronty a témata nebo předplatná:

1. **Operace zasílání zpráv**: Definovaných jako volání rozhraní API proti fronty nebo tématu/odběru koncových bodů služby. Měří nahradí zprávy odesílané nebo přijímané jako primární jednotkou fakturovatelné využití pro fronty a témata nebo předplatná.
2. **Zprostředkovaná připojení**: Definovaný jako nejvyšší počet trvalých připojení otevřete front, témat nebo předplatných v dané vzorkování hodinová období. Tato měření se používá pouze na úrovni Standard, ve kterém můžete otevřít další připojení (dříve připojení byly omezené na 100 za fronty/tématu nebo odběru) za nominální připojení.

**Standardní** úroveň zavádí jsou odstupňované ceny za operace prováděné s frontami a tématy nebo předplatnými, což vede k slevy na základě objemového až 80 % na nejvyšší úrovni využití. Je také základní poplatek úrovně Standard z 10 USD za měsíc, které umožňuje provádět 12,5 milionů operací za měsíc bez dalších poplatků.

**Premium** úroveň zajišťuje izolaci prostředků ve vrstvě CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Pro každý obor názvů Service Bus Premium můžete koupit 1, 2 nebo 4 jednotky zasílání zpráv. Jedna úloha nebo entita může zabírat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv se dá změnit podle libosti, ale fakturuje se podle 24hodinoví/denní sazby. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus. Vedle toho, že je tento výkon předvídatelnější, je také rychlejší.

> [!NOTE]
> Témata a odběry jsou dostupné jenom v Standard nebo Premium cenové úrovně; na úrovni Basic podporuje pouze fronty.

Základní poplatek úrovně Standard se účtuje jenom jednou za měsíc za jedno předplatné Azure. To znamená, že po vytvoření služby Service Bus jednoho standardního názvového můžete vytvořit libovolný počet dalších standardních názvových prostorů chcete v rámci jednoho předplatného Azure, aniž by se účtovaly další základní poplatky.

[Cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabulka shrnuje funkční rozdíly mezi úrovněmi Standard a Premium.

## <a name="messaging-operations"></a>Operace zasílání zpráv

Fronty a témata nebo předplatná se účtují po "operace", ne podle zpráv. Operace se odkazuje na jakékoli volání rozhraní API provedená oproti frontě nebo tématu/odběru koncového bodu služby. Patří sem operace správy, odeslání/přijetí a stavu relace.

| Typ operace | Popis |
| --- | --- |
| Správa |Vytvoření, čtení, aktualizace, odstranění (CRUD) front nebo témat/odběrů. |
| Zasílání zpráv |Odesílání a příjem zpráv s frontami nebo tématy nebo předplatnými. |
| Stav relace |Získání nebo nastavení stavu relace ve frontě nebo tématu nebo odběru. |

Nákladů, prohlédněte si ceny uvedené na [cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) stránky.

## <a name="brokered-connections"></a>Zprostředkovaná připojení

*Zprostředkovaná připojení* podle vzorce používání, které se týkají velkého počtu "trvale připojené" odesílatelů/příjemců pro fronty, témata nebo předplatná. Odesílatelé trvale připojené/příjemci jsou ty, které se připojují pomocí protokolu AMQP nebo HTTP s nenulové přijímat časový limit (třeba HTTP dlouhý interval dotazování). HTTP odesílateli a příjemci s okamžitou časový limit negenerují zprostředkovaných připojení.

Připojení kvóty a omezení dalších služeb najdete v tématu [kvótách služby Service Bus](service-bus-quotas.md) článku. Další informace o zprostředkovaných připojení, najdete v článku [nejčastější dotazy k](#faq) části dále v tomto článku.

Na úrovni Standard odebere limit zprostředkovaných připojení na obor názvů a počty využití agregace zprostředkované připojení přes předplatné Azure. Další informace najdete v tématu [zprostředkovaných připojení](https://azure.microsoft.com/pricing/details/service-bus/) tabulky.

> [!NOTE]
> 1 000 zprostředkovaných připojení součástí zasílání zpráv úrovně Standard (v základním poplatku), lze sdílet mezi všechny fronty, témata a odběry v rámci přidruženého předplatného Azure.
>
>

<br />

> [!NOTE]
> Pro účely fakturace se používá nejvyšší počet souběžných připojení poměrně přepočítaných na hodiny na základě 744 hodin za měsíc.
>
>

### <a name="premium-tier"></a>Úroveň Premium

Zprostředkovaná připojení se na úrovni Premium neúčtují.

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Co je zprostředkované připojení a jak se mi účtovat pro ně?

Zprostředkované připojení se definuje jedním z těchto způsobů:

1. Připojení AMQP klienta k tématu/odběru a fronty služby Service Bus.
2. Volání HTTP při příjmu zprávy z tématu nebo fronty Service Bus, které má hodnotu časového limitu příjmu větší než nula.

Poplatkům za Service Bus pro nejvyšší počet souběžných zprostředkovaných připojení, která překročí zahrnuté množství (1 000 na úrovni Standard). Špičky se měří po hodinách. Výsledek měření se vydělí 744 hodinami za měsíc a přičte se k měsíčnímu fakturačnímu období. Zahrnuté množství (1 000 hodin zprostředkovaných připojení za měsíc) se na konci fakturačního období porovná s úhrnem poměrných hodinových špiček.

Příklad:

1. Každý z 10 000 zařízení se připojuje přes samostatné připojení AMQP a přijímá příkazy z tématu Service Bus. Zařízení odesílá události telemetrie do centra událostí. Pokud všechna zařízení připojená 12 hodin každý den, platí následující poplatky za připojení (navíc k žádné jiné téma poplatkům za Service Bus): 10 000 připojení * 12 hodin * 31 dnů / 744 = 5 000 zprostředkovaných připojení. Protože máte měsíčně povolených 1000 zprostředkovaných připojení vám bude účtovat 4 000 zprostředkovaných připojení se sazbou 0.03 za zprostředkované připojení, tedy celkem 120 $.
2. 10 000 zařízení přijímá zprávy z fronty Service Bus přes protokol HTTP nenulovým časovým limitem. Pokud všechna zařízení připojená 12 hodin každý den, zobrazí se následující poplatky za připojení (navíc k poplatkům jiné služby Service Bus): 10 000 připojení pro přijetí přes protokol HTTP * 12 hodin každý den * 31 dnů / 744 hodin = 5 000 zprostředkovaných připojení.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Vztahují se poplatky za připojení na fronty a předplatná?

Ano. Za odesílání událostí přes HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Události přijímané přes HTTP s časovým limitem větším než nula, někdy označuje jako "dlouhý interval dotazování" generuje poplatky za zprostředkované připojení. Připojení AMQP generují poplatky za zprostředkované připojení bez ohledu na to, jestli se připojení použije k odeslání nebo přijetí. Prvních 1000 zprostředkovaných připojení ve všech názvových prostorech předplatného Azure je zahrnutých bez dalších poplatků (kromě základního poplatku). Protože tyto limity stačí na pokrytí mnoha scénářů zasílání zpráv služby service, poplatky za zprostředkovaná připojení narostou jen tehdy, relevantní, pokud plánujete použít připojení přes AMQP nebo HTTP s dlouhým intervalem dotazování u velkého počtu klientů; Chcete-li například efektivněji Streamovat události nebo zapnout obousměrnou komunikaci s mnoha zařízení nebo instancí aplikace.

## <a name="next-steps"></a>Další postup

* Úplné podrobnosti o cenách služby Service Bus, najdete v článku [stránce s cenami služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Zobrazit [nejčastějších dotazech ke službě Service Bus](service-bus-faq.md#pricing) pro některé běžné nejčastější dotazy o Service bus, ceny a fakturace.

[Azure portal]: https://portal.azure.com
