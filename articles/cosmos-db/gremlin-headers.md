---
title: Hlavičky odpovědi Azure Cosmos DB Gremlin
description: Referenční dokumentace k metadatům odpovědí serveru, která umožňuje další řešení potíží
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600517"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Hlavičky odpovědi serveru Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Tento článek se věnuje hlavičkám, které server Gremlin služby Cosmos DB vrací volajícímu po provedení požadavku. Tyto hlavičky jsou užitečné při řešení potíží s výkonem požadavků a vytváření aplikací, které se nativně integrují se službou Cosmos DB, a umožňují zjednodušit zákaznickou podporu.

Mějte na paměti, že při použití závislosti na těchto hlavičkách omezujete přenositelnost své aplikace na jiné implementace Gremlin. V rámci návratu získáte užší integraci s Cosmos DB Gremlin. Tato záhlaví nejsou TinkerPop Standard.

## <a name="headers"></a>Hlavičky

| Hlavička | Typ | Ukázková hodnota | Při zahrnutí | Vysvětlení |
| --- | --- | --- | --- | --- |
| **x-MS-Request-poplatek** | double | 11,3243 | Success and Failure | Velikost kolekce nebo propustnosti databáze spotřebované v [jednotkách žádosti (ru/s nebo ru)](request-units.md) pro zprávu částečné odpovědi. Tato hlavička se nachází v každém pokračování pro požadavky, které mají více bloků dat. Odráží náklady na konkrétní blok odezvy. Pouze pro požadavky, které se skládají z bloku s jednou odpovědí, tato hlavička se shoduje s celkovými náklady na průchod. Ve většině složitých procházení však tato hodnota představuje částečné náklady. |
| **x-MS-Total – poplatek za požadavek** | double | 423,987 | Success and Failure | Velikost kolekce nebo propustnosti databáze spotřebované v [jednotkách žádosti (ru/s nebo ru)](request-units.md) pro celý požadavek. Tato hlavička se nachází v každém pokračování pro požadavky, které mají více bloků dat. Indikuje kumulativní poplatek od začátku žádosti. Hodnota této hlavičky v posledním bloku indikuje úplný poplatek za požadavek. |
| **x-MS-server-time-MS** | double | 13,75 | Success and Failure | Tato hlavička je obsažena pro účely řešení potíží s latencí. Označuje dobu v milisekundách, po kterou Cosmos DB Server Gremlin spuštěn a vytvoří zprávu částečné odpovědi. Použití hodnoty této hlavičky a jejich porovnání s celkovými aplikacemi latence žádosti může počítat režijní náklady na latenci sítě. |
| **x-MS-Total-server-time-MS** | double | 130,512 | Success and Failure | Celkový čas (v milisekundách), který Cosmos DB Server Gremlin spuštěn celý průchod. Tato hlavička je obsažena v každé částečné odpovědi. Představuje kumulativní dobu provádění od začátku požadavku. Poslední odezva udává celkovou dobu provádění. Tato hlavička je užitečná k rozlišení mezi klientem a serverem jako zdrojem latence. V klientovi můžete porovnat dobu provádění procházení s hodnotou této hlavičky. |
| **x-MS-stav-kód** | long | 200 | Success and Failure | Záhlaví označuje interní důvod pro dokončení nebo ukončení žádosti. Pro aplikaci se doporučuje podívat na hodnotu této hlavičky a provést nápravná opatření. |
| **x-MS-SubStatus-Code** | long | 1003 | Pouze selhání | Cosmos DB je databáze s více modely, která je postavená na sjednocené vrstvě úložiště. Tato hlavička obsahuje další přehledy o příčině selhání, pokud dojde k selhání v nižších vrstvách zásobníku vysoké dostupnosti. Aplikace se doporučuje uložit tuto hlavičku a použít ji při kontaktování Cosmos DB zákaznické podpory. Hodnota této hlavičky je užitečná pro Cosmos DB inženýr pro rychlé řešení potíží. |
| **x-MS-opakovat – za-MS** | řetězec (TimeSpan) | 00:00:03.9500000 | Pouze selhání | Toto záhlaví je řetězcové vyjádření typu rozhraní .NET [TimeSpan](/dotnet/api/system.timespan) . Tato hodnota bude zahrnutá jenom v žádostech, které selhaly kvůli poskytnutému vyčerpání propustnosti. Aplikace by měla znovu odeslat procházení po uplynutí časového limitu. |
| **x-MS-Activity-ID** | řetězec (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | Záhlaví obsahuje jedinečný identifikátor na straně serveru žádosti. Každému požadavku je server přiřazen jedinečný identifikátor pro účely sledování. Aplikace by měly protokolovat identifikátory aktivit vrácené serverem pro požadavky, které zákazníci mohou chtít kontaktovat zákaznickou podporu. Pracovníci podpory Cosmos DB můžou najít konkrétní žádosti pomocí těchto identifikátorů v telemetrie služby Cosmos DB. |

## <a name="status-codes"></a>Stavové kódy

Většina běžných kódů vrácených pro `x-ms-status-code` atribut status serveru je uvedená níže.

| Status | Vysvětlení |
| --- | --- |
| **401** | Chybová zpráva `"Unauthorized: Invalid credentials provided"` se vrátí, když heslo ověřování neodpovídá Cosmos DB klíč účtu. V Azure Portal přejděte na účet Cosmos DB Gremlin a zkontrolujte, jestli je klíč správný.|
| **404** | Souběžné operace, které se pokoušejí současně odstranit a aktualizovat stejný okraj nebo vrchol. Chybová zpráva `"Owner resource does not exist"` značí, že v parametrech připojení je nesprávně zadaná databáze nebo kolekce ve formátu `/dbs/<database name>/colls/<collection or graph name>`.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` K tomu obvykle dochází v případě, že v grafu již existuje vrchol nebo hrana s identifikátorem.| 
| **412** | Stavový kód je doplněn chybovou zprávou `"PreconditionFailedException": One of the specified pre-condition is not met` . Tato chyba je popsána v případě narušení optimistického řízení souběžnosti mezi čtením okraje nebo vrcholu a jejich zápisem zpět do úložiště po úpravě. Většina běžných situací, kdy k této chybě dochází, je úprava vlastností, například `g.V('identifier').property('name','value')` . Gremlin Engine přečetla vrchol, upraví ho a zapíše ho zpátky. Pokud se při paralelním pokusu o zápis stejného vrcholu nebo hrany spustí jiný průchod, zobrazí se tato chyba jedna z nich. Aplikace by měla znovu odeslat průchod serveru.| 
| **429** | Došlo k omezení požadavku a po uplynutí doby uvedené v hlavičce **x-ms-retry-after-ms** by se měl zopakovat.| 
| **500** | Chybová zpráva obsahující `"NotFoundException: Entity with the specified id does not exist in the system."` značí, že se znovu vytvořila databáze nebo kolekce se stejným názvem. Tato chyba zmizí během 5 minut, jakmile se změna rozšíří a zneplatní mezipaměti v různých komponentách služby Cosmos DB. Pokud se chcete tomuto problému vyhnout, používejte vždy jedinečné názvy databází a kolekcí.| 
| **1000** | Tento stavový kód se vrátí, když server úspěšně analyzoval zprávu, ale nedokázala ji spustit. Obvykle indikuje problém s dotazem.| 
| **1001** | Tento kód se vrátí, když server dokončí procházení prochází, ale nedokáže serializovat odpověď zpátky na klienta. K této chybě může dojít, když procházení generuje složitý výsledek, který je příliš velký nebo nevyhovuje specifikaci protokolu TinkerPop. Při výskytu této chyby by aplikace měla zjednodušit procházení. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` se vrátí, když přecházení překročí povolený limit paměti. Limit paměti je **2 GB** na průchod.| 
| **1004** | Tento stavový kód indikuje špatný požadavek grafu. Požadavek může být poškozen, pokud dojde k chybě deserializace, nehodnotný typ je deserializován jako typ hodnoty nebo Nepodporovaná operace Gremlin. Aplikace by neměla požadavek opakovat, protože nebude úspěšná. | 
| **1007** | Obvykle je tento stavový kód vrácen chybovou zprávou `"Could not process request. Underlying connection has been closed."` . K této situaci může dojít, když se ovladač klienta pokusí použít připojení, které je serverem zavřeno. Aplikace by měla opakovat procházení na jiném připojení.
| **1008** | Cosmos DB Gremlin Server může ukončit připojení k rebilanci provozu v clusteru. Ovladače klienta by měly tuto situaci zpracovat a použít pouze živá připojení k odesílání požadavků na server. V některých případech nemusí klientské ovladače zjistit, zda bylo připojení ukončeno. Když dojde k chybě aplikace, `"Connection is too busy. Please retry after sometime or open more connections."` měla by se opakovat procházení na jiném připojení.
| **1009** | Operace nebyla dokončena ve vyhrazeném čase a byla zrušena serverem. Optimalizujte své procházení tak, aby se rychle spouštěly, a to tak, že filtruje vrcholy nebo hrany na každém segmentu směrování a zúžení rozsahu hledání. Časový limit požadavku má výchozí hodnotu **60 sekund**. |

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

Příklad, který ukazuje, jak číst atribut status z klienta Gremlin Java:

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
* [Stavové kódy HTTP pro Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Běžné hlavičky odpovědí REST Azure Cosmos DB](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Požadavky poskytovatele ovladačů TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
