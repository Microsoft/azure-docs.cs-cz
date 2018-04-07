---
title: Azure Stream Analytics výstup do databáze systému Cosmos
description: Tento článek popisuje, jak používat Azure Stream Analytics se uložit výstup do Azure Cosmos databáze pro výstup JSON, archivace dat a nízkou latencí dotazy na nestrukturovaných dat JSON.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f7115f7d19cd44ae7d0812d3aa6c48d8dd58c20d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics výstup do Azure Cosmos DB  
Stream Analytics můžete cílit na [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) pro výstup JSON, povolení archivace a nízkou latencí dotazy dat na nestrukturovaných dat JSON. Tento dokument popisuje některé osvědčené postupy při implementaci této konfigurace.

Pro ty, kteří jsou obeznámeni s Cosmos DB, podívejte se na [studijní postup Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) začít pracovat. 

> [!Note]
> V tomto okamžiku Azure Stream Analytics podporuje jenom připojení pomocí CosmosDB **rozhraní SQL API**.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovány. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, data nemusí být uložen správně. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Základní informace o DB Cosmos jako cíl výstupu
Výstup Azure Cosmos DB v Stream Analytics, který umožňuje zapisovat zpracování výsledků jako výstup JSON do vaší kolekcí Cosmos DB datového proudu. Stream Analytics nevytváří kolekcí do databáze, místo toho by bylo potřeba vytvořit předem. Toto je tak, aby fakturace nákladů na Cosmos DB kolekce jsou transparentní pro vás, a tak, aby mohli vyladit výkon, konzistence a kapacitu vaší kolekce přímo pomocí [rozhraní API DB Cosmos](https://msdn.microsoft.com/library/azure/dn781481.aspx). Doporučujeme používat jednu databázi na DB Cosmos jednu úlohu streamování logicky jednotlivé kolekce pro úlohu streamování.

Některé možnosti Cosmos DB kolekce jsou podrobně popsány níže.

## <a name="tune-consistency-availability-and-latency"></a>Vyladění konzistence, dostupností a latencí
Tak, aby odpovídaly vaše požadavky aplikací, umožňuje Cosmos DB správně vyladit databáze a kolekce a ujistěte se, kompromis mezi konzistencí, dostupností a latencí. V závislosti na tom, jaké úrovně konzistenci čtení potřeb scénář proti čtení a zápisu latence, že můžete úroveň konzistence na vašem účtu databáze. Také ve výchozím nastavení, Cosmos DB umožňuje synchronní indexování na každé operace CRUD do vaší kolekce. Toto je další možností užitečné k řízení výkonu zápisu nebo čtení v Cosmos DB. Další informace najdete v článku [změnit vaší databáze a dotaz úrovně konzistence](../cosmos-db/consistency-levels.md) článku.

## <a name="upserts-from-stream-analytics"></a>Upserts ze služby Stream Analytics
Stream Analytics integrace s Cosmos DB umožňuje vložit nebo aktualizovat záznamy v Cosmos DB kolekce na základě daného sloupce ID dokumentu. Tím se také označuje jako *Upsert*.

Stream Analytics využívá optimistické Upsert přístup, kde aktualizace jsou pouze provádět při vložení nezdaří z důvodu konfliktu ID dokumentu. Tato aktualizace je prováděno pomocí Stream Analytics jako opravy, takže umožňuje částečné aktualizace v dokumentu, tj. přidání nových vlastností nebo nahrazení stávající vlastnosti se provádí postupně. Všimněte si, že není sloučit změny v hodnotách vlastnosti pole ve vaší výsledku dokumentu JSON v poli celý, získávání přepsat, tj. pole.

## <a name="data-partitioning-in-cosmos-db"></a>Vytvoření oddílů dat v databázi systému Cosmos
Cosmos DB [oddíly kolekce](../cosmos-db/partition-data.md) se o doporučený postup pro vytváření oddílů vaše data. 

Pro jeden Cosmos DB kolekce Stream Analytics stále umožňuje oddílu vaše data, podle schémat dotazů a požadavkům na výkon vaší aplikace. Každá kolekce může obsahovat až 10GB dat (maximum) a aktuálně neexistuje žádný způsob, jak vertikálně navýšit kapacitu (nebo přetečení) kolekce. Pro škálování, Stream Analytics můžete zapsat do více kolekcí s danou předponu (viz níže podrobnosti o použití). Stream Analytics používá konzistentní [Hash oddílu překladač](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) strategie na základě uživatele zadaný sloupec PartitionKey oddílu svoje záznamy o výstup. Počet kolekcí s danou předponu při spuštění úlohu streamování slouží jako počet oddílů výstup, ke kterému se úloha zapíše do paralelně (Cosmos DB kolekce = výstup oddíly). Pro jednu kolekci s Opožděné indexování to jenom vložení, o 0.4 MB/s zápisu propustnost je možné očekávat. Použití více kolekcí můžete povolit dosáhnout vyšší propustnost a zvýšené kapacity.

Pokud chcete zvýšit počet oddílů v budoucnosti, musíte zastavit úlohu, změnit rozdělení dat z existující kolekce do nové kolekce a potom restartujte úlohu služby Stream Analytics. Další podrobnosti o používání PartitionResolver a opětovné vytváření oddílů společně s ukázkový kód, bude součástí následné post. Článek [dělení a škálování v Cosmos DB](../cosmos-db/sql-api-partition-data.md) také podrobné informace o to.

## <a name="cosmos-db-settings-for-json-output"></a>Nastavení cosmos DB pro výstup JSON
Vytváření Cosmos DB jako výstupu v Stream Analytics generuje řádku informace, jak vidíte níže. Tato část obsahuje vysvětlení definici vlastnosti.

Dělené kolekce | Více kolekcí "Jednoho oddílu"
---|---
![documentdb stream analytics výstup obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb stream analytics výstup obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> **Více kolekcí "Jednoho oddílu"** scénář vyžaduje klíč oddílu a konfigurace je podporována. 

* **Výstup Alias** – na alias odkazovat tento výstup v dotazu ASA  
* **Název účtu** – název nebo identifikátor URI účtu Cosmos DB koncového bodu.  
* **Účet klíč** – sdílený přístupový klíč pro účet Cosmos DB.  
* **Databáze** – název databáze DB Cosmos.  
* **Vzor názvu** – název kolekce nebo jejich vzor pro kolekce, který se má použít. Formát názvu kolekce se dá vytvořit pomocí tokenu volitelná {partition}, na kterém oddíly začínají od 0. Ukázka platné vstupní hodnoty jsou následující:  
  1\) kolekce – jednu kolekci s názvem "Kolekce" musí existovat.  
  2\) kolekce {partition} – takových kolekcí, musí existovat – "MyCollection0", "MyCollection1", "MyCollection2" a tak dále.  
* **Klíč oddílu** – volitelné. To je potřeba jenom Pokud používáte tokenu {partition} ve vzoru názvu vaší kolekce. Název pole ve výstupních událostech používaný k určení klíče pro rozdělení výstupu do kolekcí Pro výstup jedinou kolekci všechny libovolný výstupního sloupce lze použít například ID oddílu.  
* **ID dokumentu** – volitelné. Název pole ve výstupních událostech používaný k určení primárního klíče, na které insert nebo update jsou založené operace.  
