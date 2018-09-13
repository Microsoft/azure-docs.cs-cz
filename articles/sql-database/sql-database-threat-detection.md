---
title: Detekce hrozeb – Azure SQL Database | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.subservice: advanced-threat-protection
ms.custom: security
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: vanto
ms.openlocfilehash: 1f30d55bea825549453d9c8a3077ad17756e6a6f
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714241"
---
# <a name="azure-sql-database-threat-detection"></a>Detekce hrozeb Azure SQL Database

Detekce hrozeb Azure SQL Database detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Detekce hrozeb je součástí [rozšířené ochrany před internetovými útoky SQL](sql-advanced-threat-protection.md) nabídky (ATP), která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální ochrany ATP v programu SQL.

## <a name="what-is-threat-detection"></a>Co je detekce hrozeb?

Detekce hrozeb SQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování upozornění zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění při podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, a útok prostřednictvím injektáže SQL, útoky, a také přístup neobvyklé databázové a dotazy vzory. Detekce hrozeb SQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivity a doporučuje akce na tom, jak zkoumat a zmírnit hrozby. Funkce detekce hrozeb usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé zabezpečení systémy pro monitorování. 

Zajišťuje úplné šetření, se doporučuje povolit [auditování služby SQL Database](sql-database-auditing.md), která zapisuje události auditu databáze protokolu ve vašem účtu úložiště Azure.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Nastavení detekce hrozeb pro vaši databázi na webu Azure Portal
1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na stránku konfigurace serveru Azure SQL Database, který chcete chránit. V nastavení zabezpečení, vyberte **Advanced Threat Protection**.
3. Na **Advanced Threat Protection** stránka konfigurace:

   - Povolte rozšířenou ochranu před internetovými útoky na serveru.
   - V **nastavení detekce hrozeb**v **odeslat výstrahy Komu** textové pole, zadejte seznam e-mailů přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Nastavení detekce hrozeb pomocí Powershellu

Ukázkový skript, naleznete v tématu [konfigurace auditování a detekce hrozeb pomocí prostředí PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklé databázové aktivity při zjištění podezřelé události

Obdržíte e-mailové oznámení po detekci neobvyklých databázových aktivit. E-mailu obsahuje informace o podezřelé události zabezpečení včetně povahy neobvyklých aktivit, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mail obsahuje informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení databáze.

![Sestava neobvyklých aktivit](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Klikněte na tlačítko **zobrazené poslední výstrahy SQL** odkaz v e-mailu můžete spustit na portálu Azure portal a zobrazení stránky s upozorněními Azure Security Center, která obsahuje základní informace o aktivní zjištěných hrozeb na SQL database.

   ![Aktivity hrozby](./media/sql-database-threat-detection/active_threats.png)

2. Kliknutím na konkrétní výstrahu zobrazíte další podrobnosti a akce pro zkoumání této hrozby a oprava budoucími hrozbami.

   Například útok prostřednictvím injektáže SQL je jedním z nejběžnějších problémů zabezpečení webových aplikací na Internetu, který se používá k útoku na aplikace řízené daty. Útočníci využívají ohrožení zabezpečení aplikací k vložit škodlivé příkazy SQL do vstupních polí aplikace, porušení nebo úpravy dat v databázi. Podrobnosti výstrahy pro výstrahy útok prostřednictvím injektáže SQL, zahrnují zranitelné příkaz jazyka SQL, který byl zneužít.

   ![Konkrétní výstrahy](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Prozkoumejte výstrahy detekce hrozeb pro vaši databázi na webu Azure Portal

Detekce hrozeb služby SQL Database integruje výstrahy se jeho službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé dlaždice detekce hrozeb SQL v databázi a oken ochrany ATP v programu SQL na webu Azure Portal sleduje stav aktivní hrozby.

Klikněte na tlačítko **výstrahu o detekci hrozeb** spustit Azure Security Center oznámení stránce a získejte přehled o aktivní hrozby SQL v databázi nalezen.

   ![Výstrahy detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alert2 detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Upozornění detekce hrozeb služby SQL Database v Azure 
Detekce hrozeb pro Azure SQL Database detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a ji můžete spustit následující upozornění:
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
* Další informace o [auditování služby Azure SQL Database](sql-database-auditing.md)
* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [stránku s cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
