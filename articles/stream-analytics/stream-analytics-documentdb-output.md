---
title: Výstup Azure Stream Analytics ke službě Cosmos DB
description: Tento článek popisuje, jak používat Azure Stream Analytics se uložit výstup do služby Azure Cosmos DB pro výstup JSON pro archivaci dat a dotazy s nízkou latencí nestrukturovaných dat JSON.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 4be3de8de4332e8ffb0e88e612a3041829ccd606
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658568"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Výstup Azure Stream Analytics ke službě Azure Cosmos DB  
Stream Analytics můžete směrovat [služby Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) výstup ve formátu JSON, povolení archivace a s nízkou latencí dotazy na data nestrukturovaných dat JSON. Tento dokument uvádí i některé doporučené postupy pro implementaci této konfigurace.

Pro ty, kteří zkušenosti se službou Cosmos DB, podívejte se na [výuky pro služby Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) začít. 

> [!Note]
> V tuto chvíli Azure Stream Analytics podporuje jenom připojení ke službě Azure Cosmos DB pomocí **rozhraní SQL API**.
> Další rozhraní API služby Azure Cosmos DB se zatím nepodporují. Pokud bod Azure Stream Analytics k účtům Azure Cosmos DB vytvořené pomocí jiných rozhraní API, nemusí být data uložená správně. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Základy služby Cosmos DB jako cíl výstupu
Výstup Azure Cosmos DB ve službě Stream Analytics, který umožňuje zapisovat datový proud zpracování výsledky jako výstup ve formátu JSON do vašeho kolekcí Cosmos DB. Stream Analytics nepodporuje vytváření kolekcí v databázi, místo toho by bylo potřeba vytvořit předem. Je to tak, aby fakturačních nákladů kolekce Cosmos DB se řídí vám a tak, aby mohli vyladit výkon, konzistence a kapacitu vaší kolekce přímo pomocí [API služby Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> 0.0.0.0 je nutné přidat do seznamu povolených IP adres ze brána firewall služby Azure Cosmos DB.

Některé možnosti kolekce Cosmos DB je podrobně popsaný níže.

## <a name="tune-consistency-availability-and-latency"></a>Vyladění konzistencí, dostupností a latencí
Tak, aby odpovídaly vašim požadavkům na aplikace, služby Azure Cosmos DB vám umožní bez problémů můžete vyladit databáze a kolekce a ujistěte se, kompromisy mezi konzistencí, dostupností a latencí. V závislosti na tom, jaké úrovně konzistence čtení vašim potřebám scénář pro čtení a zápis latenci, že můžete použít úrovně konzistence na váš účet databáze. Ve výchozím nastavení, služby Azure Cosmos DB umožňuje synchronní indexování u každé operace CRUD do kolekce. Toto je další užitečná možnost řízení výkonu zápisu/čtení ve službě Azure Cosmos DB. Další informace najdete v článku [změnit úrovně konzistence vaší databáze a dotazu](../cosmos-db/consistency-levels.md) článku.

## <a name="upserts-from-stream-analytics"></a>Upsertuje ze Stream Analytics
Integrace Stream Analytics pomocí služby Azure Cosmos DB umožňuje vložit nebo aktualizovat záznamy v kolekci na základě daného sloupce ID dokumentu. To se také označuje jako *Upsert*.

Stream Analytics používá optimistické upsert přístupu, kde aktualizace jsou pouze provedeno při vložení selže kvůli konfliktu ID dokumentu. Tato aktualizace se provádí jako opravu, takže umožňuje částečné aktualizace v dokumentu, který je, přidání nových vlastností nebo nahrazuje existující vlastnost se provádí postupně. Však není sloučit změny hodnoty vlastnosti pole ve vašich výsledku dokumentu JSON v celého pole získávání přepsány, to znamená, pole.

Pokud vstupní dokument JSON má existující pole ID, že pole je automaticky použít jako sloupec ID dokumentu ve službě Cosmos DB a jakékoli další zápisy jsou zpracovány jako takové, což vede k jednomu z těchto situací:
- Jedinečné ID vést k vložení
- duplicitní ID a "ID dokumentu' nastavena na"ID"vede má upsertovat
- duplicitní ID a 'ID dokumentu' není sada vede k chybě, po první dokument

Pokud chcete uložit <i>všechny</i> dokumenty, včetně těch s duplicitním ID přejmenovat pole ID v dotazu (s klíčovým slovem AS) a umožní Cosmos DB vytvořit pole ID nebo nahraďte ID s hodnotou jiného sloupce (pomocí klíčového slova AS, nebo pomocí nastavení "ID dokumentu").

## <a name="data-partitioning-in-cosmos-db"></a>Dělení dat v databázi Cosmos DB
Azure Cosmos DB [neomezené](../cosmos-db/partition-data.md) kontejnery jsou doporučený postup pro dělení dat, jako Azure Cosmos DB automaticky škáluje oddíly na základě vašich úloh. Při zápisu do neomezené kontejnery, Stream Analytics používá libovolný počet paralelních zapisovače předchozí dotaz nebo vstupu pro krok schéma rozdělení oddílů.
> [!Note]
> V tuto chvíli je v Azure Stream Analytics podporuje pouze neomezený počet kolekcí se klíče oddílů na nejvyšší úrovni. Například `/region` je podporována. Vnořené klíče oddílů (třeba `/region/name`) nejsou podporovány. 

Pro pevné kolekce Azure Cosmos DB Stream Analytics umožňuje žádný způsob, jak škálovat nahoru nebo navýšení kapacity až jsou úplné. Mají maximální limit je 10 GB a propustnosti 10 000 RU/s.  Pokud chcete migrovat data z pevný kontejner do neomezeného kontejneru (například jeden s minimálně 1 000 RU/s a klíč oddílu), budete muset použít [nástroj pro migraci dat](../cosmos-db/import-data.md) nebo [změnit informační kanál knihovny](../cosmos-db/change-feed.md).

Zápis na pevné více kontejnerů se vyřazuje a není doporučený postup pro horizontální navýšení kapacity vaší úlohy Stream Analytics. Tento článek [dělení a škálování ve službě Cosmos DB](../cosmos-db/sql-api-partition-data.md) poskytuje další podrobnosti.

## <a name="cosmos-db-settings-for-json-output"></a>Nastavení služby cosmos DB pro výstup ve formátu JSON

Vytváření služby Cosmos DB jako výstup ve službě Stream Analytics generuje řádku informace, jak je vidět níže. Tato část obsahuje vysvětlení definici vlastnosti.

![documentdb stream analytics výstupní obrazovky](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Pole           | Popis|
|-------------   | -------------|
|Alias pro výstup    | Alias pro najdete na tomto výstup v dotazu Azure Stream Analytics.|
|Předplatné    | Zvolte vaše předplatné Azure.|
|Account ID      | Název nebo identifikátor URI účtu služby Azure Cosmos DB koncového bodu.|
|Klíč účtu     | Sdílený přístupový klíč pro účet služby Azure Cosmos DB.|
|Databáze        | Název databáze Azure Cosmos DB.|
|Vzor názvu kolekce | Název kolekce pro kolekci, který se má použít. `MyCollection` je platný vstup ukázkové – jednu kolekci s názvem `MyCollection` musí existovat.  |
|ID dokumentu     | Volitelné. Název sloupce ve výstupních událostech používaný jako jedinečný klíč, na které insert nebo update musí být operace založené. Pokud pole ponecháte prázdné, všechny události se vloží, bez možnosti aktualizace.|
