---
title: Přehled omezování Azure Service Bus | Microsoft Docs
description: Přehled omezení Service Bus – úrovně Standard a Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85340951"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Omezování operací na Azure Service Bus

Nativní cloudová řešení poskytují pojem neomezených prostředků, které se můžou škálovat pomocí vašich úloh. I když je tento pojem v cloudu více pravdivý než v místních systémech, stále existují omezení, která existují v cloudu.

Tato omezení mohou způsobit omezování požadavků klientských aplikací v úrovních Standard a Premium, jak je popsáno v tomto článku. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Omezování na úrovni Standard Azure Service Bus

Úroveň Azure Service Bus úrovně Standard funguje jako víceklientské nastavení s průběžnými platbami. Zde je více oborů názvů ve stejném clusteru, které mají přidělené prostředky. Úroveň Standard je doporučená volba pro prostředí pro vývojáře, testování a QA spolu s provozními systémy s nízkou propustností.

V minulosti měly Azure Service Bus omezení omezení výhradně závislé na využití prostředků. Existuje však příležitost k upřesnění logiky omezování a zajištění předvídatelného chování omezení pro všechny obory názvů, které sdílejí tyto prostředky.

V rámci pokusu o zajištění spravedlivého využití a distribuce prostředků ve všech oborech názvů Azure Service Bus Standard, které používají stejné prostředky, byla logika omezování změněna tak, aby byla založená na kreditech.

> [!NOTE]
> Je důležité si uvědomit, že omezování nepředstavuje ***nové*** Azure Service Bus ani žádnou cloudovou nativní službu.
>
> Omezování na základě kreditů jednoduše usnadňuje způsob, jakým jednotlivé obory názvů sdílejí prostředky v prostředí úrovně Standard s více klienty, a tím umožňuje spravedlivé použití všemi obory názvů, které sdílejí prostředky.

### <a name="what-is-credit-based-throttling"></a>Co je omezování na základě kreditu?

Omezování založené na kreditech omezuje počet operací, které mohou být provedeny na daném oboru názvů v konkrétním časovém období. 

Níže je uvedený pracovní postup omezení na základě kreditu – 

  * Na začátku každého časového období poskytujeme pro každý obor názvů určitý počet kreditů.
  * Jakékoli operace prováděné klientskými aplikacemi odesílatele nebo přijímače se započítávají na tyto kredity (a odečtou se od dostupných kreditů).
  * Pokud jsou kredity vyčerpány, následné operace budou omezeny až do začátku příštího časového období.
  * Kredity jsou doplněny na začátku dalšího časového období.

### <a name="what-are-the-credit-limits"></a>Jaké jsou limity kreditů?

Limity kreditů jsou aktuálně nastavené na kredity "1000" každou sekundu (na obor názvů).

Ne všechny operace jsou vytvořeny jako stejné. Zde jsou kreditní náklady na jednotlivé operace – 

| Operace | Náklady na kredit|
|-----------|-----------|
| Datové operace (Send, SendAsync, Receive, metody ReceiveAsync, prohlížet) |1 kredit na zprávu |
| Operace správy (vytváření, čtení, aktualizace, odstranění ve frontách, témata, odběry, filtry) | 10 kreditů |

> [!NOTE]
> Pamatujte na to, že při odeslání do tématu se každá zpráva před tím, než je v předplatném k dispozici, vyhodnotí proti filtrům.
> Každé vyhodnocení filtru se také počítá na základě limitu kreditu (tj. 1 kredit na vyhodnocení filtru).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak zjistím, že se omezujem?

Pokud jsou požadavky klientské aplikace omezené, obdrží vaše aplikace a protokoluje níže uvedenou odpověď serveru.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Jak se můžu vyhnout omezení?

Se sdílenými prostředky je důležité vyhodnotit určité množství poctivého využití v různých Service Bus oborech názvů, které tyto prostředky sdílejí. Omezování zajišťuje, že jakákoli špička v jedné úloze nezpůsobí omezení jiných úloh na stejných prostředcích.

Jak je uvedeno dále v tomto článku, nedochází k omezení rizik, protože klientské sady SDK (a další nabídky Azure PaaS) mají předdefinované výchozí zásady opakování. Všechny omezené žádosti se budou opakovat pomocí exponenciálního omezení rychlosti a nakonec procházejí, když se kredity dokončí.

Pochopení může být u některých aplikací citlivé na omezení. V takovém případě se doporučuje [migrovat aktuální obor názvů Service Bus Standard do úrovně Premium](service-bus-migrate-standard-premium.md). 

Při migraci můžete přidělit vyhrazené prostředky do oboru názvů Service Bus a vhodně škálovat prostředky, pokud máte špičku v zatížení a snížit pravděpodobnost omezení. Pokud se navíc vaše zatížení sníží na normální úrovně, můžete škálovat prostředky přidělené vašemu oboru názvů.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Omezování na úrovni Azure Service Bus Premium

Úroveň [Premium Azure Service Bus](service-bus-premium-messaging.md) přiděluje vyhrazené prostředky v souvislosti s jednotkami pro zasílání zpráv každému nastavení oboru názvů zákazníkem. Tyto vyhrazené prostředky poskytují předvídatelné propustnost a latenci a jsou doporučovány pro systémy vysoké propustnosti nebo citlivé na produkční úrovni.

Úroveň Premium také zákazníkům umožňuje škálovat kapacitu propustnosti, když se na nich setkávají špičky v zatížení.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Jak omezuje práci v Service Bus Premium?

Díky exkluzivnímu přidělení prostředků pro Service Bus Premium je omezování čistě založené na omezeních prostředků, které jsou přiděleny oboru názvů.

Pokud je počet požadavků větší, než je možné, že se aktuální prostředky můžou vycházet z provozu, požadavky budou omezené.

### <a name="how-will-i-know-that-im-being-throttled"></a>Jak zjistím, že se omezujem?

Existují různé způsoby identifikace omezení v Azure Service Bus Premium – 
  * **Omezené požadavky** se zobrazují na [Azure monitor metriky žádostí](service-bus-metrics-azure-monitor.md#request-metrics) o identifikaci počtu požadavků, které byly omezeny.
  * Vysoké **využití procesoru** znamená, že aktuální přidělení prostředků je vysoké a požadavky můžou být omezené, pokud se aktuální zatížení neomezuje.
  * Vysoké **využití paměti** znamená, že aktuální přidělení prostředků je vysoké a požadavky můžou být omezené, pokud se aktuální zatížení nesníží.

### <a name="how-can-i-avoid-being-throttled"></a>Jak se můžu vyhnout omezení?

Vzhledem k tomu, že obor názvů Service Bus Premium již obsahuje vyhrazené prostředky, můžete snížit možnost snížení kapacity tím, že v události (nebo předvídání) špičky v rámci úlohy omezíte počet jednotek zasílání zpráv, které jsou přiděleny vašemu oboru názvů.

Horizontální navýšení/snížení kapacity se dá dosáhnout vytvořením [runbooků](../automation/automation-create-alert-triggered-runbook.md) , které se můžou aktivovat změnami ve výše uvedených metrikách.

## <a name="faqs"></a>Nejčastější dotazy

### <a name="how-does-throttling-affect-my-application"></a>Jaký vliv má omezování na moji aplikaci?

Když je požadavek omezený, znamená to, že je služba zaneprázdněná, protože se týká více požadavků, než umožňují prostředky. Pokud se stejná operace zopakuje znovu za chvíli, poté, co služba dokončí svou aktuální úlohu, je možné žádost akceptovat.

Vzhledem k tomu, že omezování je očekávané chování jakékoli cloudové nativní služby, sestavili jsme logiku opakování do Azure Service Bus samotné sady SDK. Výchozí nastavení je automatické opakování s exponenciálním zálohováním, aby se zajistilo, že se pokaždé neomezuje stejný požadavek.

Výchozí logika opakování bude platit pro každou operaci.

### <a name="does-throttling-result-in-data-loss"></a>Znamená to omezení ztráty dat?

Azure Service Bus je optimalizované pro stálost, zajišťujeme, aby všechna data odesílaná do Service Bus byla uložena do úložiště předtím, než služba potvrdí úspěch žádosti.

Po úspěšném potvrzení žádosti o přijetí (potvrzení) Service Bus znamená, že Service Bus úspěšně zpracoval požadavek. Pokud Service Bus vrátí "NACK" (selhání), znamená to, že Service Bus nebylo možné zpracovat požadavek a klientská aplikace musí požadavek opakovat.

Pokud je ale požadavek omezený, služba neznamená, že nemůže požadavek přijmout a zpracovat hned z důvodu omezení prostředků. To neznamená, že **by došlo ke** ztrátě dat, protože Service Bus v žádosti nikdo prohlédl. V takovém případě se spoléhá na výchozí zásady opakování Service Bus SDK zajistí, že se požadavek nakonec zpracuje.

## <a name="next-steps"></a>Další kroky

Další informace a příklady použití zasílání zpráv Service Bus najdete v následujících tématech pokročilých:

* [Přehled Service Busho zasílání zpráv](service-bus-messaging-overview.md)
* [Rychlý start: Odesílání a přijímání zpráv pomocí webu Azure Portal a architektury .NET](service-bus-quickstart-portal.md)
* [Kurz: Aktualizace zásob pomocí portálu Azure Portal a témat/odběrů](service-bus-tutorial-topics-subscriptions-portal.md)

