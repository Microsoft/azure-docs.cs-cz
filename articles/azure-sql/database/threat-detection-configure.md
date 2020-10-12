---
title: Konfigurace Rozšířené ochrany před internetovými útoky
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby pro databázi v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: 678de4354012dcea5b954980109fd71c1004aca4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619283"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Konfigurace rozšířené ochrany před internetovými útoky pro Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Rozšířená ochrana před internetovými útoky](threat-detection-overview.md) pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a **hrubou silou přihlašovacích údajů SQL** – další podrobnosti najdete v tématu [výstrahy rozšířené ochrany před hrozbami](threat-detection-overview.md#alerts)

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](threat-detection-overview.md#explore-detection-of-a-suspicious-event) nebo [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](threat-detection-overview.md) je součástí [Azure Defenderu pro nabídku SQL](azure-defender-for-sql.md) , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. K Rozšířené ochraně před internetovými útoky se dá využít a spravovat prostřednictvím centrálního portálu Azure Defender pro SQL Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Nastavení rozšířené ochrany před internetovými útoky v Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru, který chcete chránit. V nastavení zabezpečení vyberte **Security Center**.
3. Na stránce konfigurace v programu **Azure Defender** :

   - Povolte na serveru Azure Defender.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Nastavení Advanced Threat Protection s využitím PowerShellu

Příklad skriptu najdete v tématu [Konfigurace auditování a rozšířené ochrany před internetovými útoky pomocí prostředí PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](threat-detection-overview.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci SQL](../managed-instance/threat-detection-configure.md).  
- Přečtěte si další informace o [Azure Defenderu pro SQL](azure-defender-for-sql.md).
- Další informace o [auditování](../../azure-sql/database/auditing-overview.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
