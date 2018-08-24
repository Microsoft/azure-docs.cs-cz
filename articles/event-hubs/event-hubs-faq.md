---
title: Nejčastější dotazy k Azure Event Hubs | Dokumentace Microsoftu
description: Azure Event Hubs – nejčastější dotazy (FAQ)
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/07/2018
ms.author: shvija
ms.openlocfilehash: 50491515c7a553107666e29dcba2b4bd0836f409
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746225"
---
# <a name="event-hubs-frequently-asked-questions"></a>Nejčastější dotazy k Event Hubs

## <a name="general"></a>Obecné

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaký je rozdíl mezi Event Hubs úrovně Basic a Standard?

Úroveň Standard služby Azure Event Hubs poskytuje funkce nad rámec co je k dispozici na úrovni Basic. Následující funkce jsou součástí Standard:

* Delší dobu uchování událostí
* Další zprostředkovaná připojení se při překročení limitu účtovat větší než číslo zahrnuté
* Více než jedné [skupina uživatelů](event-hubs-features.md#consumer-groups)
* [zachycení](event-hubs-capture-overview.md)

Další informace o cenových úrovních, včetně vyhrazená Služba Event Hubs, najdete v článku [podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Co jsou jednotky propustnosti služby Event Hubs?

Explicitně vybrat jednotky propustnosti služby Event Hubs, buď pomocí webu Azure portal nebo šablony Resource Manageru Event Hubs. Jednotky propustnosti platí pro všechna centra event hubs v oboru názvů služby Event Hubs a každou jednotkou propustnosti získává obor názvů následující možnosti:

* Až 1 MB událostí příchozího přenosu dat (událostí odeslaných do centra událostí), ale žádné více než 1 000 událostí příchozího přenosu dat, operace správy nebo ovládací prvek volání rozhraní API za sekundu.
* Až 2 MB za sekundu odchozího přenosu dat události (události spotřebované z centra událostí), ale ne více než 4096 událostí odchozího přenosu dat.
* Až 84 GB úložiště událostí (dostatečné pro výchozí dobu uchování, která je 24 hodin).

Jednotky propustnosti centra událostí se účtují po hodinách na základě maximální počtu vybraných jednotek během příslušné hodiny. Můžete automaticky [zvýšit počet jednotek propustnosti pomocí automatické rozšiřování](event-hubs-auto-inflate.md) jak se zvyšuje využití.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Jak se vynucují limity jednotky propustnosti služby Event Hubs?

Pokud příchozí přenos dat celkem propustnosti nebo frekvence událostí příchozího přenosu dat celkový počet ve všech centrech event hubs v oboru názvů překročí celkový počet přidělených jednotek propustnosti, odesílatelů jsou omezené a obdržíte chyby naznačující překročení kvóty pro příchozí přenos.

Pokud se propustnost celkový počet odchozího přenosu dat nebo frekvence celkový počet událostí odchozího přenosu dat ve všech centrech event hubs v oboru názvů překročí celkový počet přidělených jednotek propustnosti, příjemci jsou omezené a přijímat chyby naznačující překročení kvóty pro odchozí přenos. Kvóty příchozího a odchozího přenosu dat se uplatňují odděleně tak, aby žádný odesílatel nezpomalil spotřebu událostí způsobit ani příjemce zabránit událostí odesílaných do centra událostí.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Je nějaký limit počtu jednotek propustnosti, který jde vybrat?

Používá se výchozí kvóta 20 jednotek propustnosti za obor názvů. Vyšší kvóty jednotek propustnosti si můžete vyžádat vyplněním lístku podpory. Nad limit jednotek propustnosti 20 sady jsou k dispozici v 20 až 100 jednotek propustnosti. Všimněte si, že pomocí více než 20 jednotek propustnosti odebere změnit počet jednotek propustnosti bez vyplňování lístku podpory.

Použití [automatické rozšiřování](event-hubs-auto-inflate.md) funkce, můžete automaticky zvýšit počet jednotek propustnosti, jak se zvyšuje využití.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Můžete použít samostatné připojení AMQP k odeslání a příjem z více event hubs?

Ano, pokud jsou všechna centra event hubs v oboru názvů stejný.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Co je maximální doba uchovávání pro události?

Event Hubs úrovně Standard úroveň momentálně podporuje maximální doba uchování období 7 dní. Centra událostí nejsou určená k trvalému ukládání dat. Doby uchování delší než 24 hodin jsou určené pro scénáře, ve kterých je výhodné přehrát datového proudu událostí do stejných systémů; Chcete-li například natrénovat nebo ho ověřit nový model strojového učení na stávajících datech. Pokud potřebujete zprávy uchování nad rámec 7 dní, takže [Event Hubs Capture](event-hubs-capture-overview.md) na události rozbočovače načítá data z vašeho centra událostí do účtu úložiště nebo účet služby Azure Data Lake podle vašeho výběru. Povolení funkce zachytávání se účtují poplatky podle vaší zakoupené jednotky propustnosti.

### <a name="where-is-azure-event-hubs-available"></a>Pokud je k dispozici služby Azure Event Hubs?

Azure Event Hubs je k dispozici ve všech podporovaných oblastech Azure. Pro seznam, přejděte [oblastí Azure](https://azure.microsoft.com/regions/) stránky.  

## <a name="best-practices"></a>Osvědčené postupy

### <a name="how-many-partitions-do-i-need"></a>Počet oddílů budu potřebovat?

Všimněte si, že počet oddílů v Centru událostí nemůže modifikovat po dokončení instalace. To na paměti je potřeba přemýšlet o tom, kolik oddíly je třeba před zahájení práce. 

Event Hubs je navržena k umožnění čtečku jeden oddíl na skupinu uživatelů. Ve většině případů použití stačí výchozí nastavení čtyři oddíly. Pokud chcete ke škálování zpracování událostí, můžete zvážit přidání další oddíly. Neexistuje žádné omezení konkrétní propustnost na oddíl, ale celková propustnost ve vašem oboru názvů, je omezen počet jednotek propustnosti. Zvýšení počtu jednotek propustnosti ve vašem oboru názvů, možná budete chtít povolit souběžných čtenářů k dosažení vlastní maximální propustnost další oddíly.

Ale pokud máte model, ve které má vaše aplikace spřažení na konkrétní oddíl zvýšením počtu oddílů nemusí být jakékoli výhody. Další informace o tom, naleznete v tématu [dostupnost a konzistence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-more-pricing-information"></a>Kde najdu Další informace o cenách?

Kompletní informace o cenách služby Event Hubs najdete v tématu [podrobnosti o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Platí se poplatek za uchování událostí služby Event Hubs pro více než 24 hodin?

Standardní Event Hubs úrovně neumožňuje období delší než 24 hodin, maximálně 7 dnů uchovávání zpráv. Pokud velikost celkového počtu uložených událostí překročí úložiště pro počet vybraných jednotek propustnosti (84 GB za každou jednotku propustnosti), velikost, která překročí se účtuje za publikované sazby úložiště objektů Blob v Azure. Pokrývá povolené úložiště každé jednotky propustnosti všechny náklady na úložiště na dobu uchování 24 hodin (výchozí) i v případě, že jednotka propustnosti umožňuje maximálního povoleného příchozího přenosu.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak je velikost úložiště služby Event Hubs počítá a účtuje?

Celková velikost všech uložených událostí, včetně interní rezervy na záhlaví událostí nebo na strukturu diskového úložiště ve všech centrech event hubs, se měří průběžně během dne. Na konci dne se vypočítá největší dosažená velikost úložiště. Povolené denní úložiště se vypočítá podle minimálního počtu jednotek propustnosti vybraných během dne (každá jednotka propustnosti má povolenou velikost 84 GB). Pokud celková velikost překročí počítané denní povolené úložiště, účtuje se překročené úložiště podle sazeb Azure Blob storage (v **místně redundantní úložiště** rychlost).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak se počítají událostí příchozího přenosu dat služby Event Hubs?

Každá událost odeslaná do centra událostí se počítá jako Účtovaná zpráva. *Událost příchozího přenosu dat* se definuje jako jednotka dat, která je menší než nebo rovna 64 KB. Událost, která je menší než nebo rovna 64 KB velikost se považuje za jednu účtovanou událost. Pokud je událost větší než 64 KB, vypočítá se počet účtovaných událostí podle velikosti události v násobcích 64 KB. Například událost velikosti 8 KB odeslaná do centra událostí se účtuje jako jedna událost, ale zpráva velikosti 96 KB odeslaná do centra událostí se účtuje jako dvě události.

Události spotřebované z centra událostí, jak dobře jako volání ovládacího prvku, jako jsou kontrolní body a operace správy, se nepočítají události fakturovatelné příchozího přenosu dat, ale kumulovat až jednotek propustnosti.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Nevztahují poplatky za zprostředkované připojení do služby Event Hubs?

Poplatky za připojení použije, pouze pokud je použit protokol AMQP. Za odesílání událostí přes HTTP se neúčtují žádné poplatky za připojení, bez ohledu na počet odesílajících systémů nebo zařízení. Pokud plánujete protokol AMQP (pro příklad, chcete efektivněji Streamovat události nebo zapnout obousměrnou komunikaci v příkazu IoT a řídit scénáře) použít, najdete v článku [informace o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) stránku podrobné informace o tom, kolik připojení jsou zahrnuté v jednotlivých úrovních služby.

### <a name="how-is-event-hubs-capture-billed"></a>Jak se funkce Event Hubs Capture účtuje?

Zachycení je povolena, když má libovolné Centrum událostí v oboru názvů povolenou možnost zachycení. Funkce Event Hubs Capture se účtuje po hodinách na základě zakoupených jednotek propustnosti. Když je počet jednotek propustnosti zvýší nebo sníží, účtování funkce Event Hubs Capture odráží tyto změny v přírůstcích po celých hodinách. Další informace o Event Hubs Capture fakturaci najdete v tématu [informace o cenách služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Se mi bude účtovat účet úložiště, který vyberu pro Event Hubs Capture?

Capture využívá účet úložiště, které poskytnete při povolené v Centru událostí. Toto je váš účet úložiště, jakékoli změny pro tuto konfiguraci se účtují na vaše předplatné Azure.

## <a name="quotas"></a>Kvóty

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existují nějaké kvóty spojené s Event Hubs?

Seznam všech kvótách služby Event Hubs najdete v tématu [kvóty](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované služby Event Hubs a jejich doporučené akce?

Seznam možných výjimky služby Event Hubs najdete v tématu [výjimky přehled](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnostické protokoly

Event Hubs podporuje dva typy [diagnostické protokoly](event-hubs-diagnostic-logs.md) -zachycení a protokoly chyb a provozní protokoly – které jsou reprezentovány ve formátu json a je možné zapnout na webu Azure portal.

### <a name="support-and-sla"></a>Podpora a SLA

Technická podpora Event Hubs je k dispozici prostřednictvím [komunitní fóra](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Podpora k fakturaci a správě předplatného se poskytuje zadarmo.

Další informace o smlouvě SLA najdete v tématu [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/) stránky.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Event Hubs automatické rozšiřování](event-hubs-auto-inflate.md)
