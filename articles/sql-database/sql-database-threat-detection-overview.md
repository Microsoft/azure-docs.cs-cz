---
title: Rozšířená ochrana před internetovými útoky – Azure SQL Database
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
ms.date: 03/31/2019
ms.openlocfilehash: fca68fbc1ee30b1df100255e463eca2fc895980b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686971"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection pro Azure SQL Database

Rozšířená ochrana před internetovými útoky pro [Azure SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí nabídky [Rozšířené služby Data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím centrálního portálu SQL ADS.

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>Co je rozšířená ochrana před internetovými útoky

 Rozšířená ochrana před internetovými útoky poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelům se zobrazí výstraha o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení a útocích prostřednictvím injektáže SQL a také o vzorcích dotazů neobvyklé Database. Rozšířená ochrana před internetovými útoky integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky usnadňuje řešení potenciálních hrozeb pro databázi, aniž by museli být odborníkem na zabezpečení nebo mohli spravovat pokročilé systémy monitorování zabezpečení.

V případě úplného šetření doporučujeme povolit [SQL Database auditování](sql-database-auditing.md), které zapisuje databázové události do protokolu auditu ve vašem účtu služby Azure Storage.  

## <a name="advanced-threat-protection-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky

Rozšířená ochrana před internetovými útoky pro Azure SQL Database detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:

- **Zranitelnost vůči INJEKTÁŽE SQL**: Tato výstraha se aktivuje, když aplikace vygeneruje chybný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:

  - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
  - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
- **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL nedávno přihlásil z neobvyklého datového centra Azure. V některých případech výstraha rozpozná legitimní akci (nová aplikace v Azure, Power BI, editor dotazů SQL v Azure). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil s použitím neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

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

Kliknutím na **Rozšířená výstraha ochrany před internetovými útoky** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi nebo datovém skladu.

   ![Výstraha rozšířené ochrany před internetovými útoky](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Rozšířená ochrana před internetovými útoky alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky v databázích s jednou a ve fondu](sql-database-threat-detection.md).
- Přečtěte si víc o [Rozšířené ochraně před internetovými útoky ve spravované instanci](sql-database-managed-instance-threat-detection.md).
- Přečtěte si další informace o [pokročilém zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování Azure SQL Database](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na stránce s [cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
