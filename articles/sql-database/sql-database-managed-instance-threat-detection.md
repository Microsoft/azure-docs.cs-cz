---
title: Konfigurace rozšířené ochrany před internetovými útoky – spravovaná instance
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 69292a934af8b8777f11ab58ed3fe306abf8b408
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822552"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Konfigurace rozšířené ochrany před internetovými útoky v Azure SQL Database Managed instance

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) pro [spravovanou instanci](sql-database-managed-instance-index.yml) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **získat přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace a získat** **pověření hrubou silou SQL** – viz Další podrobnosti najdete v podrobnostech [rozšířené ochrany před hrozbami](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](sql-database-threat-detection-overview.md) je součástí nabídky [Rozšířené služby Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím centrálního portálu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými útoky v Azure Portal

1. [https://portal.azure.com](https://portal.azure.com)spusťte Azure Portal.
2. Přejděte na stránku konfigurace spravované instance, kterou chcete chránit. Na stránce **Nastavení** vyberte **Upřesnit zabezpečení dat**.
3. Na stránce Konfigurace rozšířeného zabezpečení dat
   - Zapněte **Pokročilé** zabezpečení dat.
   - Nakonfigurujte **seznam e-mailů** , které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
   - Vyberte **účet úložiště Azure** , ve kterém se ukládají záznamy auditu neobvyklé Threat.
   - Vyberte **typy rozšířené ochrany před hrozbami** , které byste chtěli nakonfigurovat. Přečtěte si další informace o [výstrahách rozšířené ochrany před hrozbami](sql-database-threat-detection-overview.md).
4. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady zabezpečení dat.

   ![Rozšířená ochrana před internetovými útoky](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Ceny na snímcích obrazovky vždy neodpovídají aktuální ceně a jsou příkladem.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](sql-database-threat-detection-overview.md).
- Další informace o spravovaných instancích najdete v tématu [co je spravovaná instance](sql-database-managed-instance.md).
- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky pro](sql-database-threat-detection.md)izolovanou databázi.
- Přečtěte si další informace o [auditování spravované instance](https://go.microsoft.com/fwlink/?linkid=869430).
- Přečtěte si další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
