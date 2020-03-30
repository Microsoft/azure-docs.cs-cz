---
title: 'Známé problémy: Migrace z MongoDB do Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Zjistěte o známých problémech a omezeních migrace s migracemi z MongoDB do Azure Cosmos DB pomocí služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 194da036260a78b27748dfc7f755212ab4f30b1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78256034"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Známé problémy/omezení migrace s migrací z MongoDB do rozhraní API Azure Cosmos DB pro MongoDB

Známé problémy a omezení spojené s migrací z MongoDB do rozhraní API Cosmos DB pro MongoDB jsou popsány v následujících částech.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migrace se nezdaří v důsledku použití nesprávného certifikátu SSL

* **Příznak**: Tento problém je zřejmý, když se uživatel nemůže připojit ke zdrojovému serveru MongoDB. Přestože mají všechny porty brány firewall otevřené, uživatel se stále nemůže připojit.

| Příčina         | Řešení |
| ------------- | ------------- |
| Použití certifikátu podepsaného svým držitelem ve službě Migrace databáze Azure může vést k selhání migrace z důvodu nesprávného certifikátu SSL. Chybová zpráva může obsahovat "Vzdálený certifikát je neplatný podle postupu ověření." | Použijte originální certifikát od certifikační autority.  Certifikáty podepsané svým držitelem se obvykle používají pouze v interních testech. Při instalaci originálnícertifikát z autority certifikační autority pak můžete použít SSL ve službě Migrace databáze Azure bez problémů (připojení k Cosmos DB použít SSL přes Rozhraní API Mongo).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Nelze získat seznam databází mapovat v DMS

* **Příznak**: Nelze získat seznam DB na okno **nastavení databáze** při použití dat z režimu **úložiště Azure** na select **source** blade.

| Příčina         | Řešení |
| ------------- | ------------- |
| Připojovací řetězec účtu úložiště chybí informace SAS a proto nelze ověřit. | Vytvořte SAS na kontejneru objektů blob v Průzkumníku úložiště a použijte adresu URL s informacemi o kontejneru SAS jako připojovací řetězec podrobností zdroje.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Použití nepodporované verze databáze

* **Příznak**: Migrace se nezdaří.

| Příčina         | Řešení |
| ------------- | ------------- |
| Pokusíte se migrovat do Azure Cosmos DB z nepodporované verze MongoDB. | Jako nové verze MongoDB jsou vydány, jsou testovány k zajištění kompatibility se službou Migrace databáze Azure a služba je pravidelně aktualizována, aby přijímala nejnovější verze. Pokud je okamžitá potřeba migrovat, jako řešení můžete exportovat databáze/kolekce do Služby Azure Storage a bod zdroj do výsledného výpisu. Vytvořte SAS v kontejneru objektů blob v Průzkumníku úložiště a pak použijte adresu URL s informacemi o Kontejneru SAS jako připojovací řetězec podrobností zdroje.<br><br> |

## <a name="next-steps"></a>Další kroky

* Zobrazit kurz [Migrace MongoDB do rozhraní API Azure Cosmos DB pro MongoDB online pomocí DMS](tutorial-mongodb-cosmos-db-online.md).
* Zobrazit kurz [Migrace MongoDB do rozhraní API Azure Cosmos DB pro MongoDB offline pomocí DMS](tutorial-mongodb-cosmos-db.md).