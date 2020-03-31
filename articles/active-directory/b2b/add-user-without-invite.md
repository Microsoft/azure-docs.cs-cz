---
title: Přidání b2B hostů bez odkazu na pozvánku nebo e-mailu – Azure AD
description: Uživateli typu Host můžete do služby Azure AD přidat další uživatele typu Host, aniž byste museli uplatnit pozvánku ve spolupráci služby Azure Active Directory B2B.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050894"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Přidání uživatelů typu Host pro spolupráci B2B bez odkazu na pozvánku nebo e-mailu

Nyní můžete pozvat uživatele typu Host odesláním přímého odkazu na sdílenou aplikaci. Pomocí této metody uživatelé typu Host již nemusí používat e-mail s pozvánkou, s výjimkou některých zvláštních případů. Uživatel typu Host klikne na odkaz na aplikaci, zkontroluje a přijme podmínky ochrany osobních údajů a pak k ní bez problémů přistupuje. Další informace naleznete v tématu [Uplatnění pozvání na spolupráci B2B](redemption-experience.md).   

Předtím, než byla tato nová metoda k dispozici, můžete pozvat uživatele typu Host bez nutnosti e-mailu s pozvánkou přidáním pozvacího uživatele (z vaší organizace nebo z partnerské organizace) do role **adresáře pozvacího hosta** a následným uživatelem, který přidá uživatele typu Host do adresáře, skupin nebo aplikací prostřednictvím uživatelského rozhraní nebo prostředí PowerShell. (Pokud používáte PowerShell, můžete e-mail s pozvánkou úplně potlačit). Například:

1. Uživatel v hostitelské organizaci (například WoodGrove) pozve jednoho uživatele z Sam@litware.compartnerské organizace (například) jako hosta.
2. Správce hostitelské organizace [nastaví zásady,](delegate-invitations.md) které Samovi umožní identifikovat a přidat další uživatele z partnerské organizace (Litware). (Sam musí být přidán do role **pozvaní hosta.)**
3. Nyní může Sam přidávat další uživatele z Litware do adresáře, skupin nebo aplikací WoodGrove, aniž by bylo nutné pozvánky uplatnit. Pokud má Sam příslušná oprávnění k výčtu v Litware, stane se to automaticky.
 
Tato původní metoda stále funguje. Nicméně, je tu malý rozdíl v chování. Pokud používáte PowerShell, zjistíte, že pozvaný účet hosta má nyní stav **PendingAcceptance** namísto okamžitého zobrazení **Přijato**. I když stav čeká na vyřízení, uživatel typu Host se může stále přihlásit a přistupovat k aplikaci, aniž by klikal na odkaz s pozvánkou na e-mail. Stav čekající na vyřízení znamená, že uživatel ještě neprošel [prostředím pro souhlas](redemption-experience.md#consent-experience-for-the-guest), kde přijímá podmínky ochrany osobních údajů zvoucí organizace. Uživatel typu Host uvidí tuto obrazovku souhlasu při prvním přihlášení. 

Pokud pozvete uživatele do adresáře, musí uživatel typu Host přistupovat https://portal.azure.com/přímo k adrese URL portálu Azure specifickému pro klienta (například *k tenantovi .onmicrosoft.com),* aby mohl zobrazit podmínky ochrany osobních údajů a souhlasit s ním.

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Uplatnění pozvánky na spolupráci B2B](redemption-experience.md)
- [Delegování pozvánek pro spolupráci Azure Active Directory s B2B](delegate-invitations.md)
- [Jak pracovníci informací přidávají uživatele spolupráce B2B?](add-users-information-worker.md)

