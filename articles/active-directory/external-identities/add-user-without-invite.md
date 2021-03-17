---
title: Přidání hostů B2B bez odkazu na pozvánku nebo e-mailu – Azure AD
description: Uživatelům typu Host můžete umožnit, aby k Azure AD přidali další uživatele typu Host bez uplatnění pozvánky v Azure Active Directory spolupráce B2B.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908827"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Přidání uživatelů typu Host pro spolupráci B2B bez odkazu na pozvánku nebo e-mailu

Nyní můžete pozvat uživatele typu Host odesláním [přímého odkazu](redemption-experience.md#redemption-through-a-direct-link) na sdílenou aplikaci. Pomocí této metody už uživatelé typu host již nepotřebují používat e-mail s pozvánkou, s výjimkou některých zvláštních případů. Uživatel typu Host klikne na odkaz aplikace, zkontroluje a přijme podmínky ochrany osobních údajů a pak bezproblémově přistupuje k aplikaci. Další informace najdete v tématu věnovaném [uplatnění pozvánky B2B na spolupráci](redemption-experience.md).

Před tím, než byla tato nová metoda k dispozici, můžete pozvat uživatele typu Host bez vyžadování e-mailu s pozvánkou přidáním pozvánky (z vaší organizace nebo z partnerské organizace) do role adresáře **pozvání hosta** a poté, co uživatel přizvaní přidá uživatele typu Host do adresáře, skupiny nebo aplikace prostřednictvím uživatelského rozhraní nebo prostřednictvím prostředí PowerShell. (Pokud používáte PowerShell, můžete úplně potlačit e-mail s pozvánkou). Například:

1. Uživatel v organizaci hostitele (například WoodGrove) vyzývá jednoho uživatele z partnerské organizace (například Sam@litware.com ) jako host.
2. Správce v organizaci hostitele [nastavuje zásady](delegate-invitations.md) , které umožňují Sam identifikovat a přidat další uživatele z partnerské organizace (Litware). (Do role **pozvat hosta** musí být přidán účet Sam.)
3. Nyní může Sam přidat další uživatele z Litware do adresáře WoodGrove, do skupin nebo aplikací, aniž by museli uplatnit pozvánky. Pokud má Sam příslušné oprávnění výčtu v Litware, proběhne automaticky.
 
Tato původní metoda pořád funguje. Existuje však malý rozdíl v chování. Pokud používáte PowerShell, všimnete si, že pozvaný účet Guest teď má stav **PendingAcceptance** místo toho, aby se hned zobrazila **přijatá**. I když stav čeká na vyřízení, může se uživatel typu Host stále přihlašovat a přistupovat k aplikaci bez kliknutí na odkaz e-mailové pozvánky. Stav čekání na vyřízení znamená, že uživatel se ještě neprošlý [možností souhlasu](redemption-experience.md#consent-experience-for-the-guest), kde přijímá podmínky ochrany osobních údajů v rámci pozvání organizace. Uživatel typu Host uvidí tuto obrazovku pro vyjádření souhlasu při prvním přihlášení. 

Pokud uživatele přistupujete k adresáři, musí uživatel typu Host přistupovat přímo k adrese URL Azure Portal pro konkrétního tenanta (například https://portal.azure.com/ *resourcetenant*. onmicrosoft.com), aby si mohli zobrazit podmínky ochrany osobních údajů a souhlasím s nimi.

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Uplatnění pozvánky B2B pro spolupráci](redemption-experience.md)
- [Delegování pozvánek pro spolupráci Azure Active Directory s B2B](delegate-invitations.md)
- [Jak mohou informační pracovníci přidat uživatele spolupráce B2B?](add-users-information-worker.md)

