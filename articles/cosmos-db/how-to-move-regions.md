---
title: Přečtěte si, jak přesunout Azure Cosmos DB účet do jiné oblasti.
description: Přečtěte si, jak přemístit účet Azure Cosmos DB do jiné oblasti.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059247"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Postup přesunutí účtu Azure Cosmos DB do jiné oblasti

Tento článek popisuje, jak přesunout oblast, kde jsou data replikována v Azure Cosmos DB nebo jak migrovat metadata účtu (Azure Resource Manager) a také data z jedné oblasti do druhé.

## <a name="move-data-from-one-region-to-another"></a>Přesunout data z jedné oblasti do druhé

Azure Cosmos DB podporuje replikaci dat nativně, takže přesouvání dat z jedné oblasti do druhé je jednoduché a dá se provést pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI a zahrnuje následující kroky:

1. Přidat k účtu novou oblast

    Postup přidání nové oblasti na účet Azure Cosmos DB najdete v tématu [Přidání nebo odebrání oblastí pro Azure Cosmos DB účet](how-to-manage-database-account.md#addremove-regions-from-your-database-account) .

1. Provedení ručního převzetí služeb při selhání v nové oblasti

    V situacích, kdy je oblast, kterou odebíráte, aktuálně oblast zápisu pro účet, bude nutné iniciovat převzetí služeb při selhání do nové oblasti přidané výše. Jedná se o operaci s nulovou výpadkou. Pokud přesouváte oblast čtení v účtu s více oblastmi, můžete tento krok přeskočit. Postup pro zahájení převzetí služeb při selhání [v účtu Azure Cosmos provedení ručního převzetí služeb při selhání](how-to-manage-database-account.md#manual-failover)

1. Odebrat původní oblast

    Postup odebrání oblasti z Azure Cosmos DB účtu najdete v tématu [Přidání nebo odebrání oblastí pro Azure Cosmos DB účet](how-to-manage-database-account.md#addremove-regions-from-your-database-account) .

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Migrace metadat Azure Cosmos DB účtu

Azure Cosmos DB neumožňuje nativně migrovat metadata metadat účtu z jedné oblasti do druhé. Chcete-li migrovat metadata účtu a zákaznická data z jedné oblasti do druhé, musí být v požadované oblasti vytvořen nový účet a data musí být zkopírována ručně. Téměř nulová migrace výpadku pro SQL API vyžaduje použití [ChangeFeed](change-feed.md) nebo nástroje, který ho využívá. Pokud migrujete rozhraní API MongoDB, Cassandra nebo jiné rozhraní API nebo získáte další informace o možnostech při migraci dat mezi účty, podívejte se na [Možnosti migrace místních nebo cloudových dat na Azure Cosmos DB](cosmosdb-migrationchoices.md). Následující postup ukazuje, jak migrovat účet Azure Cosmos DB pro rozhraní SQL API a jeho data z jedné oblasti do druhé:

1. Vytvoření nového účtu Azure Cosmos DB v požadované oblasti

    Pokud chcete vytvořit nový účet prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku, přečtěte si téma [Vytvoření účtu Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Vytvoření nové databáze a kontejneru

    Postup vytvoření nové databáze a kontejneru najdete v tématu [vytvoření kontejneru Azure Cosmos](how-to-create-container.md) .

1. Migrace dat pomocí nástroje Azure Cosmos DB Live data migrační Tool

    Migrace dat s téměř nulovým výpadkem najdete v tématu [Azure Cosmos DB Nástroj pro migraci živých dat](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Aktualizovat připojovací řetězec aplikace

    Když je nástroj Live migrační stále spuštěný, aktualizujte informace o připojení v novém nasazení vašich aplikací. Koncové body a klíče pro vaši aplikaci lze načíst z Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Řízení přístupu (IAM) ve Azure Portal – demonstrace zabezpečení databáze NoSQL":::

1. Přesměrovat požadavky do nové aplikace

    Jakmile je nová aplikace připojená k Azure Cosmos DB můžete přesměrovat požadavky klientů na nové nasazení.

1. Odstranit prostředky, které už nepotřebujete

    Po úplném přesměrování požadavků na novou instanci můžete odstranit starý účet Azure Cosmos DB a nástroj pro migraci živých dat.

## <a name="next-steps"></a>Další kroky

Další informace a příklady, jak spravovat účet Azure Cosmos i databáze a kontejnery, najdete v následujících článcích:

* [Správa účtu služby Azure Cosmos DB](how-to-manage-database-account.md)
* [Změnit informační kanál v Azure Cosmos DB](change-feed.md)
