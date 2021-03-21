---
title: Oznámení o & procesu žádosti – Správa nároků Azure AD
description: Přečtěte si o procesu požadavků pro balíček pro přístup a při posílání e-mailových oznámení v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746231"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Žádost o proces a e-mailová oznámení v Azure AD – Správa nároků

Když uživatel odešle požadavek do balíčku pro přístup, zahájí proces doručení žádosti o přístup. Správa nároků na Azure AD posílá schvalovatelům a žadatelům e-mailová oznámení, když během procesu dojde k klíčovým událostem. Tento článek popisuje proces žádosti a e-mailová oznámení, která se odesílají.

## <a name="request-process"></a>Proces žádosti

Uživatel, který potřebuje přístup k přístupovému balíčku, může odeslat žádost o přístup. V závislosti na konfiguraci zásady může požadavek vyžadovat schválení. Při schválení žádosti proces začne přiřazovat přístup uživatelů ke každému prostředku v balíčku pro přístup. Následující diagram znázorňuje přehled procesu a různých stavů:

![Diagram procesu schválení](./media/entitlement-management-process/request-process.png)

| State | Popis |
| --- | --- |
| Odesláno | Uživatel odešle požadavek. |
| Čeká na schválení | Pokud zásada pro balíček pro přístup vyžaduje schválení, požadavek se přesune na čeká na schválení. |
| Platnost vypršela | Pokud žádný schvalovatel neschválí žádost v rámci časového limitu žádosti o schválení, vyprší platnost žádosti. Chcete-li akci opakovat, bude uživatel muset odeslat žádost znovu. |
| Denied | Schvalovatel odepře požadavek. |
| Schválené | Schvalovatel schválí žádost. |
| Doručování | Uživatel **nemá** přiřazený přístup ke všem prostředkům v balíčku pro přístup. Pokud se jedná o externího uživatele, uživatel pravděpodobně neotevřel adresář prostředků ještě předtím. Také možná nepřijali výzvu k vyjádření souhlasu. |
| Doručeno | Uživatel má přiřazený přístup ke všem prostředkům v balíčku pro přístup. |
| Přístup rozšířený | Pokud jsou v zásadách povoleny rozšíření, uživatel rozšíření přiřazení. |
| Platnost přístupu vypršela | Vypršela platnost přístupu uživatele k balíčku přístupu. K opětovnému získání přístupu bude uživatel muset odeslat žádost. |

## <a name="email-notifications"></a>E-mailová oznámení

Pokud jste schvalovatel, pošleme vám e-mailová oznámení, když potřebujete schválit žádost o přístup. Oznámení se zobrazí také po dokončení žádosti o přístup. Také se vám odesílají e-mailová oznámení upozorňující na stav vaší žádosti, pokud jste žadatel.

Následující diagramy znázorňují, kdy jsou tato e-mailová oznámení odeslána schvalovatelům nebo žadateli. V [tabulce e-mailových oznámení](entitlement-management-process.md#email-notifications-table) vyhledejte odpovídající číslo pro e-mailová oznámení zobrazená v diagramech.

### <a name="first-approvers-and-alternate-approvers"></a>První schvalovatelé a alternativní schvalovatelé
Následující diagram znázorňuje možnosti prvních schvalovatelů a alternativních schvalovatelů a e-mailová oznámení, která obdrží během procesu žádosti:

![Tok procesu prvního a alternativního schvalovatele](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Žadatelé
Následující diagram znázorňuje prostředí žadatelů a e-mailová oznámení, která obdrží během procesu žádosti:

![Tok procesu žadatele](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>schválení 2 fáze
Následující diagram znázorňuje prostředí schvalovatelů fáze 1 a fáze 2 a e-mailová oznámení, která obdrží během procesu žádosti:

![tok procesu schválení 2 fáze](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabulka e-mailových oznámení
Následující tabulka poskytuje další podrobnosti o každé z těchto e-mailových oznámení. Pokud chcete tyto e-maily spravovat, můžete použít pravidla. Například v Outlooku můžete vytvořit pravidla pro přesun e-mailů do složky, pokud předmět obsahuje slova z této tabulky:

| # | Předmět e-mailu | Při odeslání | Odesláno do |
| --- | --- | --- | --- |
| 1 | Požaduje se akce: schválení nebo zamítnutí přesměrovaného požadavku uživatelem *[Date]* | Tento e-mail se pošle alternativním schvalovatelům fáze-1 (po eskalaci žádosti), aby provedl akci. | Fáze 1 – alternativní schvalovatelé |
| 2 | Požaduje se akce: schválit nebo odepřít požadavek podle *[Date]* | Tento e-mail se pošle prvnímu schvalovateli, pokud je eskalace zakázaná a provede akci. | První schvalovatel |
| 3 | Připomenutí: schvalte nebo odepřete požadavek pomocí *[Date]* pro *[žadatel]* . | Tento e-mail s připomenutím se pošle prvnímu schvalovateli, pokud je eskalace zakázaná. E-mail si vyžádá, aby probral akci, pokud ne. | První schvalovatel |
| 4 | Schválit nebo zamítnout žádost pomocí *[Time]* dne *[Date]* | Tento e-mail se pošle prvnímu schvalovateli (Pokud je povolená eskalace), aby probral akci. | První schvalovatel |
| 5 | Připomenutí vyžadované akcí: schvalte nebo odepřete požadavek podle *[Date]* pro *[žadatel]* . | Tento e-mail s připomenutím se pošle prvnímu schvalovateli, pokud je eskalace povolená. E-mail si vyžádá, aby probral akci, pokud ne. | První schvalovatel |
| 6 | Vypršela platnost žádosti pro *[access_package]* . | Tento e-mail se pošle prvnímu schvalovateli a prvnímu schvalovateli – 1 po vypršení platnosti žádosti. | První schvalovatel, fáze-1 alternativních schvalovatelů |
| 7 | Žádost schválená pro *[žadatel]* na *[access_package]* | Tento e-mail se pošle prvnímu schvalovateli a prvnímu schvalovateli – 1 alternativním schvalovatelům při dokončení žádosti. | První schvalovatel, fáze-1 alternativních schvalovatelů |
| 8 | Žádost schválená pro *[žadatel]* na *[access_package]* | Tento e-mail se pošle prvnímu schvalovateli a fázi 1 žádosti o 2 fázi při schválení žádosti fáze-1. | První schvalovatel, fáze-1 alternativních schvalovatelů |
| 9 | Žádost byla zamítnuta *[access_package]* . | Tento e-mail se pošle žadateli, když je jeho žádost zamítnutá. | Requestor |
| 10 | Platnost vaší žádosti vypršela pro *[access_package]* . | Tento e-mail odešle žadateli na konci jedné nebo 2 fáze žádosti. E-mail oznámí žadateli, že platnost žádosti vypršela. | Requestor |
| 11 | Požaduje se akce: schválit nebo odepřít požadavek podle *[Date]* | Tento e-mail se pošle druhému schvalovateli, pokud je eskalace zakázaná, aby se mohla provést akce. | Druhý schvalovatel |
| 12 | Připomenutí vyžadované akcí: schválit nebo zamítnout požadavek podle *[datum]* | Tento e-mail s připomenutím se pošle druhému schvalovateli, pokud je eskalace zakázaná. Oznámení se požádá, aby provedla akci, pokud ještě nebyla. | Druhý schvalovatel |
| 13 | Požaduje se akce: schválit nebo zamítnout požadavek podle *[Date]* pro *[žadatel]* | Tento e-mail se pošle druhému schvalovateli, pokud je eskalace povolená a provede akci. | Druhý schvalovatel |
| 14 | Připomenutí vyžadované akcí: schvalte nebo odepřete požadavek podle *[Date]* pro *[žadatel]* . | Tento e-mail s připomenutím se pošle druhému schvalovateli, pokud je eskalace povolená. Oznámení se požádá, aby provedla akci, pokud ještě nebyla. | Druhý schvalovatel |
| 15 | Požaduje se akce: schválení nebo zamítnutí přesměrovaného požadavku uživatelem *[Date]* | Tento e-mail se pošle alternativním schvalovatelům fáze 2, pokud je povolená eskalace, aby se tato akce mohla provést. | Fáze 2 – alternativní schvalovatele |
| 16 | Žádost schválená pro *[žadatel]* na *[access_package]* | Tento e-mail se pošle druhým schvalovateli a fázi 2 – alternativní schvalovatele při schvalování žádosti. | Druhý schvalovatel, fáze 2 – alternativní schvalovatelé |
| 17 | Vypršela platnost žádosti pro *[access_package]* . | Tento e-mail se pošle druhému schvalovateli nebo alternativním schvalovatelům po vypršení platnosti žádosti. | Druhý schvalovatel, fáze 2 – alternativní schvalovatelé |
| 18 | Teď máte přístup k *[access_package]* . | Tento e-mail se pošle koncovým uživatelům, aby mohli začít používat svůj přístup. | Requestor |
| 19 | Rozšíří přístup pro *[access_package]* do *[Date]* . | Tento e-mail se pošle koncovým uživatelům předtím, než vyprší platnost přístupu. | Requestor |
| 20 | Pro *[access_package]* skončil přístup. | Tento e-mail se pošle koncovým uživatelům po vypršení platnosti přístupu. | Requestor |

### <a name="access-request-emails"></a>E-maily žádostí o přístup

Když žadatel pošle žádost o přístup balíčku pro přístup nakonfigurovaného tak, aby vyžadoval schválení, všichni schvalovatelé přidaní k zásadě obdrží e-mailové oznámení s podrobnostmi o žádosti. Mezi podrobnosti v e-mailu patří: název organizace žadatele a obchodní odůvodnění; a požadovaný počáteční a koncové datum přístupu (Pokud je k dispozici). Podrobnosti budou také zahrnuty při odeslání žádosti a vypršení platnosti žádosti.

E-mail obsahuje schvalovatele odkazů, kteří můžou kliknout na odkaz a přejít na tento přístup ke schválení nebo zamítnutí žádosti o přístup. Tady je ukázka e-mailového oznámení, které se pošle prvnímu schvalovateli nebo druhému schvalovateli (Pokud je povolené schválení ve dvou fázích) k dokončení žádosti o přístup:

![Schválit žádost o přístup k e-mailu balíčku](./media/entitlement-management-shared/approver-request-email.png)

Schvalovatelé také mohou dostávat e-maily s připomenutím. E-mail požádá schvalovatele, aby na žádost narozhodl rozhodnutí. Tady je ukázka e-mailového oznámení, které schvalovatel obdrží, aby se připomenout, aby mohli provést následující akce:

![E-mail s požadavkem na přístup k připomenutí](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternativní e-maily žádosti o další schvalovatele

Pokud je povolené nastavení alternativní schvalovatelé a požadavek pořád čeká na vyřízení, přepošle se. Alternativní schvalovatelé obdrží e-mail ke schválení nebo zamítnutí žádosti. Můžete povolit alternativní schvalovatele ve fázi 1 a fázi 2. Zde je příklad e-mailu oznámení, které alternativní schvalovatelé obdrží:

![E-mail s žádostí o alternativní schvalovatele](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Schvalovatel i alternativní schvalovatelé můžou žádost schválit nebo zamítnout.

### <a name="approved-or-denied-emails"></a>Schválené nebo zakázané e-maily

 Když schvalovatel obdrží žádost o přístup odeslanou žadatelem, může žádost o přístup schválit nebo zamítnout. Schvalovatel potřebuje k rozhodnutí přidat obchodní odůvodnění. Tady je ukázka e-mailu odeslané schvalovatelům a alternativním schvalovatelům po schválení žádosti:

![Schválená žádost o přístup k e-mailu balíčku](./media/entitlement-management-process/approver-request-email-approved.png)

Po schválení žádosti o přístup a jejím přístupu se žadateli pošle e-mailové oznámení, že teď mají přístup k balíčku přístupu. Tady je ukázka e-mailového oznámení, které se pošle žadateli, když jim udělí přístup k balíčku pro přístup:

![E-mail žádosti o přístup schváleného žadatele](./media/entitlement-management-process/requestor-email-approved.png)

Po odepření žádosti o přístup se žadateli pošle e-mailové oznámení. Tady je ukázka e-mailového oznámení, které se pošle žadateli při odepření žádosti o přístup:

![Žádost žadatele o zamítnutí e-mailu](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2. – e-maily žádostí o přístup k schválení

Pokud je povoleno schválení ve dvou fázích, musí alespoň dva schvalovatelé žádost schválit, jednu z každé fáze, aby žadateli mohli získat přístup.

V průběhu fáze 1 dostane první schvalovatel e-mail s požadavkem na přístup a provede rozhodnutí. Pokud žádost schválí, všichni první schvalovatelé a alternativní schvalovatelé ve fázi 1 (Pokud je povolená eskalace) obdrží oznámení o dokončení fáze 1. Zde je příklad e-mailu s oznámením, které je odesláno, když je dokončena fáze-1:

![2. e-mail s požadavkem na přístup](./media/entitlement-management-process/approver-request-email-2stage.png)

Po schválení prvního nebo alternativního schvalovatele žádost ve fázi 1, fáze-2 začne. V průběhu fáze 2 dostane druhý schvalovatel e-mail s oznámením o žádosti o přístup. Po druhém schvalovateli nebo alternativním schvalovateli ve fázi 2 (Pokud je povolená eskalace) se rozhodne schválit nebo odmítnout žádost, e-maily oznámení se odešlou první a druhé schvalovateli a všem alternativním schvalovatelům ve fázi 1 a fázi 2 a také žadateli.

### <a name="expired-access-request-emails"></a>E-mail s žádostí o přístup s vypršenou platností

Žádosti o přístup můžou vypršet, pokud žádný schvalovatel žádost neschválil nebo nezamítl. 

Když požadavek dosáhne svého nakonfigurovaného data a vypršení platnosti, už ho schvalovatelé nemůžou schválit nebo odepřít. Tady je ukázkový e-mail s oznámením odeslaným do všech prvních sekund (Pokud je povolené schválení ve dvou fázích) a alternativní schvalovatelé:

![E-mail žádosti o přístup pro schvalovatele vypršel](./media/entitlement-management-process/approver-request-email-expired.png)

Žadateli se také pošle e-mailové oznámení, které jim oznámí, že vypršela platnost žádosti o přístup a že musí žádost o přístup znovu odeslat. Na následujícím obrázku vidíte prostředí žadatele a e-mailová oznámení, která obdrží, když požadují rozšiřování přístupu:

![Postup rozšiřování procesu přístupu žadatele](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Tady je ukázka e-mailového oznámení, které se pošle žadateli v případě vypršení platnosti žádosti o přístup:

![E-mail s požadavkem na přístup žadatele vypršel](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
