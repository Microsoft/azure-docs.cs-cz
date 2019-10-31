---
title: Žádost o proces a e-mailová oznámení v Azure AD – Správa nároků – Azure Active Directory
description: Přečtěte si o procesu požadavků pro balíček pro přístup a při posílání e-mailových oznámení v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199899"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Žádost o proces a e-mailová oznámení v Azure AD – Správa nároků

Když uživatel odešle požadavek do balíčku pro přístup, zahájí proces doručení žádosti o přístup. Správa nároků na Azure AD posílá schvalovatelům a žadatelům e-mailová oznámení, když během procesu dojde k klíčovým událostem. Tento článek popisuje proces žádosti a e-mailová oznámení, která se odesílají.

## <a name="request-process"></a>Proces žádosti

Uživatel, který potřebuje přístup k přístupovému balíčku, může odeslat žádost o přístup. V závislosti na konfiguraci zásady může požadavek vyžadovat schválení. Při schválení žádosti proces začne přiřazovat přístup uživatelů ke každému prostředku v balíčku pro přístup. Následující diagram znázorňuje přehled procesu a různých stavů:

![Diagram procesu schválení](./media/entitlement-management-process/request-process.png)

| Stav | Popis |
| --- | --- |
| Předán | Uživatel odešle požadavek. |
| Čeká na schválení | Pokud zásada pro balíček pro přístup vyžaduje schválení, požadavek se přesune na čeká na schválení. |
| Platnost vypršela | Pokud žádný schvalovatel neschválí žádost v rámci časového limitu žádosti o schválení, vyprší platnost žádosti. Chcete-li akci opakovat, bude uživatel muset odeslat žádost znovu. |
| Odepřen | Schvalovatel odepře požadavek. |
| Schválené | Schvalovatel schválí žádost. |
| Doručování | Uživatel **nemá** přiřazený přístup ke všem prostředkům v balíčku pro přístup. Pokud se jedná o externího uživatele, uživatel se ještě možná nepřipojil k adresáři prostředků a nepřijal výzvu k vyjádření souhlasu. |
| Doručeno | Uživatel má přiřazený přístup ke všem prostředkům v balíčku pro přístup. |
| Přístup rozšířený | Pokud jsou v zásadách povoleny rozšíření, uživatel rozšíření přiřazení. |
| Platnost přístupu vypršela | Vypršela platnost přístupu uživatele k balíčku přístupu. K opětovnému získání přístupu bude uživatel muset odeslat žádost. |

## <a name="email-notifications"></a>E-mailová oznámení

Pokud jste schvalovatelem, pošleme vám e-mailová oznámení, když potřebujete schválit žádost o přístup a kdy se žádost o přístup dokončila. Pokud jste žadatel, poslali jste e-mailová oznámení, která označují stav vaší žádosti.

Následující diagramy ukazují, kdy jsou tato e-mailová oznámení odeslána schvalovatelům nebo žadateli. V [tabulce e-mailových oznámení](entitlement-management-process.md#email-notifications-table) vyhledejte odpovídající číslo pro e-mailová oznámení zobrazená v diagramech.

### <a name="primary-approvers-and-alternate-approvers"></a>Primární schvalovatelé a alternativní schvalovatelé
Následující diagram znázorňuje prostředí primárních schvalovatelů a alternativních schvalovatelů a e-mailová oznámení, která obdrží během procesu žádosti:

![Tok procesu primárního a alternativního schvalovatele](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Žadatelé
Následující diagram znázorňuje prostředí žadatelů a e-mailová oznámení, která obdrží během procesu žádosti:

![Tok procesu žadatele](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabulka e-mailových oznámení
Následující tabulka poskytuje další podrobnosti o každé z těchto e-mailových oznámení. Pokud chcete tyto e-maily spravovat, můžete použít pravidla. Například v Outlooku můžete vytvořit pravidla pro přesun e-mailů do složky, pokud předmět obsahuje slova z této tabulky:

| # | Předmět e-mailu | Při odeslání | Odesláno do |
| --- | --- | --- | --- |
| 1\. místo | Požaduje se akce: schválení nebo zamítnutí přesměrovaného požadavku uživatelem *[Date]* | Tento e-mail se pošle alternativním schvalovatelům fáze-1 (po eskalaci žádosti), aby provedl akci. | Fáze 1 alternativní schvalovatel |
| 2 | Požaduje se akce: schválit nebo odepřít požadavek podle *[Date]* | Tento e-mail se pošle primárním schvalovatelům fáze 1, pokud je eskalace zakázaná, aby se akce mohla provést. | Fáze 1 primárního schvalovatele |
| 3 | Připomenutí: schvalte nebo odepřete požadavek pomocí *[Date]* pro *[žadatel]* . | Tento e-mail s připomenutím se pošle primárním schvalovatelům fáze 1, pokud je eskalace zakázaná, a to v případě, že ještě neudělali nějakou akci. | Fáze 1 primárního schvalovatele |
| 4 | Schválit nebo zamítnout žádost pomocí *[Time]* dne *[Date]* | Tento e-mail se pošle primárním schvalovatelům fáze 1 (Pokud je povolená eskalace), aby mohl provést akci. | Fáze 1 primárního schvalovatele |
| 5 | Připomenutí vyžadované akcí: schvalte nebo odepřete požadavek podle *[Date]* pro *[žadatel]* . | Tento e-mail s připomenutím se pošle primárním schvalovatelům fáze 1, pokud je povoleno zvýšení úrovně, a to v případě, že ještě neučinili nějakou akci. | Fáze 1 primárního schvalovatele |
| 6 | Vypršela platnost žádosti pro *[access_package]* | Tento e-mail se pošle primárním schvalovatelům fáze-1 nebo alternativním schvalovatelům fáze-1 pro jednu fázi nebo požadavek na více fází po vypršení platnosti žádosti. | Fáze 1 primárního schvalovatele, fáze 1, alternativní schvalovatel |
| 7 | Žádost schválená pro *[žadatel]* na *[access_package]* | Po dokončení žádosti se tento e-mail pošle na primární schvalovatele fáze 1 nebo do sekundárního schvalovatele fáze-1. | Fáze 1 primárního schvalovatele, fáze 1, alternativní schvalovatel |
| 8 | Žádost schválená pro *[žadatel]* na *[access_package]* | Tento e-mail se pošle primárním schvalovatelům fáze-1 nebo alternativním schvalovatelům fáze-1 s žádostí o 2 fáze, a to pouze v případě, že je schválena fáze 1. | Fáze 1 primárního schvalovatele, fáze 1, alternativní schvalovatel |
| 9 | Žádost byla zamítnuta *[access_package]* | Tento e-mail se pošle žadateli jenom v případě, že je jeho žádost zamítnutá. | Žadatele |
| 10 | Platnost vaší žádosti vypršela pro *[access_package]* . | Po vypršení platnosti žádosti se tento e-mail pošle žadateli na konci fáze 1 v rámci jedné fáze nebo žádosti o více fází. | Žadatele |
| 18 | Teď máte přístup k *[access_package]* . | Tento e-mail se pošle koncovým uživatelům, aby mohli začít používat svůj přístup. | Žadatele |
| 19 | Rozšíří přístup pro *[access_package]* do *[Date]* . | Tento e-mail se pošle koncovým uživatelům předtím, než vyprší platnost přístupu. | Žadatele |
| 20 | Pro *[access_package]* skončil přístup. | Tento e-mail se pošle koncovým uživatelům po vypršení platnosti přístupu. | Žadatele |

### <a name="access-request-emails"></a>E-maily žádostí o přístup

Když žadatel pošle žádost o přístup balíčku pro přístup nakonfigurovaného tak, aby vyžadoval schválení, všichni schvalovatelé přidaní k zásadě obdrží e-mailové oznámení s podrobnostmi o žádosti. Podrobnosti zahrnují jméno žadatele, organizaci, počáteční a koncové datum (Pokud je k dispozici), obchodní odůvodnění, čas odeslání žádosti a čas vypršení platnosti žádosti.

E-mail obsahuje schvalovatele odkazů, kteří můžou kliknout na myAccess (Přejít na) a schválit nebo zamítnout žádost o přístup. Tady je ukázka e-mailového oznámení, které se pošle schvalovateli, když žadatel odešle žádost o přístup:

![Schválit žádost o přístup k e-mailu balíčku](./media/entitlement-management-shared/approver-request-email.png)

Primárním schvalovatelům se také pošle e-mailové oznámení s připomenutím k provedení akce a rozhodnutí pro požadavek. Tady je ukázka e-mailu primárních schvalovatelů oznámení, které jim přiřadí upozornění na provedení akce:

![E-mail s požadavkem na přístup k připomenutí](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Alternativní e-maily žádostí o schvalovatele

Pokud je v případě, že žádost stále čeká na vyřízení, je přesměrování na alternativní schvalovatele povoleno, požadavek se přepošle. Alternativní schvalovatel dostane e-mail s oznámením o schválení nebo zamítnutí žádosti. Zde je příklad e-mailu oznámení, které alternativní schvalovatelé obdrží:

![Alternativní e-mail žádosti o schvalovatele](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Primární schvalovatelé i alternativní schvalovatelé můžou žádost schválit nebo zamítnout.

### <a name="approved-or-denied-emails"></a>Schválené nebo zakázané e-maily

Žadateli jsou upozorňováni, když je žádost o přístup schválena a k dispozici pro přístup, nebo když je žádost o přístup zamítnuta. Když schvalovatel obdrží žádost o přístup odeslanou žadatelem, může žádost o přístup schválit nebo zamítnout. Schvalovatel potřebuje k rozhodnutí přidat obchodní odůvodnění. Tady je ukázka e-mailu odeslaného primárnímu nebo alternativnímu schvalovateli po schválení žádosti:

![Zkontrolovat e-mail s požadavkem na přístup](./media/entitlement-management-process/approver-request-email-approved.png)

Po schválení žádosti o přístup a jejím přístupu se žadateli pošle e-mailové oznámení, že teď mají přístup k balíčku přístupu. Tady je ukázka e-mailového oznámení, které se pošle žadateli, když se jim udělí přístup k balíčku pro přístup:

![E-mail s žádostí o přístup vypršel](./media/entitlement-management-process/requestor-email-approved.png)

Po odepření žádosti o přístup se žadateli pošle e-mailové oznámení. Tady je ukázka e-mailového oznámení, které se pošle žadateli při odepření žádosti o přístup:

![Žádost žadatele o zamítnutí e-mailu](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>E-mail s žádostí o přístup s vypršenou platností

Žádosti o přístup můžou vypršet, pokud žádný schvalovatel žádost neschválil nebo nezamítl. 

Když požadavek dosáhne svého nakonfigurovaného data a vypršení platnosti, už ho schvalovatelé nemůžou schválit nebo odepřít. Tady je ukázka e-mailu oznámení odeslaného všem primárním a alternativním schvalovatelům:

 ![E-mail žádosti o přístup pro schvalovatele vypršel](./media/entitlement-management-process/approver-request-email-expired.png)

 Žadateli se také pošle e-mailové oznámení, které jim oznámí, že vypršela platnost žádosti o přístup a že musí žádost o přístup znovu odeslat. Tady je ukázka e-mailového oznámení, které se pošle žadateli v případě vypršení platnosti žádosti o přístup:

![E-mail s požadavkem na přístup žadatele vypršel](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
