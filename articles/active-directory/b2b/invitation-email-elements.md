---
title: Prvky B2B spolupráce e-mailová pozvánka – Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory s B2B spolupráce pozvánky e-mailová šablona
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eaab50360269ac1231db2696ba095b6d8841f74
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162998"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Prvky B2B spolupráce e-mailová pozvánka – Azure Active Directory

E-mailových pozvánek jsou zásadní pro partnery na palubě jako uživatele spolupráce B2B Azure AD. Můžete využít ke zvýšení důvěryhodnosti příjemce. můžete přidat legitimitu a vyhovuje výběr z reálného prostředí k e-mailu, abyste měli jistotu, příjemce, jako **Začínáme** tlačítko k přijetí pozvánky. Tento vztah důvěryhodnosti je, že klíč znamená, že omezíte třecí plochy sdílení. A budete chtít také zajistit e-mailu vypadají skvěle!

![E-mailová pozvánka Azure AD s B2b](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>S vysvětlením, e-mailu
Podívejme se na několik elementů e-mailu, abyste věděli, jak nejlépe používat jejich funkce.

### <a name="subject"></a>Subjekt
Předmět e-mailu odpovídá následujícímu vzoru: Zveme vás k &lt;tenantname&gt; organizace

### <a name="from-address"></a>Adresa odesílatele
Používáme vzor LinkedIn jako adresa odesílatele.  By měl být jasné, kdo je odesílatel pozvánky a ze společnosti a i vyjasnit, e-mailu přichází z Microsoftu e-mailovou adresu. Formát je: &lt;Zobrazované jméno odesílatele pozvánky&gt; z &lt;tenantname&gt; (přes Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Zpáteční adresa
Odpovědi e-mailu je nastavena k e-mailu odesílatel pozvánky, pokud je k dispozici, takže odpovídání na e-mailu odešle e-mailu zpět odesílatel pozvánky.

### <a name="branding"></a>Branding
E-mailových pozvánek z vašeho tenanta používání firemní branding, které jste nastavil pro vašeho tenanta. Pokud chcete využít výhod této funkce, které [tady](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) jsou uvedené podrobnosti o tom, jak ho nakonfigurovat. Banner s logem se zobrazí v e-mailu. Velikost bitové kopie postupujte podle pokynů kvality a [tady](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) pro dosažení co nejlepších výsledků. Kromě toho název společnosti také se zobrazí při volání akce.

### <a name="call-to-action"></a>Výzva k akci
Výzvou k akci se skládá ze dvou částí: s vysvětlením, proč příjemce obdrží e-mailu a co příjemce je požádaná, jak je řešit.
- V části "Proč" se dají řešit pomocí následujícímu vzoru: Dostali jste pozvání k přístupu k aplikacím v &lt;tenantname&gt; organizace

- A "co zobrazí se výzva provedete" oddíl je označen přítomnost **Začínáme** tlačítko. Po přidání příjemce bez nutnosti pozvánky, nebude se zobrazí toto tlačítko.

### <a name="inviters-information"></a>Informace odesílatele pozvánky.
Odesílatel pozvánky pro zobrazovaný název je součástí e-mailu. A kromě toho, pokud jste nastavili profilový obrázek pro váš účet Azure AD, pozval e-mail bude obsahovat daný obrázek i. Oba jsou určení ke zvýšení spolehlivosti vaší příjemce e-mailu.

Pokud ještě jste nenastavili profilový obrázek, zobrazí se ikona s iniciály odesílatel pozvánky místo na obrázku:

  ![Zobrazení iniciály odesílatel pozvánky.](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Tělo
Text obsahuje zprávu, že odesílatel pozvánky kontextovému při [pozvání uživatelů typu Host do adresáře, skupiny nebo aplikace](add-users-administrator.md) nebo [pomocí e-mailové pozvánce API](customize-invitation-api.md). Takže nezpracovává značky HTML z bezpečnostních důvodů je textová oblast.

### <a name="footer-section"></a>Zápatí
Zápatí obsahuje značky společnosti Microsoft a umožní příjemce vědět, pokud byla odeslána e-mailu byla odeslaná z nemonitorovaného aliasu. Zvláštní případy:

- Odesílající uživatel nemá e-mailovou adresu ve zvoucí tenantů

  ![Obrázek odesílatele pozvánky nemá e-mailovou adresu ve zvoucí tenantů](media/invitation-email-elements/inviter-no-email.png)


- Příjemce nemusí uplatnění pozvání

  ![Když příjemce nemusí uplatnění pozvánky](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Jak se určuje jazyk
Jazyk, budou zobrazovat uživateli guest v e-mailová pozvánka se určuje podle následující nastavení. Tato nastavení jsou uvedeny v pořadí podle priority. Pokud nastavení není nakonfigurované, určuje další nastavení v seznamu jazyk. 
- **MessageLanguage** vlastnost [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) objektu, pokud se použije Pozvánka vytvořit rozhraní API
-   **PreferredLanguage** zadána vlastnost na hostu [objekt uživatele](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   **Jazyk oznámení** nastavte ve vlastnostech domovském tenantovi uživatele typu Host (pro pouze klienty Azure AD)
-   **Jazyk oznámení** nastavte ve vlastnostech prostředku tenanta

Pokud žádná z těchto nastavení nejsou nakonfigurována, výchozí hodnota je jazyk na angličtinu (US).

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD](what-is-b2b.md)
- [Jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](add-users-administrator.md)
- [Jak informačních pracovníků vynutit přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)
