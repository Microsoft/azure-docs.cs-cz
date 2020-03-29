---
title: Kroky optimalizace po migraci s rozhraním API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje techniky optimalizace po migraci z MongoDB do APi Azure Cosmos DB pro Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063617"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroky optimalizace po migraci při použití rozhraní API Azure Cosmos DB pro MongoDB

Po migraci dat uložených v databázi MongoDB do rozhraní API Azure Cosmos DB pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Tato příručka obsahuje kroky, které byste měli zvážit po migraci. Postup migrace najdete v [kurzu migrace MongoDB do rozhraní API Služby Azure Cosmos DB pro MongoDB.](../dms/tutorial-mongodb-cosmos-db.md)

V tomto průvodci se naučíte:

- [Připojení aplikace](#connect-your-application)
- [Optimalizace zásad indexování](#optimize-the-indexing-policy)
- [Konfigurace globální distribuce pro rozhraní API služby Azure Cosmos DB pro MongoDB](#globally-distribute-your-data)
- [Nastavit úroveň konzistence](#set-consistency-level)

> [!NOTE]
> Jediným povinným krokem po migraci na úrovni aplikace je změna připojovacího řetězce ve vaší aplikaci tak, aby ukazoval na váš nový účet Azure Cosmos DB. Všechny ostatní kroky migrace jsou doporučené optimalizace.
>

## <a name="connect-your-application"></a>Připojení aplikace

1. V novém okně se přihlásíte na [portál Azure](https://www.portal.azure.com/)
2. Na [portálu Azure](https://www.portal.azure.com/)otevřete v levém podokně nabídku **Všechny prostředky** a najděte účet Azure Cosmos DB, do kterého jste migrovali data.
3. Otevřete okno **připojovacího řetězce.** Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.
4. Použijte informace o připojení v konfiguraci vaší aplikace (nebo jiných relevantních místech) tak, aby odrážely rozhraní API Azure Cosmos DB pro připojení MongoDB ve vaší aplikaci.
![Připojovací řetězec](./media/mongodb-post-migration/connection-string.png)

Další podrobnosti najdete na stránce [Připojení aplikace MongoDB k Azure Cosmos DB.](connect-mongodb-account.md)

## <a name="optimize-the-indexing-policy"></a>Optimalizace zásad indexování

Všechna datová pole se ve výchozím nastavení automaticky indexují během migrace dat do služby Azure Cosmos DB. V mnoha případech je tato výchozí zásada indexování přijatelná. Obecně platí, že odebrání indexů optimalizuje požadavky na zápis a výchozí zásady indexování (tj. automatické indexování) optimalizuje požadavky na čtení.

Další informace o indexování najdete [v tématu indexování dat v rozhraní API Azure Cosmos DB pro MongoDB,](mongodb-indexing.md) jakož i [indexování v článcích Azure Cosmos DB.](index-overview.md)

## <a name="globally-distribute-your-data"></a>Globálně distribuovat data

Azure Cosmos DB je dostupná ve všech [oblastech Azure](https://azure.microsoft.com/regions/#services) po celém světě. Po výběru výchozí úrovně konzistence pro váš účet Azure Cosmos DB můžete přidružit jednu nebo více oblastí Azure (v závislosti na vašich potřebách globální distribuce). Pro vysokou dostupnost a kontinuitu provozu vždy doporučujeme provozovat alespoň ve 2 oblastech. Můžete si přečíst tipy pro [optimalizaci nákladů na nasazení ve více oblastech v Azure Cosmos DB](optimize-cost-regions.md).

Pokud chcete globálně distribuovat data, přečtěte si článek [Globální distribuce dat v rozhraní API služby Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Nastavit úroveň konzistence

Azure Cosmos DB nabízí 5 dobře definovaných [úrovní konzistence](consistency-levels.md). Chcete-li si přečíst o mapování mezi úrovněmi konzistence MongoDB a Azure Cosmos DB, přečtěte si [úrovně konzistence a azure cosmos DB API](consistency-levels-across-apis.md). Výchozí úroveň konzistence je úroveň konzistence relace. Změna úrovně konzistence je volitelná a můžete ji optimalizovat pro svou aplikaci. Změna úrovně konzistence pomocí portálu Azure:

1. Přejděte do okna **Výchozí konzistence** v části Nastavení.
2. Vyberte [úroveň konzistence](consistency-levels.md)

Většina uživatelů ponechá úroveň konzistence na výchozí nastavení konzistence relace. Existují však [kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md).

## <a name="next-steps"></a>Další kroky

* [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md)
* [Připojení k účtu Azure Cosmos DB pomocí Studia 3T](mongodb-mongochef.md)
* [Jak globálně distribuovat čtení pomocí rozhraní API Azure Cosmos DB pro MongoDB](mongodb-readpreference.md)
* [Vypršení platnosti dat s využitím rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md).
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Indexování ve službě Azure Cosmos DB](index-overview.md)
* [Jednotky požadavků v Azure Cosmos DB](request-units.md)