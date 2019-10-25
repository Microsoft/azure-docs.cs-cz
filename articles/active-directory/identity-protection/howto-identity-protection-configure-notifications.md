---
title: Oznámení Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak oznámení podporují vaše aktivity šetření.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81452f4d1f77c07222bbff05093a7e8d5d0a1bee
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887543"
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

Nakonfigurujte e-mailová rizika uživatelů v **Azure Portal** v části **Azure Active Directory** > **zabezpečení** > **Identity Protection** > **uživatelé při zjištěných výstrahách**.

## <a name="weekly-digest-email"></a>Týdenní e-mail pro výtah

Týdenní e-mailová zpráva o Digest obsahuje souhrn nových detekcí rizik.  
Zahrnuje:

- Ohrožení uživatelé
- Podezřelé aktivity
- Zjištěná ohrožení zabezpečení
- Odkazy na související sestavy v Identity Protection

![Týdenní e-mail pro výtah](./media/howto-identity-protection-configure-notifications/400.png)

Ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.

### <a name="configure-weekly-digest-email"></a>Konfigurace týdenního e-mailu pro vydigest

Jako správce můžete zapnout nebo vypnout odesílání e-mailů na týden Digest a vybrat uživatele, kteří mají přiřazený příjem e-mailů.

V **Azure Portal** v části **Azure Active Directory** > **zabezpečení** > **Identity Protection** > **týden Digest**nakonfigurujte e-maily týdně vydigest.

## <a name="see-also"></a>Další informace najdete v tématech

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
