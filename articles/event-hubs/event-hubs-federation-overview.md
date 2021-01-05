---
title: Federace více lokalit a více oblastí – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje přehled o federaci více lokalit a více oblastí pomocí Azure Event Hubs.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 7deb6fe04241225f1f97a204cc62b4aefad9f440
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97804033"
---
# <a name="multi-site-and-multi-region-federation"></a>Federace více lokalit a více oblastí

Mnoho propracovaných řešení vyžaduje, aby byly stejné datové proudy událostí k dispozici pro použití ve více umístěních, nebo vyžaduje, aby datové proudy událostí byly shromažďovány ve více umístěních a poté konsolidovány do konkrétního umístění pro spotřebu. Často je potřeba rozšířit nebo snížit datové proudy událostí nebo provádět převody formátu událostí, a to i v rámci jedné oblasti a řešení.

Prakticky to znamená, že vaše řešení bude uchovávat více Event Hubs, často v různých oblastech a Event Hubsch oborech názvů, a pak mezi nimi replikovat události. Můžete také vyměňovat události se zdroji a cíli, jako je [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)nebo [Apache Kafka](https://kafka.apache.org). 

Udržování více aktivních Event Hubs v různých oblastech taky umožňuje klientům vybírat a přepínat mezi nimi, pokud se jejich obsah slučuje. díky tomu je celkový odolný systém proti problémům s regionální dostupností.

Tato "federace" kapitola vysvětluje vzory federace a způsob, jak tyto vzory realizovat pomocí [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) bez serveru, nebo [Azure Functions][1] modulu runtime, s možností mít v cestě toku událostí vlastní transformaci nebo kód pro obohacení. 

## <a name="federation-patterns"></a>Vzory federace

K dispozici je celá řada možných motivů, proč můžete chtít přesunout události mezi různými Event Hubs nebo jinými zdroji a cíli a vytvořit výčet nejdůležitějších vzorů v této části a také vytvořit odkaz na podrobnější pokyny k příslušnému vzoru. 

- [Odolnost proti událostem regionální dostupnosti](#resiliency-against-regional-availability-events)
- [Optimalizace latence](#latency-optimization)
- [Ověřování, snižování a obohacení](#validation-reduction-and-enrichment)
- [Integrace se službou Analytics Services](#integration-with-analytics-services)
- [Konsolidace a normalizace datových proudů událostí](#consolidation-and-normalization-of-event-streams)
- [Rozdělení a směrování datových proudů událostí](#splitting-and-routing-of-event-streams)
- [Projekce protokolu](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Odolnost proti událostem regionální dostupnosti 

![Dostupnost podle oblastí](media/event-hubs-federation-overview/regional-availability.jpg)

I když jsou maximální dostupnost a spolehlivost nejdůležitějšími provozními prioritami Event Hubs, existuje mnoho způsobů, jak může producent nebo spotřebitel zabránit v komunikaci s přiřazeným "primárním" centrem událostí kvůli potížím se sítí nebo překladem IP adres nebo v případě, kdy může centrum událostí skutečně dočasně nereagovat nebo vracet chyby. 

Tyto podmínky nejsou "katastrofální důsledky", takže budete chtít opustit regionální nasazení úplně, jak můžete dělat při [zotavení po havárii](event-hubs-geo-dr.md) , ale obchodní scénář některých aplikací už může mít vliv na události dostupnosti, které posledních nepřesahují více než několik minut nebo dokonce i sekund. 

Existují dva základní způsoby řešení těchto scénářů:

- Vzor [replikace][4] má za následek replikaci obsahu primárního centra událostí do sekundárního centra událostí, přičemž primární centrum událostí je obecně používané aplikací pro vytváření i využívání událostí a sekundární slouží jako záložní možnost pro případ, že primární centrum událostí nebude k dispozici. Vzhledem k tomu, že replikace je jednosměrná od primární k sekundárnímu, přepnutí obou výrobců i příjemců z nedostupného primárního na sekundární způsobí, že původní primární událost už nebude dostávat nové události, a nebude už tak aktuální.
  Čistě replikace je proto vhodná jenom pro jednosměrné scénáře převzetí služeb při selhání. Po provedení převzetí služeb při selhání se stará primární primární událost opuštěna a nové sekundární centrum událostí se musí vytvořit v jiné cílové oblasti.
- Vzor [sloučení][5] rozšiřuje model replikace tím, že provádí průběžné sloučení obsahu dvou nebo více Event Hubs. Každá událost, která byla původně předložena do jednoho z Event Hubs zahrnutých ve schématu, se replikuje do druhé Event Hubs. Události se při replikaci přidávají do poznámek tak, že je následně ignoruje proces replikace cíle replikace. Výsledky použití vzoru sloučení jsou dva nebo více Event Hubs, které budou obsahovat stejnou sadu událostí v rámci trvalého konzistentního způsobem. 
  
V obou případech nebude obsah Event Hubs identický. Události od libovolného producenta seskupené podle stejného klíče oddílu se zobrazí ve stejném relativním pořadí jako původně odeslané, ale absolutní pořadí událostí se může lišit. To platí zejména pro situace, kdy se liší počet oddílů zdroje a cíle Event Hubs, což je žádoucí pro některé z rozšířených vzorů, které jsou zde popsané. [Rozdělovač nebo směrovač](#splitting-and-routing-of-event-streams) může získat řez mnohem většího centra událostí se stovkami oddílů a trychtýřem do menšího centra událostí s pouze několik oddíly, které jsou vhodnější pro zpracování podmnožiny s omezenými prostředky zpracování. V opačném případě může [konsolidace](#consolidation-and-normalization-of-event-streams) odfiltrovat data z několika menších Event Hubs do jediného a většího centra událostí s větším množstvím oddílů, aby se vypořádat s konsolidovanou propustností a požadavky na zpracování.

Kritérium pro zachování událostí společně je klíč oddílu a nikoli původní ID oddílu. Další informace o relativním pořadí a o tom, jak provést převzetí služeb při selhání z jednoho centra událostí do dalšího, aniž by se museli spoléhat na stejný rozsah posunů streamu, najdete v popisu vzoru [replikace][4] .


Směrné 
- [Vzor replikace][4]
- [Vzor sloučení][5]

### <a name="latency-optimization"></a>Optimalizace latence 

![Optimalizace latence](media/event-hubs-federation-overview/latency-optimization.jpg)  

Datové proudy událostí jsou jednou výrobci zapisovány, ale mohou být čteny libovolným počtem pokusů od uživatelů událostí. V případě scénářů, kdy je datový proud událostí v oblasti sdílen více uživateli a je zapotřebí k němu opakovaně přicházet během zpracování analýz nacházející se v jiné oblasti nebo v rámci všech požadavků, které by omezují souběžným spotřebitelům, může být užitečné umístit kopii streamu událostí poblíž analytického procesoru, aby se snížila latence zpětného volání. 

Dobrými příklady, jak by se replikace měla upřednostnit při vzdáleném zpracování událostí z různých oblastí, jsou obzvláště ta, kde jsou tyto oblasti extrémně navzájem od sebe, a v případě, že se jedná o skoro antipodesou, může být zeměpisná latence a jejich latence pro každou dobu odezvy snadno vyšší než 250 ms.
Rychlost světla nemůžete zrychlit, ale můžete snížit počet cyklů vysoké latence pro interakci s daty.

Směrné 
- [Vzor replikace][4]

### <a name="validation-reduction-and-enrichment"></a>Ověřování, snižování a obohacení

![Ověřování, zmenšení, obohacení](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Datové proudy událostí mohou být odesílány do centra událostí od klientů mimo vaše vlastní řešení. Tyto datové proudy událostí můžou vyžadovat, aby se pro externě odeslané události kontrolovala kompatibilita s daným schématem a aby se nevyhovující události vynechaly. 

Ve scénářích, kdy je u klientů extrémně šířka pásma omezená, protože se jedná o mnoho scénářů "Internet věcí" s měřením šířky pásma nebo kdy se události původně odesílají přes jiné sítě než IP s omezenými velikostmi paketů, můžou být události vyladěny s referenčními daty, aby bylo možné přidat další kontext, který je možné použít pro procesory navazujících událostí.

V jiných případech, zejména při konsolidaci datových proudů, se může stát, že se data události sníží ve složitosti nebo v Sheer velikosti tím, že se vynechají nějaké podrobnosti.

Tato operace může nastat jako součást toků replikace, konsolidace nebo sloučení. 

Směrné 
- [Vzor editoru][6]

### <a name="integration-with-analytics-services"></a>Integrace se službou Analytics Services

![Integrace se službou Analytics Services](media/event-hubs-federation-overview/integration.jpg)

Několik cloudových služeb Azure, jako je například Azure Stream Analytics nebo Azure synapse, nejlépe funguje s datovými proudy nebo předzpracovanými daty z Azure Event Hubs a Azure Event Hubs také umožňuje integraci s několika Open-Source analytickými balíčky, jako je Apache Samza, Apache Flink, Apache Spark a Apache Storm. 

Pokud vaše řešení primárně používá Service Bus nebo Event Grid, můžete tyto události snadno zpřístupnit pro tyto analytické systémy a také pro archivaci Event Hubs zachytávání, pokud je rozřadíte do centra událostí. Event Grid se to dá nativně s [integrací centra událostí](../event-grid/handler-event-hubs.md), Service Bus postupovat podle pokynů pro [Service Bus replikace](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub).

Azure Stream Analytics [se integruje přímo s Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Směrné 
- [Vzor replikace][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Konsolidace a normalizace datových proudů událostí

![Konsolidace a normalizace datových proudů událostí](media/event-hubs-federation-overview/consolidation.jpg)

Globální řešení se často skládají z oblastí, které jsou z velké části nezávislé, včetně toho, že mají vlastní analytické možnosti, ale Supra-oblastní a globální analytické perspektivy budou vyžadovat integrovanou perspektivu a to je důvod, proč se v příslušných regionálních oblastech pro místní perspektivu vyhodnocuje střední konsolidace stejných proudů událostí. 

Normalizace je charakter scénáře konsolidace, kdy dva nebo více příchozích datových proudů událostí má stejný druh událostí, ale s různými strukturami nebo různými kódováními a události, které jsou překódovány nebo transformovány předtím, než mohou být spotřebovány. 

Normalizace může zahrnovat také kryptografickou práci, jako je dešifrování kompletních šifrovaných datových částí a jejich opětovné šifrování s různými klíči a algoritmy pro cílovou skupinu uživatelů pro příjem dat. 

Směrné 
- [Vzor sloučení][5]
- [Vzor editoru][6]

### <a name="splitting-and-routing-of-event-streams"></a>Rozdělení a směrování datových proudů událostí

![Rozdělení a směrování datových proudů událostí](media/event-hubs-federation-overview/splitting.jpg)

Azure Event Hubs se občas používá ve scénářích pro publikování a předplacení, kdy příchozí torrentované události daleko překročily kapacitu Azure Service Bus nebo Azure Event Grid. obě tyto funkce mají nativní možnosti filtrování a distribuce pro publikování a jejich upřednostnění pro tento model. 

I když je skutečná schopnost "publikování a odběr" ponechává předplatitelům, aby si vybrali požadované události, dělicí vzorek má za to, že se události mapují na oddíly předem určeným distribučním modelem a určení spotřebitelé pak bude exkluzivně vyčítat z "jejich" oddílu. Když centrum událostí ukládá celkový provoz do vyrovnávací paměti, může se pak obsah konkrétního oddílu, který představuje zlomek původního objemu propustnosti, replikovat do fronty pro spolehlivou a transakční a konkurenční spotřebu zákazníků.

Řada scénářů, ve kterých se Event Hubs primárně používá pro přesun událostí v rámci aplikace v rámci oblasti, má některé případy, kdy je možné vybrat události, které se dají použít jenom v jednom oddílu, ale taky je potřeba je zpřístupnit jinde. Tento scénář je podobný rozdělení scénáře, ale může používat škálovatelný směrovač, který bere v úvahu všechny zprávy přicházející v centru událostí a výběry určitých položek, a to jenom pár pro další směrování a může odlišit cíle směrování pomocí metadat a obsahu událostí. 

Směrné
- [Vzor směrování][7]

### <a name="log-projections"></a>Projekce protokolu 

![Projekce protokolu](media/event-hubs-federation-overview/log-projection.jpg)

V některých scénářích budete chtít mít přístup k nejnovější hodnotě odeslané z libovolného podproudu události a běžně rozlišující klíč oddílu. V Apache Kafka to často dosahuje povolením "komprimace protokolů" v tématu, které zahodí všechny nejnovější události označené jedinečným klíčem. Přístup k komprimaci protokolů má tři složené nevýhody: 

- Komprimace vyžaduje nepřetržitou reorganizaci protokolu, což je velmi náročná operace pro zprostředkovatele optimalizovaného pro úlohy, které jsou jenom pro připojení. 
- Komprimace je destruktivní a neumožňuje pro komprimaci a nezhuštěnou perspektivu stejného datového proudu.
- Komprimovaný datový proud má stále sekvenční model přístupu, což znamená, že vyhledávání požadované hodnoty v protokolu vyžaduje čtení celého protokolu v nejhorším případě, což obvykle vede k optimalizaci, které implementují přesný vzor, který je uveden zde: projektování obsahu protokolu do databáze nebo do mezipaměti. 

Komprimovaný protokol je nakonec úložištěm klíč-hodnota a jako takový je to nejhorší možná možnost implementace takového úložiště. Je mnohem efektivnější pro vyhledávání a dotazy k vytvoření a použití trvalé projekce protokolu do správného úložiště hodnot klíčů nebo jiné databáze. 

Vzhledem k tomu, že události jsou neměnné a pořadí je vždy zachované v protokolu, jakákoli projekce protokolu do úložiště hodnot klíčů bude vždy identická pro stejnou škálu událostí, což znamená, že projekce, kterou si aktualizujete, vždy poskytne autoritativní zobrazení a nikdy není dobrý důvod k jejich opětovnému sestavení z obsahu protokolu po sestavení. 

Směrné
- [Projekce protokolu][8]

## <a name="replication-application-technologies"></a>Technologie replikačních aplikací

Implementace výše uvedených vzorů vyžaduje škálovatelné a spolehlivé spouštěcí prostředí pro úlohy replikace, které chcete nakonfigurovat a spustit. V Azure jsou běhová prostředí, která jsou nejvhodnější pro tyto úlohy, nestavové úlohy, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) úlohy replikace stavového streamu a [Azure Functions](../azure-functions/functions-overview.md) pro nestavové úlohy replikace.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Aplikace stavové replikace v Azure Stream Analytics

U aplikací pro stavovou replikaci, které potřebují zvážit vztahy mezi událostmi, vytvářet složené události, rozšiřovat události nebo snižovat události, vytvářet agregace dat a transformovat datové části událostí, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) je nejlepší možností implementace.

V Azure Stream Analytics [vytváříte úlohy](../stream-analytics/stream-analytics-quick-create-portal.md) , které integrují [vstupy](../stream-analytics/stream-analytics-add-inputs.md) a [výstupy](../stream-analytics/stream-analytics-define-outputs.md) a integrují data ze vstupů prostřednictvím [dotazů](/stream-analytics-query/stream-analytics-query-language-reference) , které poskytují výsledek, který je pak k dispozici na výstupech.

Dotazy jsou založené na [jazyce SQL Query](/stream-analytics-query/stream-analytics-query-language-reference) a dají se použít k snadnému filtrování, řazení, agregaci a připojení streamování dat v časovém intervalu. Tento jazyk SQL můžete také roztáhnout pomocí uživatelem definovaných funkcí [jazyka JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) a jazyka [C# (UDF)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). Můžete snadno upravit možnosti řazení událostí a dobu trvání časových oken při provádění agregačních operací prostřednictvím jednoduchých jazykových konstrukcí nebo konfigurací.

Každá úloha obsahuje jeden nebo několik výstupů pro transformovaná data a můžete řídit, co se stane v reakci na informace, které jste analyzovali. Můžete například:

- Odesílání dat do služeb, jako jsou Azure Functions, Service Bus témat nebo front pro aktivaci komunikace nebo vlastních pracovních postupů.
- Odešlete data na řídicí panel Power BI pro řídicí panel v reálném čase.
- Ukládejte data do jiných služeb úložiště Azure (například Azure Data Lake, Azure synapse Analytics atd.), abyste mohli provádět dávkové analýzy, nebo naučit modely strojového učení na základě velmi rozsáhlých indexovaných fondů historických dat.
- V databázích ([SQL Database](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ) se ukládají projekce (označované také jako "materializovaná zobrazení").

### <a name="stateless-replication-applications-in-azure-functions"></a>Bezstavové replikační aplikace v Azure Functions

Pro nestavové úlohy replikace, kde chcete předávané události bez ohledu na jejich datovou část nebo procesy zpracovávat jednotlivě, aniž byste museli brát v úvahu relace událostí (s výjimkou jejich relativního pořadí), můžete použít Azure Functions, což poskytuje značnou flexibilitu.

Azure Functions má předem připravené, škálovatelné triggery a výstupní vazby pro [azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), [azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)a [azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md)a také vlastní rozšíření pro [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)a [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). Většina aktivačních událostí se dynamicky přizpůsobí požadavkům propustnosti tím, že se počet souběžně prováděných instancí nahoru a dolů na základě dokumentovaných metrik. 

Pro sestavování projekce protokolu Azure Functions podporuje výstupní vazby pro [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) a [Azure Table Storage](../azure-functions/functions-bindings-storage-table-output.md).

Azure Functions může běžet pod [spravovanou identitou Azure](../active-directory/managed-identities-azure-resources/overview.md) a v takovém případě může uchovávat konfigurační hodnoty pro přihlašovací údaje v přísném úložišti řízeném přístupem v rámci [Azure Key Vault](../key-vault/general/overview.md).

Azure Functions navíc umožňuje úlohám replikace přímo integraci se službami Azure Virtual Networks a [koncovými body služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro všechny služby zasílání zpráv Azure a je snadno integrovaná do [Azure monitor](../azure-monitor/overview.md).

S plánem Azure Functions spotřeby se předem připravené triggery můžou rovnoměrně škálovat na nulu, zatímco nejsou k dispozici žádné zprávy pro replikaci, což znamená, že se vám neúčtují žádné náklady, aby byla konfigurace připravená na zálohování. klíčovým nevýhodouem použití plánu spotřeby je, že latence pro úlohy replikace "vychází z tohoto stavu" je výrazně vyšší než u plánů hostování, ve kterých je infrastruktura udržována v provozu.  

Na rozdíl od všech z Apache Kafka nich většina běžných replikačních modulů pro zasílání zpráv a událostí, jako je například [nástroje MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) , vyžaduje, abyste poskytovali hostitelské prostředí a mohli škálovat replikační modul sami. Který zahrnuje konfiguraci a integraci funkcí zabezpečení a sítě a usnadnění toku dat monitorování, a pak ještě nemáte příležitost k vložení vlastních úloh replikace do toku. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Výběr mezi Azure Functions a Azure Stream Analytics

Azure Stream Analytics (ASA) je nejlepší volbou, kdykoli potřebujete zpracovat datovou část událostí při jejich replikaci. ASA může kopírovat události jednu po jedné nebo může vytvořit agregované hodnoty, které odhustí informace datových proudů událostí před jejich předáním. Může se snadno vyplynulě [doplňovat referenční data](../stream-analytics/stream-analytics-use-reference-data.md) uložená v Azure Blob Storage nebo Azure SQL Database bez nutnosti importovat taková data do datového proudu.

Pomocí programu ASA můžete snadno vytvářet trvalá a materializovaná zobrazení datových proudů v databázích ve velkém měřítku. Je to zcela nadřízený přístup k modelu Apache Kafka "komprimace" protokolu clunky a stálým projektům Kafka datových proudů na straně klienta. 

ASA může snadno zpracovávat události s datovými částmi kódovanými ve [formátech CSV, JSON a Apache Avro](../stream-analytics/stream-analytics-parsing-json.md) a [vlastní deserializace](../stream-analytics/custom-deserializer.md) můžete připojit pro jakýkoliv jiný formát.

Pro všechny úlohy replikace, do kterých chcete zkopírovat datové proudy událostí "tak, jak jsou", bez zásahu do datových částí, nebo pokud potřebujete implementovat směrovač, proveďte kryptografickou práci, změňte kódování datových částí nebo pokud potřebujete úplnou kontrolu nad obsahem datového proudu, Azure Functions je nejlepší možností.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme prozkoumali řadu vzorů federace a v Azure jsme vysvětlili roli Azure Functions jako modul runtime replikace událostí a zasílání zpráv.

Dále si můžete přečíst, jak nastavit aplikaci replikátoru pomocí Azure Stream Analytics nebo Azure Functions a pak jak replikovat toky událostí mezi Event Hubs a různými jinými systémy pro zpracování událostí a zpráv:

- [Vzorce úloh replikace událostí][10]
- [Zpracování dat pomocí Azure Stream Analytics][9]
- [Aplikace replikátoru událostí v Azure Functions][1]
- [Replikace událostí mezi Event Hubs][2]
- [Replikace událostí do Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication