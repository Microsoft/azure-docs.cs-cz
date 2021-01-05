---
title: Vzorce úlohy replikace událostí – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje podrobné pokyny pro implementaci konkrétních vzorů úloh replikace událostí.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 494de442b636d535fa1ed6fdeeeda28db9783952
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861379"
---
# <a name="event-replication-tasks-patterns"></a>Vzorce pro úlohy replikace událostí

Přehled [federace](event-hubs-federation-overview.md) a [funkce replikátoru](event-hubs-federation-replicator-functions.md) vysvětlují racionální a základní prvky úloh replikace a před pokračováním v tomto článku se doporučuje seznámení s nimi.

V tomto článku najdete podrobné pokyny k implementaci pro několik vzorů zvýrazněných v části Přehled.

## <a name="replication"></a>Replikace

Vzor replikace kopíruje události z jednoho centra událostí do následujícího nebo z centra událostí do jiného cílového umístění, jako je například fronta Service Bus. Události jsou předávány bez provedení jakýchkoli úprav datové části události.

Implementace tohoto modelu je pokrytá [replikací událostí mezi Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) a [replikací událostí mezi Event Hubs a Service Busmi](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) ukázkami a v kurzu [použití Apache Kafka nástroje MirrorMaker s Event Hubsm](event-hubs-kafka-mirror-maker-tutorial.md) pro konkrétní případ replikace dat z Apache Kafka Broker do Event Hubs.

### <a name="streams-and-order-preservation"></a>Uchování datových proudů a objednávek

Replikace, ať už prostřednictvím Azure Functions nebo Azure Stream Analytics, necílí na zajištění vytváření přesně 1:1 klonů zdrojového centra událostí do cílového centra událostí, ale zaměřuje se na zachování relativního pořadí událostí, kde je aplikace vyžaduje. Aplikace tuto zprávu komunikuje seskupením souvisejících událostí se stejným klíčem oddílu a [Event Hubs uspořádá zprávy se stejným klíčem oddílu postupně ve stejném oddílu](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Informace o posunu jsou jedinečné pro každé centrum událostí a posuny pro stejné události se budou lišit v rámci instancí centra událostí. Chcete-li vyhledat pozici ve zkopírovaném datovém proudu událostí, použijte posuny založené na čase a podívejte se na [Rozšířená metadata přiřazená službou](#service-assigned-metadata).
>
> Posuny založené na čase spustí přijímač v určitém časovém okamžiku:
> - *EventPosition. FromStart ()* – všechna zachovaná data si znovu přečtěte.
> - *EventPosition. FromEnd ()* – načte všechna nová data z doby připojení.
> - *EventPosition. FromEnqueuedTime (DateTime)* – všechna data začínající od daného data a času.
>
> V EventProcessor nastavíte pozici prostřednictvím InitialOffsetProvider na EventProcessorOptions. V případě jiných rozhraní API přijímače je pozice předána konstruktorem. 


Předem připravené pomocníky funkcí replikace, které se používají v pokynech pro [Azure Functions, zajistí](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) , aby se streamy událostí se stejným klíčem oddílu načtené ze zdrojového oddílu odeslaly do cílového centra událostí jako dávka v původním datovém proudu a se stejným klíčem oddílu.

Pokud je počet oddílů zdrojového a cílového centra událostí stejný, všechny datové proudy v cíli se namapují na stejné oddíly, jako kdyby byly ve zdroji.
Pokud je počet oddílů jiný, což je v některých dalších vzorech, které jsou popsané v následujícím seznamu, mapování se bude lišit, ale datové proudy se vždycky uchovávají společně a v daném pořadí.

Relativní pořadí událostí náležejících k různým datovým proudům nebo nezávislým událostem bez klíče oddílu v cílovém oddílu může být vždy odlišné od zdrojového oddílu.

### <a name="service-assigned-metadata"></a>Metadata přiřazená službou

Metadata, která jsou přiřazena ke službě, získaná ze zdrojového centra událostí, původní čas a číslo sekvence a posunutí jsou nahrazené novými hodnotami přiřazenými službou v cílovém centru událostí, ale s [podpůrnými funkcemi](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), úlohami replikace, které jsou k dispozici v našich ukázkách, se původní hodnoty uchovávají ve vlastnostech uživatele: `repl-enqueue-time` (ISO8601 řetězec), `repl-sequence` , `repl-offset` .

Tyto vlastnosti jsou typu String a obsahují hodnotu dokument příslušných původních vlastností. Pokud se událost přepošle víckrát, přiřadí se k již existujícím vlastnostem metadata přiřazená službě s hodnotami, které jsou odděleny středníky.

### <a name="failover"></a>Převzetí služeb při selhání

Pokud používáte replikaci pro účely zotavení po havárii, můžete chránit před událostmi týkajícími se místní dostupnosti ve službě Event Hubs, nebo proti přerušením sítě. kterýkoli takový scénář selhání bude vyžadovat převzetí služeb při selhání z jednoho centra událostí až po další, aby producenti a/nebo příjemci používali sekundární koncový bod.

U všech scénářů s podporou převzetí služeb při selhání se předpokládá, že požadované prvky oborů názvů jsou strukturně totožné, což znamená, že Event Hubs a skupiny uživatelů se shodují s názvem a že pravidla sdíleného přístupového podpisu nebo pravidla řízení přístupu na základě rolí jsou nastavena stejným způsobem. Můžete vytvořit (a aktualizovat) sekundární obor názvů podle [pokynů pro přesunutí oborů názvů](move-across-regions.md) a vynechání kroku vyčištění.

Chcete-li vynutit, aby producenti a spotřebitelé přepnuli, je nutné vytvořit informace o tom, který obor názvů se má použít k vyhledávání v umístění, které je snadno dosažitelné a aktualizované. Pokud se producenti nebo příjemci setkávají s častými nebo trvalými chybami, měli by se na toto místo obrátit a upravit jejich konfiguraci. Existuje spousta způsobů, jak tuto konfiguraci sdílet, ale odkazujeme dvě na následující: DNS a sdílené složky.

#### <a name="dns-based-failover-configuration"></a>Konfigurace převzetí služeb při selhání na základě služby DNS

Jedním z kandidátských přístupů je uchovávat informace v záznamech SRV služby DNS ve službě DNS, kterou řídíte, a odkazovat na příslušné koncové body centra událostí. 

> [!IMPORTANT] 
> Uvědomte si, že Event Hubs neumožňuje, aby se jeho koncovým bodům přímo aliasoval pomocí záznamů CNAME, což znamená, že jako prostředek pro adresy koncového bodu použijete jako odolný mechanismus vyhledávání a nebudete moct přímo překládat informace IP adresy.

Předpokládejme, že vlastníte doménu `example.com` a, pro vaši aplikaci, zónu `test.example.com` . U dvou alternativních Event Hubs nyní vytvoříte dvě další vnořené zóny a záznam SRV v každém z nich.

Záznamy SRV jsou podle společné konvence, s předponou `_azure_eventhubs._amqp` a uchovávají dva záznamy koncového bodu: jeden pro AMQP-over-TLS na portu 5671 a jeden pro AMQP-over-WebSockets na portu 443, jak odkazuje na koncový bod Event Hubs oboru názvů, který odpovídá zóně.

| Zóna                   | Záznam SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

V zóně aplikace pak vytvoříte záznam CNAME, který odkazuje na podřízenou zónu odpovídající primárnímu centru událostí:

| Záznam CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Pomocí klienta DNS, který umožňuje explicitně dotazování na záznamy CNAME a SRV (integrované klienty Java a .NET umožňují pouze jednoduché rozlišení názvů k IP adresám), můžete přeložit požadovaný koncový bod. V případě [DnsClient.NET](https://dnsclient.michaco.net/)je například funkce Lookup:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Funkce vrátí cílový název hostitele registrovaný pro port 5671 zóny aktuálně s aliasem, jak je uvedeno výše.

K provádění převzetí služeb při selhání je potřeba upravit záznam CNAME a nasměrovat ho do alternativní zóny.

Výhodou použití DNS a konkrétně [Azure DNS](../dns/dns-overview.md)je to, že Azure DNS informace se globálně replikují, a proto odolný proti výpadkům v jedné oblasti.

Tento postup se podobá tomu, jak [Event Hubs geograficky Dr](event-hubs-geo-dr.md) funguje, ale plně pod vlastním ovládacím prvkem a funguje i s aktivními a aktivními scénáři.

#### <a name="file-share-based-failover-configuration"></a>Konfigurace převzetí služeb při selhání na základě sdílených souborů

Nejjednodušší alternativou k použití DNS pro sdílení informací koncového bodu je zadání názvu primárního koncového bodu do souboru prostého textu a obsluha souboru z infrastruktury, která je robustní proti výpadkům a pořád umožňuje aktualizace.

Pokud jste již spustili vysoce dostupnou infrastrukturu webu s globální dostupností a replikací obsahu, přidejte takový soubor a soubor znovu publikujte, pokud je nutný přepínač.

> [!CAUTION]
> Název koncového bodu byste měli publikovat jenom tímto způsobem, ne úplným připojovacím řetězcem, včetně tajných klíčů.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Další pokyny pro převzetí služeb při selhání pro uživatele

Pro uživatele centra událostí jsou další požadavky na strategii převzetí služeb při selhání závislé na potřebách procesoru událostí.

Pokud dojde k havárii, která vyžaduje opětovné sestavení systému, včetně databází, ze zálohových dat, a databáze jsou přímo nebo prostřednictvím mezilehlého zpracování z událostí držených v centru událostí, obnovte zálohu a potom budete chtít spustit opětovné přehrání událostí do systému od okamžiku, kdy se vytvořila záloha databáze, a ne od okamžiku, kdy byl původní systém zničen.

Pokud se selhání týká pouze řezu systému nebo skutečně pouze jednoho centra událostí, které je nedostupné, pravděpodobně budete chtít pokračovat v zpracování událostí z přibližně stejné pozice, ve které bylo zpracování přerušeno.

Chcete-li realizovat buď scénář a použití procesoru událostí příslušné sady Azure SDK, [vytvořte nové úložiště kontrolních bodů](event-processor-balance-partition-load.md#checkpointing) a zadejte počáteční pozici oddílu na základě _časového razítka_ , ze kterého chcete pokračovat v zpracování.

Pokud stále máte přístup k úložišti kontrolního bodu centra událostí, které jste přepnuli, [Rozšířená metadata](#service-assigned-metadata) popsaná výše vám pomůžou přeskočit události, které už byly ošetřené, a obnovit je přesně tam, kde jste naposledy opustili.

## <a name="merge"></a>Sloučit

Vzor sloučení má jeden nebo více úloh replikace odkazujících na jeden cíl, případně i u běžných výrobců současně odesílají události do stejného cíle.

Variace těchto vzorců jsou:

- Dvě nebo více funkcí replikace současně získávají události ze samostatných zdrojů a odesílají je do stejného cíle.
- Jedna další funkce replikace získává události ze zdroje, zatímco cíl je také používán přímo producenty.
- Předchozí vzor, ale zrcadlený ze dvou nebo více Event Hubs v důsledku toho Event Hubs obsahující stejné datové proudy, bez ohledu na to, kde jsou události vytvářeny.

První dvě variace vzorů jsou triviální a neliší se od úloh jednoduché replikace.

Poslední scénář vyžaduje opětovné replikaci již replikovaných událostí. Technika je znázorněna a vysvětlena v ukázce [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) .

## <a name="editor"></a>Editor

Vzor editoru se vytváří na základě vzoru [replikace](#replication) , ale zprávy jsou před přesměrováním změněny. 

Příklady těchto úprav:

- **_Překódování_** – Pokud obsah události (také označovaný jako "tělo" nebo "datová část") přijde ze zdroje kódovaného pomocí formátu _Apache Avro_ nebo některého vlastního formátu serializace, ale očekává se, že systém, který je vlastníkem cíle, pro obsah, který má být kódovaný v kódování _JSON_ , úloha replikace překódování nejprve oddělí datovou část z _Apache Avro_ do grafu objektů v paměti a potom tento graf serializovat do formátu _JSON_ pro událost, která se předává. Překódování zahrnuje i úlohy Komprese a dekomprese **obsahu** .
- **_Transformace_** – události, které obsahují strukturovaná data, mohou vyžadovat přeformování těchto dat, aby je uživatelé mohli snadněji spotřebovat. To může zahrnovat práci, jako je například sloučení vnořených struktur, vyřazení nadbytečných datových elementů nebo přetvarování datové části tak, aby přesně vyhovovalo danému schématu.
- **_Dávkování_** – události mohou být přijímány v dávkách (více událostí v jednom přenosu) ze zdroje, ale musí být předávány jednotlivě do cíle nebo naopak. Úkol proto může předat více událostí na základě jednoho přenosu událostí vstupu nebo agregovat sadu událostí, které jsou následně přeneseny společně.
- **_Ověřování_** – data událostí z externích zdrojů se často musí zkontrolovat, jestli jsou v souladu se sadou pravidel, než se dají přeposlání. Pravidla mohou být vyjádřena pomocí schémat nebo kódu. Události, které se neshodují s dodržováním předpisů, mohou být vyřazeny s problémem zaznamenaným v protokolech nebo mohou být předány do zvláštního cílového cíle, aby je bylo možné dále zpracovat.
- Data o **_obohacení_** událostí pocházejících z některých zdrojů mohou vyžadovat rozšíření s dalšími kontexty, aby je bylo možné použít v cílových systémech. To může zahrnovat hledání referenčních dat a vkládání těchto dat do události nebo přidávání informací o zdroji, který je známou úlohou replikace, ale není obsažen v událostech.
- **_Filtrování_** – některé události přicházející ze zdroje může být nutné z cíle zajímat na základě některého pravidla. Filtr testuje událost na základě pravidla a událost zruší, pokud událost neodpovídá pravidlu. Filtrování duplicitních událostí tím, že se na základě určitých kritérií vyřadí další události se stejnými hodnotami je forma filtrování.
- **_Kryptografie_** – úloha replikace může být schopná dešifrovat obsah doručený ze zdrojového nebo šifrovaného obsahu, který je předaný cíli, nebo může být nutné ověřit integritu obsahu a metadat relativně k podpisu, který je v události zavedený, nebo takový podpis připojit.
- **_Ověření identity_** – úloha replikace může připojit metadata, která jsou potenciálně chráněná digitálním podpisem, na událost, která potvrzuje, že událost byla přijata přes konkrétní kanál nebo v určitou dobu.
- **_Řetězení_** – úloha replikace může použít signatury na datové proudy událostí, jako je ochrana datového proudu a chybějící události, které mohou být zjištěny.

Vzorce pro transformaci, dávkování a obohacení se obecně nejlépe implementují s [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) úlohami. 

Všechny tyto vzory se dají implementovat pomocí Azure Functions pomocí [triggeru Event Hubs](../azure-functions/functions-bindings-event-hubs-trigger.md) pro získání událostí a [výstupní vazby centra událostí](../azure-functions/functions-bindings-event-hubs-output.md) pro jejich doručování.

## <a name="routing"></a>Směrování

Vzor směrování se sestavuje na vzorech [replikace](#replication) , ale místo toho, aby měl jeden zdroj a jeden cíl, úloha replikace má několik cílů, který je znázorněný tady v C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Funkce směrování bude brát v úvahu metadata zprávy a/nebo datovou část zprávy a pak vybere jedno z dostupných cílů pro odeslání.

V Azure Stream Analytics můžete dosáhnout stejného s definováním více výstupů a následným provedením dotazu na výstup.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projekce protokolu

Vzor projekce protokolu sloučí datový proud události do indexované databáze s událostmi, které se stávají záznamy v databázi. Události se obvykle přidávají do stejné kolekce nebo tabulky a klíč oddílu centra událostí se stane smluvní stranou primárního klíče, který hledá jedinečný záznam.

Projekce protokolu může vytvořit datovou řadu historian dat události nebo zhuštěného zobrazení, kde se pro každý klíč oddílu zachová jenom nejnovější událost. Tvar cílové databáze je nakonec až do vás a potřeb vaší aplikace. Tento model se také označuje jako "zdroje událostí".

> [!TIP]
> Můžete snadno vytvořit projekce protokolu do [Azure SQL Database](../stream-analytics/sql-database-output.md) a [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) v Azure Stream Analytics a měli byste preferovat tuto možnost.

Následující funkce Azure Functions nakomprimuje obsah centra událostí do kolekce Azure CosmosDB.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

- [Aplikace replikátoru událostí v Azure Functions][1]
- [Replikace událostí mezi Event Hubs][2]
- [Replikace událostí do Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
