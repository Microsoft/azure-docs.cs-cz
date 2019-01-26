---
title: Přidat uživatele spolupráce B2B bez pozvánky ke službě Azure Active Directory | Dokumentace Microsoftu
description: Můžete nechat přidat další uživatele typu Host do služby Azure AD bez uplatnění pozvání v Azure Active Directory s B2B spolupráce uživatele typu Host.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 90b085ca0cf57391d24ddfbdfe56776afb26cc64
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075869"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Přidání uživatelů typu Host pro spolupráci B2B bez pozvánky

Nyní můžete pozvat uživatele typu Host odesláním na přímý odkaz na sdílené aplikace. Pomocí této metody uživatele typu Host už nemusí používat e-mailová pozvánka s výjimkou v některé zvláštní případy. Uživatel typu Host klikne odkaz na aplikaci, kontroly a přijme podmínky ochrany osobních údajů a bez problémů přistupuje aplikace. Další informace najdete v tématu [uplatnění pozvání spolupráce B2B](redemption-experience.md).   

Předtím, než tato nová metoda byla k dispozici, může pozvat uživatele typu Host bez nutnosti e-mailová pozvánka přidáním odesílatele pozvánky (z vaší organizace nebo z partnerské organizace) **odesílatel pozvánky hostů** roli adresáře a pak s odesílatel pozvánky přidat uživatele typu Host do adresáře, skupiny nebo aplikace prostřednictvím uživatelského rozhraní nebo Powershellu. (Pokud používáte PowerShell, můžete potlačit e-mailová pozvánka úplně). Příklad:

1. Uživatel v organizaci hostitele (například WoodGrove) vyzývá jeden uživatel od partnerské organizace (třeba Sam@litware.com) jako hosta.
2. Správce v organizaci hostitele [nastaví zásady](delegate-invitations.md) , která umožňují Sam k identifikaci a přidat další uživatele v organizaci partnera poskytujícího (Litware). (Sam, musí být přidaný do **odesílatel pozvánky hostů** role.)
3. Nyní Sam můžete přidat další uživatele z Litware do adresáře, skupiny nebo aplikace společnosti WoodGrove bez nutnosti pozvánky uplatnit. Pokud Sam má oprávnění odpovídající výčet Litware, dojde automaticky.
 
Tato metoda původní stále funguje. Je však malý rozdíl v chování. Pokud používáte PowerShell, můžete si všimnout, že teď má účet pozvaný hosta **PendingAcceptance** stav místo okamžitě zobrazení **přijato**. I když je ve stavu čekající na vyřízení, uživatel typu Host stále přihlásit a přístup k aplikaci bez kliknutí na odkaz na e-mailové pozvání. Stav Čekání znamená, že není uživatel zatím prošli [souhlas prostředí](redemption-experience.md#privacy-policy-agreement), kde přijmou podmínky ochrany osobních údajů zvoucí organizaci. Uživatel typu Host při prvním přihlášení se zobrazí tato obrazovka pro vyjádření souhlasu. 

Pokud můžete pozvat uživatele do adresáře, musí uživatel typu Host přístup k prostředku specifickým pro tenanta webu Azure portal přímo adresu URL (například https://portal.azure.com/ *resourcetenant*. onmicrosoft.com) k zobrazení a souhlasit s podmínkami ochrany osobních údajů.

### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Delegování pozvánek pro spolupráci Azure Active Directory s B2B](delegate-invitations.md)
- [Jak informačních pracovníků vynutit přidat uživatele spolupráce B2B?](add-users-information-worker.md)

