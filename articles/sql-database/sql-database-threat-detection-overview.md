---
title: Rozšířená ochrana před internetovými útoky
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální bezpečnostní hrozby v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 0b231adafabff8414b1cf742e1441e938a3fa212
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848190"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection pro Azure SQL Database

Rozšířená ochrana před internetovými útoky pro [Azure SQL Database](sql-database-technical-overview.md) a [Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí nabídky [Rozšířené služby Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

> [!NOTE]
> Toto téma se týká Azure SQL serveru a SQL Database a Azure synapse, které jsou vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá při odkazování na SQL Database a Azure synapse.

## <a name="what-is-advanced-threat-protection"></a>Co je rozšířená ochrana před internetovými útoky

 Rozšířená ochrana před internetovými útoky poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelům se zobrazí výstraha o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení a útocích prostřednictvím injektáže SQL a také o vzorcích dotazů neobvyklé Database. Rozšířená ochrana před internetovými útoky integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky usnadňuje řešení potenciálních hrozeb pro databázi, aniž by museli být odborníkem na zabezpečení nebo mohli spravovat pokročilé systémy monitorování zabezpečení.

V případě úplného šetření doporučujeme povolit [SQL Database auditování](sql-database-auditing.md), které zapisuje databázové události do protokolu auditu ve vašem účtu služby Azure Storage.  

## <a name="advanced-threat-protection-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky

Rozšířená ochrana před internetovými útoky pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Seznam výstrah pro Azure SQL Database najdete [v tématu výstrahy pro SQL Database a SQL Data Warehouse v Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte aktivity databáze neobvyklé při detekci podezřelé události

Po detekci neobvykléch databázových aktivit obdržíte e-mailové oznámení. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy aktivit neobvyklé, názvu databáze, názvu serveru, názvu aplikace a času události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby pro databázi.

![Sestava aktivity neobvyklé](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kliknutím na odkaz **Zobrazit nedávné výstrahy SQL** v e-mailu spustíte Azure Portal a zobrazí se stránka Azure Security Center výstrahy, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.

   ![Hrozby aktivit](./media/sql-database-threat-detection/active_threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro šetření této hrozby a opravaí budoucích hrozeb.

   Například injektáže SQL je jedním z nejběžnějších problémů zabezpečení webových aplikací na internetu, které se používají k útoku na aplikace řízené daty. Útočníci využívají chyby zabezpečení aplikací k vkládání škodlivých příkazů SQL do vstupních polí aplikace, při porušení nebo úpravě dat v databázi. V případě výstrah pro vložení SQL zahrnuje podrobnosti výstrahy ohrožený příkaz SQL, který byl zneužit.

   ![Konkrétní výstraha](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Prozkoumejte rozšířené výstrahy ochrany před hrozbami pro vaši databázi v Azure Portal

Rozšířená ochrana před internetovými útoky integruje své výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé SQL Advanced Threat Protection dlaždice v rámci databáze a okna služby SQL ADS v Azure Portal sledují stav aktivních hrozeb.

Kliknutím na **Rozšířená výstraha ochrany před internetovými útoky** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi.

   ![Výstraha rozšířené ochrany před internetovými útoky](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Rozšířená ochrana před internetovými útoky alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky v databázích s jednou a ve fondu](sql-database-threat-detection.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci](sql-database-managed-instance-threat-detection.md).
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování Azure SQL Database](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
