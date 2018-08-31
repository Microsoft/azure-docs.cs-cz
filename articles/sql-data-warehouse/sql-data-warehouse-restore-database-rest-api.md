---
title: Obnovení Azure SQL Data Warehouse – rozhraní REST API | Dokumentace Microsoftu
description: Obnovení Azure SQL Data Warehouse pomocí rozhraní REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 71236afd3f72497887f42667f971539ed294bef1
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307532"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Obnovení služby Azure SQL Data Warehouse pomocí rozhraní REST API
Obnovení Azure SQL Data Warehouse pomocí rozhraní REST API.

## <a name="before-you-begin"></a>Než začnete
**Ověřte kapacitu jednotek DTU.** Každý datový sklad SQL je hostitelem logického SQL serveru (např. myserver.database.windows.net), který má výchozí [kvóty DTU](../sql-database/sql-database-what-is-a-dtu.md).  Předtím, než bude možné obnovit SQL Data Warehouse, ověřte, že serveru SQL server má dostatek zbývající kvóta DTU databáze obnovena. Chcete-li požádat o další DTU, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Obnovit aktivní nebo pozastaveného datového skladu
Obnovení datového skladu:

1. Získá seznam bodů obnovení databáze pomocí operace získat body obnovení databáze.
2. Začněte pomocí obnovení [vytvořit žádost o obnovení databáze](https://msdn.microsoft.com/library/azure/dn509571.aspx) operace.
3. Sledovat stav obnovení pomocí [stav operace databáze](https://msdn.microsoft.com/library/azure/dn720371.aspx) operace.

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené datového skladu pomocí následujících [nakonfigurovat svou databázi po obnovení](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Obnovit odstraněný datový sklad
Chcete-li obnovit odstraněný datový sklad:

1. Seznam všech obnovitelné odstraněný datový sklad pomocí [vyřadit seznamu obnovitelné databáze](https://msdn.microsoft.com/library/azure/dn509562.aspx) operace.
2. Získá podrobnosti o odstraněný datový sklad, který chcete obnovit pomocí operace [Get obnovitelné přetažené databázové] [Get obnovitelné přetažené databázové].
3. Začněte pomocí obnovení [vytvořit žádost o obnovení databáze](https://msdn.microsoft.com/library/azure/dn509571.aspx) operace.
4. Sledovat stav obnovení pomocí [stav operace databáze](https://msdn.microsoft.com/library/azure/dn720371.aspx) operace.

> [!NOTE]
> Po dokončení obnovení, nakonfigurujte svůj datový sklad, naleznete v tématu [nakonfigurovat svou databázi po obnovení](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Další postup
Další informace o obchodní kontinuity podnikových procesů funkce edice Azure SQL Database, přečtěte si prosím [přehled zajištění provozní kontinuity podnikání Azure SQL Database](../sql-database/sql-database-business-continuity.md).
