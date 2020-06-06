---
title: Oznámení Azure Active Directory Identity Protection
description: Přečtěte si, jak oznámení podporují vaše aktivity šetření.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b5208d2da3635e822049859cae9c8f17b6105a
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84464245"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení Azure Active Directory Identity Protection

Azure AD Identity Protection posílá dva typy automatických e-mailů s oznámením, které vám pomůžou se správou rizik uživatelů a detekcí rizik:

- E-mail zjištěných uživatelů v nebezpečí
- Týdenní e-mail pro výtah

Tento článek poskytuje přehled e-mailů s oznámením.

## <a name="users-at-risk-detected-email"></a>E-mail zjištěných uživatelů v nebezpečí

V reakci na zjištěný účet hrozí Azure AD Identity Protection vygenerovat e-mailové upozornění s **uživateli s rizikem zjištěným** jako předmětem. E-mail obsahuje odkaz na sestavu **[Uživatelé označení příznakem rizika](../reports-monitoring/concept-user-at-risk.md)** . Osvědčeným postupem je okamžitě prozkoumat riziko pro uživatele.

Konfigurace pro tuto výstrahu vám umožní určit, na jakou úroveň rizika uživatele má být výstraha vygenerována. E-mail se vygeneruje, když úroveň rizika uživatele dosáhne toho, co jste určili. Po přesunu na tuto úroveň rizika uživatele ale nebudete dostávat noví uživatelé upozorňující na riziko zjištěná e-mailová upozornění pro tohoto uživatele. Pokud jste například nastavili zásady na upozornění pro středně velké uživatelské riziko a uživatel Jan se přesune na střední riziko, obdržíte riziko zjištěného e-mailu pro uživatele Jan. V případě, že se Jan pak přesune na vysoké riziko nebo má další zjišťování rizik, nedostanete upozornění zjištěná druhým uživatelem.

![E-mail zjištěných uživatelů v nebezpečí](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurace výstrah zjištěných uživateli v případě rizik

Jako správce můžete nastavit:

- **Úroveň rizika uživatele, která aktivuje generování tohoto e-mailu** – ve výchozím nastavení je úroveň rizika nastavena na "vysoké" riziko.
- **Příjemci tohoto e-mailu** – ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.
   - Volitelně můžete **Přidat další e-maily pro příjem oznámení o výstrahách** . Tato funkce je verze Preview a uživatelé musí mít příslušná oprávnění k zobrazení propojených sestav v Azure Portal.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity ochrany**  >  **uživatelů při zjištěných výstrahách**nakonfigurujte e-mailová rizika uživatelů.

## <a name="weekly-digest-email"></a>Týdenní e-mail pro výtah

Týdenní e-mailová zpráva o Digest obsahuje souhrn nových detekcí rizik.  
Obsahuje:

- Zjištěni noví rizikové uživatelé
- Zjištěna nová riziková přihlášení (v reálném čase)
- Odkazy na související sestavy v Identity Protection

![Týdenní e-mail pro výtah](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.

### <a name="configure-weekly-digest-email"></a>Konfigurace týdenního e-mailu pro vydigest

Jako správce můžete zapnout nebo vypnout odesílání e-mailů na týden Digest a vybrat uživatele, kteří mají přiřazený příjem e-mailů.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity**Security –  >  **týdenní výtah**nakonfigurujte e-maily s týdenním shrnutím.

## <a name="see-also"></a>Viz také

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
