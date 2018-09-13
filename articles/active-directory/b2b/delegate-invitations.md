---
title: Delegování pozvánek pro spolupráci Azure Active Directory s B2B | Dokumentace Microsoftu
description: Je možné konfigurovat Azure Active Directory s B2B spolupráce uživatelské vlastnosti
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 40f6d3cdd3ab8926e48463beaae15b2580458cc1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642734"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegování pozvánek pro spolupráci Azure Active Directory s B2B

Se spoluprací Azure Active Directory (Azure AD) business-to-business (B2B) není nutné být globálním správcem odeslat pozvánky. Místo toho můžete pomocí zásad a delegování pozvánek na uživatele, jehož role zajistí, aby odeslat pozvánky. Je důležité nový způsob delegování pozvánek uživatelů typu Host přes roli odesílatel pozvánky hostů.

## <a name="guest-inviter-role"></a>Roli odesílatel pozvánky hostů
Jsme můžete přiřadit uživatele k roli odesílatel pozvánky hostů odeslat pozvánky. Nemusíte být členem role Globální správce k odeslání pozvánky. Ve výchozím nastavení můžete běžní uživatelé také volat rozhraní API pozvánku, pokud globální správce zakázal pozvánky pro běžní uživatelé. Uživatele můžete také vyvolat rozhraní API pomocí webu Azure portal nebo Powershellu.

Tady je příklad, který ukazuje, jak přidat uživatele k roli odesílatel pozvánky hostů pomocí prostředí PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Ovládací prvek, který můžou posílat pozvánky

![Určuje, jak pozvat](media/delegate-invitations/control-who-to-invite.png)

Se spoluprací Azure AD B2B správce tenanta můžete nastavit tyto zásady pozvánky:

- Vypnout pozvánky
- Pouze správci a uživatelé v roli odesílatel pozvánky hostů můžou posílat pozvánky
- Správci, roli odesílatel pozvánky hostů a členové můžou posílat pozvánky
- Všichni uživatelé, včetně hostů, můžou posílat pozvánky

Ve výchozím nastavení klienti nastavené na #4. (Všechny uživatele, včetně hostů, můžete pozvat uživatele B2B).

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidání uživatelů typu Host pro spolupráci B2B bez pozvánky](add-user-without-invite.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)


