---
title: Nejčastější dotazy k Azure Event Hubs | Microsoft Docs
description: Nejčastější dotazy (FAQ) Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 06/07/2018
ms.author: sethm
ms.openlocfilehash: 4c28f1ae5517e54fab259e680128d6c666c82fbe
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235307"
---
# <a name="event-hubs-frequently-asked-questions"></a>Služba Event Hubs a nejčastější dotazy

## <a name="general"></a>Obecné

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaký je rozdíl mezi základní centra událostí a standardní úrovně?

Úroveň Standard služby Azure Event Hubs poskytuje funkce nad rámec k dispozici v základní vrstvě. Následující funkce jsou standardní součástí:
* Uchování delší událostí
* Další zprostředkovaná připojení s Nadlimitní poplatek za více než číslo zahrnuté
* Více než jedné skupiny příjemců
* [Zachycení](event-hubs-capture-overview.md)

Další informace o cenových úrovní, včetně vyhrazené centra událostí, najdete v článku [podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Jaké jsou jednotky propustnosti centra událostí?

Můžete vybrat explicitně jednotky propustnosti centra událostí, buď prostřednictvím portálu Azure nebo šablony správce prostředků centra událostí. Jednotky propustnosti, na které se týkají všech centrech událostí v oboru názvů služby Event Hubs a jednotlivých jednotek propustnosti obor názvů opravňuje k použití následující možnosti:

* Až 1 MB za sekundu události příchozího (událostí odeslaných do centra událostí), ale žádné více než 1 000 události příchozího, operace správy nebo ovládacího prvku volání rozhraní API za sekundu.
* Až 2 MB za sekundu odchozí události (událostí spotřebované z centra událostí).
* Až 84 GB úložiště událostí (dostatečné pro výchozí dobu uchování, která je 24 hodin).

Jednotky propustnosti centra událostí se účtují HODINOVĚ, založené na maximální počet jednotek vybraný během zadané hodiny. Můžete automaticky [zvýšit počet jednotek propustnosti](event-hubs-auto-inflate.md) při rostoucí vašeho využití.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Jak se vynucují omezení jednotka propustnosti centra událostí?
Překročí-li propustnost celkový příjem příchozích dat nebo rychlost celkový příjem příchozích dat událostí ve všech centrech událostí v oboru názvů jednotky příspěvky agregovanou propustnost, odesílatelé jsou omezené a přijímat chyby naznačující, že došlo k překročení kvóty příchozí.

Překročí-li celkový odchozí propustnost nebo rychlost odchozí celkový počet událostí ve všech centrech událostí v oboru názvů jednotky příspěvky agregovanou propustnost, příjemci jsou omezené a přijímat chyby naznačující, že byla překročena kvóta odchozí. Příchozí a odchozí kvóty se vynucují samostatně, tak, aby žádné odesílatele může způsobit příjmu událostí zpomalit ani příjemce zabránit události odesílány do centra událostí.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Je nějaký limit počtu jednotek propustnosti, který jde vybrat?
Je výchozí kvótu 20 jednotek propustnosti na obor názvů. Vyplnění lístek podpory, může vyžádat větší kvótu jednotek propustnosti. Překračuje limit jednotky propustnosti 20 sady jsou k dispozici v jednotky propustnosti 20 a 100. Všimněte si, že pomocí více než 20 jednotky propustnosti odebere umožňuje změnit počet jednotek propustnosti bez vyplnění lístek podpory.

Pomocí [zvýšilo automaticky](event-hubs-auto-inflate.md) funkce, můžete automaticky zvýšit počet jednotek propustnosti při rostoucí vašeho využití.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Může používat jednoho připojení AMQP odesílat a přijímat z více event hubs?
Ano, pokud jsou všechny služby event hubs ve stejném oboru názvů.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Jaký je interval maximální doba uchování pro události?
Maximální doba uchování období 7 dní v současné době podporuje úrovně Standard centra událostí. Centra událostí nejsou určená k trvalému ukládání dat. Období uchovávání dat kratší než 24 hodin, které jsou určené pro scénáře, ve kterých je vhodné o přehrání datového proudu událostí do stejné systémy; Chcete-li například cvičení nebo ověřte nový model strojového učení na existující data. Pokud třeba zprávy uchování za 7 dní, povolení [zaznamenat centra událostí](event-hubs-capture-overview.md) na událost rozbočovače získává data ze Centrum událostí k účtu úložiště nebo účet služby Azure Data Lake dle vlastního výběru. Povolení zachycení způsobuje poplatků, podle vašeho zakoupené jednotky propustnosti.

### <a name="where-is-azure-event-hubs-available"></a>Kde je k dispozici Azure Event Hubs?

Azure Event Hubs je k dispozici ve všech oblastech podporovaných Azure. Seznam, naleznete [oblastí Azure](https://azure.microsoft.com/regions/) stránky.  

## <a name="best-practices"></a>Osvědčené postupy

### <a name="how-many-partitions-do-i-need"></a>Kolik oddíly potřebuji?

Všimněte si, že počet oddílů v Centru událostí nelze změnit po dokončení instalace. Si uvědomit je důležité si myslíte o tom, kolik oddíly je třeba před Začínáme. 

Služba Event Hubs je navržena k umožnění čtečku jeden oddíl na skupinu uživatelů. Ve většině případů použití stačí výchozí nastavení čtyři oddíly. Pokud hledáte škálování zpracování událostí, můžete zvážit přidání další oddíly. Neexistuje žádné omezení konkrétní propustnost pro oddíl, ale agregovanou propustnost v oboru názvů, je omezen počet jednotek propustnosti. Jak v oboru názvů zvýšíte počet jednotek propustnosti, můžete další oddíly, které mají-li povolit souběžných čtenářů, abyste dosáhli svých vlastních maximální propustnost.

Ale pokud je model, ve které má vaše aplikace spřažení na konkrétní oddíl, zvýšit počet oddílů nemusí být všech výhod, které pro vás. Další informace o tom najdete v tématu [dostupnosti a konzistence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-more-pricing-information"></a>Kde můžete najít další informace o cenách?
Kompletní informace o cenách služby Event Hubs naleznete v tématu [podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Je k dispozici zdarma pro zachování události Event Hubs pro víc než 24 hodin?
Úroveň Standard centra událostí povolit uchování zpráv období je delší než 24 hodin, maximálně 7 dnů. Pokud velikost celkový počet událostí uložených překračuje příspěvek na úložiště pro počet jednotek propustnosti vybrané (84 GB za jednotku propustnosti), velikost, která překračuje příspěvek na výši publikované rychlost úložiště objektů Blob v Azure. Příspěvek na úložiště v jednotlivých jednotek propustnosti pokrývá všechny náklady na úložiště pro období 24 hodin (výchozí) i v případě, že jednotka propustnosti se používá maximální příchozího příspěvek na.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak je velikost úložiště služby Event Hubs vypočítat a účtovat?
Celková velikost všech uložené události, včetně žádné vnitřní režie pro záhlaví události nebo na struktury úložiště disku ve všech centrech událostí se měří v průběhu dne. Na konci dne se vypočítá největší dosažená velikost úložiště. Povolené denní úložiště se vypočítá podle minimálního počtu jednotek propustnosti vybraných během dne (každá jednotka propustnosti má povolenou velikost 84 GB). Pokud celková velikost překračuje počítané denní příspěvek úložiště, nadbytečné úložiště se fakturuje pomocí ceníku úložiště objektů Blob v Azure (v **místně redundantní úložiště** rychlost).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak jsou vypočítávány události příchozího Event Hubs?
Jednotlivých událostí odeslaných do centra událostí se počítá jako fakturovatelný zprávu. *Příjem příchozích dat událostí* je definován jako jednotka data, která je menší než nebo rovna 64 KB. Událost, která je menší než nebo roven 64 KB velikostí se považuje za jeden fakturovatelný událostí. Pokud tato událost je větší než 64 KB, počet fakturovatelný událostí se vypočítává podle velikost události v násobcích 64 KB. Například 8 KB událostí odeslaných do centra událostí se fakturuje jako jednu událost, ale 96 KB zprávy odeslané do centra událostí se fakturuje jako dvě události.

Události spotřebované z centra událostí, a taky operace správy a řízení volání, jako například kontrolní body, se počítají jako fakturovatelný příjem příchozích dat událostí, ale nabíhat až povoleného užívání jednotek propustnosti.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Event Hubs vztahují náklady zprostředkované připojení?
Připojení poplatky jenom když se používá protokol AMQP. Za odesílání událostí přes HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Pokud máte v úmyslu použít AMQP (například k dosažení efektivnější vysílání datového proudu událostí nebo k povolení obousměrnou komunikaci v příkazu IoT a řízení scénáře), najdete v článku [informace o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) stránku Podrobnosti o tom, kolik připojení jsou zahrnuty v jednotlivých úrovních služeb.

### <a name="how-is-event-hubs-capture-billed"></a>Jak se funkce Event Hubs Capture účtuje?
Snímek se povolí, když má povolenou možnost zachycení žádné Centrum událostí v oboru názvů. Zaznamenat centra událostí se účtují HODINOVĚ za jednotku zakoupené propustnosti. Počet jednotek propustnosti zvětšit nebo zmenšit, zachycení událostí centra fakturace odráží tyto změny v přírůstcích po celou hodinu. Další informace o fakturaci zaznamenat centra událostí najdete v tématu [informace o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Bude možné účtován pro účet úložiště, kterou jsem vybral pro zachycení centra událostí?
Zachycení používá účet úložiště, který zadáte, pokud je povoleno v Centru událostí. Toto je váš účet úložiště, všechny změny pro tuto konfiguraci se účtují k předplatnému Azure.

## <a name="quotas"></a>Kvóty

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existují žádné kvóty přidružené služby Event Hubs?

Seznam všech kvót služby Event Hubs naleznete v tématu [kvóty](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované centrům událostí a jejich doporučované akce?

Seznam možných centra událostí výjimek najdete v tématu [výjimky přehled](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Služba Event Hubs podporuje dva typy [protokolů diagnostiky](event-hubs-diagnostic-logs.md) -zaznamenat protokoly chyb a operační protokoly – které jsou reprezentované ve formátu json i lze zapnout prostřednictvím portálu Azure.

### <a name="support-and-sla"></a>Podpora a SLA

Technická podpora pro Event Hubs je k dispozici prostřednictvím [komunitní fóra](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Podpora k fakturaci a správě předplatného se poskytuje zadarmo.

Další informace o naší smlouvě SLA najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) stránky.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Event Hubs Auto zvýšilo](event-hubs-auto-inflate.md)
