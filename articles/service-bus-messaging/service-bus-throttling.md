---
title: Přehled omezení azure service bus | Dokumenty společnosti Microsoft
description: Přehled omezení service busu – standardní a prémiové úrovně.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598285"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Omezení operací na Azure Service Bus

Cloudová nativní řešení poskytují představu o neomezených prostředcích, které lze škálovat s vaší úlohou. I když tento pojem je více platí v cloudu, než je s místními systémy, stále existují omezení, které existují v cloudu.

Tato omezení může způsobit omezení požadavků klientských aplikací v úrovních Standard i Premium, jak je popsáno v tomto článku. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Omezení na úrovni Azure Service Bus Standard

Úroveň Azure Service Bus Standard funguje jako nastavení pro více klientů s cenovým modelem s průběžnými platbami. Zde více oborů názvů ve stejném clusteru sdílet přidělené prostředky. Standardní úroveň je doporučenou volbou pro vývojářská, testovací a qa prostředí spolu s produkčními systémy s nízkou propustností.

V minulosti azure service bus měl hrubé omezení omezení výhradně závislé na využití prostředků. Existuje však možnost upřesnit logiku omezení a poskytnout předvídatelné omezení chování pro všechny obory názvů, které sdílejí tyto prostředky.

Ve snaze zajistit spravedlivé využití a distribuci prostředků ve všech oborech názvů Azure Service Bus Standard, které používají stejné prostředky, byla logika omezení upravena tak, aby byla založena na kreditu.

> [!NOTE]
> Je důležité si uvědomit, že omezení ***není nic nového*** pro Azure Service Bus nebo žádné cloudové nativní služby.
>
> Omezení založené na kreditech je jednoduše zpřesnění způsobu, jakým různé obory názvů sdílejí prostředky v prostředí úrovně Standard s více tenanty, a tím umožňuje spravedlivé využití všemi obory názvů, které sdílejí prostředky.

### <a name="what-is-credit-based-throttling"></a>Co je omezení založené na úvěrech?

Omezení na základě úvěru omezuje počet operací, které lze provést v daném oboru názvů v určitém časovém období. 

Níže je pracovní postup pro omezení založené na úvěrech - 

  * Na začátku každého časového období poskytujeme každému oboru názvů určitý počet kreditů.
  * Všechny operace prováděné klientskými aplikacemi odesílatele nebo příjemce se započítávají do těchto kreditů (a odečtou od dostupných kreditů).
  * Pokud jsou kredity vyčerpány, následné operace budou omezeny až do začátku dalšího časového období.
  * Kredity jsou doplňovány na začátku dalšího časového období.

### <a name="what-are-the-credit-limits"></a>Jaké jsou úvěrové limity?

Úvěrové limity jsou v současné době nastaveny na 1000 kreditů každou sekundu (na obor názvů).

Ne všechny operace jsou stvořeni sobě rovni. Zde jsou úvěrové náklady na každou z operací - 

| Operace | Kreditní náklady|
|-----------|-----------|
| Datové operace (Odeslat, SendAsync, Příjem, ReceiveAsync, Peek) |1 kredit na zprávu |
| Operace správy (vytvořit, číst, aktualizovat, odstranit ve frontách, témata, odběry, filtry) | 10 kreditů |

> [!NOTE]
> Vezměte prosím na vědomí, že při odesílání na téma, každá zpráva je vyhodnocena proti filtry před zpřístupněním na předplatné.
> Každé vyhodnocení filtru se také započítává do limitu úvěru (tj. 1 kredit na vyhodnocení filtru).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak poznám, že mě škrtí?

Při požadavku klientské aplikace jsou omezeny, bude níže uvedená odpověď serveru přijata vaší aplikací a zaznamenány.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Jak se mohu vyhnout škrticí?

Se sdílenými prostředky je důležité vynutit nějaký druh spravedlivého využití v různých oborech názvů service bus, které tyto prostředky sdílejí. Omezení zajišťuje, že všechny špičky v jedné pracovní zátěže nezpůsobí další úlohy na stejné prostředky, které mají být omezeny.

Jak je uvedeno dále v článku, neexistuje žádné riziko v omezení, protože klientské sady SDK (a další nabídky Azure PaaS) mají výchozí zásady opakování integrované do nich. Všechny omezené požadavky budou opakovány s exponenciálním backoffem a nakonec projdou, když jsou titulky doplněny.

Je pochopitelné, že některé aplikace mohou být citlivé na omezení. V takovém případě se doporučuje [migrovat aktuální obor názvů Service Bus Standard na premium](service-bus-migrate-standard-premium.md). 

Při migraci můžete přidělit vyhrazené prostředky do oboru názvů Service Bus a odpovídajícím způsobem vertikálně navýšit kapacitu prostředků, pokud dojde ke špičce v zatížení a snížit pravděpodobnost omezení. Navíc při zatížení sníží na normální úroveň, můžete vertikálně snížit kapacitu prostředků přidělených do oboru názvů.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Omezení na úrovni Azure Service Bus Premium

Úroveň [Azure Service Bus Premium](service-bus-premium-messaging.md) přiděluje vyhrazené prostředky, pokud jde o jednotky zasílání zpráv, každému nastavení oboru názvů zákazníkem. Tyto vyhrazené prostředky poskytují předvídatelnou propustnost a latenci a jsou doporučeny pro systémy s vysokou propustností nebo citlivými systémy produkčního stupně.

Kromě toho úroveň Premium také umožňuje zákazníkům navýšit kapacitu jejich propustnost, když dojde ke špičce v zatížení.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Jak funguje omezení v service bus premium?

Při přidělování výhradních prostředků pro service bus premium je omezení čistě řízeno omezeními prostředků přidělených oboru názvů.

Pokud počet požadavků jsou větší než aktuální prostředky mohou služby, pak požadavky budou omezeny.

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak poznám, že mě škrtí?

Existují různé způsoby, jak identifikovat omezení v Azure Service Bus Premium - 
  * **Omezené požadavky** se zobrazí na metriky požadavku na monitorování Azure k [identifikaci,](service-bus-metrics-azure-monitor.md#request-metrics) kolik požadavků bylo omezeno.
  * Vysoké **využití procesoru** označuje, že aktuální přidělení prostředků je vysoká a požadavky může získat omezení, pokud aktuální zatížení nesnižuje.
  * Vysoké **využití paměti** označuje, že aktuální přidělení prostředků je vysoká a požadavky může získat omezení, pokud aktuální zatížení nesnižuje.

### <a name="how-can-i-avoid-being-throttled"></a>Jak se mohu vyhnout škrticí?

Vzhledem k tomu, že obor názvů Service Bus Premium již obsahuje vyhrazené prostředky, můžete snížit možnost omezení tím, že zvýšíte počet jednotek zasílání zpráv přidělených vašemu oboru názvů v události (nebo předvídání) špičky v zatížení.

Škálování nahoru/dolů lze dosáhnout vytvořením [runbooků,](../automation/automation-create-alert-triggered-runbook.md) které lze aktivovat změnami ve výše uvedených metrikách.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="how-does-throttling-affect-my-application"></a>Jak omezení ovlivňuje mou aplikaci?

Pokud je požadavek omezen, znamená to, že služba je zaneprázdněna, protože čelí více požadavkům, než prostředky umožňují. Pokud je stejná operace vyzkoušena znovu po několika okamžicích, jakmile služba pracuje prostřednictvím aktuálního pracovního vytížení, může být požadavek dodržen.

Vzhledem k tomu, že omezení je očekávané chování jakékoli nativní služby cloudu, jsme vytvořili logiku opakování do samotné sady Azure Service Bus SDK. Výchozí hodnota je nastavena na automatické opakování s exponenciální back-off zajistit, že nemáme stejný požadavek je omezen pokaždé.

Výchozí logika opakování se bude vztahovat na každou operaci.

### <a name="does-throttling-result-in-data-loss"></a>Má omezení za následek ztrátu dat?

Azure Service Bus je optimalizovaná pro trvalost, zajistíme, že všechna data odeslaná do service bus se zavázala k úložišti, než služba potvrdí úspěch požadavku.

Jakmile je požadavek úspěšně "Potvrzení" (potvrzeno) service bus, znamená to, že service bus úspěšně zpracoval požadavek. Pokud Service Bus vrátí 'NACK' (selhání), pak znamená, že Service Bus nebyl schopen zpracovat požadavek a klientská aplikace musí opakovat požadavek.

Však při požadavku je omezen, služba znamená, že nemůže přijmout a zpracovat požadavek právě teď z důvodu omezení prostředků. To **neznamená** žádnou ztrátu dat, protože service bus jednoduše nezkoumal požadavek. V tomto případě spoléhání se na výchozí zásady opakování service bus SDK zajišťuje, že požadavek je nakonec zpracována.

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv service bus naleznete v následujících pokročilých tématech:

* [Přehled zasílání zpráv service bus](service-bus-messaging-overview.md)
* [Rychlý start: Odesílání a přijímání zpráv pomocí webu Azure Portal a architektury .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizace zásob pomocí portálu Azure Portal a témat/odběrů](service-bus-tutorial-topics-subscriptions-portal.md)

