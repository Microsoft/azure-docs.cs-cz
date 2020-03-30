---
title: Elastické škálování pomocí rozhraní Cassandra API v Db Azure Cosmos
description: Seznamte se s možnostmi, které jsou k dispozici pro škálování účtu rozhraní API Azure Cosmos DB Cassandra a jejich výhodami a nevýhodami
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474675"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Elastické škálování účtu rozhraní API Azure Cosmos DB Cassandra

Existuje celá řada možností, jak prozkoumat elastickou povahu rozhraní API Azure Cosmos DB pro Cassandra. Chcete-li pochopit, jak efektivně škálovat v Azure Cosmos DB, je důležité pochopit, jak zřídit správné množství jednotek požadavků (RU/s) pro účet požadavky na výkon ve vašem systému. Další informace o jednotkách požadavků naleznete v článku [jednotky požadavků.](request-units.md) 

Pro rozhraní CASSANDRA API můžete načíst poplatek jednotky požadavku pro jednotlivé dotazy pomocí [sad SDK .NET a Java](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). To je užitečné při určování výše Ru / s budete muset dát do služby.

![Databázové operace spotřebovávají jednotky požadavků](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Omezení manipulační rychlosti (429 chyb)

Azure Cosmos DB vrátí sazby omezené (429) chyby, pokud klienti spotřebovávají více prostředků (RU/s) než částka, kterou jste zřízené. Rozhraní CASSSandra API v Azure Cosmos DB překládá tyto výjimky na přetížené chyby v nativním protokolu Cassandra. 

Pokud váš systém není citlivý na latenci, může být dostačující pro zpracování omezení propustnosti pomocí opakování. Podívejte se na [ukázku kódu Jazyka Java,](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) jak transparentně zpracovat omezení rychlosti pomocí [rozšíření Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) pro [zásady opakování Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) v Jazyce Java. Rozšíření [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) můžete také použít ke zpracování omezení rychlosti.

## <a name="manage-scaling"></a>Správa změny měřítka

Pokud potřebujete minimalizovat latenci, existuje spektrum možností pro správu škálování a zřizování propustnosti (RU) v rozhraní CASSANDRA API:

* [Ručně pomocí portálu Azure](#use-azure-portal)
* [Programově pomocí prvků roviny ovládacího prvku](#use-control-plane)
* [Programově pomocí příkazů CQL s konkrétní sadou SDK](#use-cql-queries)
* [Dynamicky pomocí autopilota](#use-autopilot)

V následujících částech jsou vysvětleny výhody a nevýhody každého přístupu. Poté se můžete rozhodnout pro nejlepší strategii pro vyvážení potřeb škálování vašeho systému, celkových nákladů a potřeb efektivity vašeho řešení.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Použití portálu Azure

Prostředky můžete škálovat v účtu rozhraní API Azure Cosmos DB Cassandra pomocí portálu Azure. Další informace naleznete v článku [o propustnost provision na kontejnery a databáze](set-throughput.md). Tento článek vysvětluje relativní výhody nastavení propustnost na úrovni [databáze](set-throughput.md#set-throughput-on-a-database) nebo [kontejneru](set-throughput.md#set-throughput-on-a-container) na webu Azure Portal. Termíny "databáze" a "kontejner" uvedené v těchto článcích mapovat na "keyspace" a "tabulka" respektive pro Cassandra API.

Výhodou této metody je, že se jedná o jednoduchý způsob správy kapacity propustnosti v databázi. Nevýhodou však je, že v mnoha případech váš přístup k škálování může vyžadovat určité úrovně automatizace, které mají být nákladově efektivní a vysoce výkonné. V dalších částech jsou vysvětleny příslušné scénáře a metody.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Použití roviny ovládacího prvku

Rozhraní API Azure Cosmos DB pro Cassandra poskytuje možnost programovat propustnost pomocí různých funkcí řídicí roviny. Pokyny a ukázky najdete v článcích [Správce prostředků Azure](manage-cassandra-with-resource-manager.md), [Powershell u](powershell-samples-cassandra.md)a v článku s [příkazy k videím Azure.](cli-samples-cassandra.md)

Výhodou této metody je, že můžete automatizovat škálování nahoru nebo dolů prostředků na základě časovače účet pro špičkovou aktivitu nebo období nízké aktivity. Podívejte se na naši [ukázku,](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) jak toho dosáhnout pomocí Azure Functions a Powershell.

Nevýhodou tohoto přístupu může být, že nemůžete reagovat na nepředvídatelné měnící se potřeby měřítka v reálném čase. Místo toho může být nutné využít kontext aplikace v systému, na úrovni klienta/sady SDK nebo pomocí [funkce Autopilot](provision-throughput-autopilot.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Použití dotazů CQL s konkrétní sadou SDK

Systém můžete dynamicky škálovat pomocí kódu spuštěním [příkazů CQL ALTER](cassandra-support.md#keyspace-and-table-options) pro danou databázi nebo kontejner.

Výhodou tohoto přístupu je, že umožňuje reagovat na potřeby škálování dynamicky a vlastním způsobem, který vyhovuje vaší aplikaci. S tímto přístupem můžete stále využít standardní ru/ s poplatky a sazby. Pokud jsou potřeby škálování vašeho systému většinou předvídatelné (přibližně 70 % nebo více), může být použití sady SDK s cql nákladově efektivnější metodou automatického škálování než použití automatického pilota. Nevýhodou tohoto přístupu je, že může být poměrně složité implementovat opakování, zatímco omezení rychlosti může zvýšit latenci.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Použití autopilota

Kromě ručního nebo programového způsobu zřizování propustnosti můžete také nakonfigurovat kontejnery Cosmos Azure v režimu autopilota. Režim autopilota se automaticky a okamžitě škáluje podle vašich potřeb spotřeby v rámci stanovených rozsahů RU, aniž by došlo ke kompromisu s la. Další informace najdete v [článku vytvoření kontejnerů a databází Azure Cosmos v režimu autopilota.](provision-throughput-autopilot.md)

Výhodou tohoto přístupu je, že je nejjednodušší způsob, jak spravovat škálování potřeb ve vašem systému. Zaručuje, že nebude uplatňovat omezení rychlosti **v rámci nakonfigurovaných rozsahů ŽP**. Nevýhodou je, že pokud jsou potřeby škálování ve vašem systému předvídatelné, může být autopilot méně nákladově efektivním způsobem, jak pracovat s vašimi potřebami škálování než pomocí výše uvedených přístupů na míru řídicí roviny nebo úrovně SDK.

## <a name="next-steps"></a>Další kroky

- Začínáme s [vytvořením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) prostřednictvím aplikace Java
