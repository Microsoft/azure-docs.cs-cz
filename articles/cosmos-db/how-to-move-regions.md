---
title: Přesunutí účtu Azure Cosmos DB do jiné oblasti
description: Přečtěte si, jak přesunout Azure Cosmos DB účet do jiné oblasti.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342024"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Přesunutí účtu Azure Cosmos DB do jiné oblasti
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak:

- Přesuňte oblast, kde jsou data replikována v Azure Cosmos DB.
- Migrujte metadata a data účtu (Azure Resource Manager) z jedné oblasti do druhé.

## <a name="move-data-from-one-region-to-another"></a>Přesunout data z jedné oblasti do druhé

Azure Cosmos DB podporuje replikaci dat nativně, takže přesun dat z jedné oblasti do druhé je jednoduchý. Můžete to provést pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Zahrnuje následující kroky:

1. Přidejte k účtu novou oblast.

    Chcete-li přidat novou oblast do účtu Azure Cosmos DB, přečtěte si téma [Přidání nebo odebrání oblastí pro účet Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Proveďte ruční převzetí služeb při selhání do nové oblasti.

    Když je oblast, která se odebírá, aktuálně oblast zápisu pro účet, bude nutné spustit převzetí služeb při selhání do nové oblasti přidané v předchozím kroku. Jedná se o operaci s nulovou dobou výpadku. Pokud přesouváte oblast čtení v účtu s více oblastmi, můžete tento krok přeskočit. 
    
    Pokud chcete spustit převzetí služeb při selhání, přečtěte si téma [ruční převzetí služeb při selhání v účtu Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Odeberte původní oblast.

    Pokud chcete odebrat oblast z Azure Cosmos DB účtu, přečtěte si téma [Přidání nebo odebrání oblastí ze svého účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrace metadat Azure Cosmos DB účtu

Azure Cosmos DB neumožňuje nativně migrovat metadata účtu z jedné oblasti do druhé. Pokud chcete migrovat metadata účtu i zákaznická data z jedné oblasti do druhé, musíte v požadované oblasti vytvořit nový účet a potom data zkopírovat ručně. 

Migrace v blízkosti nulového výpadku pro rozhraní SQL API vyžaduje použití [kanálu změn](change-feed.md) nebo nástroje, který ho používá. Pokud migrujete rozhraní MongoDB API, rozhraní API Cassandra nebo jiné rozhraní API, nebo pokud chcete získat další informace o možnostech migrace dat mezi účty, přečtěte si téma [Možnosti migrace místních nebo cloudových dat do Azure Cosmos DB](cosmosdb-migrationchoices.md). 

Následující kroky ukazují, jak migrovat účet Azure Cosmos DB pro rozhraní SQL API a jeho data z jedné oblasti do druhé:

1. Vytvořte nový účet Azure Cosmos DB v požadované oblasti.

    Pokud chcete vytvořit nový účet prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure, přečtěte si téma [Vytvoření účtu Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Vytvoří novou databázi a kontejner.

    Pokud chcete vytvořit novou databázi a kontejner, přečtěte si téma [vytvoření kontejneru Azure Cosmos](how-to-create-container.md).

1. Migrujte data pomocí nástroje Azure Cosmos DB živým nástrojem pro migraci dat.

    Pokud chcete migrovat data s téměř nulovým výpadkem, přečtěte si téma [Azure Cosmos DB Nástroj pro migraci živých dat](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Aktualizujte připojovací řetězec aplikace.

    Když je stále spuštěn nástroj pro migraci živých dat, aktualizujte informace o připojení v novém nasazení aplikace. Můžete načíst koncové body a klíče pro vaši aplikaci z Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Řízení přístupu v Azure Portal, které demonstruje zabezpečení databáze NoSQL.":::

1. Přesměruje požadavky na novou aplikaci.

    Po připojení nové aplikace k Azure Cosmos DB můžete přesměrovat požadavky klientů na nové nasazení.

1. Odstraňte všechny prostředky, které už nepotřebujete.

    Po úplném přesměrování požadavků na novou instanci můžete odstranit starý účet Azure Cosmos DB a nástroj pro migraci živých dat.

## <a name="next-steps"></a>Další kroky

Další informace a příklady, jak spravovat účet Azure Cosmos i databáze a kontejnery, najdete v následujících článcích:

* [Správa účtu služby Azure Cosmos DB](how-to-manage-database-account.md)
* [Změnit informační kanál v Azure Cosmos DB](change-feed.md)
