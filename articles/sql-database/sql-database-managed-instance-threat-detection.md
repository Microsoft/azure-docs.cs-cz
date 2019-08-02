---
title: Konfigurace detekce hrozeb – Azure SQL Database spravovaná instance | Microsoft Docs
description: Detekce hrozeb detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 02/04/2019
ms.openlocfilehash: 6d1f2a9547f01de91a8e7739d827a91154842d7b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567296"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurace detekce hrozeb (Preview) v Azure SQL Database Managed instance

[Detekce hrozeb](sql-database-threat-detection-overview.md) pro [spravovanou instanci](sql-database-managed-instance-index.yml) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **získat přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a nepřímým vynutit **přihlašovací údaje SQL** – zobrazit další podrobnosti [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí nabídky [pokročilých dat zabezpečení](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. K detekci hrozeb se dá dostat a spravovat prostřednictvím portálu centrálních SQL ADS. Služba detekce hrozeb se účtuje 15 $/měsíc na spravovanou instanci s prvních 30 dnů zdarma.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Nastavte detekci hrozeb pro spravovanou instanci v Azure Portal

1. Spusťte Azure Portal v [https://portal.azure.com](https://portal.azure.com).
2. Přejděte na stránku konfigurace spravované instance, kterou chcete chránit. Na stránce **Nastavení** vyberte možnost **detekce hrozeb**.
3. Na stránce konfigurace detekce hrozeb
   - Zapněte detekci **hrozeb.**
   - Nakonfigurujte **seznam e-mailů** , které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
   - Vyberte **účet úložiště Azure** , ve kterém se ukládají záznamy auditu neobvyklé Threat.
4. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady detekce hrozeb.

   ![detekce hrozeb](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [detekci hrozeb](sql-database-threat-detection-overview.md).
- Další informace o spravovaných instancích najdete v tématu [co je spravovaná instance](sql-database-managed-instance.md).
- Přečtěte si další informace o [detekci hrozeb pro](sql-database-threat-detection.md)izolovanou databázi.
- Přečtěte si další informace o [auditování spravované instance](https://go.microsoft.com/fwlink/?linkid=869430).
- Přečtěte si další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
