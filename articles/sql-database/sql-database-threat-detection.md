---
title: Rozšířená ochrana před internetovými útoky – Azure SQL Database | Microsoft Docs
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby pro databázi v jedné databázi nebo elastickém fondu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 755a3b391cb7b4909169b034cc8d89892ec2ed05
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816538"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database rozšířené ochrany před internetovými útoky pro databáze s jednou nebo ve fondu

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) pro databáze s jednou a fondem detekuje aktivity neobvyklé ukazující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **získat přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace a získat** **pověření hrubou silou SQL** – viz Další podrobnosti najdete v podrobnostech [rozšířené ochrany před hrozbami](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) je součástí nabídky [Rozšířené služby Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím centrálního portálu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými útoky v Azure Portal

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení vyberte **Upřesnit zabezpečení dat**.
3. Na stránce konfigurace **rozšířeného zabezpečení dat** :

   - Povolit pokročilé zabezpečení dat na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení rozšířené ochrany před internetovými útoky](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ceny na snímcích obrazovky vždy neodpovídají aktuální ceně a jsou příkladem.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Nastavení rozšířené ochrany před internetovými útoky pomocí PowerShellu

Příklad skriptu najdete v tématu [Konfigurace auditování a rozšířené ochrany před internetovými útoky pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](sql-database-threat-detection-overview.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci](sql-database-managed-instance-threat-detection.md).  
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
