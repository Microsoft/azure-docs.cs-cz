---
title: Detekce hrozeb – Azure SQL Database | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení ke službě Azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 2f52cbaf5d50eca13a87ff8e745b0f8baefbe23e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210365"
---
# <a name="azure-sql-database-threat-detection"></a>Detekce hrozeb Azure SQL Database

Detekce hrozeb Azure SQL pro [Azure SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Detekce hrozeb je součástí [pokročilé zabezpečení dat SQL](sql-advanced-threat-protection.md) (reklamy) nabídky, která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Detekce hrozeb je možné získat přístup a spravovat prostřednictvím portálu pro centrální SQL reklamy.

> [!NOTE]
> Toto téma se týká k Azure SQL serveru a databází SQL Database a SQL Data Warehouse, které jsou vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse.


## <a name="what-is-threat-detection"></a>Co je detekce hrozeb

Detekce hrozeb SQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování upozornění zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění při podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, a útok prostřednictvím injektáže SQL, útoky, a také přístup neobvyklé databázové a dotazy vzory. Detekce hrozeb SQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivity a doporučuje akce na tom, jak zkoumat a zmírnit hrozby. Funkce detekce hrozeb usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé zabezpečení systémy pro monitorování.

Zajišťuje úplné šetření, se doporučuje povolit [auditování služby SQL Database](sql-database-auditing.md), která zapisuje události auditu databáze protokolu ve vašem účtu úložiště Azure.  

## <a name="azure-sql-database-threat-detection-alerts"></a>Upozornění detekce hrozeb služby SQL Database v Azure

Detekce hrozeb pro Azure SQL Database detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a ji můžete spustit následující upozornění:

- **Zranitelnost vůči útoku prostřednictvím injektáže SQL**: Tato výstraha se aktivuje, pokud aplikace vygeneruje Chybný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:

  - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
  - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL**: Tato výstraha se aktivuje při výskytu aktivního zneužití zranitelnosti identifikované aplikace zranitelnost vůči útoku prostřednictvím injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklého datového centra Azure, která už na tomto serveru se v poslední době. V některých případech výstraha rozpozná legitimní akci (nová aplikace v Azure, Power BI, editor dotazů SQL v Azure). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru pomocí neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace**: Tato výstraha se aktivuje, když se potenciálně škodlivé aplikace používá pro přístup k databázi. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Přihlašovací údaje SQL útok hrubou silou**: Tato výstraha se aktivuje po neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklé databázové aktivity při zjištění podezřelé události

Obdržíte e-mailové oznámení po detekci neobvyklých databázových aktivit. E-mailu obsahuje informace o podezřelé události zabezpečení včetně povahy neobvyklých aktivit, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mail obsahuje informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení databáze.

![Sestava neobvyklých aktivit](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klikněte na tlačítko **zobrazené poslední výstrahy SQL** odkaz v e-mailu můžete spustit na portálu Azure portal a zobrazení stránky s upozorněními Azure Security Center, která obsahuje základní informace o aktivní zjištěných hrozeb na SQL database.

   ![Aktivita hrozby](./media/sql-database-threat-detection/active_threats.png)

2. Kliknutím na konkrétní výstrahu zobrazíte další podrobnosti a akce pro zkoumání této hrozby a oprava budoucími hrozbami.

   Například útok prostřednictvím injektáže SQL je jedním z nejběžnějších problémů zabezpečení webových aplikací na Internetu, který se používá k útoku na aplikace řízené daty. Útočníci využívají ohrožení zabezpečení aplikací k vložit škodlivé příkazy SQL do vstupních polí aplikace, porušení nebo úpravy dat v databázi. Podrobnosti výstrahy pro výstrahy útok prostřednictvím injektáže SQL, zahrnují zranitelné příkaz jazyka SQL, který byl zneužít.

   ![Konkrétní výstrahy](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Prozkoumejte výstrahy detekce hrozeb pro vaši databázi na webu Azure Portal

Detekce hrozeb služby SQL Database integruje výstrahy se jeho službou [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živé dlaždice zjišťování do databáze a oknech reklamy SQL na webu Azure Portal sledovat stav aktivní hrozby hrozeb SQL.

Klikněte na tlačítko **výstrahu o detekci hrozeb** spustit Azure Security Center oznámení stránce a získejte přehled o aktivní hrozby SQL na databázi ani na datový sklad zjistí.

   ![Výstrahy detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert2 detekce hrozeb](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Další postup

- Další informace o [detekce hrozeb v izolované databáze](sql-database-threat-detection.md).
- Další informace o [detekce hrozeb ve spravované instanci](sql-database-managed-instance-threat-detection.md).
- Další informace o [SQL rozšířené zabezpečení dat](sql-advanced-threat-protection.md).
- Další informace o [auditování služby Azure SQL Database](sql-database-auditing.md)
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete v tématu [stránku s cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
