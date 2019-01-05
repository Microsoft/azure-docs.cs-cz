---
title: Detekce hrozeb – Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: c59d0ea489343dbf748412910c4f759f601de0e2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042372"
---
# <a name="azure-sql-database-managed-instance-threat-detection-preview"></a>Azure SQL Database Managed Instance detekce hrozeb (Preview)

Azure SQL [detekce hrozeb](sql-database-threat-detection-overview.md) pro [SQL Database Managed Instance](sql-database-managed-instance-index.yml) detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb můžete identifikovat **útok prostřednictvím injektáže SQL potenciální**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**, a **hrubou silou přihlašovací údaje SQL** – další podrobnosti najdete v [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Budete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailová oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [webu Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí [rozšířené ochrany před internetovými útoky SQL](sql-advanced-threat-protection.md) nabídky (ATP), která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální ochrany ATP v programu SQL. Služba detekce hrozeb účtuje se 15$ / měsíc za Managed Instance pomocí prvních 30 dnů zdarma.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Nastavení detekce hrozeb pro Managed Instance na webu Azure Portal

1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na konfigurační stránku Managed Instance, které chcete chránit. V **nastavení** stránce **detekce hrozeb**.
3. Na stránce konfigurace detekce hrozeb
   - Zapnout **ON** detekce hrozeb.
   - Konfigurace **seznam e-mailů** přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
   - Vyberte **účtu služby Azure storage** kde jsou uloženy záznamy auditu neobvyklé hrozeb.
4. Klikněte na tlačítko **Uložit** uložte zásadu detekce hrozeb nová nebo aktualizovaná.

   ![Detekce hrozeb](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Další postup

- Další informace o [detekce hrozeb](sql-database-threat-detection-overview.md).
- Další informace o Managed Instance, přečtěte si téma [co je Managed Instance](sql-database-managed-instance.md).
- Další informace o [detekce hrozeb pro izolované databáze](sql-database-threat-detection.md).
- Další informace o [Managed Instance auditování](https://go.microsoft.com/fwlink/?linkid=869430).
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
