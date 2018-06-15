---
title: Service Bus ceny a fakturace | Microsoft Docs
description: Přehled služby Service Bus cenové struktury.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 8ccb44b5009588c28bc79bb45e1a7640ead6c817
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159782"
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus ceny a fakturace

Ve verzi Standard se nabízí Azure Service Bus a [Premium](service-bus-premium-messaging.md) vrstev. Můžete vybrat vrstvy služby pro každý obor názvů sběrnice služby, který vytvoříte, a tento výběr vrstvy platí mezi všechny entity vytvořené v daném oboru názvů.

> [!NOTE]
> Podrobné informace o cenách aktuální Service Bus, najdete v článku [Azure Service Bus stránce s cenami](https://azure.microsoft.com/pricing/details/service-bus/)a [nejčastější dotazy týkající se Service Bus](service-bus-faq.md#pricing).
>
>

Service Bus používá následující 2 měřidla pro fronty a témata nebo odběry:

1. **Zasílání zpráv operace**: definován jako volání rozhraní API proti fronta nebo téma/odběr koncové body služby. Toto měření nahradí zprávy odesílané nebo přijímané jako primární jednotka fakturovatelný využití pro fronty a témata nebo předplatných.
2. **Zprostředkované připojení**: definován jako počet ve špičce trvalé připojení otevřete před fronty, témata a odběry, během období daného hodinových vzorkování. Toto monitorování se vztahuje pouze na vrstvě Standard, ve kterém můžete otevřít další připojení (dříve připojení byly omezeno na 100 podle fronty, tématu nebo předplatného) za úplatu nominální připojení.

**Standardní** vrstvy zavádí dělené ceny pro operace provedené pomocí fronty a témata nebo odběry, což vede k slevy na základě svazku až 80 % na nejvyšší úrovni využití. Je také úrovně Standard základní poplatek za 10 za měsíc, která umožňuje provádět operace 12,5 milionů měsíčně bez dalších poplatků.

**Premium** vrstvy nabízí izolaci prostředků v rovině CPU a paměti, aby každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Pro každý obor názvů Service Bus Premium můžete koupit 1, 2 nebo 4 jednotky zasílání zpráv. Jedna úloha nebo entita může zabírat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv se dá změnit podle libosti, ale fakturuje se podle 24hodinoví/denní sazby. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus. Vedle toho, že je tento výkon předvídatelnější, je také rychlejší.

Všimněte si, že poplatků základní úroveň Standard je účtován pouze jednou za měsíc na předplatné Azure. To znamená, po vytvoření oboru názvů Service Bus jednu standardní vrstvy, můžete vytvořit libovolný počet dalších standardní obory názvů tak, jak chcete v rámci této stejného předplatného Azure, aniž by docházelo k základní spojené další poplatky.

[Ceny služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabulka shrnuje funkční rozdíly mezi úrovních Standard a Premium.

## <a name="messaging-operations"></a>Operace zasílání zpráv

Fronty a témata nebo odběry fakturují se za "operace," není jednotlivých zpráv. Operace se odkazuje na všechny volání rozhraní API proti fronta nebo téma/odběr koncového bodu služby. Patří sem operace správy, odeslání/přijetí a stavu relace.

| Typ operace | Popis |
| --- | --- |
| Správa |Vytváření, čtení, aktualizaci, odstranění (CRUD) před fronty a témata nebo předplatných. |
| Zasílání zpráv |Odesílat a přijímat zprávy z fronty nebo témata nebo předplatných. |
| Stav relace |Získání nebo nastavení stavu relace na fronta nebo téma/odběr. |

Náklady na podrobnosti najdete v tématu ceny uvedené na [ceny služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) stránky.

## <a name="brokered-connections"></a>Zprostředkovaná připojení

*Zprostředkované připojení* zohlednit vzorce, které zahrnují velké množství "trvalé připojené" odesílatelé nebo příjemci před fronty, témata a odběry. Trvalé připojené odesílatelé nebo příjemci jsou ty, které se připojují prostřednictvím protokolu AMQP nebo HTTP s nenulovou hodnotou přijímat časový limit (například HTTP dlouhé dotazování). HTTP odesílateli a příjemci s okamžitou časový limit negenerují zprostředkovaná připojení.

Připojení kvóty a omezení jiné služby najdete v tématu [Service Bus kvóty](service-bus-quotas.md) článku. Další informace o zprostředkovaná připojení najdete v tématu [– nejčastější dotazy](#faq) později v tomto článku.

Úroveň Standard počty využití agregační zprostředkované připojení mezi předplatné Azure a odstraňuje omezení zprostředkovaných připojení na obor názvů. Další informace najdete v tématu [zprostředkované připojení](https://azure.microsoft.com/pricing/details/service-bus/) tabulky.

> [!NOTE]
> 1 000 zprostředkované připojení jsou součástí zasílání zpráv úrovně Standard (prostřednictvím základní zdarma) a mohou být sdíleny všechny fronty, témata a odběry v rámci přidružené předplatné Azure.
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

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Jaké jsou zprostředkované připojení a jak mohu získat účtovat poplatek za je?

Zprostředkované připojení se definuje jedním z těchto způsobů:

1. AMQP připojení z klienta do fronty Service Bus nebo tématu nebo předplatného.
2. Volání HTTP při příjmu zprávy z tématu nebo fronty Service Bus, které má hodnotu časového limitu příjmu větší než nula.

Service Bus poplatky za nejvyšší počet souběžných zprostředkovaná připojení, které překračují zahrnuté množství (1000 na vrstvě Standard). Špičky se měří po hodinách. Výsledek měření se vydělí 744 hodinami za měsíc a přičte se k měsíčnímu fakturačnímu období. Zahrnuté množství (1 000 hodin zprostředkovaných připojení za měsíc) se na konci fakturačního období porovná s úhrnem poměrných hodinových špiček.

Příklad:

1. Každý z 10 000 zařízení připojí pomocí jednoho připojení AMQP a přijímá příkazy z tématu Service Bus. Zařízení odesílat telemetrická data události do centra událostí. Pokud se všechna zařízení připojit 12 hodin denně, tyto připojení poplatky (kromě jiných Service Bus tématu poplatků): 10 000 připojení * 12 hodin * 31 dnů / 744 = 5 000 zprostředkované připojení. Po měsíční příspěvek ve výši 1 000 zprostředkované připojení by se vám účtovat pro 4000 zprostředkovaná připojení ve výši 0.03 za zprostředkované připojení celkem $120.
2. 10 000 zařízení příjem zpráv z fronty Service Bus prostřednictvím protokolu HTTP, zadání nenulové vypršení časového limitu. Pokud se všechna zařízení připojit 12 hodin denně, zobrazí se následující poplatky připojení (kromě jiných Service Bus poplatků): 10 000 HTTP přijímat připojení * 12 hodin za den * 31 dnů nebo hodin 744 = 5 000 zprostředkované připojení.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Vztahují se poplatky za připojení na fronty a předplatná?

Ano. Za odesílání událostí přes HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Příjem událostí s protokolem HTTP pomocí vypršení časového limitu větší než nula, které se někdy označuje jako "dlouhé dotazování," generuje náklady zprostředkované připojení. Připojení AMQP generují poplatky za zprostředkované připojení bez ohledu na to, jestli se připojení použije k odeslání nebo přijetí. Prvních 1000 zprostředkovaná připojení mezi všechny standardní obory názvů v předplatné Azure jsou zahrnuty bez dalších poplatků (kromě základní zdarma). Protože tyto příspěvky dostatek tak, aby pokrýval scénáře zasílání zpráv mnoha service-to-service, náklady zprostředkované připojení obvykle pouze stát relevantní, pokud máte v úmyslu použít protokol AMQP nebo HTTP dlouho dotazování s velkým počtem klientů. Chcete-li například dosažení efektivnější vysílání datového proudu událostí nebo povolit obousměrnou komunikaci s mnoha zařízení nebo instancí aplikace.

## <a name="next-steps"></a>Další postup

* Úplné podrobnosti o cenách služby Service Bus, najdete v článku [Service Bus stránce s cenami](https://azure.microsoft.com/pricing/details/service-bus/).
* Najdete v článku [nejčastější dotazy týkající se Service Bus](service-bus-faq.md#pricing) pro některé běžné nejčastější dotazy o službě Service bus ceny a fakturace.

[Azure portal]: https://portal.azure.com
