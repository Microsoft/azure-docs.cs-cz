---
title: Přidání uživatelů spolupráce B2B do Azure Active Directory bez pozvánku | Microsoft Docs
description: Můžete je nechat uživatele guest, přidat další uživatele typu Host do služby Azure AD bez uplatňuje Pozvánka v Azure Active Directory s B2B spolupráce.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/21/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 50c64386f1eab07c299112617283b1d8d7295295
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591047"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Přidat uživatele typu Host spolupráce B2B bez Pozvánka

Nyní můžete pozvat uživatele typu Host odesláním se přímý odkaz na sdílené aplikaci. Pomocí této metody pro použití e-mailová pozvánka s výjimkou v některých zvláštních případech již nepotřebujete uživatele typu Host. Uživatel typu Host klikne na odkaz na aplikaci, zkontroluje a přijímá zásady ochrany osobních údajů a následně bezproblémově přistupuje k aplikaci. Další informace najdete v tématu [uplatnění pozvánku spolupráce B2B](redemption-experience.md).   

Předtím, než tato nová metoda byla k dispozici, může pozvat uživatele typu Host bez nutnosti e-mailová pozvánka přidáním pozvání odeslal (z vaší organizace nebo od organizace partnera) do **pozvání hosta odeslal** directory role a potom s pozvání odeslal přidat uživatele typu Host do adresáře, skupiny nebo aplikace pomocí uživatelského rozhraní nebo pomocí prostředí PowerShell. (Pokud používáte PowerShell, můžete potlačit e-mailová pozvánka zcela). Příklad:

1. Uživatel v organizaci hostitele (například WoodGrove) vyzývá jeden uživatel od partnerské organizace (například Sam@litware.com) jako hosta.
2. Správce v organizaci hostitele [nastavuje zásady](delegate-invitations.md) které umožňují Sam identifikovat a přidat další uživatele od organizace partnera (Litware). (Sam musí být přidán do **pozvání hosta odeslal** role.)
3. Nyní Sam můžete přidat další uživatele z Litware do adresáře WoodGrove, skupiny nebo aplikace bez nutnosti pozvánek k uplatnit. Pokud Sam má odpovídající výčtu oprávnění v Litware, probíhá automaticky.
 
Tato metoda původní stále funguje. Je však malý rozdíl v chování. Pokud používáte prostředí PowerShell, můžete si všimnout, teď má účet pozvané hosta **PendingAcceptance** stav místo okamžitě zobrazující **platných**. I když je ve stavu čekající na vyřízení, můžete uživatele guest stále přihlásit a přístup k aplikaci bez kliknutí na odkaz žádosti e-mailu. Čekající stav znamená, že nebyla dosud prošli uživatele [souhlas prostředí](redemption-experience.md#privacy-policy-agreement), kde budou přijměte zásady ochrany osobních údajů pozváním organizace. Host uživateli se zobrazí tuto obrazovku souhlasu při prvním přihlášení. 

Pokud můžete pozvat uživatele do adresáře, uživatele guest, musí přístup k portálu Azure konkrétního klienta prostředků přímo adresu URL (například https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) k zobrazení a souhlasím s podmínkami ochrany osobních údajů.

### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Uplatnění pozvánku spolupráce B2B](redemption-experience.md)
- [Delegát pozvánky pro spolupráci Azure Active Directory s B2B](delegate-invitations.md)
- [Jak informační pracovníci přidat uživatele spolupráce B2B?](add-users-information-worker.md)

