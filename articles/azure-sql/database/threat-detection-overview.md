---
title: Rozšířená ochrana před internetovými útoky
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení v Azure SQL Database, spravované instanci Azure SQL a Azure synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 27d44ba8be56839f248312e1bdb4b0e4b7761110
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047017"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance--azure-synapse"></a>Rozšířená ochrana před internetovými útoky pro Azure SQL Database, spravovaná instance SQL & Azure synapse
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Rozšířená ochrana před internetovými útoky pro [Azure SQL Database](sql-database-paas-overview.md), [spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) a [Azure synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí [rozšířené nabídky zabezpečení dat](advanced-data-security.md) , což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

## <a name="overview"></a>Přehled

Rozšířená ochrana před internetovými útoky poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelům se zobrazí výstraha o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení a útocích prostřednictvím injektáže SQL a také o vzorcích dotazů neobvyklé Database. Rozšířená ochrana před internetovými útoky integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky usnadňuje řešení potenciálních hrozeb pro databázi, aniž by museli být odborníkem na zabezpečení nebo mohli spravovat pokročilé systémy monitorování zabezpečení.

V případě úplného šetření doporučujeme povolit auditování, které zapisuje databázové události do protokolu auditu ve vašem účtu služby Azure Storage.  Pokud chcete povolit auditování, přečtěte si téma [auditování pro Azure SQL Database a Azure synapse](../../azure-sql/database/auditing-overview.md) nebo [auditování pro SPRAVOVANOU instanci Azure SQL](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Výstrahy

Rozšířená ochrana před internetovými útoky pro Azure SQL Database detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Seznam výstrah pro Azure SQL Database najdete [v tématu výstrahy pro SQL Database a SQL Data Warehouse v Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Prozkoumat detekci podezřelé události

Po detekci neobvykléch databázových aktivit obdržíte e-mailové oznámení. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy aktivit neobvyklé, názvu databáze, názvu serveru, názvu aplikace a času události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby pro databázi.

![Sestava aktivity neobvyklé](./media/threat-detection-overview/anomalous_activity_report.png)

1. Kliknutím na odkaz **Zobrazit nedávné výstrahy SQL** v e-mailu spustíte Azure Portal a zobrazí se stránka Azure Security Center výstrahy, která poskytuje přehled aktivních hrozeb zjištěných v databázi.

   ![Hrozby aktivit](./media/threat-detection-overview/active_threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro šetření této hrozby a opravaí budoucích hrozeb.

   Například injektáže SQL je jedním z nejběžnějších problémů zabezpečení webových aplikací na internetu, které se používají k útoku na aplikace řízené daty. Útočníci využívají chyby zabezpečení aplikací k vkládání škodlivých příkazů SQL do vstupních polí aplikace, při porušení nebo úpravě dat v databázi. V případě výstrah pro vložení SQL zahrnuje podrobnosti výstrahy ohrožený příkaz SQL, který byl zneužit.

   ![Konkrétní výstraha](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-azure-portal"></a>Prozkoumat výstrahy v Azure Portal

Rozšířená ochrana před internetovými útoky integruje své výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé SQL Advanced Threat Protection dlaždice v rámci databáze a okna služby SQL ADS v Azure Portal sledují stav aktivních hrozeb.

Kliknutím na **Rozšířená výstraha ochrany před internetovými útoky** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi.

   ![Výstraha rozšířené ochrany před internetovými útoky](./media/threat-detection-overview/threat_detection_alert.png)

   ![Rozšířená ochrana před internetovými útoky alert2](./media/threat-detection-overview/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky v Azure SQL Database & Azure synapse](threat-detection-configure.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci Azure SQL](../managed-instance/threat-detection-configure.md).
- Přečtěte si další informace o [pokročilém zabezpečení dat](advanced-data-security.md).
- Další informace o [auditování Azure SQL Database](../../azure-sql/database/auditing-overview.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
