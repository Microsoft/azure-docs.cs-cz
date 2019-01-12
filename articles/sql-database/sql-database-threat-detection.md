---
title: Detekce hrozeb – Azure SQL Database | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze v jedné databázi nebo elastický fond.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/11/2019
ms.openlocfilehash: 330726eecc19659d978b1072ad02ad6d5a4ccb8b
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244371"
---
# <a name="azure-sql-database-threat-detection-for-single-database"></a>Detekce hrozeb Azure SQL Database pro izolované databáze

Azure SQL [detekce hrozeb](sql-database-threat-detection-overview.md) pro [SQL Database](sql-database-technical-overview.md) izolované databáze detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb můžete identifikovat **útok prostřednictvím injektáže SQL potenciální**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**, a **hrubou silou přihlašovací údaje SQL** – další podrobnosti najdete v [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Budete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailová oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [webu Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí [rozšířené ochrany před internetovými útoky SQL](sql-advanced-threat-protection.md) nabídky (ATP), která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální ochrany ATP v programu SQL. Služba detekce hrozeb účtuje se 15$ za měsíc v rámci logického serveru, se prvních 30 dnů zdarma.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Nastavení detekce hrozeb pro vaši databázi na webu Azure Portal

1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení, vyberte **Advanced Threat Protection**.
3. Na **Advanced Threat Protection** stránka konfigurace:

   - Povolte rozšířenou ochranu před internetovými útoky na serveru.
   - V **nastavení detekce hrozeb**v **odeslat výstrahy Komu** textové pole, zadejte seznam e-mailů přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Nastavení detekce hrozeb pomocí Powershellu

Ukázkový skript, naleznete v tématu [konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Další postup

- Další informace o [detekce hrozeb](sql-database-threat-detection-overview.md).
- Další informace o [detekce hrozeb ve spravované instanci](sql-database-managed-instance-threat-detection.md).  
- Další informace o [SQL Advanced Threat Protection](sql-advanced-threat-protection.md).
- Další informace o [auditování služby Azure SQL Database](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete v tématu [stránku s cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
