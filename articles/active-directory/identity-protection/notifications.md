---
title: Oznámení Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak oznámení podporují vaše aktivity šetření.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcc7d2d03b3c4a3f4c8c772f38aa3785080d986
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335364"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení Azure Active Directory Identity Protection

Azure AD Identity Protection posílá dva typy automatických e-mailů s oznámením, které vám pomůžou spravovat rizika a rizikové události uživatelů:

- E-mail zjištěných uživatelů v nebezpečí
- Týdenní e-mail pro výtah

Tento článek poskytuje přehled e-mailů s oznámením.

## <a name="users-at-risk-detected-email"></a>E-mail zjištěných uživatelů v nebezpečí

V reakci na zjištěný účet hrozí Azure AD Identity Protection vygenerovat e-mailové upozornění s **uživateli** s rizikem zjištěným jako předmětem. E-mail obsahuje odkaz na sestavu **[Uživatelé označení příznakem rizika](../reports-monitoring/concept-user-at-risk.md)** . Osvědčeným postupem je okamžitě prozkoumat riziko pro uživatele.

Konfigurace pro tuto výstrahu vám umožní určit, na jakou úroveň rizika uživatele má být výstraha vygenerována. E-mail se vygeneruje, když úroveň rizika uživatele dosáhne toho, co jste určili. Po přesunu na tuto úroveň rizika uživatele ale nebudete dostávat noví uživatelé upozorňující na riziko zjištěná e-mailová upozornění pro tohoto uživatele. Pokud jste například nastavili zásady na upozornění pro středně velké uživatelské riziko a uživatel Jan se přesune na střední riziko, obdržíte riziko zjištěného e-mailu pro uživatele Jan. V případě, že se Jan pak přesune na vysoké riziko nebo má další rizikové události, nebudete dostávat druhé uživatele v upozornění na zjištěnou rizikovost.

![E-mail zjištěných uživatelů v nebezpečí](./media/notifications/01.png)

### <a name="configuration"></a>Konfiguraci

Jako správce můžete nastavit:

- **Úroveň rizika uživatele, která aktivuje generování tohoto e** -mailu – ve výchozím nastavení je úroveň rizika nastavena na "vysoké" riziko.
- **Příjemci tohoto e** -mailu – ve výchozím nastavení mají příjemci všechny globální správce. Globální správci můžou také přidat další globální správce, správce zabezpečení, čtenáře zabezpečení jako příjemce.  

Pokud chcete otevřít související dialogové okno, klikněte na **výstrahy** v části **Nastavení** na stránce **Identity Protection** .

![E-mail zjištěných uživatelů v nebezpečí](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>Týdenní e-mail pro výtah

Týdenní e-mailová zpráva o Digest obsahuje souhrn nových rizikových událostí.  
Zahrnuje:

- Ohrožení uživatelé
- Podezřelé aktivity
- Zjištěná ohrožení zabezpečení
- Odkazy na související sestavy v Identity Protection

    ![Náprava](./media/notifications/400.png "Náprava")

### <a name="configuration"></a>Konfiguraci

Jako správce můžete přepínat odeslání e-mailu s týdenním výtahem.

![Rizika uživatele](./media/notifications/62.png "Rizika uživatele")

Pokud chcete otevřít související dialogové okno, klikněte na **týdenní výtah** v části **Nastavení** na stránce **Identita Protection** .

![E-mail zjištěných uživatelů v nebezpečí](./media/notifications/04.png)

## <a name="see-also"></a>Viz také:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
