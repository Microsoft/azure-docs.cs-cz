---
title: Konfigurace Rozšířené ochrany před internetovými útoky
titleSuffix: Azure SQL Managed Instance
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686320"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Konfigurace rozšířené ochrany před internetovými útoky ve spravované instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Rozšířená ochrana před internetovými útoky](../database/threat-detection-overview.md) pro [SPRAVOVANOU instanci Azure SQL](sql-managed-instance-paas-overview.md) detekuje aktivity neobvyklé s neobvyklými a potenciálně škodlivými pokusy o přístup k databázím nebo jejich zneužití. Rozšířená ochrana před internetovými útoky může identifikovat **potenciální INJEKTÁŽE SQL**, **přístup z neobvyklého umístění nebo datového centra**, **přístup z neznámého objektu zabezpečení nebo potenciálně škodlivé aplikace**a **hrubou silou přihlašovacích údajů SQL** – další podrobnosti najdete v tématu [výstrahy rozšířené ochrany před hrozbami](../database/threat-detection-overview.md#alerts)

Můžete dostávat oznámení o zjištěných hrozbách prostřednictvím [e-mailových oznámení](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) nebo [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Rozšířená ochrana před internetovými útoky](../database/threat-detection-overview.md) je součástí [rozšířené nabídky zabezpečení dat](../database/advanced-data-security.md) , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

##  <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com). 
2. Přejděte na stránku konfigurace instance spravované instance SQL, kterou chcete chránit. Na stránce **Nastavení** vyberte **Upřesnit zabezpečení dat**.
3. Na stránce Konfigurace rozšířeného zabezpečení dat
   - Zapněte **Pokročilé** zabezpečení dat.
   - Nakonfigurujte **seznam e-mailů** , které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
   - Vyberte **účet úložiště Azure** , ve kterém se ukládají záznamy auditu neobvyklé Threat.
   - Vyberte **typy rozšířené ochrany před hrozbami** , které byste chtěli nakonfigurovat. Přečtěte si další informace o [výstrahách rozšířené ochrany před hrozbami](../database/threat-detection-overview.md).
4. Kliknutím na **Uložit** uložte nové nebo aktualizované zásady zabezpečení dat.

   ![Rozšířená ochrana před internetovými útoky](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky](../database/threat-detection-overview.md).
- Další informace o spravovaných instancích najdete v tématu [co je spravovaná instance Azure SQL](sql-managed-instance-paas-overview.md).
- Přečtěte si další informace o [Rozšířené ochraně před internetovými útoky pro Azure SQL Database](../database/threat-detection-configure.md).
- Přečtěte si další informace o [auditování spravované instance SQL](https://go.microsoft.com/fwlink/?linkid=869430).
- Přečtěte si další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
