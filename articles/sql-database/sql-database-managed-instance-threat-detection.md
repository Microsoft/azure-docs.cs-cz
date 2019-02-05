---
title: Konfigurace zjišťování hrozeb – Azure SQL Database managed instance | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze do spravované instance.
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
ms.date: 02/04/2019
ms.openlocfilehash: d8522967154a69b8473475932f2074bc98b4f24d
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731219"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurace zjišťování hrozeb (Preview) ve spravované instanci Azure SQL Database

[Detekce hrozeb](sql-database-threat-detection-overview.md) pro [spravovanou instanci](sql-database-managed-instance-index.yml) detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Detekce hrozeb můžete identifikovat **útok prostřednictvím injektáže SQL potenciální**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**, a **hrubou silou přihlašovací údaje SQL** – další podrobnosti najdete v [výstrahy detekce hrozeb](sql-database-threat-detection-overview.md#threat-detection-alerts).

Budete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailová oznámení](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) nebo [webu Azure portal](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)

[Detekce hrozeb](sql-database-threat-detection-overview.md) je součástí [rozšířené zabezpečení dat](sql-database-advanced-data-security.md) (reklamy) nabídky, která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální SQL reklamy. Služba detekce hrozeb účtuje se 15$ / měsíc za spravované instance s prvních 30 dnů zdarma.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Nastavení detekce hrozeb pro vaše spravované instance na webu Azure Portal

1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace spravované instance, které chcete chránit. V **nastavení** stránce **detekce hrozeb**.
3. Na stránce konfigurace detekce hrozeb
   - Zapnout **ON** detekce hrozeb.
   - Konfigurace **seznam e-mailů** přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
   - Vyberte **účtu služby Azure storage** kde jsou uloženy záznamy auditu neobvyklé hrozeb.
4. Klikněte na tlačítko **Uložit** uložte zásadu detekce hrozeb nová nebo aktualizovaná.

   ![Detekce hrozeb](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Další postup

- Další informace o [detekce hrozeb](sql-database-threat-detection-overview.md).
- Další informace o spravované instance najdete v tématu [co je managed instance](sql-database-managed-instance.md).
- Další informace o [pro izolované databáze detekce hrozeb](sql-database-threat-detection.md).
- Další informace o [managed instance auditování](https://go.microsoft.com/fwlink/?linkid=869430).
- Další informace o [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro).
