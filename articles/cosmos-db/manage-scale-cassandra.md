---
title: Elastické škálování pomocí rozhraní API Cassandra v Azure Cosmos DB
description: Přečtěte si o dostupných možnostech škálování Azure Cosmos DB rozhraní API Cassandra účtu a jejich výhodách a nevýhodách.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340936"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastické škálování účtu Azure Cosmos DB rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

K dispozici je celá řada možností, jak prozkoumat elastickou povahu rozhraní Azure Cosmos DB API pro Cassandra. Pokud chcete pochopit, jak efektivně škálovat v Azure Cosmos DB, je důležité pochopit, jak zřídit správné množství jednotek žádostí (RU/s), aby se zohlednily nároky na výkon ve vašem systému. Další informace o jednotkách žádostí najdete v článku [o jednotkách žádosti](request-units.md) . 

Pro rozhraní API Cassandra můžete načíst poplatek za jednotky žádosti pro jednotlivé dotazy pomocí [sad .NET a Java SDK](./find-request-unit-charge-cassandra.md). To je užitečné při určování množství RU/s, které budete muset zřídit ve službě.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databázové operace spotřebovávají jednotky žádosti" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Omezení rychlosti zpracování (429 chyb)

Azure Cosmos DB vrátí chyby s omezením četnosti (429), pokud klienti spotřebují více prostředků (RU/s), než je množství, které jste zřídili. Rozhraní API Cassandra v Azure Cosmos DB překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. 

Pokud váš systém není citlivý na latenci, může být dostačující pro zpracování rychlosti propustnosti pomocí opakovaných pokusů. Informace o tom, jak zvládnout omezení četnosti transparentní pomocí [rozšíření Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) pro [zásady opakování Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) v jazyce Java, najdete v [ukázce kódu Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) . K obsluze omezení rychlosti můžete také použít [rozšíření Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) .

## <a name="manage-scaling"></a>Správa škálování

Pokud potřebujete minimalizovat latenci, existuje spektrum možností správy škálování a propustnosti (ru) v rozhraní API Cassandra:

* [Ručně pomocí Azure Portal](#use-azure-portal)
* [Prostřednictvím kódu programu pomocí funkce plochy ovládacího prvku](#use-control-plane)
* [Prostřednictvím kódu programu pomocí příkazů CQL s konkrétní sadou SDK](#use-cql-queries)
* [Dynamicky pomocí automatického škálování](#use-autoscale)

Následující části vysvětlují výhody a nevýhody jednotlivých přístupů. Pak se můžete rozhodnout, co nejlepší strategii pro vyrovnání požadavků na škálování vašeho systému, celkových nákladů a potřeb efektivity pro vaše řešení.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Použití Azure Portal

Prostředky můžete škálovat v Azure Cosmos DB rozhraní API Cassandra účet pomocí Azure Portal. Další informace najdete v článku o [zřízení propustnosti v kontejnerech a databázích](set-throughput.md). Tento článek vysvětluje relativní výhody nastavení propustnosti na úrovni [databáze](set-throughput.md#set-throughput-on-a-database) nebo [kontejneru](set-throughput.md#set-throughput-on-a-container) v Azure Portal. Výrazy "Database" a "Container" zmíněné v těchto článcích jsou mapovány na "místo" a "Tabulka" v tomto rozhraní API Cassandra.

Výhodou této metody je, že jde o jednoduchý klíč způsob, jak spravovat kapacitu propustnosti v databázi. Nevýhodou je však, že váš přístup k škálování může vyžadovat, aby určité úrovně automatizace byly nákladově efektivní a vysoké. Další části vysvětlují relevantní scénáře a metody.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Použití roviny ovládacího prvku

Rozhraní API pro Azure Cosmos DB pro Cassandra poskytuje schopnost programově upravovat propustnost pomocí našich různých funkcí pro kontrolu rovin. Pokyny a ukázky najdete v článcích [Azure Resource Manager](./templates-samples-cassandra.md), [POWERSHELL](powershell-samples.md)a [Azure CLI](cli-samples.md) .

Výhodou této metody je, že můžete automatizovat horizontální navýšení nebo snížení kapacity prostředků na základě časovače k účtu aktivity špičky nebo obdobích s nízkou aktivitou. Podívejte se na [naši ukázku, jak to můžete udělat](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) pomocí Azure functions a PowerShellu.

Nevýhodou tohoto přístupu může být to, že nemůžete reagovat na nepředvídatelné změny potřeb škálování v reálném čase. Místo toho možná budete muset využít kontext aplikace v systému, na úrovni klienta/sady SDK nebo pomocí [automatického škálování](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Použití dotazů CQL s konkrétní sadou SDK

Systém lze škálovat dynamicky pomocí kódu spuštěním příkazu [ALTER CQL](cassandra-support.md#keyspace-and-table-options) pro danou databázi nebo kontejner.

Výhodou tohoto přístupu je, že vám umožní reagovat na požadavky na škálování dynamicky a vlastním způsobem, který odpovídá vaší aplikaci. S tímto přístupem můžete dál využívat ceny a sazby Standard RU/s. Pokud jsou požadavky na škálování vašeho systému hlavně předvídatelné (přibližně 70% nebo více), může být použití sady SDK s CQL cenově výhodnější metodou automatického škálování než použití automatického škálování. Nevýhodou tohoto přístupu je, že může být poměrně složitá implementace opakování, zatímco omezení rychlosti může zvýšit latenci.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Použít zřízenou propustnost automatického škálování

Kromě standardního (ručního) nebo programového způsobu zřizování propustnosti můžete nakonfigurovat také kontejnery Azure Cosmos v propustnosti zřízené pomocí automatického škálování. Automatické škálování automaticky a okamžitě se škáluje podle potřeb spotřeby v zadaném rozsahu RU bez narušení SLA. Další informace najdete v článku [Vytvoření kontejnerů a databází Azure Cosmos v tématu věnovaném automatickému škálování](provision-throughput-autoscale.md) .

Výhodou tohoto přístupu je, že je nejjednodušší způsob, jak spravovat požadavky na škálování ve vašem systému. Neuplatní se tak omezení četnosti **v rámci konfigurovaných rozsahů ru**. Nevýhodou je, že pokud jsou v systému předvídatelné požadavky na škálování, může být automatické škálování méně nákladově efektivním způsobem, jakým je potřeba zpracovat požadavky na škálování, než na základě výše uvedených přístupů na úrovni ovládacího prvku Bespoke nebo úrovně SDK.

Pokud chcete nastavit nebo změnit maximální propustnost (ru) pro automatické škálování pomocí CQL, použijte následující postup (podle toho, jak se má odpovídajícím způsobem nahradit místo nebo název tabulky):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java