---
title: Žádost o proces a e-mailová oznámení ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si o procesu požadavků pro balíček pro přístup a při posílání e-mailových oznámení v Azure Active Directory správě nároků (Preview).
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
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ad17f4c4d338e1d0b8118c1a172e0aaad7118e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489104"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Žádost o proces a e-mailová oznámení ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Když uživatel odešle požadavek do balíčku pro přístup, spustí se proces, který tuto žádost doručí. Správa nároků na Azure AD taky posílá schvalovatelům a žadatelům e-mailová oznámení v případě, že během procesu dojde k klíčovým událostem.

Tento článek popisuje proces žádosti a e-mailová oznámení, která se odesílají.

## <a name="request-process"></a>Proces žádosti

Uživatel, který potřebuje přístup k přístupovému balíčku, může odeslat žádost o přístup. V závislosti na konfiguraci zásady může požadavek vyžadovat schválení. Při schválení žádosti proces začne přiřazovat přístup uživatelů ke každému prostředku v balíčku pro přístup. Následující diagram znázorňuje přehled procesu a různých stavů.

![Diagram procesu schválení](./media/entitlement-management-process/request-process.png)

| Stav | Popis |
| --- | --- |
| Odesláno | Uživatel odešle požadavek. |
| Čeká na schválení | Pokud zásada pro balíček pro přístup vyžaduje schválení, požadavek se přesune na čeká na schválení. |
| Vypršela platnost | Pokud žádný schvalovatel neschválí žádost v rámci časového limitu žádosti o schválení, vyprší platnost žádosti. Chcete-li akci opakovat, bude uživatel muset odeslat žádost znovu. |
| Odmítnuté | Schvalovatel odepře požadavek. |
| Schválené | Schvalovatel schválí žádost. |
| Doručování | Uživatel **nemá** přiřazený přístup ke všem prostředkům v balíčku pro přístup. Pokud se jedná o externího uživatele, uživatel ještě nepoužil adresář prostředků a nepřijal výzvu k oprávnění. |
| Doručeno | Uživatel má přiřazený přístup ke všem prostředkům v balíčku pro přístup. |
| Přístup rozšířený | Pokud jsou v zásadách povoleny rozšíření, uživatel rozšíření přiřazení. |
| Platnost přístupu vypršela | Vypršela platnost přístupu uživatele k balíčku přístupu. K opětovnému získání přístupu bude uživatel muset odeslat žádost. |

## <a name="email-notifications"></a>E-mailová oznámení

Pokud jste schvalovatelem, pošleme vám e-mailová oznámení, když potřebujete schválit žádost o přístup a kdy se žádost o přístup dokončila. Pokud jste žadatel, poslali jste e-mailová oznámení, která označují stav vaší žádosti. Následující diagram znázorňuje odeslání těchto e-mailových oznámení.

![E-mailový proces správy nároků](./media/entitlement-management-process/email-notifications.png)

Následující tabulka poskytuje další podrobnosti o každé z těchto e-mailových oznámení.

| # | Předmět e-mailu | Při odeslání | Odesláno do |
| --- | --- | --- | --- |
| 1 | Požaduje se akce: Kontrola žádosti o přístup z *[žadatel]* na *[přístup k balíčku]* v *[Date]* | Když žadatel odešle požadavek na balíček pro přístup | Všichni schvalovatelé |
| 2 | Požaduje se akce: Kontrola žádosti o přístup z *[žadatel]* na *[přístup k balíčku]* v *[Date]* | X dní před vypršením časového limitu žádosti o schválení | Všichni schvalovatelé |
| 3 | Oznámení o stavu: platnost žádosti o přístup *[žadatel]* na *[přístup k balíčku]* vypršela. | Když schvalovatelé neschválí nebo zamítnou žádost o přístup v rámci trvání žádosti | Žadatel |
| 4 | Oznámení o stavu: *[žadatel]* žádost o přístup k *[přístup k balíčku]* byl dokončen. | Když první Schvalovatel schválí nebo zamítne žádost o přístup. | Všichni schvalovatelé |
| 5 | Byl odepřen přístup k *[přístup k balíčku]* . | Když žadateli byl zamítnut přístup k balíčku přístupu | Žadatel |
| 6 | Teď máte přístup k *[přístup k balíčku]* .  | Když žadateli byl udělen přístup ke všem prostředkům v balíčku pro přístup | Žadatel |
| 7 | Váš přístup k *[přístup k balíčku]* vyprší za X dní. | X dní předtím, než vyprší přístup žadatele k balíčku přístupu | Žadatel |
| 8 | Platnost vašeho přístupu k *[přístupového balíčku]* vypršela. | Když vyprší platnost přístupu žadatele k balíčku přístupu | Žadatel |

### <a name="access-request-emails"></a>E-maily žádostí o přístup

Když žadatel odešle žádost o přístup k balíčku, který je nakonfigurovaný tak, aby vyžadoval schválení, všichni schvalovatelé, kteří jsou nakonfigurovaní v zásadách, obdrží e-mailové oznámení s podrobnostmi o žádosti. Podrobnosti zahrnují jméno žadatele, organizaci, počáteční a koncové datum, pokud jsou k dispozici, obchodní odůvodnění, čas odeslání žádosti a čas vypršení platnosti žádosti. E-mail obsahuje odkaz, kde schvalovatelé můžou žádost o přístup schválit nebo zamítnout. Tady je ukázka e-mailového oznámení, které se pošle schvalovateli, když žadatel podá žádost o přístup.

![Zkontrolovat e-mail s požadavkem na přístup](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Schválené nebo zakázané e-maily

Žadateli jsou upozorňováni, když je žádost o přístup schválena a k dispozici pro přístup, nebo když je žádost o přístup zamítnuta. Když schvalovatel obdrží žádost o přístup odeslanou žadatelem, může žádost o přístup schválit nebo zamítnout. Schvalovatel potřebuje k rozhodnutí přidat obchodní odůvodnění.

Pokud je žádost o přístup schválena, Správa nároků spustí proces udělení přístupu žadateli ke každému z prostředků v balíčku pro přístup. Jakmile žadateli získá přístup ke každému prostředku v balíčku pro přístup, pošle se mu e-mailové oznámení, že žádost o přístup byla schválena a že teď mají přístup k přístupovému balíčku. Tady je ukázka e-mailového oznámení, které se pošle žadateli, když se jim udělí přístup k balíčku pro přístup.

Po odepření žádosti o přístup se žadateli pošle e-mailové oznámení. Tady je ukázka e-mailového oznámení, které se pošle žadateli při odepření žádosti o přístup.

### <a name="expired-access-request-emails"></a>E-mail s žádostí o přístup s vypršenou platností

Žadateli jsou po vypršení platnosti žádosti o přístup upozorněni. Když žadatel odešle žádost o přístup, bude mít žádost po dobu, po jejímž uplynutí vyprší. Pokud nejsou k dispozici žádní schvalovatelé, kteří odešlou rozhodnutí o schválení nebo zamítnutí, bude tato žádost nadále zůstat ve stavu čekání na schválení. Když požadavek dosáhne nastavené doby vypršení platnosti, platnost žádosti vyprší a nelze je nadále schvalovat nebo odepřít schvalovateli. V takovém případě požadavek přejde do stavu s vypršenou platností. Žádost, jejíž platnost vypršela, již nelze schválit nebo odepřít. Žadateli se pošle e-mailové oznámení, že jeho žádost o přístup vypršela a že musí žádost o přístup znovu odeslat. Tady je ukázka e-mailového oznámení, které se pošle žadateli v případě vypršení platnosti žádosti o přístup.

![E-mail s žádostí o přístup vypršel](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)
