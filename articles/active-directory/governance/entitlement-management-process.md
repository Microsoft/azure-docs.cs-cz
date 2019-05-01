---
title: Požádat o procesu a e-mailová oznámení ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Další informace o procesu žádosti o přístup k balíčku a odeslání e-mailová oznámení ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873655"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Požádat o procesu a e-mailová oznámení ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Když uživatel odešle žádost o přístup k balíčku, je spuštěn proces k poskytování tohoto požadavku. Správa oprávnění Azure AD také odešle e-mailová oznámení schvalovatele a žadatele při události kláves se generují během procesu.

Tento článek popisuje proces požadavku a e-mailová oznámení, které se odesílají.

## <a name="request-process"></a>Zpracování žádosti o

Uživatel, který potřebuje přístup k balíčku přístup můžete odeslat požadavek na přístup. V závislosti na konfiguraci zásad požadavku může vyžadovat schválení. Při schválení žádosti, začne procesu přiřadit přístup uživatelům pro jednotlivé zdroje v přístupu k balíčku. Následující diagram znázorňuje přehled o procesu a různých stavů.

![Diagram procesu schvalování](./media/entitlement-management-process/request-process.png)

| Stav | Popis |
| --- | --- |
| Odesláno | Uživatel odešle žádost. |
| Čeká na schválení | Pokud zásady přístupu k balíčku vyžaduje schválení, žádost o přesune čekající na schválení. |
| Vypršela platnost | Pokud žádné schvalovatelů zkontrolujte v žádosti v rámci časového limitu žádosti o schválení, vyprší platnost požadavku. To zkusit znovu, uživatel bude muset znovu odeslat požadavek. |
| Odmítnuté | Schvalovatel zamítne žádost. |
| Schválené | Schvalovatel schválí požadavek. |
| Doručování | Uživatel má **není** byl přiřazen přístup ke všem prostředkům v přístupu k balíčku. Pokud je to externí uživatel, uživatel dosud přistupovat adresář prostředků a přijmout výzvu oprávnění. |
| Doručeno | Uživatel má přiřazený přístup ke všem prostředkům v přístupu k balíčku. |
| Přístup k rozšířené | Pokud mají být povoleny v zásadách, uživatel rozšířené přiřazení. |
| Vypršela platnost přístupu | Vypršela platnost přístupu uživatele k přístupu k balíčku. Chcete-li získat přístup, uživatel bude mít odeslat žádost. |

## <a name="email-notifications"></a>E-mailová oznámení

Pokud jste schvalovatel, odešlou se e-mailová oznámení, když budete potřebovat ke schválení žádosti o přístup, a po dokončení žádosti o přístup. Pokud jste žadatele, odesílají e-mailová oznámení, které označují stav své žádosti. Následující diagram znázorňuje při těchto e-mailová oznámení se odesílají.

![Proces oprávnění správy e-mailu](./media/entitlement-management-process/email-notifications.png)

Následující tabulka obsahuje další podrobnosti o každém z těchto e-mailová oznámení.

| # | Předmět e-mailu | Při odesílání | Odesílat |
| --- | --- | --- | --- |
| 1 | Požaduje se akce: Žádost o revizi přístupu z *[žadatele]* k *[přístupu k balíčku]* podle *[date]* | Když žadatele odešle žádost o přístup k balíčku | Všechny schvalovatele |
| 2 | Požaduje se akce: Žádost o revizi přístupu z *[žadatele]* k *[přístupu k balíčku]* podle *[date]* | X dní před schválením časový limit požadavku | Všechny schvalovatele |
| 3 | Oznámení o stavu: *[žadatele]* na žádost o přístup k *[přístupu k balíčku]* vypršela platnost | Když schvalovatelů není schválit nebo odmítnout žádosti o přístup v rámci doba trvání žádosti | Žadatel |
| 4 | Oznámení o stavu: *[žadatele]* žádost o přístup k *[přístupu k balíčku]* bylo dokončeno | Při první schvalovatel schválí nebo zamítne požadavek na přístup | Všechny schvalovatele |
| 5 | Máte odepřený přístup k *[přístupu k balíčku]* | Když žadatele o byl odepřen přístup k přístupu k balíčku | Žadatel |
| 6 | Teď máte přístup k *[přístupu k balíčku]*  | Když žadatele udělen přístup ke každému prostředku v přístupu k balíčku | Žadatel |
| 7 | Váš přístup k *[přístupu k balíčku]* do konce platnosti zbývá X dní. | Vypršení platnosti X dní před přístup žadatele k přístupu k balíčku | Žadatel |
| 8 | Váš přístup k *[přístupu k balíčku]* vypršela platnost | Kdy vyprší platnost přístup žadatele přístupu k balíčku | Žadatel |

### <a name="review-access-request-emails"></a>E-mailech žádosti o revize přístupu

Když žadatele odešle požadavek na přístup, který je nakonfigurovaný tak, aby vyžadovala schválení přístupu k balíčku, všichni schvalovatelé nakonfigurovali v zásadách obdrží oznámení o e-mailu s podrobnostmi žádosti. Podrobnosti jsou název pro žadatele, organizace, přistupovat k počáteční a koncové datum-li zadán, obchodní odůvodnění, kdy byl požadavek odeslán a kdy skončí platnost této žádosti. E-mailu obsahuje odkaz, kde schvalovatelů můžete schválit nebo zamítnout žádost o přístup. Tady je ukázka e-mailové oznámení, odeslaný approver když žadatele odešle požadavek na přístup.

![E-mail se žádostí o kontrolu přístupu](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Schválený nebo odepřený e-mailů

Žadatele upozorněni při jejich žádost o přístup je schválený a pro přístup k dispozici nebo při jejich žádost o přístup byl odepřen. Když schvalující návrhů požadavek na přístup odeslané žadatele, jejich schválit nebo zamítnout žádost o přístup. Schvalovatel je potřeba přidat obchodní odůvodnění pro své rozhodnutí.

Při schválení žádosti o přístup, Správa nároků spustí proces udělování přístupu žadatele na každý z prostředků v přístupu k balíčku. Po udělení přístupu ke každému prostředku v balíčku přístup žadatel e-mailové oznámení se pošle žadateli, že jejich žádost o přístup byla schválena a, teď mají přístup k přístupu k balíčku. Tady je ukázka e-mailové oznámení, odesílané do žadatele, když získají přístup k balíčku přístup.

Pokud je požadavek na přístup odepřen, je oznámení e-mailem pošle žadateli. Tady je ukázka e-mailové oznámení, odeslaný žadatele při jejich žádost o přístup byl odepřen.

### <a name="expired-access-request-emails"></a>Vypršela platnost e-mailů žádost o přístup

Vypršela platnost žádosti o jejich přístup se zobrazí oznámení žadatele. Když žadatele odešle požadavek na přístup, požadavek má doba trvání žádosti, po jejímž uplynutí vyprší jeho platnost. Pokud neexistují žádné schvalovatelům odeslat rozhodnutí o schválit nebo zamítnout, požadavek se nadále zůstanou ve stavu čekající na schválení. Když požadavek dosáhne jeho doba nakonfigurované vypršení platnosti, žádost vyprší platnost a je možné už schválené nebo odepřít schvalovateli. V takovém případě žádosti začnou stav vypršené platnosti. Vypršela platnost žádosti můžete už schválené nebo byl odepřen. E-mailové oznámení se pošle žadateli, kterému vypršela platnost jejich žádost o přístup, a že budou muset znovu odeslat žádost o přístup. Tady je ukázka e-mailové oznámení, které je odesláno žadatele, pokud vypršela platnost jejich žádost o přístup.

![Přístup k žádosti o e-mail o vypršení platnosti](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Další postup

- [Žádost o přístup přístupu k balíčku](entitlement-management-request-access.md)
- [Schválit nebo odmítnout žádosti o přístup](entitlement-management-request-approve.md)
