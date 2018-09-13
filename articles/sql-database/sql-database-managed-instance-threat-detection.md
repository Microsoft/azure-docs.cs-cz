---
title: Detekce hrozeb – Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: vanto
ms.openlocfilehash: 76033438ad785412aa97358d80b5e4fdbf4c2bfb
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716991"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database Managed Instance detekce hrozeb

Detekce hrozeb detekuje neobvyklé aktivity a potenciálně nebezpečné pokusí o přístup k databázím nebo jejich zneužití v Azure SQL Database Managed Instance (preview).

## <a name="overview"></a>Přehled

Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení do Managed Instance. Detekce hrozeb je teď ve verzi preview pro Managed Instance.

Detekce hrozeb poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování výstrah zabezpečení na neobvyklé databázové aktivity. Detekce hrozeb usnadňuje řešení potenciálních ohrožení k Managed Instance bez nutnosti odborné zabezpečení nebo se musí spravovat pokročilé systémy monitorování zabezpečení. Zajišťuje úplné šetření se doporučuje povolit Azure Managed Instance auditování, která zapisuje události auditu databáze protokolu ve vašem účtu úložiště Azure. 

Detekce hrozeb SQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), a každé chráněné Managed Instance se účtuje stejná cena jako za Azure Security Center úrovně Standard, na 15 USD/uzel/měsíc, ve kterém každý chráněný Managed Instance se počítá jako jeden uzel.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Nastavení detekce hrozeb pro Managed Instance na webu Azure Portal
1. Spuštění webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).
2. Přejděte na konfigurační stránku Managed Instance, které chcete chránit. V **nastavení** stránce **detekce hrozeb**. 
3. Na stránce konfigurace detekce hrozeb 
   - Zapnout **ON** detekce hrozeb.
   - Konfigurace **seznam e-mailů** přijímat výstrahy zabezpečení po detekci neobvyklých databázových aktivit.
   - Vyberte **účtu služby Azure storage** kde jsou uloženy záznamy auditu neobvyklé hrozeb. 
4.  Klikněte na tlačítko **Uložit** uložte zásadu detekce hrozeb nová nebo aktualizovaná.

   ![Detekce hrozeb](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Prozkoumejte neobvyklé aktivity Managed Instance při zjištění podezřelé události

1. Obdržíte e-mailové oznámení po detekci neobvyklých databázových aktivit. 

   E-mailu obsahuje informace o podezřelé události zabezpečení včetně povahy neobvyklých aktivit, název databáze, název serveru a čas události. Kromě toho poskytuje informace o možných příčinách a doporučených akcích pro šetření a zmírnění potenciálního ohrožení Managed Instance.

   ![e-mail detekce hrozeb](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Klikněte na tlačítko **zobrazené poslední výstrahy SQL** odkaz v e-mailu můžete spustit na portálu Azure portal a zobrazení stránky s upozorněními Azure Security Center, která obsahuje základní informace o aktivní zjištěných hrozeb SQL na spravované instanci databáze.

   ![aktivní hrozby](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Kliknutím na konkrétní výstrahu zobrazíte další podrobnosti a akce pro zkoumání této hrozby a oprava budoucími hrozbami.

   Například útok prostřednictvím injektáže SQL je jedním z běžných problémů zabezpečení webových aplikací na Internetu. Útok prostřednictvím injektáže SQL se používá k útoku na aplikace řízené daty. Útočníci využívají ohrožení zabezpečení aplikací k vložit škodlivé příkazy SQL do vstupních polí aplikace, porušení nebo úpravy dat v databázi. Podrobnosti výstrahy pro výstrahy útok prostřednictvím injektáže SQL, zahrnují zranitelné příkaz jazyka SQL, který byl zneužít.

   ![Útok prostřednictvím injektáže SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Spravované výstrahy detekce hrozeb Instance 

Detekce hrozeb pro spravovanou instanci detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a ji můžete spustit následující upozornění:
- **Zranitelnost vůči útoku prostřednictvím injektáže SQL:** Tato výstraha se aktivuje, pokud aplikace v databázi vygeneruje chybný příkaz SQL. Může to znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:
 - Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
 - Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
- **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. To znamená, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Managed Instance, když někdo přihlásil k Managed Instance z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo operace údržby pro vývojáře). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník a tak dále).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Managed Instance, když někdo přihlásil k Managed Instance z datového centra Azure, která nebyla přístup k této spravované Instance v nedávné době. V některých případech výstraha detekuje legitimní akci (nová aplikace v Azure, Power BI, Editor dotazů SQL Azure a tak dále). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Managed Instance serveru, když někdo přihlásil k Managed Instance pomocí neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (operace údržby nový vývojář aplikace). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další postup

- Další informace o Managed Instance, přečtěte si téma [co je Managed Instance](sql-database-managed-instance.md)
- Další informace o [auditování spravované Instance](https://go.microsoft.com/fwlink/?linkid=869430) 
- Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
