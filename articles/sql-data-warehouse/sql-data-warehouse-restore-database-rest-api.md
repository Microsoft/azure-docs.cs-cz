---
title: Obnovení Azure SQL Data Warehouse – rozhraní REST API | Microsoft Docs
description: Obnovte Azure SQL Data Warehouse pomocí rozhraní REST API.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524420"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Obnovit Azure SQL Data Warehouse pomocí rozhraní REST API
Obnovte Azure SQL Data Warehouse pomocí rozhraní REST API.

## <a name="before-you-begin"></a>Než začnete
**Ověření vaší DTU kapacity.** Každý datový sklad SQL je hostitelem logický SQL server (například myserver.database.windows.net), který má výchozí [kvóty DTU](../sql-database/sql-database-what-is-a-dtu.md).  Před obnovením SQL Data Warehouse, ověřte, zda serveru SQL server má dostatek zbývající kvóty DTU pro databáze obnovena. Chcete-li požádat o další DTU, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Obnovit aktivní nebo pozastavený datového skladu
Obnovení datového skladu:

1. Získejte seznam bodů obnovení databáze pomocí operace Get body obnovení databáze.
2. Zahájit obnovení pomocí [požadavek na obnovení databáze vytvořit](https://msdn.microsoft.com/library/azure/dn509571.aspx) operaci.
3. Sledování stavu obnovení pomocí [databáze stav operace](https://msdn.microsoft.com/library/azure/dn720371.aspx) operaci.

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené datového skladu pomocí následujících [nakonfigurovat databázi po obnovení](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Obnovení odstraněného datového skladu
Chcete-li obnovit odstraněné datového skladu:

1. Seznam všech sklad obnovitelné odstraněná data pomocí [seznamu obnovitelné vyřadit databáze](https://msdn.microsoft.com/library/azure/dn509562.aspx) operaci.
2. Získání podrobností o pro odstraněné datový sklad, který chcete obnovit pomocí operace [Get obnovitelné vynechaných databáze] [Get obnovitelné vynechaných databáze].
3. Zahájit obnovení pomocí [požadavek na obnovení databáze vytvořit](https://msdn.microsoft.com/library/azure/dn509571.aspx) operaci.
4. Sledování stavu obnovení pomocí [databáze stav operace](https://msdn.microsoft.com/library/azure/dn720371.aspx) operaci.

> [!NOTE]
> Konfigurace datového skladu po dokončení obnovení najdete v tématu [nakonfigurovat databázi po obnovení](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Další postup
Další informace o funkcích kontinuity obchodních edice Azure SQL Database, přečtěte si [Azure SQL Database obchodní kontinuity přehled](../sql-database/sql-database-business-continuity.md).
