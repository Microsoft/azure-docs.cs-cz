---
title: Detekce – Azure SQL Database hrozby | Microsoft Docs
description: Detekce hrozeb zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-sql-database-threat-detection"></a>Detekce hrozeb databáze Azure SQL

Služba detekce hrozeb databáze Azure SQL zjistila neobvyklé aktivity, které indikují neobvyklou a potenciálně škodlivé pokusy o přístup k nebo zneužití databáze.

Detekce hrozeb je součástí [pokročilé ochrana před internetovými útoky SQL](sql-advanced-threat-protection.md) nabídky (ATP), což je jednotná balíček pro rozšířené možnosti zabezpečení SQL. Detekce hrozeb lze získat přístup a spravovat prostřednictvím portálu centrální SQL ATP.

## <a name="what-is-threat-detection"></a>Co je detekce hrozeb?

Detekce hrozeb SQL poskytuje novou vrstvu zabezpečení, která uživatelům umožňuje zjistit a reagovat na potenciální hrozby, kdy k nim dojde tím, že poskytuje výstrahy zabezpečení na neobvyklé aktivity. Uživatelé obdrží výstrahu při databáze podezřelé aktivity, potenciální ohrožení zabezpečení, a Injektáž SQL, a také přístup nezvyklé databázové před útoky a dotazuje vzory. Detekce hrozeb SQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivity a doporučujeme akce o tom, jak prozkoumat a zmírnit riziko. Detekce hrozeb SQL umožňuje snadný přístup na potenciální hrozby adres do databáze bez nutnosti odborné zabezpečení nebo Správa pokročilým zabezpečením monitorování systémů. 

Pro úplnou šetření prostředí, se doporučuje povolit [auditování databáze SQL](sql-database-auditing.md), která zapisuje události databáze do auditu protokolu v účtu úložiště Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Nastavení detekce hrozeb pro vaši databázi na portálu Azure
1. Spuštění portálu Azure v [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení, vyberte **Advanced Threat Protection**.
3. Na **Advanced Threat Protection** stránku konfigurace:

   - Povolte Advanced Threat Protection na serveru.
   - V **nastavení detekce hrozby**v **odeslat upozornění do** text zadejte seznam e-mailů výstrahy zabezpečení při zjištění nezvyklé databázové aktivity.
  
   ![Nastavení detekce hrozeb.](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Nastavení detekce hrozeb pomocí prostředí PowerShell

Příklad skriptu najdete v tématu [konfigurace auditování a zjišťování hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte nezvyklé databázové aktivity, při zjištění podezřelé události

Obdržíte e-mail s oznámením při zjištění nezvyklé databázové aktivity. E-mailu obsahuje informace o události podezřelé zabezpečení, včetně povahu neobvyklé aktivity, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mailu obsahuje informace o možné příčiny a doporučené akce ke zkoumání a zmírnit potenciální hrozbu do databáze.

![Sestava neobvyklé aktivity](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Klikněte **zobrazení SQL nedávné** odkaz v e-mailu spuštění portálu Azure a zobrazit stránku výstrahy Azure Security Center, který nabízí přehled active hrozby zjištěné v databázi SQL.

   ![Activty hrozeb](./media/sql-database-threat-detection/active_threats.png)

2. Klikněte na konkrétní výstrahu získat další podrobnosti a akcí pro příčin této hrozby a nápravě budoucí hrozeb.

   Například Injektáž SQL je jedním z nejběžnějších problémů zabezpečení webové aplikace na Internetu, která se používá k útoku datové aplikace. Útočníci využít výhod ohrožení zabezpečení aplikace se zlými úmysly příkazy SQL, do pole pro zadání aplikací, vložit před nedodržením nebo upravovat data v databázi. Podrobnosti výstrah Injektáž SQL výstrahy, zahrnují citlivé příkaz jazyka SQL, který byl zneužití.

   ![Konkrétní výstrahu](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Seznamte se výstrah o zjištěných hrozbách pro vaši databázi na portálu Azure

Detekce hrozeb databáze SQL se integruje se jeho výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé dlaždice detekce hrozeb SQL v databázi a okna SQL ATP na portálu Azure sleduje stav active hrozeb.

Klikněte na tlačítko **výstrahu o detekci hrozeb** spustit Azure Security Center výstrahy stránky a získat přehled o active SQL hrozby zjištěné v databázi.

   ![Výstraha detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alert2 detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure výstrahy detekce hrozeb databáze SQL 
Pro databázi SQL Azure služba detekce hrozeb zjistila neobvyklé aktivity, které indikují neobvyklou a potenciálně škodlivé pokusy o přístup k nebo zneužití databází a ji můžete aktivovat tyto výstrahy:
- **Zranitelnost vůči útoku prostřednictvím injektáže SQL:** Tato výstraha se aktivuje, pokud aplikace v databázi vygeneruje chybný příkaz SQL. Může to znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:
   - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
   - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
- **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL nedávno přihlásil z neobvyklého datového centra Azure. V některých případech výstraha rozpozná legitimní akci (nová aplikace v Azure, Power BI, editor dotazů SQL v Azure). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil s použitím neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další postup

* Další informace o [SQL Advanced Threat Protection](sql-advanced-threat-protection.md). 
* Další informace o [auditování databáze SQL Azure](sql-database-auditing.md)
* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [stránky SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)  
