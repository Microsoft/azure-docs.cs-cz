---
title: Kroky optimalizace po migraci s rozhraním API Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje techniky optimalizace po migraci z MongoDB k rozhraní APi pro Azure Cosmos DB pro Mongo DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: lbosq
ms.openlocfilehash: 3a8da5df4c661a160c7ace37144f6ac1a9859da2
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942259"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Kroky optimalizace po migraci při použití rozhraní API Azure Cosmos DB pro MongoDB 

Po migraci dat uložených v databázi MongoDB Azure Cosmos DB k rozhraní API pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Tento průvodce popisuje kroky, které byste měli zvážit po dokončení migrace. Postup migrace najdete v [kurzu migrace MongoDB Azure Cosmos DB do rozhraní API pro MongoDB](../dms/tutorial-mongodb-cosmos-db.md) .

V tomto průvodci se naučíte:
- [Připojení aplikace](#connect-account)
- [Optimalizace zásad indexování](#indexing)
- [Konfigurace globální distribuce pro rozhraní API Azure Cosmos DB pro MongoDB](#distribute-data)
- [Nastavit úroveň konzistence](#consistency)

> [!NOTE]
> Jediným krokem po migraci na úrovni aplikace je změna připojovacího řetězce v aplikaci tak, aby odkazoval na nový Azure Cosmos DB účet. Všechny ostatní kroky migrace jsou doporučené optimalizace.
>

## <a id="connect-account"></a>Připojení aplikace 

1. V novém okně se přihlaste k [Azure Portal](https://www.portal.azure.com/)
2. Z [Azure Portal](https://www.portal.azure.com/)v levém podokně otevřete nabídku **všechny prostředky** a vyhledejte účet Azure Cosmos DB, na který jste migrovali svá data.
3. Otevřete okno **připojovací řetězec** . Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.
4. Použijte informace o připojení v konfiguraci vaší aplikace (nebo na jiných relevantních místech), aby odrážely Azure Cosmos DB rozhraní API pro připojení MongoDB ve vaší aplikaci. 
![řetězec připojení](./media/mongodb-post-migration/connection-string.png)

Další podrobnosti najdete na stránce [připojení aplikace MongoDB k Azure Cosmos DB](connect-mongodb-account.md) .

## <a id="indexing"></a>Optimalizace zásad indexování

Všechna datová pole jsou ve výchozím nastavení automaticky indexována během migrace dat do Azure Cosmos DB. V mnoha případech je tato výchozí zásada indexování přijatelná. Obecně platí, že odebráním indexů optimalizujete požadavky na zápis a výchozí zásady indexování (tj. automatické indexování) optimalizují požadavky na čtení.

Další informace o indexování najdete v tématu [indexování dat v rozhraní Azure Cosmos DB API pro MongoDB](mongodb-indexing.md) a [indexování v Azure Cosmos DBch](index-overview.md) článcích.

## <a id="distribute-data"></a>Globální distribuce dat

Azure Cosmos DB je k dispozici ve všech [oblastech Azure](https://azure.microsoft.com/regions/#services) po celém světě. Po výběru výchozí úrovně konzistence pro účet Azure Cosmos DB můžete přidružit jednu nebo více oblastí Azure (v závislosti na vašich globálních potřebách distribuce). Pro zajištění vysoké dostupnosti a kontinuity podnikových aplikací vždy doporučujeme, abyste spustili aspoň dvě oblasti. Tipy pro [optimalizaci nákladů na nasazení ve více oblastech najdete v Azure Cosmos DB](optimize-cost-regions.md).

Pokud chcete globálně distribuovat data, přečtěte si prosím [globálně distribuované data na rozhraní API Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Nastavit úroveň konzistence
Azure Cosmos DB nabízí 5 jasně definovaných [úrovní konzistence](consistency-levels.md). Pokud si chcete přečíst o mapování mezi MongoDB a Azure Cosmos DB úrovní konzistence, načtěte si [úrovně konzistence a rozhraní API pro Azure Cosmos DB](consistency-levels-across-apis.md). Výchozí úroveň konzistence je úroveň konzistence relace. Změna úrovně konzistence je volitelná a můžete ji optimalizovat pro vaši aplikaci. Postup změny úrovně konzistence pomocí Azure Portal:

1. V části Nastavení otevřete okno **výchozí konzistence** .
2. Vyberte [úroveň konzistence](consistency-levels.md) .

Většina uživatelů opouští úroveň konzistence ve výchozím nastavení konzistence relace. Existují však [kompromisy týkající se dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Další kroky

* [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md)
* [Připojení k účtu Azure Cosmos DB pomocí Studio 3T](mongodb-mongochef.md)
* [Jak globálně distribuovat čtení pomocí rozhraní Azure Cosmos DB API pro MongoDB](mongodb-readpreference.md)
* [Vypršení platnosti dat s využitím rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md).
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Indexování v Azure Cosmos DB](index-overview.md)
* [Jednotky žádostí ve službě Azure Cosmos DB](request-units.md)





