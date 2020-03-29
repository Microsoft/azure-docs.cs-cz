---
title: Konfigurace Rozšířené ochrany před internetovými útoky
description: Pokročilá ochrana před internetovými hrozbami detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze v jedné databázi nebo elastickém fondu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822512"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database Advanced Threat Protection pro jednu nebo sdruženou databázi

[Pokročilá ochrana před internetovými hrozbami](sql-database-threat-detection-overview.md) pro jednu a sdruženou databázi detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Pokročilá ochrana před internetovými útoky může identifikovat **potenciální injektáž SQL**, **přístup z neobvyklého umístění nebo datového centra**, přístup z neznámé **holokaustu nebo potenciálně škodlivé aplikace**a pověření SQL hrubé **síly** - další podrobnosti naleznete v [výstrahách Advanced Threat Protection .](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Oznámení o zjištěných hrozbách můžete dostávat prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [portálu Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) je součástí [rozšířené nabídky zabezpečení dat](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými hrozbami na webu Azure Portal

1. Spusťte portál [https://portal.azure.com](https://portal.azure.com)Azure na webu .
2. Přejděte na konfigurační stránku serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení vyberte **rozšířené zabezpečení dat**.
3. Na stránce konfigurace **rozšířeného zabezpečení dat:**

   - Povolte rozšířené zabezpečení dat na serveru.
   - V **rozšířené nastavení ochrany před internetovými zprávami**, v poli **Odeslat výstrahy do** textového pole, zadejte seznam e-mailů pro příjem výstrah zabezpečení při zjišťování neobvyklých databázových aktivit.
  
   ![Nastavení rozšířené ochrany před hrozbami](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ceny na snímcích obrazovky ne vždy odrážejí aktuální cenu a jsou příkladem.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Nastavení Advanced Threat Protection s využitím PowerShellu

Příklad skriptu [najdete v tématu Konfigurace auditování a rozšířené ochrany před hrozbami pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [rozšířené ochraně před internetovými hrozbami](sql-database-threat-detection-overview.md).
- Další informace o [rozšířené ochraně před internetovými hrozbami ve spravované instanci](sql-database-managed-instance-threat-detection.md).  
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování](sql-database-auditing.md)
- Další informace o [Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na [stránce s cenami databáze SQL.](https://azure.microsoft.com/pricing/details/sql-database/)  
