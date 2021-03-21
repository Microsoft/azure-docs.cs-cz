---
title: Oznámení Azure Active Directory Identity Protection
description: Přečtěte si, jak oznámení podporují vaše aktivity šetření.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997660"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Oznámení Azure Active Directory Identity Protection

Azure AD Identity Protection posílá dva typy automatických e-mailů s oznámením, které vám pomůžou se správou rizik uživatelů a detekcí rizik:

- E-mail zjištěných uživatelů v nebezpečí
- Týdenní e-mail pro výtah

Tento článek poskytuje přehled e-mailů s oznámením.

## <a name="users-at-risk-detected-email"></a>E-mail zjištěných uživatelů v nebezpečí

V reakci na zjištěný účet hrozí Azure AD Identity Protection vygenerovat e-mailové upozornění s **uživateli s rizikem zjištěným** jako předmětem. E-mail obsahuje odkaz na sestavu **[Uživatelé označení příznakem rizika](./overview-identity-protection.md)** . Osvědčeným postupem je okamžitě prozkoumat riziko pro uživatele.

Konfigurace pro tuto výstrahu vám umožní určit, na jakou úroveň rizika uživatele má být výstraha vygenerována. E-mail se vygeneruje, když úroveň rizika uživatele dosáhne toho, co jste zadali. Pokud jste například nastavili zásady na výstrahu středně velkému uživateli a skóre rizika uživatele od uživatele Jan se z důvodu rizika přihlášení v reálném čase přesune na střední riziko, obdržíte uživateli zjištěné riziko. Pokud má uživatel následnou detekci rizik, která by způsobila, že výpočet úrovně rizika uživatele bude představovat zadanou úroveň rizika (nebo vyšší), obdržíte při přepočítání skóre uživatele zjištěné e-maily další uživatelé s riziky zjištěnými uživateli. Pokud se například uživatel přesune k střednímu riziku 1. ledna, obdržíte e-mailové oznámení, pokud je u nastavení nastaveno výstrahy na střední riziko. Pokud má stejný uživatel jiné zjišťování rizik od 5. ledna, což je také středně velké riziko, a skóre rizika uživatele se přepočítá a je stále střední, obdržíte další e-mailové oznámení. 

Další e-mailové oznámení se ale pošle jenom v případě, že čas, kdy došlo k detekci rizika (která způsobila změnu na úrovni rizika uživatele), je novější než poslední e-mail byl odeslán. Například uživatel se přihlásí od 1. ledna v 9:00 a nejedná se o riziko v reálném čase (což znamená, že se z důvodu tohoto přihlášení nevygeneroval žádný e-mail). Deseti minut 5:10 později se stejný uživatel přihlásí znovu a má vysoké riziko v reálném čase, což způsobí, že se úroveň rizika uživatele přesune na vysokou a pošle se e-mail. V 5:15 ráno se v případě nedostatku do offline režimu projeví rizikové skóre pro původní přihlášení v 5. Další uživatel označený příznakem pro rizikovou e-maily se neposílá, protože čas prvního přihlášení byl před druhým přihlášením, které už spustilo e-mailové oznámení.

Aby nedocházelo k přetížení e-mailů, dostanete jenom jednu e-mailovou zprávu během 5 sekundového období. Tato prodleva znamená, že pokud více uživatelů přejde na zadanou úroveň rizika během stejného 5 sekundového období, agregujeme a pošleme jeden e-mail, který bude reprezentovat změnu na úrovni rizika pro všechny z nich.

Pokud vaše organizace povolila samoobslužnou nápravu, jak je popsáno v článku, [uživatel se Azure AD Identity Protection](concept-identity-protection-user-experience.md) může napravit riziko před tím, než budete mít příležitost prozkoumat. Můžete zobrazit rizikové uživatele a riziková přihlášení, která byla opravena přidáním "opraveného" do filtru **stavu rizika** v sestavách rizikových **uživatelů** nebo **rizikových přihlášení** .

![E-mail zjištěných uživatelů v nebezpečí](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurace výstrah zjištěných uživateli v případě rizik

Jako správce můžete nastavit:

- **Úroveň rizika uživatele, která aktivuje generování tohoto e-mailu** – ve výchozím nastavení je úroveň rizika nastavena na "vysoké" riziko.
- **Příjemci tohoto e-mailu** – uživatelé v rolích Globální správce, správce zabezpečení nebo čtenář zabezpečení jsou automaticky přidáni do tohoto seznamu. Pokusíme se odeslat e-maily prvních 20 členům každé role. Pokud je uživatel zaregistrovaný v PIM, aby na vyžádání promohl zvýšit na jednu z těchto rolí, **bude dostávat jenom e-maily, pokud se ve chvíli, kdy se e-mail pošle, zvýší**.
   - Volitelně můžete **Přidat vlastní e-maily** , které jsou tady definované, musí mít příslušná oprávnění k zobrazení propojených sestav v Azure Portal.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity ochrany**  >  **uživatelů při zjištěných výstrahách** nakonfigurujte e-mailová rizika uživatelů.

## <a name="weekly-digest-email"></a>Týdenní e-mail pro výtah

Týdenní e-mailová zpráva o Digest obsahuje souhrn nových detekcí rizik.  
Obsahuje:

- Zjištěni noví rizikové uživatelé
- Zjištěna nová riziková přihlášení (v reálném čase)
- Odkazy na související sestavy v Identity Protection

![Týdenní e-mail pro výtah](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Do tohoto seznamu se automaticky přidají uživatelé v rolích Globální správce, správce zabezpečení nebo čtenáře zabezpečení. Pokusíme se odeslat e-maily prvních 20 členům každé role. Pokud je uživatel zaregistrovaný v PIM, aby na vyžádání promohl zvýšit na jednu z těchto rolí, **bude dostávat jenom e-maily, pokud se ve chvíli, kdy se e-mail pošle** , zvýší.

### <a name="configure-weekly-digest-email"></a>Konfigurace týdenního e-mailu pro vydigest

Jako správce můžete zapnout nebo vypnout odesílání e-mailů na týden Digest a vybrat uživatele, kteří mají přiřazený příjem e-mailů.

V **Azure Portal** v části **Azure Active Directory**  >  **zabezpečení**  >  **identity** Security –  >  **týdenní výtah** nakonfigurujte e-maily s týdenním shrnutím.

## <a name="see-also"></a>Viz také

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
