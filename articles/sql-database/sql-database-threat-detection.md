---
title: Konfigurace rozšířené ochrany před internetovými útoky – Azure SQL Database
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby pro databázi v jedné databázi nebo elastickém fondu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8a6d40f37dec3488009a702d52d6437cd345717b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686952"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database rozšířené ochrany před internetovými útoky pro databáze s jednou nebo ve fondu

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) pro databáze s jednou a fondem detekuje aktivity neobvyklé ukazující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **získat přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace a získat** **pověření hrubou silou SQL** – viz Další podrobnosti najdete v podrobnostech [rozšířené ochrany před hrozbami](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) je součástí nabídky [Rozšířené služby Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím centrálního portálu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými útoky v Azure Portal

1. [https://portal.azure.com](https://portal.azure.com)spusťte Azure Portal.
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení vyberte **Upřesnit zabezpečení dat**.
3. Na stránce konfigurace **rozšířeného zabezpečení dat** :

   - Povolit pokročilé zabezpečení dat na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení rozšířené ochrany před internetovými útoky](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ceny na snímcích obrazovky vždy neodpovídají aktuální ceně a jsou příkladem.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Nastavení rozšířené ochrany před internetovými útoky pomocí PowerShellu

Příklad skriptu najdete v tématu [Konfigurace auditování a rozšířené ochrany před internetovými útoky pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](sql-database-threat-detection-overview.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci](sql-database-managed-instance-threat-detection.md).  
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
