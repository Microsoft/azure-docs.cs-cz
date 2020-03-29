---
title: Prvky e-mailu s pozvánkou B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Šablona e-mailu s pozvánkou na spolupráci služby Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768373"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Prvky e-mailu s pozvánkou na spolupráci B2B – Azure Active Directory

Pozvánky e-maily jsou důležitou součástí, aby partnery na palubě jako uživatelé spolupráce B2B ve službě Azure AD. Můžete je použít ke zvýšení důvěryhodnosti příjemce. do e-mailu můžete přidat legitimaci a sociální důkaz, abyste se ujistili, že se příjemce cítí pohodlně s výběrem tlačítka **Začínáme** pro přijetí pozvánky. Tato důvěra je klíčovým prostředkem ke snížení tření sdílení. A také chcete, aby e-mail vypadal skvěle!

![Snímek obrazovky s e-mailem s pozvánkou B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Vysvětlení e-mailu
Podívejme se na několik prvků e-mailu, abyste věděli, jak nejlépe využít jejich schopnosti.

### <a name="subject"></a>Subjekt
Předmět e-mailu se řídí následujícím vzorem: Jste &lt;pozváni&gt; do organizace název tenanta

### <a name="from-address"></a>Z adresy
Pro adresu Od používáme vzor podobný LinkedIn.  Měli byste si ujasnit, kdo je pozvaný uživatel a ze které společnosti, a také objasnit, že e-mail pochází z e-mailové adresy společnosti Microsoft. Formát je: Pozvánky <invites@microsoft.com> &lt;společnosti Microsoft nebo&gt; &lt;zobrazované&gt; jméno pozvaného uživatele z názvu klienta (přes Microsoft). <invites@microsoft.com>

### <a name="reply-to"></a>Odpovědět na
E-mail s odpovědí je nastaven na e-mail pozvaných, pokud je k dispozici, takže odpověď na e-mail odešle e-mail zpět pozvaku.

### <a name="branding"></a>Branding
E-maily s pozvánkami od vašeho tenanta používají firemní značku, kterou jste pro svého tenanta nastavili. Pokud chcete tuto možnost využít, [zde](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) jsou podrobnosti o tom, jak ji nakonfigurovat. V e-mailu se zobrazí logo banneru. Postupujte podle velikosti obrazu a kvality [pokyny zde](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) pro dosažení nejlepších výsledků. Kromě toho se název společnosti také zobrazuje ve výzvě k akci.

### <a name="call-to-action"></a>Výzva k akci
Výzva k akci se skládá ze dvou částí: vysvětlení, proč příjemce obdržel poštu a co je příjemce požádán, aby s tím udělal.
- Část "proč" lze řešit pomocí následujícího vzoru: Byli jste pozváni &lt;k&gt; přístupu k aplikacím v organizaci název tenanta

- A část "k čemu jste požádáni" je označena přítomností tlačítka **Začínáme.** Pokud byl příjemce přidán bez nutnosti pozvánek, toto tlačítko se nezobrazí.

### <a name="inviters-information"></a>Informace pozvaného uživatele
Zobrazované jméno pozvatele je součástí e-mailu. A navíc pokud jste nastavili profilový obrázek pro svůj účet Azure AD, bude zvoucí e-mail obsahovat i tento obrázek. Obě jsou určeny ke zvýšení důvěry příjemce v e-mail.

Pokud jste ještě nenastavili profilový obrázek, zobrazí se místo obrázku ikona s iniciálami pozvaného uživatele:

  ![Snímek obrazovky s pozvánkou se zobrazenými iniciálami pozvaného](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Tělo
Text obsahuje zprávu, kterou pozvaný uživatel vytvoří při [pozvání uživatele typu Host do adresáře, skupiny nebo aplikace nebo](add-users-administrator.md) pomocí rozhraní API pro [pozvání](customize-invitation-api.md). Jedná se o textovou oblast, takže z bezpečnostních důvodů nezpracovává značky HTML.

  ![Snímek obrazovky s textem e-mailu s pozvánkou](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Zápatí sekce
Zápatí obsahuje značku společnosti Microsoft a umožňuje příjemci zjistit, zda byl e-mail odeslán z nemonitorovaného aliasu. 

Zvláštní případy:

- Pozvač nemá e-mailovou adresu v zvoucí mši.

  ![Snímek obrazovky, když pozvaný nemá e-mail v zvoucí mši](media/invitation-email-elements/inviter-no-email.png)


- Příjemce nemusí pozvánku uplatnit.

  ![Snímek obrazovky, když příjemce nepotřebuje pozvánku uplatnit](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Jak je jazyk určen
Jazyk prezentovaný uživateli typu Host v e-mailu s pozvánkou je určen následujícím nastavením. Tato nastavení jsou uvedena v pořadí podle priority. Pokud nastavení není nakonfigurováno, určuje jazyk další nastavení v seznamu. 
- Vlastnost **messageLanguage** pozvaného objektu [UserMessageInfo,](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) pokud se používá rozhraní API vytvořit pozvánku
-   Vlastnost **preferredLanguage** zadaná v [objektu uživatele hosta](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   **Jazyk oznámení** nastavený ve vlastnostech domovského klienta uživatele hosta (jenom pro klienty Azure AD)
-   **Jazyk oznámení** nastavený ve vlastnostech klienta prostředku

Pokud žádné z těchto nastavení nejsou nakonfigurovány, výchozí jazyk je angličtina (US).

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce Azure AD B2B](what-is-b2b.md)
- [Jak správci Služby Azure AD přidávají uživatele spolupráce B2B?](add-users-administrator.md)
- [Jak pracovníci informací přidávají uživatele spolupráce B2B?](add-users-information-worker.md)
- [Uplatnění pozvánky na spolupráci B2B](redemption-experience.md)
- [Přidání uživatelů spolupráce B2B bez pozvání](add-user-without-invite.md)
