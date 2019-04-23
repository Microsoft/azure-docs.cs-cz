---
title: Optimalizace pomigrační kroky při používání rozhraní API služby Azure Cosmos DB pro MongoDB
description: Tento dokument poskytuje techniky optimalizace po migraci z MongoDB APi služby Azure Cosmos DB pro Mongo DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013536"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimalizace pomigrační kroky při používání rozhraní API služby Azure Cosmos DB pro MongoDB 

Po dokončení migrace dat uložených v databázi MongoDB API služby Azure Cosmos DB pro MongoDB můžete připojit ke službě Azure Cosmos DB a spravovat data. Tato příručka obsahuje pokyny, které byste měli zvážit po migraci. Zobrazit [migraci MongoDB API služby Azure Cosmos DB pro MongoDB kurzu](../dms/tutorial-mongodb-cosmos-db.md) pro kroky migrace.

V tomto průvodci se naučíte:
- [Připojení aplikace](#connect-account)
- [Optimalizace zásady indexování](#indexing)
- [Konfigurace globální distribuce služby Azure Cosmos DB API pro MongoDB](#distribute-data)
- [Nastavení úrovně konzistence](#consistency)

> [!NOTE]
> Připojovací řetězec ve vaší aplikaci tak, aby odkazovala na váš nový účet Azure Cosmos DB se mění pouze povinný krok po migraci na úrovni aplikace. Všechny ostatní kroky migrace se doporučuje optimalizace.
>

## <a id="connect-account"></a>Připojení aplikace 

1. V nové přihlašovací okno do [webu Azure portal](https://www.portal.azure.com/)
2. Z [webu Azure portal](https://www.portal.azure.com/), v levém podokně otevřete **všechny prostředky** nabídky a Najít účet služby Azure Cosmos DB, do které jste migrovali data.
3. Otevřít **připojovací řetězec** okno. Pravé podokno obsahuje všechny informace, které potřebujete pro úspěšné připojení ke svému účtu.
4. Informace o připojení v konfiguraci vaší aplikace (nebo z jiných míst relevantní) tak, aby odrážely API Azure Cosmos DB určený pro připojení k MongoDB ve vaší aplikaci. 
![Připojovací řetězec](./media/mongodb-post-migration/connection-string.png)

Další podrobnosti najdete [připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md) stránky.

## <a id="indexing"></a>Optimalizace zásady indexování

Všechna datová pole jsou automaticky indexovány, ve výchozím nastavení, při migraci dat do služby Azure Cosmos DB. V mnoha případech je přijatelné toto výchozí nastavení zásady indexování. Obecně platí odebrání indexy optimalizuje požadavky na zápis a má výchozí zásady (například automatické indexování) indexování optimalizuje požadavky na čtení.

Další informace o indexování, naleznete v tématu [Data indexování v rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-indexing.md) také [indexování ve službě Azure Cosmos DB](index-overview.md) článků.

## <a id="distribute-data"></a>Distribuujte data globálně

Azure Cosmos DB je k dispozici ve všech [oblastí Azure](https://azure.microsoft.com/regions/#services) po celém světě. Po výběru výchozí úrovně konzistence účtu služby Azure Cosmos DB, můžete přidružit jeden nebo více oblastí Azure (v závislosti na potřebách globální distribuce). Pro zajištění vysoké dostupnosti a kontinuity obchodních procesů vždy doporučujeme spustit v aspoň 2 oblasti. Můžete zkontrolovat tipy pro [optimalizovat náklady na nasazení ve více oblastech ve službě Azure Cosmos DB](optimize-cost-regions.md).

Chcete-li Distribuujte data globálně, přejděte prosím sem [Distribuujte data globálně v rozhraní API služby Azure Cosmos DB pro MongoDB](tutorial-global-distribution-mongodb.md). 

## <a id="consistency"></a>Nastavení úrovně konzistence
Azure Cosmos DB nabízí 5 jasně definovaných [úrovně konzistence](consistency-levels.md). Přečtěte si informace o mapování mezi úrovní konzistence MongoDB a Azure Cosmos DB, přečtěte si téma [úrovně konzistence a rozhraní API služby Azure Cosmos DB](consistency-levels-across-apis.md). Výchozí úroveň konzistence je úroveň konzistence relace. Změna úrovně konzistence je volitelný a optimalizujte ji pro vaši aplikaci. Chcete-li změnit úroveň konzistence pomocí webu Azure portal:

1. Přejděte **výchozí konzistence** okně v části nastavení.
2. Vyberte vaše [úrovně konzistence](consistency-levels.md)

Většina uživatelů ponechte jejich úrovně konzistence v výchozí nastavení konzistence relace. Existují však [kompromisy dostupnost a výkon pro různé úrovně konzistence](consistency-levels-tradeoffs.md). 

## <a name="next-steps"></a>Další postup

* [Připojení aplikace MongoDB ke službě Azure Cosmos DB](connect-mongodb-account.md)
* [Připojte se k účtu Azure Cosmos DB použití studia 3T](mongodb-mongochef.md)
* [Jak globálně distribuovat přečte pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-readpreference.md)
* [Vypršení platnosti dat pomocí rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-time-to-live.md)
* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
* [Indexování ve službě Azure Cosmos DB](index-overview.md)
* [Jednotky žádosti v Azure Cosmos DB](request-units.md)





