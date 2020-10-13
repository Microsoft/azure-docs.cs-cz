---
title: Prvky e-mailu s pozvánkou B2B – Azure Active Directory | Microsoft Docs
description: E-mailová šablona pozvánky pro spolupráci s Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03391ba5a82c128197c86ea6ed84389552fadb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439840"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Prvky e-mailu s pozvánkou pro spolupráci B2B Azure Active Directory

E-maily pozvánky jsou důležitou součástí, která zajistí, aby partneři na panelu jako uživatelé spolupráce B2B v Azure AD. I když se nevyžaduje [odeslání e-mailu s pozváním na někoho, kdo používá spolupráci B2B](add-user-without-invite.md), umožní to uživateli získat všechny informace, které potřebují k rozhodnutí o tom, jestli chce pozvánku přijmout. Poskytuje jim taky odkaz, na který se můžou v budoucnu kdykoli odkázat, když se potřebují vrátit k vašim prostředkům.

![Snímek obrazovky s e-mailem pozvánky B2B](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Tato nová šablona e-mailu se pořád nasazuje všem klientům, takže někteří klienti pořád používají starší návrh. Po konci května 2020 budou tyto šablony používat pozvánky ze všech tenantů.

## <a name="explaining-the-email"></a>Vysvětlení e-mailu

Pojďme se podívat na několik prvků e-mailu, abyste věděli, jak nejlépe využít jejich možnosti.

### <a name="subject"></a>Předmět

Předmět e-mailu se řídí tímto vzorem:

&lt;uživatelské jméno &gt; vás pozvalo k přístupu k aplikacím v rámci organizace.

### <a name="from-address"></a>Z adresy

Pro adresu od používáme vzor podobný LinkedInu. Tento vzor by měl být jasný, že i když z něj pochází e-mail invites@microsoft.com , ale pozvánka pochází z jiné organizace. Formát je: pozvánky Microsoftu  <invites@microsoft.com> nebo pozvánky Microsoftu jménem &lt; tenanta &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Odpovědět na

Pokud je k dispozici e-mailová adresa odesílatele, pošle se e-mail odesílateli, aby odpověď na e-mail poslala e-mailu zpět pozvánce.

### <a name="phishing-warning"></a>Upozornění phishing

E-mail začíná stručným upozorněním na uživatele o phishingu a upozorňuje na to, že by měl přijímat jenom pozvánky, které očekává. Je vhodné se ujistit, že partneři, které zvete, nebudou v rámci pozvánky překvapeny tím, že je budou moci předem uvést.

![Obrázek podvodného upozornění v e-mailu](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Informace pozvat

E-mail obsahuje informace o pozvánce a organizaci, ze které posílá Pozvánka. To zahrnuje jméno odesílatele a e-mailovou adresu a také název a primární doménu přidruženou k organizaci. Všechny tyto informace by vám měly požádat, aby Pozvánka pomohly o přijetí pozvánky.

![Obrázek informací pozvat do e-mailu](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Zpráva pozvánky

Pokud Pozvánka obsahuje zprávu jako součást své pozvánky [, když vyzve uživatele typu Host k adresáři, skupině nebo aplikaci](add-users-administrator.md) nebo když [používají rozhraní API pozvánky](customize-invitation-api.md), zpráva se zvýrazní v hlavní části e-mailu. K dispozici jsou také názvy a profily pozvánky, pokud je nastavila. Samotná zpráva je textová oblast, z důvodů zabezpečení proto nezpracovává značky HTML.

![Obrázek zprávy s pozvánkou v e-mailu](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Tlačítko přijmout a adresa URL pro přesměrování

Další část e-mailu obsahuje informace o tom, kde se Pozvánka přijme po přijetí pozvánky, a také na tlačítko.  V budoucnu může Pozvánka vždy použít tento odkaz k přímému návratu do vašich prostředků.

![Obrázek tlačítka přijmout a adresy URL pro přesměrování v e-mailu](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Oddíl footer

Zápatí obsahuje další informace o odeslané pozvánce. K blokování budoucích pozvánek je vždy k dispozici možnost pozvání. Pokud organizace [nastavila prohlášení o zásadách ochrany osobních údajů](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area), zobrazí se tady odkaz na tento příkaz.  V opačném případě Poznámka znamená, že organizace nevytvořila prohlášení o zásadách ochrany osobních údajů.

![Obrázek části zápatí v e-mailu](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Blokování organizace (zrušení odběru)

V pozvání od organizace obsahuje zápatí možnost **blokovat budoucí pozvánky**. Uživatel typu Host může vybrat tento odkaz k blokování všech budoucích pozvání z organizace. Tato akce také přidá organizaci do seznamu odhlášeného uživatele v [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Zobrazení organizací, které jste zablokovali

Uživatel typu Host může podle těchto kroků zobrazit nebo exportovat organizace, které zablokovali:

1. Přejít na [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .
2. Zadejte svůj e-mail a postupujte podle kroků pro přihlášení k e-mailu ověřování jednorázového hesla.
3. Zobrazte si organizace, které jste zablokovali, nebo je exportujte pomocí kopírování a vložení.
   > [!NOTE]
   > Pokud chcete organizaci, kterou jste zablokovali, dovolit, aby vás pozvala, můžete zvolit organizaci a vybrat **Další**.

## <a name="how-the-language-is-determined"></a>Způsob určení jazyka

Jazyk prezentovaný uživateli hosta v e-mailu s pozvánkou se určuje pomocí následujících nastavení. Tato nastavení jsou uvedena v pořadí podle priority. Pokud není nastavení nakonfigurované, bude jazyk určovat další nastavení v seznamu.

- Vlastnost **messageLanguage** objektu [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) , pokud se používá rozhraní API pro vytvoření pozvánky
-   Vlastnost **preferredLanguage** zadaná v [objektu uživatele](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) hosta
-   **Jazyk oznámení** nastavený ve vlastnostech domovského tenanta uživatele hosta (jenom pro klienty Azure AD)
-   **Jazyk oznámení** nastavený ve vlastnostech tenanta prostředků

Pokud není nakonfigurované žádné z těchto nastavení, výchozí jazyk je angličtina (US).

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B Azure AD](what-is-b2b.md)
- [Jak Azure Active Directory správci přidávají uživatele spolupráce B2B?](add-users-administrator.md)
- [Jak mohou informační pracovníci přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
- [Přidat uživatele spolupráce B2B bez pozvánky](add-user-without-invite.md)
