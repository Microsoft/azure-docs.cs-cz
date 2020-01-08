---
title: Detekce hrozeb pro datové služby v Azure Security Center | Microsoft Docs
description: Tento článek uvádí výstrahy datových služeb, které jsou dostupné v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665730"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detekce hrozeb pro datové služby v Azure Security Center

 Azure Security Center analyzuje protokoly služeb datových úložišť a vyvolá upozornění, když detekuje hrozbu pro vaše datové prostředky. V tomto článku jsou uvedené výstrahy, které Security Center generuje pro následující služby:

* [Azure SQL Database a Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database a SQL Data Warehouse<a name="data-sql"></a>

Rozšířená ochrana před internetovými útoky pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Výstrahy se zobrazí, když dojde k podezřelým databázovým aktivitám, potenciálním ohrožením zabezpečení nebo útokům prostřednictvím injektáže SQL, jakož i ke vzorům dotazů neobvyklé Database.

Rozšířená ochrana před internetovými útoky pro Azure SQL Database a SQL je součástí jednotného balíčku [ADS (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) pro pokročilé funkce zabezpečení SQL, zahrnující databáze SQL Azure, Azure SQL Database spravované instance, Azure SQL Data Warehouse databáze a SQL servery na Azure Virtual Machines.

Další informace:

* [Jak povolit rozšířenou ochranu před internetovými útoky pro Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Jak povolit rozšířenou ochranu před internetovými útoky pro SQL servery na Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Seznam výstrah ochrany před hrozbami pro SQL Database a SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage<a name="azure-storage"></a>

Rozšířená ochrana před internetovými útoky pro úložiště (aktuálně dostupná jenom pro úložiště objektů BLOB) detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby, aniž byste museli být odborníkem na zabezpečení a pomáhají spravovat systémy monitorování zabezpečení.

>[!NOTE]
>Rozšířená ochrana před internetovými útoky pro úložiště není v současnosti dostupná v oblastech cloudu Azure pro státní správu a svrchované oblasti.

Další informace:

* [Jak povolit rozšířenou ochranu před internetovými útoky pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Seznam výstrah ochrany před hrozbami pro Azure Storage](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Výstrahy Azure Cosmos DB jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k účtům Azure Cosmos DB nebo jejich zneužití.

Další informace:

* [Rozšířená ochrana před internetovými útoky pro Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)
