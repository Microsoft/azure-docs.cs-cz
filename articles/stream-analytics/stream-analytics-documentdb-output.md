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
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112747"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics výstup do Azure Cosmos DB  
Stream Analytics můžete cílit na [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) pro výstup JSON, povolení archivace a nízkou latencí dotazy dat na nestrukturovaných dat JSON. Tento dokument popisuje některé osvědčené postupy při implementaci této konfigurace.

Pro ty, kteří jsou obeznámeni s Cosmos DB, podívejte se na [studijní postup Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) začít pracovat. 

> [!Note]
> V tomto okamžiku Azure Stream Analytics podporuje pouze připojení k databázi Cosmos Azure pomocí **rozhraní SQL API**.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovány. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, data nemusí být uložen správně. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Základní informace o DB Cosmos jako cíl výstupu
Výstup Azure Cosmos DB v Stream Analytics, který umožňuje zapisovat zpracování výsledků jako výstup JSON do vaší kolekcí Cosmos DB datového proudu. Stream Analytics nepodporuje vytváření kolekcí v databázi, místo toho by bylo potřeba vytvořit předem. Toto je tak, aby fakturace nákladů na Cosmos DB kolekce jsou řízeny vámi, a tak, aby mohli vyladit výkon, konzistence a kapacitu vaší kolekce přímo pomocí [rozhraní API DB Cosmos](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

Některé možnosti Cosmos DB kolekce jsou podrobně popsány níže.

## <a name="tune-consistency-availability-and-latency"></a>Vyladění konzistence, dostupností a latencí
Tak, aby odpovídaly vaše požadavky aplikací, databázi Cosmos Azure můžete bez problémů ladit databáze a kolekce a ujistěte se, kompromis mezi konzistencí, dostupností a latencí. V závislosti na tom, jaké úrovně konzistenci čtení potřeb scénář proti čtení a zápisu latence, že můžete úroveň konzistence na vašem účtu databáze. Také ve výchozím nastavení, Azure Cosmos DB umožňuje synchronní indexování na každé operace CRUD do vaší kolekce. Toto je další možností užitečné k řízení výkonu zápisu nebo čtení v Azure Cosmos DB. Další informace najdete v článku [změnit vaší databáze a dotaz úrovně konzistence](../cosmos-db/consistency-levels.md) článku.

## <a name="upserts-from-stream-analytics"></a>Upserts ze služby Stream Analytics
Stream Analytics integraci s Azure Cosmos DB umožňuje vložit nebo aktualizovat záznamy v kolekci na základě daného sloupce ID dokumentu. Tím se také označuje jako *Upsert*.

Stream Analytics používá optimistické upsert přístup, kde aktualizace jsou pouze provádět při vložení selže s konflikt ID dokumentu. Tato aktualizace se provádí jako opravy, takže umožňuje částečné aktualizace v dokumentu, který je, přidání nových vlastností nebo nahrazení stávající vlastnosti se provádí postupně. Však není sloučit změny v hodnotách vlastnosti pole ve vaší výsledku dokumentu JSON v poli celý, získávání přepsat, který je pole.

## <a name="data-partitioning-in-cosmos-db"></a>Vytvoření oddílů dat v databázi systému Cosmos
Azure Cosmos DB [neomezená](../cosmos-db/partition-data.md) se o doporučený postup pro vytváření oddílů data, jako databázi Cosmos Azure automaticky přizpůsobí oddíly podle velikosti pracovní zátěže. Při zápisu do neomezený počet kontejnerů, Stream Analytics používá tolik paralelní zapisovače jako předchozí krok dotazu nebo vstupu dělení schéma.

Pro pevnou Azure Cosmos DB kolekce umožňuje Stream Analytics žádný způsob, jak škálování směrem nahoru nebo out, jakmile jsou úplné. Mají maximální limit je 10 GB a propustnost 10 000 RU/s.  Chcete-li migrovat data z kontejner pevnou s kontejnerem neomezená (například jeden s alespoň 1 000 RU/s a klíč oddílu), je potřeba použít [nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [změnu kanálu knihovny](../cosmos-db/change-feed.md).

Zápis do několika kontejnerů pevné je zastaralá a není doporučený postup pro škálování úlohu služby Stream Analytics. Článek [dělení a škálování v Cosmos DB](../cosmos-db/sql-api-partition-data.md) poskytuje další podrobnosti.

## <a name="cosmos-db-settings-for-json-output"></a>Nastavení cosmos DB pro výstup JSON
Vytváření Cosmos DB jako výstupu v Stream Analytics generuje řádku informace, jak vidíte níže. Tato část obsahuje vysvětlení definici vlastnosti.


![documentdb stream analytics výstup obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Pole           | Popis 
-------------   | -------------
Alias pro výstup    | Alias odkazoval tento výstup v dotazu ASA   
Název účtu    | Název nebo identifikátor URI účtu Azure Cosmos DB koncového bodu 
Klíč účtu     | Sdílený přístupový klíč pro účet Azure Cosmos DB
Databáze        | Název databáze Azure Cosmos DB
Název kolekce | Název kolekce pro kolekci, která má být použit. `MyCollection` je platné zadání ukázka – jednu kolekci s názvem `MyCollection` musí existovat.  
ID dokumentu     | Volitelné. Název sloupce ve výstupních událostech používaný jako jedinečný klíč, na které insert nebo update musí na základě operace. Pokud je ponecháno prázdné, všechny události se vloží, bez možnosti aktualizace.
