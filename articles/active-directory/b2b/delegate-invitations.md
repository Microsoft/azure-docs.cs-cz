---
title: Povolit nastavení externí spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak povolit externí spolupráce B2B ve službě Active Directory a spravovat, kdo může pozvat uživatele typu Host. Použijte roli odesílatel pozvánky hostů pro delegování pozvánky.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65812672"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Povolit externí spolupráce B2B a spravovat, kdo může zvát hosty

Tento článek popisuje, jak povolit spolupráce B2B ve službě Azure Active Directory (Azure AD) a určit, kteří můžou zvát hosty. Ve výchozím nastavení všichni uživatelé a hosté ve vašem adresáři můžou zvát hosty i v případě, že nejsou přiřazeny k roli správce. Nastavení externí spolupráce umožňuje zapnout pozvánky hostů zapnutí nebo vypnutí pro různé typy uživatelů ve vaší organizaci. Můžete taky delegovat pozvánky pro jednotlivé uživatele tak, že přiřadíte role, které zajistí, aby zvát hosty.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce B2B

Se spoluprací Azure AD B2B správce tenanta můžete nastavit tyto zásady pozvánky:

- Vypnout pozvánky
- Pouze správci a uživatelé v roli odesílatel pozvánky hostů můžou posílat pozvánky
- Správci, roli odesílatel pozvánky hostů a členové můžou posílat pozvánky
- Všichni uživatelé, včetně hostů, můžou posílat pozvánky

Ve výchozím nastavení všichni uživatelé, včetně hostů, můžete pozvat uživatele typu Host.

### <a name="to-configure-external-collaboration-settings"></a>Postup konfigurace nastavení externí spolupráce:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce tenanta.
2. Vyberte **Azure Active Directory** > **uživatelé** > **uživatelská nastavení**.
3. V části **externích uživatelů**vyberte **spravovat nastavení externí spolupráce**.
   > [!NOTE]
   > **Nastavení externí spolupráce** jsou také k dispozici **organizační vztahy** stránky. Ve službě Azure Active Directory v části **spravovat**, přejděte na stránku **organizační vztahy** > **nastavení**.
4. Na **nastavení externí spolupráce** zvolte zásady, které chcete povolit.

   ![Nastavení externí spolupráce](./media/delegate-invitations/control-who-to-invite.png)

  - **Oprávnění uživatelů typu Host jsou omezená**: Tato zásada určuje oprávnění pro hosté ve vašem adresáři. Vyberte **Ano** pro hosty blok z určité adresáře úkoly, jako je vytváření výčtů uživatelů, skupin nebo jiných adresářových prostředků. Vyberte **ne** poskytnout hosté stejný přístup k datům adresáře jako běžní uživatelé ve vašem adresáři.
   - **Správci a uživatelé v roli odesílatele pozvánky hostů můžou posílat pozvánky**: Povolit správci a uživatelé v roli "Odesílatel pozvánky hostů" zvát hosty, nastavte tuto zásadu na **Ano**.
   - **Členové můžou posílat pozvánky**: Bez oprávnění správce členům zvát hosty do adresáře povolit, nastavte tuto zásadu na **Ano**.
   - **Hosté můžou posílat pozvánky**: Povolit hosté zvát jiné hosty, nastavte tuto zásadu na **Ano**.
   - **Povolení e-mailu jednorázového hesla pro hosty (Preview)** : Další informace o funkci jednorázové heslo, naleznete v tématu [e-mailu ověřování jednorázovým heslem (preview)](one-time-passcode.md).
   - **Omezení spolupráce**: Další informace o povolení nebo blokování pozvánek na konkrétní domény najdete v tématu [povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Uživateli přiřadit roli odesílatel pozvánky hostů

Roli odesílatel pozvánky hostů můžou poskytnout jednotlivým uživatelům umožňuje zvát hosty, aniž by jim přiřadil jako globální správce nebo jinou roli správce. Přiřaďte roli odesílatele pozvánky hostů pro jednotlivce. Ujistěte se, nastavíte **správci a uživatelé v roli odesílatele pozvánky hostů můžou posílat pozvánky** k **Ano**.

Tady je příklad, který ukazuje, jak přidat uživatele k roli odesílatel pozvánky hostů pomocí prostředí PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidání uživatelů typu Host pro spolupráci B2B bez pozvánky](add-user-without-invite.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)


