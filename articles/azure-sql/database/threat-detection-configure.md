---
title: Konfigurace Rozšířené ochrany před internetovými útoky
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby pro databázi v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321539"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurace rozšířené ochrany před internetovými útoky pro Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Rozšířená ochrana před internetovými útoky](threat-detection-overview.md) pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a **hrubou silou přihlašovacích údajů SQL** – další podrobnosti najdete v tématu [výstrahy rozšířené ochrany před hrozbami](threat-detection-overview.md#alerts)

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](threat-detection-overview.md#explore-detection-of-a-suspicious-event) nebo [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](threat-detection-overview.md) je součástí [rozšířené nabídky zabezpečení dat](advanced-data-security.md) , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím centrálního portálu SQL Advanced Data Security.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými útoky v Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru, který chcete chránit. V nastavení zabezpečení vyberte **Upřesnit zabezpečení dat**.
3. Na stránce konfigurace **rozšířeného zabezpečení dat** :

   - Povolit pokročilé zabezpečení dat na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení rozšířené ochrany před internetovými útoky](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Nastavení Advanced Threat Protection s využitím PowerShellu

Příklad skriptu najdete v tématu [Konfigurace auditování a rozšířené ochrany před internetovými útoky pomocí prostředí PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-overview.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci SQL](../managed-instance/threat-detection-configure.md).  
- Přečtěte si další informace o [pokročilém zabezpečení dat](advanced-data-security.md).
- Další informace o [auditování](../../azure-sql/database/auditing-overview.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
