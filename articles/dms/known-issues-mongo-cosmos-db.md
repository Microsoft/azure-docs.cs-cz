---
title: 'Známé problémy: migrace z MongoDB do Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Přečtěte si o známých problémech a omezeních migrace s migracemi z MongoDB na Azure Cosmos DB pomocí Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291806"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Známé problémy/omezení migrace s migracemi z MongoDB Azure Cosmos DB do rozhraní API pro MongoDB

Známé problémy a omezení související s migrací z MongoDB Cosmos DB do rozhraní API pro MongoDB jsou popsány v následujících částech.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migrace se nezdařila z důvodu použití nesprávného certifikátu SSL.

* **Příznak**: Tento problém je zjevné, když se uživatel nemůže připojit k MongoDB zdrojovému serveru. Bez ohledu na to, že se všechny porty brány firewall otevřou, se uživatel stále nemůže připojit.

| Příčina         | Řešení |
| ------------- | ------------- |
| Použití certifikátu podepsaného svým držitelem v Azure Database Migration Service může způsobit selhání migrace z důvodu nesprávného certifikátu SSL. Chybová zpráva může obsahovat "vzdálený certifikát je podle ověřovací procedury neplatný." | Použijte originální certifikát od certifikační autority.  Certifikáty podepsané svým držitelem se obecně používají pouze při interních testech. Při instalaci originálního certifikátu od autority certifikační autority můžete použít protokol SSL v Azure Database Migration Service bez problému (připojení k Cosmos DB používat protokol SSL přes Mongo API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Nepovedlo se získat seznam databází pro mapování v DMS.

* **Příznak**: v okně **nastavení databáze** nejde získat seznam dB při použití **dat z režimu Azure Storage** v okně **Vybrat zdroj** .

| Příčina         | Řešení |
| ------------- | ------------- |
| V připojovacím řetězci účtu úložiště chybí informace o SAS, takže se nedá ověřit. | V Průzkumník služby Storage vytvořte SAS pro kontejner objektů BLOB a použijte adresu URL s informacemi o SAS kontejneru jako zdrojový připojovací řetězec.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Používání nepodporované verze databáze

* **Příznak**: migrace se nezdařila.

| Příčina         | Řešení |
| ------------- | ------------- |
| Pokusíte se migrovat na Azure Cosmos DB z nepodporované verze MongoDB. | Při vydání nových verzí MongoDB jsou testovány, aby se zajistila kompatibilita s Azure Database Migration Service a služba se pravidelně aktualizuje, aby přijímala nejnovější verze. Pokud je potřeba migrovat okamžitě, jako alternativní řešení můžete exportovat databáze nebo kolekce do Azure Storage a pak zdroj nasměrovat na výsledný výpis. V Průzkumník služby Storage vytvořte SAS pro kontejner objektů BLOB a pak použijte adresu URL s informacemi o SAS kontejneru jako zdrojový připojovací řetězec podrobností.<br><br> |

## <a name="next-steps"></a>Další kroky

* Podívejte se na kurz [migrace MongoDB Azure Cosmos DB a rozhraní API pro MongoDB online pomocí DMS](tutorial-mongodb-cosmos-db-online.md).
* Podívejte se na kurz [migrace MongoDB a rozhraní API pro Azure Cosmos DB pro MongoDB v režimu offline pomocí DMS](tutorial-mongodb-cosmos-db.md).