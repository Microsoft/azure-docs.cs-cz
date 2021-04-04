---
title: Vzory úloh replikace zpráv – Azure Service Bus | Microsoft Docs
description: Tento článek poskytuje podrobné pokyny k implementaci konkrétních vzorů úloh replikace zpráv.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657409"
---
# <a name="message-replication-tasks-patterns"></a>Vzory úloh replikace zpráv

Přehled [federace](service-bus-federation-overview.md) a [funkce replikátoru](service-bus-federation-replicator-functions.md) vysvětlují racionální a základní prvky úloh replikace a před pokračováním v tomto článku se doporučuje seznámení s nimi.

V tomto článku najdete podrobné pokyny k implementaci pro několik vzorů zvýrazněných v části Přehled. 

## <a name="replication"></a>Replikace 

Vzor replikace kopíruje zprávy z jedné fronty nebo tématu do následujícího nebo z fronty nebo tématu do jiného cílového umístění, jako je například centrum událostí. Zprávy jsou předávány bez provedení jakýchkoli úprav v datové části zprávy. 

Implementace tohoto modelu je pokrytá z [Azure Service Bus ukázka replikace zpráv do a z](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) ní.

### <a name="sequences-and-order-preservation"></a>Zachování sekvencí a pořadí

Model replikace necílí na zachování absolutního pořadí zpráv zdrojové fronty nebo tématu do cílové fronty nebo tématu, ale zaměřuje se na to, kdykoli je to nutné, při zachování relativního pořadí zpráv, kde je aplikace vyžaduje. Aplikace to umožňuje povolením podpory relace pro zdrojovou entitu a seskupení souvisejících zpráv se stejným [klíčem relace](message-sessions.md).

Předem sestavené funkce replikace zajišťují, aby se sekvence zpráv se stejným ID relace načtené ze zdrojové entity odeslaly do cílové fronty nebo tématu jako dávka v původní sekvenci a se stejným ID relace. 

### <a name="service-assigned-metadata"></a>Metadata přiřazená službou 

Metadata zprávy přiřazené službou získaná ze zdrojové fronty nebo tématu, původní čas a pořadové číslo fronty jsou nahrazeny novými hodnotami přiřazenými službou v cílové frontě nebo tématu, ale s výchozími úlohami replikace, které jsou k dispozici v našich ukázkách, jsou původní hodnoty zachovány ve vlastnostech uživatele: `repl-enqueue-time` (ISO8601 řetězec) a `repl-sequence` .

Tyto vlastnosti jsou typu String a obsahují hodnotu dokument příslušných původních vlastností.  Pokud je zpráva předána vícekrát, jsou metadata okamžitého zdroje přiřazená službě připojena k již existujícím vlastnostem s hodnotami oddělenými středníky. 

### <a name="failover"></a>Převzetí služeb při selhání

Pokud používáte replikaci pro účely zotavení po havárii, můžete chránit před místními zprávami o dostupnosti ve službě Service Bus nebo před přerušením sítě, což způsobí, že jakýkoli takový scénář selhání bude vyžadovat převzetí služeb při selhání z jedné fronty nebo tématu po další, aby producenti a/nebo příjemci používali sekundární koncový bod.

U všech scénářů s podporou převzetí služeb při selhání se předpokládá, že požadované prvky oborů názvů jsou strukturně identické, což znamená, že fronty a témata se shodují s názvem a že pravidla sdíleného přístupového podpisu nebo pravidla řízení přístupu založená na rolích se nastavují stejným způsobem. Můžete vytvořit (a aktualizovat) sekundární obor názvů podle [pokynů pro přesunutí oborů názvů](move-across-regions.md) a vynechání kroku vyčištění.

Chcete-li vynutit, aby producenti a spotřebitelé přepnuli, je nutné vytvořit informace o tom, který obor názvů se má použít k vyhledávání v umístění, které je snadno dosažitelné a aktualizované. Pokud se producenti nebo příjemci setkávají s častými nebo trvalými chybami, měli by se na toto místo obrátit a upravit jejich konfiguraci. Existuje spousta způsobů, jak tuto konfiguraci sdílet, ale odkazujeme dvě na následující: DNS a sdílené složky.

#### <a name="dns-based-failover-configuration"></a>Konfigurace převzetí služeb při selhání na základě služby DNS

Jedním z kandidátských přístupů je uchovávat informace v záznamech SRV služby DNS ve službě DNS, kterou řídíte, a nasměrovat je na příslušné koncové body fronty nebo tématu. Uvědomte si, že v centrech zpráv není umožněno přímé aliasy u svých koncových bodů pomocí záznamů CNAME, což znamená, že jako mechanismus odolného vyhledávání pro adresy koncových bodů použijete službu DNS a nechcete přímo překládat informace o IP adrese. 

Předpokládejme, že vlastníte doménu `example.com` a, pro vaši aplikaci, zónu `test.example.com` . U dvou alternativních Service Bus nyní vytvoříte dvě další vnořené zóny a záznam SRV v každém z nich. 

Záznamy SRV jsou podle společné konvence, s předponou `_azure_servicebus._amqp` a uchovávají dva záznamy koncového bodu: jeden pro AMQP-over-TLS na portu 5671 a jeden pro AMQP-over-WebSockets na portu 443, jak odkazuje na koncový bod Service Bus oboru názvů, který odpovídá zóně.

| Zóna                 | Záznam SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

V zóně aplikace pak vytvoříte záznam CNAME, který odkazuje na podřízenou zónu odpovídající vaší primární frontě nebo tématu:

| Záznam CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Pomocí klienta DNS, který umožňuje explicitně dotazování na záznamy CNAME a SRV (integrované klienty Java a .NET umožňují pouze jednoduché rozlišení názvů k IP adresám), můžete přeložit požadovaný koncový bod. V případě [DnsClient.NET](https://dnsclient.michaco.net/)je například funkce Lookup:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Tento postup se podobá tomu, jak [Service Bus geograficky Dr](service-bus-geo-dr.md) funguje, ale plně pod vlastním ovládacím prvkem a funguje i s aktivními a aktivními scénáři.

#### <a name="file-share-based-failover-configuration"></a>Konfigurace převzetí služeb při selhání na základě sdílených souborů

Nejjednodušší alternativou k použití DNS pro sdílení informací koncového bodu je zadání názvu primárního koncového bodu do souboru prostého textu a obsluha souboru z infrastruktury, která je robustní proti výpadkům a pořád umožňuje aktualizace. 

Pokud jste již spustili vysoce dostupnou infrastrukturu webu s globální dostupností a replikací obsahu, přidejte takový soubor a soubor znovu publikujte, pokud je nutný přepínač.

## <a name="merge"></a>Sloučit

Vzor sloučení má jeden nebo více úloh replikace odkazujících na jeden cíl, případně i u běžných výrobců současně odesílá zprávy do stejného cíle. 

Variace tohoto vzoru:
- Dvě nebo více funkcí replikace současně získávají zprávy ze samostatných zdrojů a odesílají je do stejného cíle.
- Jedna další funkce replikace získává zprávy ze zdroje, ale cíl je také používán přímo producenty. 
- Předchozí vzor, ale zprávy zrcadlené mezi dvěma nebo více tématy, které mají za následek tato témata obsahující stejné zprávy bez ohledu na to, kde jsou zprávy vytvářeny.

První dvě variace vzorů jsou triviální a neliší se od úloh jednoduché replikace.

Poslední scénář vyžaduje opětovné replikaci již replikovaných zpráv. Technika je znázorněna a vysvětlena v aktivní/aktivní ukázce.

## <a name="editor"></a>Editor

Vzor editoru se vytváří na základě vzoru [replikace](#replication) , ale zprávy jsou před přesměrováním změněny. Příklady těchto úprav:

- ***Překódování*** – Pokud obsah zprávy (také označovaný jako "tělo" nebo "datová část") přijde ze zdroje kódovaného pomocí formátu *Apache Avro* nebo některého vlastního formátu serializace, ale očekává se, že systém, který je vlastníkem cíle, pro obsah, který má být kódovaný v kódování *JSON* , úloha replikace překódování nejprve oddělí datovou část z *Apache Avro* do grafu objektů v paměti a potom tento graf serializovat do formátu *JSON* pro zprávu, která se předává. Překódování zahrnuje i úlohy Komprese a dekomprese **obsahu** .
- ***Transformace*** – zprávy, které obsahují strukturovaná data, mohou vyžadovat přeformování těchto dat, aby je uživatelé mohli snadněji spotřebovat. To může zahrnovat práci, jako je například sloučení vnořených struktur, vyřazení nadbytečných datových elementů nebo přetvarování datové části tak, aby přesně vyhovovalo danému schématu.
- ***Dávkování*** – zprávy mohou být přijímány v dávkách (více zpráv v jednom přenosu) ze zdroje, ale musí být předávány jednotlivě do cíle nebo naopak. Úkol proto může předat více zpráv v závislosti na jednom vstupním přenosu zprávy nebo agregovat sadu zpráv, které jsou následně přeneseny společně. 
- ***Ověřování*** – data zpráv z externích zdrojů často musejí být zkontrolována, zda jsou v souladu se sadou pravidel, než mohou být předány. Pravidla mohou být vyjádřena pomocí schémat nebo kódu. zprávy, které se neshodují s dodržováním předpisů, mohou být vyřazeny s chybou zaznamenanými v protokolech nebo mohou být předány do zvláštního cílového cíle, aby je bylo možné dále zpracovat.   
- Data ***obohacení*** z některých zdrojů mohou vyžadovat rozšíření s dalšími kontexty, aby je bylo možné použít v cílových systémech. To může zahrnovat hledání referenčních dat a vkládání těchto dat do zprávy nebo přidání informací o zdroji, který je známou úlohou replikace, ale není obsažen ve zprávách. 
- ***Filtrování*** – některé zprávy přicházející ze zdroje může být nutné z cíle zajímat na základě některého pravidla. Filtr testuje zprávu na základě pravidla a zprávu zruší, pokud zpráva neodpovídá pravidlu. Filtrování duplicitních zpráv na základě pozorování určitých kritérií a následného vyřazení dalších zpráv se stejnými hodnotami je forma filtrování.
- ***Směrování a vytváření oddílů*** – některé úlohy replikace můžou pro dva nebo více alternativních cílů způsobit a definovat pravidla, pro která se pro konkrétní zprávu vybere cíl replikace, a to na základě metadat nebo obsahu zprávy. Speciální forma směrování je rozdělení do oddílů, kde úloha explicitně přiřadí oddíly v jednom cíli replikace na základě pravidel.
- ***Kryptografie*** – úloha replikace může být schopná dešifrovat obsah doručený ze zdrojového nebo šifrovaného obsahu, který je předaný cíli, nebo může být nutné ověřit integritu obsahu a metadat relativně k podpisu, který se ve zprávě zavedl, nebo takový podpis připojit. 
- ***Ověření identity*** – úloha replikace může připojit metadata, která jsou potenciálně chráněná digitálním podpisem, do zprávy, která potvrzuje, že zpráva byla přijata přes konkrétní kanál nebo v určitou dobu.     
- ***Řetězení*** – úloha replikace může použít signatury na posloupnosti zpráv tak, že integrita sekvence je chráněná a že se můžou najít chybějící zprávy.

Všechny tyto vzory lze implementovat pomocí Azure Functions pomocí [triggeru centra zpráv](../azure-functions/functions-bindings-service-bus-trigger.md) pro získání zpráv a [výstupní vazby fronty nebo tématu](../azure-functions/functions-bindings-service-bus-output.md) pro jejich doručování. 

## <a name="routing"></a>Směrování

Vzor směrování se sestavuje na vzorech [replikace](#replication) , ale místo toho, aby měl jeden zdroj a jeden cíl, úloha replikace má několik cílů, který je znázorněný tady v C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Funkce směrování bude brát v úvahu metadata zprávy a/nebo datovou část zprávy a pak vybere jedno z dostupných cílů pro odeslání.

## <a name="next-steps"></a>Další kroky

- [aplikace replikátora zpráv v Azure Functions][1]
- [Replikace zpráv mezi Service Bus][2]
- [Replikace zpráv do Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub