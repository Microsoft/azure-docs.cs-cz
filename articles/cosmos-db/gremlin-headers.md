---
title: Hlavičky odpovědí Azure Cosmos DB Gremlin
description: Referenční dokumentace pro metadata odpovědi serveru, která umožňuje další řešení potíží
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755085"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Hlavičky odpovědí serveru Azure Cosmos DB Gremlin
Tento článek se věnuje hlavičkám, které server Gremlin služby Cosmos DB vrací volajícímu po provedení požadavku. Tyto hlavičky jsou užitečné při řešení potíží s výkonem požadavků a vytváření aplikací, které se nativně integrují se službou Cosmos DB, a umožňují zjednodušit zákaznickou podporu.

Mějte na paměti, že při převzetí závislosti na těchto hlavičkách omezujete přenositelnost vaší aplikace na jiné implementace Gremlin. Na oplátku získáváte těsnější integraci s Cosmos DB Gremlin. Tyto hlavičky nejsou standardem TinkerPop.

## <a name="headers"></a>Hlavičky

| Hlavička | Typ | Vzorová hodnota | Pokud je zahrnuto | Vysvětlení |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Success and Failure | Množství kolekce nebo propustnost databáze spotřebované v [jednotkách požadavku (RU/s nebo RU)](request-units.md) pro zprávu s částečnou odpovědí. Tato hlavička je k dispozici v každém pokračování pro požadavky, které mají více bloků. Odráží poplatek za konkrétní část odezvy. Pouze pro požadavky, které se skládají z jednoho bloku odpovědi, tato hlavička odpovídá celkovým nákladům na průchod. Pro většinu složitých průchodů však tato hodnota představuje částečné náklady. |
| **x-ms-total-request-charge** | double | 423.987 | Success and Failure | Množství kolekce nebo propustnost databáze spotřebované v [jednotkách požadavku (RU/s nebo RU)](request-units.md) pro celý požadavek. Tato hlavička je k dispozici v každém pokračování pro požadavky, které mají více bloků. Označuje kumulativní poplatek od začátku požadavku. Hodnota této hlavičky v posledním bloku označuje poplatek za úplný požadavek. |
| **x-ms-server-time-ms** | double | 13.75 | Success and Failure | Tato hlavička je součástí pro účely řešení potíží latence. Označuje dobu v milisekundách, kterou server Cosmos DB Gremlin převzal ke spuštění a vytvoření zprávy částečné odpovědi. Pomocí hodnoty této hlavičky a porovnání s celkovým latence aplikace požadavku můžete vypočítat režii latence sítě. |
| **x-ms-total-server-time-ms** | double | 130.512 | Success and Failure | Celkový čas v milisekundách, který server Cosmos DB Gremlin trval a provedl celý průchod. Toto záhlaví je součástí každé částečné odpovědi. Představuje kumulativní čas provádění od začátku požadavku. Poslední odpověď označuje celkovou dobu spuštění. Tato hlavička je užitečná pro rozlišení mezi klientem a serverem jako zdrojem latence. Čas spuštění průchodu na straně klienta můžete porovnat s hodnotou této hlavičky. |
| **x-ms-stavový kód** | long | 200 | Success and Failure | Záhlaví označuje interní důvod pro dokončení nebo ukončení požadavku. Aplikace se doporučuje podívat se na hodnotu této hlavičky a přijmout nápravná opatření. |
| **x-ms-substatus-code** | long | 1003 | Pouze selhání | Cosmos DB je vícemodelová databáze, která je postavena na vrstvě sjednoceného úložiště. Tato hlavička obsahuje další přehledy o důvodu selhání, když dojde k selhání v nižších vrstvách zásobníku vysoké dostupnosti. Aplikace se doporučuje uložit tuto hlavičku a použít ji při kontaktování cosmos DB zákaznickou podporu. Hodnota této hlavičky je užitečná pro inženýra Cosmos DB pro rychlé řešení potíží. |
| **x-ms-retry-after-ms** | řetězec (TimeSpan) | "00:00:03.9500000" | Pouze selhání | Toto záhlaví je řetězcová reprezentace typu .NET [TimeSpan.](https://docs.microsoft.com/dotnet/api/system.timespan) Tato hodnota bude zahrnuta pouze v požadavcích, které se nezdařilo vyčerpání zřízené propustky. Aplikace by měla znovu odeslat traversal znovu po instruktážní časové období. |
| **x-ms-activity-id** | řetězec (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | Hlavička obsahuje jedinečný identifikátor požadavku na straně serveru. Každému požadavku je serverem přiřazen jedinečný identifikátor pro účely sledování. Aplikace by měly protokolovat identifikátory aktivit vrácené serverem pro požadavky, o kterých mohou zákazníci chtít kontaktovat zákaznickou podporu. Pracovníci podpory Cosmos DB můžete najít konkrétní požadavky podle těchto identifikátorů v telemetrii služby Cosmos DB. |

## <a name="status-codes"></a>Stavové kódy

Nejběžnější stavové kódy vrácené serverem jsou uvedeny níže.

| Status | Vysvětlení |
| --- | --- |
| **401** | Chybová `"Unauthorized: Invalid credentials provided"` zpráva je vrácena, když ověřovací heslo neodpovídá klíči účtu Cosmos DB. Přejděte na svůj účet Cosmos DB Gremlin na webu Azure portal a potvrďte, že klíč je správný.|
| **404** | Souběžné operace, které se pokoušejí odstranit a aktualizovat stejnou hranu nebo vrchol současně. Chybová zpráva `"Owner resource does not exist"` značí, že v parametrech připojení je nesprávně zadaná databáze nebo kolekce ve formátu `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"`označuje, že průchod trval více než **30 sekund** a byl serverem zrušen. Optimalizujte své průchody tak, aby běžely rychle filtrováním vrcholů nebo hran na každém přechodu, abyste zúžili rozsah hledání.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` K tomu obvykle dochází v případě, že v grafu již existuje vrchol nebo hrana s identifikátorem.| 
| **412** | Stavový kód je doplněn `"PreconditionFailedException": One of the specified pre-condition is not met`chybovou zprávou . Tato chyba svědčí o narušení optimistické souběžnosti řízení mezi čtení hrany nebo vrcholu a zápisem zpět do úložiště po úpravě. Nejběžnější situace, kdy dojde k této chybě, je změna vlastnosti, například `g.V('identifier').property('name','value')`. Gremlin motor by číst vrchol, upravit, a odepsat zpět. Pokud je jiný průchod běží paralelně se snaží zapsat stejný vrchol nebo hranu, jeden z nich obdrží tuto chybu. Aplikace by měla znovu odeslat průchod serveru.| 
| **429** | Došlo k omezení požadavku a po uplynutí doby uvedené v hlavičce **x-ms-retry-after-ms** by se měl zopakovat.| 
| **500** | Chybová zpráva obsahující `"NotFoundException: Entity with the specified id does not exist in the system."` značí, že se znovu vytvořila databáze nebo kolekce se stejným názvem. Tato chyba zmizí během 5 minut, jakmile se změna rozšíří a zneplatní mezipaměti v různých komponentách služby Cosmos DB. Pokud se chcete tomuto problému vyhnout, používejte vždy jedinečné názvy databází a kolekcí.| 
| **1000** | Tento stavový kód je vrácen, když server úspěšně analyzovat zprávu, ale nebyl schopen spustit. Obvykle označuje problém s dotazem.| 
| **1001** | Tento kód je vrácena po dokončení serveru traversal spuštění, ale nepodaří serializovat odpověď zpět klientovi. K této chybě může dojít, když traversal generuje složitý výsledek, který je příliš velký nebo neodpovídá specifikaci protokolu TinkerPop. Aplikace by měla zjednodušit průchod, když narazí na tuto chybu. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`je vrácena, pokud průchod překročí povolený limit paměti. Limit paměti je **2 GB** na průchod.| 
| **1004** | Tento stavový kód označuje poškozený požadavek na graf. Požadavek může být poškozen při selhání deserializace, typ bez hodnoty je deserializován jako typ hodnoty nebo nepodporovaná operace gremlin požadovaná. Aplikace by neměla opakovat požadavek, protože nebude úspěšný. | 
| **1007** | Obvykle je tento stavový `"Could not process request. Underlying connection has been closed."`kód vrácen s chybovou zprávou . Tato situace může nastat, pokud se ovladač klienta pokusí použít připojení, které je právě uzavřeno serverem. Aplikace by měla opakovat průchod na jiné připojení.
| **1008** | Server Cosmos DB Gremlin může ukončit připojení a znovu vyvážit provoz v clusteru. Ovladače klienta by měly tuto situaci zpracovat a k odesílání požadavků na server používat pouze živá připojení. V některých se může, že ovladače klienta nezjistí, že připojení bylo uzavřeno. Když aplikace narazí na `"Connection is too busy. Please retry after sometime or open more connections."` chybu, by měla opakovat průchod na jiné připojení.

## <a name="samples"></a>ukázky

Ukázková klientská aplikace založená na Gremlin.Net, která čte jeden atribut stavu:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Příklad, který ukazuje, jak číst atribut stavu z klienta jazyka Java Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Další kroky
* [Stavové kódy HTTP pro Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Běžné hlavičky odpovědí Azure Cosmos DB REST](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Požadavky na poskytovatele ovladače TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
