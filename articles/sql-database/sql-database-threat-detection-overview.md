---
title: Rozšířená ochrana před internetovými útoky
description: Pokročilá ochrana před hrozbami detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení v Azure SQL Database.
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
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124761"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection pro Azure SQL Database

Pokročilá ochrana před hrozbami pro [Azure SQL Database](sql-database-technical-overview.md) a Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Advanced Threat Protection je součástí nabídky [Advanced data Security](sql-database-advanced-data-security.md) (ADS), což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Správa Advanced Threat Protection je dostupná prostřednictvím centrálního portálu SQL ADS.

> [!NOTE]
> Toto téma se týká Serveru Azure SQL a databáze SQL a Azure Synapse, které jsou vytvořené na serveru Azure SQL. Pro jednoduchost SQL Database se používá při odkazování na SQL Database a Azure Synapse.

## <a name="what-is-advanced-threat-protection"></a>Co je pokročilá ochrana před hrozbami

 Pokročilá ochrana před hrozbami poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat potenciální hrozby a reagovat na ně tak, že poskytují výstrahy zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění na podezřelé databázové aktivity, potenciální chyby zabezpečení a útoky injektáže SQL, stejně jako neobvyklé přístup k databázi a dotazy vzory. Pokročilá ochrana před internetovými zprávami integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), které obsahují podrobnosti o podezřelé aktivitě a doporučují akce, jak prozkoumat a zmírnit hrozbu. Pokročilá ochrana před hrozbami usnadňuje řešení potenciálních hrozeb pro databázi bez nutnosti být odborníkem na zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení.

Pro úplné šetření prostředí se doporučuje povolit [auditování databáze SQL](sql-database-auditing.md), který zapisuje události databáze do protokolu auditování v účtu úložiště Azure.  

## <a name="advanced-threat-protection-alerts"></a>Pokročilá upozornění na ochranu před hrozbami

Pokročilá ochrana před internetovými technologiemi pro Azure SQL Database detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:

- **Chyba zabezpečení vůči vkládání SQL**: Tato výstraha se aktivuje, když aplikace generuje vadný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:

  - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
  - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
- **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL nedávno přihlásil z neobvyklého datového centra Azure. V některých případech výstraha rozpozná legitimní akci (nová aplikace v Azure, Power BI, editor dotazů SQL v Azure). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil s použitím neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklé databázové aktivity po zjištění podezřelé události

Obdržíte e-mailové oznámení po zjištění neobvyklé databázové aktivity. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy anomální chaktivity, název databáze, název serveru, název aplikace a čas události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciálního ohrožení databáze.

![Sestava anomální aktivity](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Kliknutím na odkaz **Zobrazit poslední výstrahy SQL** v e-mailu spusťte portál Azure portal a zobrazili stránku výstrah Centra zabezpečení Azure, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.

   ![Ohrožení aktivity](./media/sql-database-threat-detection/active_threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro vyšetřování této hrozby a nápravu budoucích hrozeb.

   Například vkládání SQL je jedním z nejběžnějších problémů zabezpečení webových aplikací v Síti Internet, který se používá k útoku na aplikace řízené daty. Útočníci využívají výhod zranitelnosti aplikací k vložení škodlivých příkazů SQL do polí pro zadávání aplikací, porušení nebo úpravy dat v databázi. Pro výstrahy sql injection podrobnosti výstrahy patří zranitelné příkaz SQL, který byl zneužit.

   ![Konkrétní výstraha](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Prozkoumejte pokročilá upozornění na ochranu před hrozbami pro vaši databázi na webu Azure Portal

Advanced Threat Protection integruje své výstrahy s [Azure security center](https://azure.microsoft.com/services/security-center/). Živé dlaždice SQL Advanced Threat Protection v rámci databáze a rozhraní SQL ADS na portálu Azure sledují stav aktivních hrozeb.

Kliknutím na **upozornění Rozšířené ochrany před hrozbami** spusťte stránku výstrah Centra zabezpečení Azure a získejte přehled o aktivních hrozbách SQL zjištěných v databázi.

   ![Výstraha pokročilé ochrany před hrozbami](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Výstraha pokročilé ochrany před hrozbami2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [rozšířené ochraně před internetovými hrozbami v jedné a sdružené databázi](sql-database-threat-detection.md).
- Další informace o [rozšířené ochraně před internetovými hrozbami ve spravované instanci](sql-database-managed-instance-threat-detection.md).
- Přečtěte si další informace o [rozšířeném zabezpečení dat](sql-database-advanced-data-security.md).
- Další informace o [auditování Azure SQL Database](sql-database-auditing.md)
- Další informace o [Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Další informace o cenách najdete na [stránce s cenami databáze SQL.](https://azure.microsoft.com/pricing/details/sql-database/)  
