---
title: Elastické škálování pomocí rozhraní API Cassandra v Azure Cosmos DB
description: Přečtěte si o dostupných možnostech škálování Azure Cosmos DB rozhraní API Cassandra účtu a jejich výhodách a nevýhodách.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210785"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastické škálování účtu Azure Cosmos DB rozhraní API Cassandra

K dispozici je celá řada možností, jak prozkoumat elastickou povahu rozhraní API Azure Cosmos DB pro Cassandra. Pokud chcete pochopit, jak efektivně škálovat v Azure Cosmos DB, je důležité pochopit, jak zřídit správné množství jednotek žádostí (RU/s), aby se zohlednily nároky na výkon ve vašem systému. Další informace o jednotkách žádostí najdete v článku [o jednotkách žádosti](request-units.md) . 

![Databázové operace spotřebovávají jednotky žádosti](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Omezení rychlosti zpracování (429 chyb)

Azure Cosmos DB vrátí chyby s omezením četnosti (429), pokud klienti spotřebují více prostředků (RU/s), než je množství, které jste zřídili. Rozhraní API Cassandra v Azure Cosmos DB překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. 

Pokud váš systém není citlivý na latenci, může být dostačující pro zpracování rychlosti propustnosti pomocí opakovaných pokusů. Informace o tom, jak zvládnout omezení četnosti transparentní pomocí [rozšíření Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) pro [zásady opakování Cassandra](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) v jazyce Java, najdete v [ukázce kódu Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) . K obsluze omezení rychlosti můžete také použít [rozšíření Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) .

## <a name="manage-scaling"></a>Správa škálování

Pokud potřebujete minimalizovat latenci, existuje spektrum možností správy škálování a propustnosti (ru) v rozhraní API Cassandra:

* [Ručně pomocí Azure Portal](#use-azure-portal)
* [Prostřednictvím kódu programu pomocí funkce plochy ovládacího prvku](#use-control-plane)
* [Prostřednictvím kódu programu pomocí příkazů CQL s konkrétní sadou SDK](#use-cql-queries)
* [Dynamicky pomocí autopilotu](#use-autopilot)

Následující části vysvětlují výhody a nevýhody jednotlivých přístupů. Pak se můžete rozhodnout, co nejlepší strategii pro vyrovnání požadavků na škálování vašeho systému, celkových nákladů a potřeb efektivity pro vaše řešení.

## <a id="use-azure-portal"></a>Použití Azure Portal

Prostředky můžete škálovat v Azure Cosmos DB rozhraní API Cassandra účet pomocí Azure Portal. Další informace najdete v článku o [zřízení propustnosti v kontejnerech a databázích](set-throughput.md). Tento článek vysvětluje relativní výhody nastavení propustnosti na úrovni [databáze](set-throughput.md#set-throughput-on-a-database) nebo [kontejneru](set-throughput.md#set-throughput-on-a-container) v Azure Portal. Výrazy "Database" a "Container" zmíněné v těchto článcích jsou mapovány na "místo" a "Tabulka" v tomto rozhraní API Cassandra.

Výhodou této metody je, že jde o jednoduchý klíč způsob, jak spravovat kapacitu propustnosti v databázi. Nevýhodou je však, že váš přístup k škálování může vyžadovat, aby určité úrovně automatizace byly nákladově efektivní a vysoké. Další části vysvětlují relevantní scénáře a metody.

## <a id="use-control-plane"></a>Použití roviny ovládacího prvku

Rozhraní API pro Azure Cosmos DB pro Cassandra poskytuje schopnost programově upravovat propustnost pomocí našich různých funkcí pro kontrolu rovin. Pokyny a ukázky najdete v článcích [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [POWERSHELL](powershell-samples-cassandra.md)a [Azure CLI](cli-samples-cassandra.md) .

Výhodou této metody je, že můžete automatizovat horizontální navýšení nebo snížení kapacity prostředků na základě časovače k účtu aktivity špičky nebo obdobích s nízkou aktivitou. Podívejte se na [naši ukázku, jak to můžete udělat](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) pomocí Azure functions a PowerShellu.

Nevýhodou tohoto přístupu může být to, že nemůžete reagovat na nepředvídatelné změny potřeb škálování v reálném čase. Místo toho možná budete muset využít kontext aplikace v systému, na úrovni klienta/sady SDK nebo pomocí modulu [autopilot](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>Použití dotazů CQL s konkrétní sadou SDK

Systém lze škálovat dynamicky pomocí kódu spuštěním příkazu [ALTER CQL](cassandra-support.md#keyspace-and-table-options) pro danou databázi nebo kontejner.

Výhodou tohoto přístupu je, že vám umožní reagovat na požadavky na škálování dynamicky a vlastním způsobem, který odpovídá vaší aplikaci. S tímto přístupem můžete dál využívat ceny a sazby Standard RU/s. Pokud jsou požadavky na škálování vašeho systému hlavně předvídatelné (přibližně 70% nebo více), může být použití sady SDK s CQL cenově výhodnější metodou automatického škálování než při použití autopilotu. Nevýhodou tohoto přístupu je, že může být poměrně složitá implementace opakování, zatímco omezení rychlosti může zvýšit latenci.

## <a id="use-autopilot"></a>Použít autopilot

Kromě ručního nebo programového způsobu zřizování propustnosti můžete také nakonfigurovat kontejnery Azure Cosmos v režimu autopilotu. Režim autopilotu automaticky a okamžitě se škáluje podle potřeb spotřeby v zadaných rozsahech RU bez narušení SLA. Další informace najdete v článku [Vytvoření kontejnerů a databází Azure Cosmos v článku věnovaném režimu autopilotu](provision-throughput-autopilot.md) .

Výhodou tohoto přístupu je, že je nejjednodušší způsob, jak spravovat požadavky na škálování ve vašem systému. Garantuje, že neuplatní omezení četnosti **v rámci konfigurovaných rozsahů ru**. Nevýhodou je to, že pokud jsou požadavky na škálování v systému předvídatelné, může se stát, že Automatický pilot bude méně nákladově efektivní způsob, jakým je potřeba zpracovat požadavky na škálování, než na základě výše uvedených přístupů na úroveň Bespoke Control rovina nebo sady SDK.

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java
