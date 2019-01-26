---
title: Delegování pozvánek pro spolupráci Azure Active Directory s B2B | Dokumentace Microsoftu
description: Je možné konfigurovat Azure Active Directory s B2B spolupráce uživatelské vlastnosti
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: c50bcc8ebcb87ec39a02b4a71956fab54d3b80de
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081394"
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

Ve službě Azure Active Directory, vyberte **uživatelská nastavení**. V části **externích uživatelů**vyberte **spravovat nastavení externí spolupráce**.

> [!NOTE]
> **Nastavení externí spolupráce** jsou také k dispozici **organizační vztahy** stránky. Ve službě Azure Active Directory v části **spravovat**, přejděte na stránku **organizační vztahy** > **nastavení**.

![Nastavení externí spolupráce](./media/delegate-invitations/control-who-to-invite.png)

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


