---
title: Co je Azure Database Migration Service?
description: Přehled Azure Database Migration Service, který poskytuje bezproblémové migrace z mnoha databázových zdrojů do datových platforem Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963007"
---
# <a name="what-is-azure-database-migration-service"></a>Co je Azure Database Migration Service?

Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky (online migracemi).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrace databází do Azure pomocí známých nástrojů

Azure Database Migration Service integruje některé funkce našich stávajících nástrojů a služeb. Poskytuje zákazníkům komplexní řešení s vysokou dostupností. Služba používá [Data Migration Assistant](/sql/dma/dma-overview) ke generování hodnotících sestav, které vám poskytnou doporučení, která vás provedou provedením změn požadovaných před migrací. To je vše, co je potřeba k provedení jakékoli nápravy. Až budete připraveni zahájit proces migrace, Azure Database Migration Service provede všechny požadované kroky. Své projekty migrace můžete v klidu spustit a dál je neřešit, protože proces využívá osvědčené postupy podle zkušeností Microsoftu. 

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium.

## <a name="regional-availability"></a>Regionální dostupnost

Aktuální informace o místní dostupnosti Azure Database Migration Service najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Ceny

Aktuální informace o cenách Azure Database Migration Service najdete v tématu [Azure Database Migration Service ceny](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Další kroky

* [Stav migračních scénářů, které Azure Database Migration Service podporuje](resource-scenario-status.md).
* [Vytvořte instanci Azure Database Migration Service pomocí Azure Portal](quickstart-create-data-migration-service-portal.md).
* [Migrujte SQL Server do Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
* [Přehled požadavků pro použití Azure Database Migration Service](pre-reqs.md).
* [Nejčastější dotazy týkající se použití Azure Database Migration Service](faq.md).
* [Služby a nástroje dostupné pro scénáře migrace dat](dms-tools-matrix.md)