---
title: Konfigurace Rozšířené ochrany před internetovými útoky
titleSuffix: Azure SQL Managed Instance
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d2ea14356bf85c795769f1d406f1571f36adaa38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617909"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Konfigurace rozšířené ochrany před internetovými útoky ve spravované instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Rozšířená ochrana před internetovými útoky](../database/threat-detection-overview.md) pro [SPRAVOVANOU instanci Azure SQL](sql-managed-instance-paas-overview.md) detekuje aktivity neobvyklé s neobvyklými a potenciálně škodlivými pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a **hrubou silou přihlašovacích údajů SQL** – další podrobnosti najdete v tématu [výstrahy rozšířené ochrany před hrozbami](../database/threat-detection-overview.md#alerts)

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) nebo [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](../database/threat-detection-overview.md) je součástí [Azure Defenderu pro nabídku SQL](../database/azure-defender-for-sql.md)  , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. K Rozšířené ochraně před internetovými útoky se dá využít a spravovat prostřednictvím centrálního portálu Azure Defender pro SQL Portal.

##  <a name="azure-portal"></a>portál Azure

1. Přihlaste se k  [Azure Portal](https://portal.azure.com). 
2. Přejděte na stránku konfigurace instance spravované instance SQL, kterou chcete chránit. V části **zabezpečení**vyberte **Security Center**.
3. Na stránce konfigurace Azure Defenderu pro SQL
   - Zapněte **Azure** Defender pro SQL.
   - Nakonfigurujte **seznam e-mailů** , které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
   - Vyberte **účet úložiště Azure** , ve kterém se ukládají záznamy auditu neobvyklé Threat.
   - Vyberte **typy rozšířené ochrany před hrozbami** , které byste chtěli nakonfigurovat. Přečtěte si další informace o [výstrahách rozšířené ochrany před hrozbami](../database/threat-detection-overview.md).
4. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady Azure Defenderu pro SQL.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](../database/threat-detection-overview.md).
- Další informace o spravovaných instancích najdete v tématu [co je spravovaná instance Azure SQL](sql-managed-instance-paas-overview.md).
- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky pro Azure SQL Database](../database/threat-detection-configure.md).
- Přečtěte si další informace o [auditování spravované instance SQL](https://go.microsoft.com/fwlink/?linkid=869430).
- Přečtěte si další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
