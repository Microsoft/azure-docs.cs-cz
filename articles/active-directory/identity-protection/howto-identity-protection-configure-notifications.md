---
title: Oznámení ochrany identit služby Azure Active Directory
description: Zjistěte, jak oznámení podporují vaše aktivity vyšetřování.
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
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120127"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení ochrany identit služby Azure Active Directory

Azure AD Identity Protection odesílá dva typy automatických e-mailů s oznámením, které vám pomohou spravovat detekci rizik uživatelů a rizik:

- Uživatelé v ohrožení zjištěne e-mail
- Týdenní digest e-mail

Tento článek obsahuje přehled obou e-mailů s oznámením.

## <a name="users-at-risk-detected-email"></a>Uživatelé v ohrožení zjištěne e-mail

V reakci na zjištěný účet v ohrožení Azure AD Identity Protection generuje e-mailové upozornění s **uživateli v ohrožení zjištěna** jako předmět. E-mail obsahuje odkaz na uživatele označené pro zprávu o **[rizicích.](../reports-monitoring/concept-user-at-risk.md)** Jako osvědčený postup byste měli okamžitě prozkoumat ohrožené uživatele.

Konfigurace pro tuto výstrahu umožňuje určit, na jaké úrovni rizika uživatele chcete výstrahu generovat. E-mail bude vygenerován, jakmile úroveň rizika uživatele dosáhne toho, co jste zadali; po přesunutí na tuto úroveň rizika uživatele však nebudete dostávat nové ohrožené uživatele, kterým byla pro tohoto uživatele zjištěna e-mailová upozornění. Pokud například nastavíte zásadu tak, aby upozorňovala na střední riziko uživatele a váš uživatel Jan se přesune na střední riziko, obdržíte uživatelům zjištěným e-mailům pro Jana. Pokud se však jan přesune na vysoké riziko nebo pokud má další detekce rizik, neobdržíte upozornění, že druhý uživatel je ohrožen.

![Uživatelé v ohrožení zjištěne e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurace ohrožených uživatelů zjištěných výstrah

Jako správce můžete nastavit:

- **Úroveň rizika uživatele, která spouští generování tohoto e-mailu** - Ve výchozím nastavení je úroveň rizika nastavena na "Vysoké" riziko.
- **Příjemci tohoto e-mailu** – ve výchozím nastavení příjemci zahrnují všechny globální správce. Globální správci mohou také přidat další globální správce, správce zabezpečení, čtečky zabezpečení jako příjemci.
   - Volitelně můžete **přidat další e-maily pro příjem upozornění** tato funkce je náhled a uživatelé definované musí mít příslušná oprávnění k zobrazení propojených sestav na webu Azure Portal.

Konfigurace ohrožených uživatelů e-mailu na **webu Azure Portal** v části Azure Active **Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>Týdenní digest e-mail

Týdenní digest e-mail obsahuje souhrn nových detekcí rizik.  
Obsahuje:

- Ohrožení uživatelé
- Podezřelé aktivity
- Zjištěné chyby zabezpečení
- Odkazy na související sestavy v části Ochrana identity

![Týdenní digest e-mail](./media/howto-identity-protection-configure-notifications/400.png)

Ve výchozím nastavení příjemci zahrnují všechny globální správce. Globální správci mohou také přidat další globální správce, správce zabezpečení, čtečky zabezpečení jako příjemci.

### <a name="configure-weekly-digest-email"></a>Konfigurace týdenního e-mailu s výtahem

Jako správce můžete přepnout odesílání týdenního e-mailu digest a vybrat uživatele přiřazené k přijetí e-mailu.

Nakonfigurujte týdenní e-mail y digest na **webu Azure Portal** v části Azure Active **Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Viz také

- [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md)
