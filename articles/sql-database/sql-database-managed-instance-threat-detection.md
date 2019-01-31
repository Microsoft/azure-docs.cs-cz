---
title: Konfigurace zjišťování hrozeb – Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze ve spravované instanci.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59a3b4a4e1b08a9a9985836a9f9be44d1eff9c71
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472061"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurace zjišťování hrozeb (Preview) ve spravované instanci Azure SQL Database

Azure SQL [detekce hrozeb](sql-database-threat-detection-overview.md) pro [SQL Database Managed Instance](sql-database-managed-instance-index.yml) detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb můžete identifikovat **útok prostřednictvím injektáže SQL potenciální**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**, a **hrubou silou přihlašovací údaje SQL** – další podrobnosti najdete v [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts).

Budete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailová oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [webu Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí [pokročilé zabezpečení dat SQL](sql-advanced-threat-protection.md) (reklamy) nabídky, která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální SQL reklamy. Služba detekce hrozeb účtuje se 15$ / měsíc za Managed Instance pomocí prvních 30 dnů zdarma.

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
- Další informace o [pro izolované databáze detekce hrozeb](sql-database-threat-detection.md).
- Další informace o [Managed Instance auditování](https://go.microsoft.com/fwlink/?linkid=869430).
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
