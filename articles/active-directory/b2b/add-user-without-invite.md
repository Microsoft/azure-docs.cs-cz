---
title: Přidání uživatelů spolupráce B2B do Azure Active Directory bez pozvánku | Microsoft Docs
description: Můžete je nechat uživatele guest, přidat další uživatele typu Host do služby Azure AD bez uplatňuje Pozvánka v Azure Active Directory s B2B spolupráce.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Přidat uživatele typu Host spolupráce B2B bez Pozvánka

> [!NOTE]
> Nyní uživatele typu Host už nemusí e-mailová pozvánka s výjimkou v některých zvláštních případech. Další informace najdete v tématu [uplatnění pozvánku spolupráce B2B](redemption-experience.md).  

Můžete povolit uživateli, jako je například zástupce partnera, přidat uživatele z partnera pro vaši organizaci bez nutnosti pozvánek k uplatnit. Jediné, co musíte udělat je uživateli udělit oprávnění výčet v adresáři, kterou používáte pro org. partnera 

Udělení těchto oprávnění, když:

1. Uživatel v organizaci hostitele (například WoodGrove) vyzývá jeden uživatel od partnerské organizace (například Sam@litware.com) jako hosta.
2. Správce v organizaci hostitele [nastavuje zásady](delegate-invitations.md) které umožňují Sam identifikovat a přidat další uživatele od organizace partnera (Litware).
3. Nyní Sam můžete přidat další uživatele z Litware do adresáře WoodGrove, skupiny nebo aplikace bez nutnosti pozvánek k uplatnit. Pokud Sam má odpovídající výčtu oprávnění v Litware, probíhá automaticky.

### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Jak informační pracovníci přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- [Uplatnění pozvánku spolupráce B2B](redemption-experience.md)
- [Delegát pozvánky pro spolupráci Azure Active Directory s B2B](delegate-invitations.md)

