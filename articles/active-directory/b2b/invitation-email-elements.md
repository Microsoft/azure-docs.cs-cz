---
title: Prvky e-mailu s pozvánkou B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Šablona e-mailu s pozvánkou na spolupráci služby Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407219"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Prvky e-mailu s pozvánkou na spolupráci B2B – Azure Active Directory

Pozvánky e-maily jsou důležitou součástí, aby partnery na palubě jako uživatelé spolupráce B2B ve službě Azure AD. I když není [nutné, abyste poslali e-mail, abyste pozvali někoho, kdo používá spolupráci B2B](add-user-without-invite.md), poskytne uživateli všechny informace, které potřebuje k rozhodnutí, zda pozvání přijme. To také dává jim odkaz, které mohou vždy odkazovat v budoucnu, když se potřebují vrátit do svých zdrojů.

![Snímek obrazovky s e-mailem s pozvánkou B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Tato nová šablona e-mailu se stále zavádí všem klientům, takže někteří klienti stále používají starší návrh. Do konce května 2020 budou tuto šablonu používat pozvánky od všech klientů.

## <a name="explaining-the-email"></a>Vysvětlení e-mailu

Podívejme se na několik prvků e-mailu, abyste věděli, jak nejlépe využít jejich schopnosti.

### <a name="subject"></a>Subjekt

Předmět e-mailu se řídí tímto vzorem:

&lt;uživatelské&gt; jméno vás pozvalo k přístupu k aplikacím v rámci jejich organizace.

### <a name="from-address"></a>Z adresy

Pro adresu Od používáme vzor podobný LinkedIn. Tento vzor by měl jasně najevo, invites@microsoft.comže i když e-mail pochází z , pozvánka je z jiné organizace. Formát je: Pozvánky <invites@microsoft.com> společnosti Microsoft nebo &lt;pozvánky&gt; <invites@microsoft.com>společnosti Microsoft jménem tenanta . 

### <a name="reply-to"></a>Odpovědět na

E-mail s odpovědí je nastaven na e-mail pozvaných, pokud je k dispozici, takže odpověď na e-mail odešle e-mail zpět pozvaku.

### <a name="phishing-warning"></a>Upozornění na phishing

E-mail začíná krátkým upozorněním pro uživatele o phishingu a upozorňuje ho, že by měl přijímat pouze pozvánky, které očekává. Je vhodné zajistit, aby partneři, které zvete, nebyli vaší pozvánkou překvapeni tím, že jim ji předem zmíní.

![Obrázek upozornění na phishing v e-mailu](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informace pozvaného uživatele

E-mail obsahuje informace o pozvaném uživateli a organizaci, od které pozvánku odesílá. To zahrnuje jméno odesílatele a e-mailovou adresu, stejně jako jméno a primární doménu přidruženou k organizaci. Všechny tyto informace by měly pozvaným pomoci učinit informované rozhodnutí o přijetí pozvánky.

![Obrázek informací pozvaného v e-mailu](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Zpráva s pozvánkou

Pokud pozvač zahrne zprávu jako součást pozvánky, když [pozve uživatele hosta do adresáře, skupiny nebo aplikace nebo](add-users-administrator.md) když [použije rozhraní API pro pozvánku](customize-invitation-api.md), zobrazí se zpráva v hlavní části e-mailu zvýrazněnou zprávou. Zahrnuto je také jméno pozvatele a profilový obrázek, pokud si ho nastavili. Samotná zpráva je textová oblast, takže z bezpečnostních důvodů nezpracovává značky HTML.

![Obrázek pozvánky v e-mailu](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Tlačítko Přijmout a přesměrovat adresu URL

Další část e-mailu obsahuje informace o tom, kde bude pozvaný přijat poté, co přijme pozvánku, a tlačítko, které tak učiní.  V budoucnu může pozvaný vždy použít tento odkaz k přímému návratu k vašim zdrojům.

![Obrázek tlačítka přijmout a adresu URL přesměrování v e-mailu](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Zápatí sekce

Zápatí obsahuje další informace o odesílané pozvánce. Vždy existuje možnost, aby pozvaný uživatel zablokoval budoucí pozvánky. Pokud organizace [nastavila prohlášení o zásadách ochrany osobních údajů](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), zobrazí se zde odkaz na prohlášení.  V opačném případě poznámka označuje, že organizace nenastavila prohlášení o zásadách ochrany osobních údajů.

![Obrázek části zápatí v e-mailu](media/invitation-email-elements/footer-section.png)
 
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
