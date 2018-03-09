---
title: "Detekce - hrozby spravované Instance Azure SQL Database | Microsoft Docs"
description: "Detekce hrozeb zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze."
services: sql-database
author: rmatchoro
manager: cguyer
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: f8c08bc16d622516958b8bd182179d07edfa4891
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Databáze Azure SQL spravované Instance detekce hrozeb.

Detekce hrozeb SQL zjistí neobvyklé aktivity, které indikují neobvyklou a potenciálně škodlivé pokusí o přístup k nebo zneužití databáze v Azure SQL Database spravované instanci (preview).

## <a name="overview"></a>Přehled

Detekce hrozeb zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení spravované Instance. Detekce hrozeb je teď ve verzi preview pro spravované Instance.

Detekce hrozeb poskytuje novou vrstvu zabezpečení, která uživatelům umožňuje zjistit a reagovat na potenciální hrozby, kdy k nim dojde tím, že poskytuje výstrahy zabezpečení na nezvyklé databázové aktivity. Detekce hrozeb zjednodušuje na potenciální hrozby adresu k instanci spravovaných bez nutnosti odborné zabezpečení nebo spravovat pokročilým zabezpečením monitorování systémů. Pro úplnou šetření prostředí doporučujeme povolit Azure spravované Instance auditování, která zapisuje události databáze do auditu protokolu v účtu úložiště Azure. 

Detekce hrozeb SQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), a každá chráněné spravované Instance se fakturuje za stejnou cenu jako Azure Security Center standardní vrstvy v $15 uzlu/měsíc, kde každé chráněné spravované Instance se počítá jako jeden uzel.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Nastavení detekce hrozeb pro vaše Instance spravované na portálu Azure
1. Spuštění portálu Azure v [https://portal.azure.com](https://portal.azure.com).
2. Přejděte na stránku konfigurace spravované instance, které chcete chránit. V **nastavení** vyberte **detekce hrozeb**. 
3. Na stránce konfigurace detekce hrozeb. 
   - Zapnout **ON** detekce hrozby.
   - Konfigurace **seznam e-mailů** přijímat výstrahy zabezpečení při zjištění nezvyklé databázové aktivity.
   - Vyberte **účtu úložiště Azure** nějž se uloží záznamy auditu neobvyklé hrozeb. 
4.  Klikněte na tlačítko **Uložit** zásady detekce hrozeb nové nebo aktualizované uložit.

   ![Detekce hrozeb.](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklé aktivity spravované Instance při zjištění podezřelé události

1. Obdržíte e-mail s oznámením při zjištění nezvyklé databázové aktivity. 

   E-mailu obsahuje informace o události podezřelé zabezpečení, včetně povahu neobvyklé aktivity, název databáze, název serveru a čas události. Kromě toho poskytuje informace o možné příčiny a doporučené akce ke zkoumání a zmírnit potenciální hrozbu pro spravované Instance.

   ![detekce hrozeb – e-mailu](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Klikněte **zobrazení SQL nedávné** odkaz v e-mailu zobrazit stránku výstrahy Azure Security Center, který nabízí přehled active SQL hrozby zjištěné spravované Instance databáze a spuštění portálu Azure.

   ![aktivní hrozeb](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Klikněte na konkrétní výstrahu získat další podrobnosti a akcí pro příčin této hrozby a nápravě budoucí hrozeb.

   Například Injektáž SQL je jedním z běžné problémy zabezpečení webových aplikací na Internetu. Injektáž SQL se používá k útoku datové aplikace. Útočníci využít výhod ohrožení zabezpečení aplikace se zlými úmysly příkazy SQL, do pole pro zadání aplikací, vložit před nedodržením nebo upravovat data v databázi. Podrobnosti výstrah Injektáž SQL výstrahy, zahrnují citlivé příkaz jazyka SQL, který byl zneužití.

   ![Injektáž SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Spravované Instance detekce hrozeb výstrahy 

Detekce hrozeb pro spravované Instance zjišťuje neobvyklé aktivity, které indikují neobvyklou a potenciálně škodlivé pokusy o přístup k nebo zneužití databází a ji můžete aktivovat tyto výstrahy:
- **Zranitelnost vůči útoku prostřednictvím injektáže SQL:** Tato výstraha se aktivuje, pokud aplikace v databázi vygeneruje chybný příkaz SQL. Může to znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:
 - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
 - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že útočník pokouší vložit škodlivý příkazů SQL pomocí kódu citlivé aplikace nebo uložené procedury.
- **Přístup z neobvyklého umístění**: Toto upozornění se spustí, když dojde ke změně v vzor přístupu pro spravované Instance, kde někdo se přihlásil k instanci spravované z neobvyklého zeměpisné umístění. V některých případech výstrahu rozpozná legitimní akce (nové aplikace nebo operace údržby pro vývojáře). V ostatních případech výstrahu rozpozná škodlivou akci (bývalé zaměstnance, externí útočník a tak dále).
- **Přístup z neobvyklého datového centra Azure**: Toto upozornění se spustí, když dojde ke změně v vzor přístupu pro spravované Instance, kde někdo se přihlásil k instanci spravované z centra dat Azure, která nebyla zobrazena, přístup k této spravované Instance v nedávné době. V některých případech výstrahu rozpozná legitimní akce (nové aplikace v Azure, Power BI, Editor dotazů SQL Azure a tak dále). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého hlavní**: Toto upozornění se spustí, když dojde ke změně v vzor přístupu pro spravované Instance serveru, kde někdo přihlásil k instanci spravované pomocí neobvyklou objektu zabezpečení (uživatel SQL). V některých případech výstrahu rozpozná legitimní akce (operace údržby nové vývojář aplikace). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V ostatních případech výstrahu rozpozná útoku hrubou silou.

## <a name="next-steps"></a>Další postup

- Další informace o spravované Instance najdete v tématu [co je spravované Instance](sql-database-managed-instance.md)
- Další informace o [spravované Instance auditování](https://go.microsoft.com/fwlink/?linkid=869430) 
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
