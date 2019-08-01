---
title: Detekce hrozeb – Azure SQL Database | Microsoft Docs
description: Detekce hrozeb detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby pro databázi v jedné databázi nebo elastickém fondu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 5549d016978e8bf9491c3745e335e3c4c793212c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566336"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Azure SQL Database detekce hrozeb pro databáze s jednou nebo ve fondu

[Detekce hrozeb](sql-database-threat-detection-overview.md) pro databáze s jednou a fondem detekuje aktivity neobvyklé, které indikují neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **získat přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a nepřímým vynutit **přihlašovací údaje SQL** – zobrazit další podrobnosti [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí nabídky [pokročilých dat zabezpečení](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. K detekci hrozeb se dá dostat a spravovat prostřednictvím portálu centrálních SQL ADS. Balíček pro pokročilou zabezpečení dat se účtuje 15 $/měsíc na logický Server, přičemž prvních 30 dnů se zaúčtuje zdarma.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Nastavte detekci hrozeb pro vaši databázi v Azure Portal

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení vyberte **Upřesnit zabezpečení dat**.
3. Na stránce konfigurace **rozšířeného zabezpečení dat** :

   - Povolit pokročilé zabezpečení dat na serveru.
   - V **Nastavení detekce hrozeb**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Nastavení detekce hrozeb pomocí prostředí PowerShell

Příklad skriptu najdete v tématu [Konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [detekci hrozeb](sql-database-threat-detection-overview.md).
- Přečtěte si další informace o [detekci hrozeb ve spravované instanci](sql-database-managed-instance-threat-detection.md).  
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
