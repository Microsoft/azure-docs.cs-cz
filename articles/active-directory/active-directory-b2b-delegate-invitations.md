---
title: Delegovat pozvánky pro spolupráci Azure Active Directory s B2B | Microsoft Docs
description: Vlastnosti uživatelů služby Active Directory s B2B spolupráce se dají konfigurovat
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929267"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegát pozvánky pro spolupráci Azure Active Directory s B2B

Se spoluprací business-to-business (B2B) Azure Active Directory (Azure AD) není nutné být globálním správcem odeslat pozvánky. Místo toho můžete použít zásady a delegovat pozvánek uživatelům, jejichž role umožňují odeslání pozvánky. Je důležité nové možné delegovat pozvánek uživatele guest prostřednictvím role pozvání hosta odeslal.

## <a name="guest-inviter-role"></a>Pozvání hosta odeslal role
Jsme můžete přiřadit uživatele k roli pozvání hosta odeslal odeslat pozvánky. Nemusíte být členem role globálního správce odeslat pozvánky. Ve výchozím nastavení můžete běžní uživatelé také vyvolat rozhraní API pozvání, pokud není globální správce zakázáno pozvánky k běžní uživatelé. Uživatele můžete také vyvolat rozhraní API pomocí portálu Azure nebo Powershellu.

Tady je příklad, který ukazuje, jak přidat uživatele k roli pozvání hosta odeslal pomocí prostředí PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Ovládací prvek, který můžete pozvat

![Řídí pozvat](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

S spolupráce Azure AD B2B správce klienta můžete nastavit tyto zásady pozvání:

- Vypnout pozvánek
- Pouze správci a uživatelé v roli hosta pozvánky můžete pozvat
- Můžete pozvat správci, pozvání hosta odeslal role a členů
- Všichni uživatelé, včetně hostů, můžete pozvat

Ve výchozím nastavení jsou klienti nastavené na #4. (Všechny uživatele, včetně hostů, můžete uživatele pozvat, B2B.)

## <a name="next-steps"></a>Další postup

Na spolupráci Azure AD B2B najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Přidat uživatele typu Host spolupráce B2B bez Pozvánka](active-directory-b2b-add-user-without-invite.md)
- [Přidání uživatele spolupráce B2B k roli](active-directory-b2b-add-guest-to-role.md)


