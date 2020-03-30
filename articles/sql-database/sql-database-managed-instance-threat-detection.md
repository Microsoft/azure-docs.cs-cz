---
title: Konfigurace rozšířené ochrany před internetovými hrozbami – spravovaná instance
description: Pokročilá ochrana před internetovými zprávami detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze ve spravované instanci.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822552"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>Konfigurace rozšířené ochrany před internetovými servery ve spravované instanci Azure SQL Database

[Pokročilá ochrana před internetovými zprávami](sql-database-threat-detection-overview.md) pro [spravovanou instanci](sql-database-managed-instance-index.yml) detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Pokročilá ochrana před internetovými útoky může identifikovat **potenciální injektáž SQL**, **přístup z neobvyklého umístění nebo datového centra**, přístup z neznámé **holokaustu nebo potenciálně škodlivé aplikace**a pověření SQL hrubé **síly** - další podrobnosti naleznete v [výstrahách Advanced Threat Protection .](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Oznámení o zjištěných hrozbách můžete dostávat prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [portálu Azure](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Advanced Threat Protection](sql-database-threat-detection-overview.md) je součástí [rozšířené nabídky zabezpečení dat](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými hrozbami na webu Azure Portal

1. Spusťte portál [https://portal.azure.com](https://portal.azure.com)Azure na webu .
2. Přejděte na konfigurační stránku spravované instance, kterou chcete chránit. Na stránce **Nastavení** vyberte **Upřesnit zabezpečení dat**.
3. Na stránce Konfigurace rozšířeného zabezpečení dat
   - **Zapněte** rozšířené zabezpečení dat.
   - Nakonfigurujte **seznam e-mailů** pro příjem výstrah zabezpečení při zjišťování neobvyklých databázových aktivit.
   - Vyberte **účet úložiště Azure,** kde se ukládají záznamy auditu neobvyklých hrozeb.
   - Vyberte **typy rozšířené ochrany před hrozbami,** které chcete nakonfigurovat. Další informace o [upozorněních na pokročilou ochranu před hrozbami](sql-database-threat-detection-overview.md).
4. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady rozšířeného zabezpečení dat.

   ![Rozšířená ochrana před internetovými útoky](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > Ceny na snímcích obrazovky ne vždy odrážejí aktuální cenu a jsou příkladem.

## <a name="next-steps"></a>Další kroky

- Další informace o [rozšířené ochraně před internetovými hrozbami](sql-database-threat-detection-overview.md).
- Informace o spravovaných instancích najdete [v tématu Co je spravovaná instance](sql-database-managed-instance.md).
- Další informace o [rozšířené ochraně před internetovými hrozbami pro jednu databázi](sql-database-threat-detection.md).
- Další informace o [auditování spravovaných instancí](https://go.microsoft.com/fwlink/?linkid=869430).
- Přečtěte si další informace o [Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).
