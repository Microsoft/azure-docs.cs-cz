---
title: Povolit externí nastavení spolupráce B2B – Azure AD
description: Naučte se, jak povolit externí spolupráci služby Active Directory B2B a spravovat, kdo může pozvat uživatele typu Host. Delegovat pozvánky pomocí role pozvat hosta
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
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272899"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Povolit externí spolupráci B2B a spravovat, kdo může pozvat hosty

Tento článek popisuje, jak povolit spolupráci B2B v Azure Active Directory (Azure AD) a určit, kdo může pozvat hosty. Ve výchozím nastavení mohou všichni uživatelé a hosté ve vašem adresáři pozvat hosty i v případě, že nejsou přiřazeni k roli správce. Nastavení externí spolupráce vám umožní zapnout nebo vypnout pozvánky hostů pro různé typy uživatelů ve vaší organizaci. Můžete také delegovat pozvánky na jednotlivé uživatele přiřazením rolí, které jim umožní pozvat hosty.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce B2B

Díky spolupráci Azure AD B2B může správce tenanta nastavit následující zásady pro pozvánky:

- Vypnout pozvánky
- Pozvat pouze správce a uživatelé v roli pozvat hostitele
- Správci, role pozvat hosta a členové mohou pozvat
- Všichni uživatelé, včetně hostů, můžou pozvat

Ve výchozím nastavení mohou všichni uživatelé, včetně hostů, pozvat uživatele typu Host.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce tenanta.
2. Vyberte **Azure Active Directory** > **Uživatelé** > **nastavení uživatele**.
3. V části **externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
   > [!NOTE]
   > K dispozici jsou také **externí nastavení spolupráce** ze stránky **organizačních vztahů** . V Azure Active Directory v části **Spravovat**klikněte na **vztahy organizace** > **Nastavení**.
4. Na stránce **externí nastavení spolupráce** vyberte zásady, které chcete povolit.

   ![Nastavení externí spolupráce](./media/delegate-invitations/control-who-to-invite.png)

  - **Oprávnění uživatelů typu Host jsou omezená**: Tato zásada určuje oprávnění pro hosty ve vašem adresáři. Vyberte **Ano** , pokud chcete blokovat hosty z určitých úloh adresáře, jako je vytváření výčtu uživatelů, skupin nebo jiných prostředků adresáře. Pokud chcete dát hostům stejný přístup k datům adresáře jako běžné uživatele v adresáři, vyberte **ne** .
   - **Správci a uživatelé v roli Pozvánka hosta může pozvat**: Pokud chcete správcům a uživatelům v roli "pozvání hosta" umožnit pozvat hosty, nastavte tuto zásadu na **Ano**.
   - **Členové můžou pozvat**: Pokud chcete, aby členové adresáře bez správce mohli pozvat hosty, nastavte tuto zásadu na **Ano**.
   - **Hosté můžou pozvat**: Pokud chcete, aby hosty mohli pozvat další hosty, nastavte tuto zásadu na **Ano**.
   - **Povolení e-mailového hesla pro hosty (Preview)** : Další informace o funkci jednorázového hesla najdete v tématu e- [mailové ověřování heslem jednorázového hesla (Preview)](one-time-passcode.md).
   - **Omezení spolupráce**: Další informace o povolení nebo blokování pozvánek ke konkrétním doménám najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Přiřazení role pozvánky hosta uživateli

Pomocí role pozvat hosta můžete dát jednotlivým uživatelům možnost pozvat hosty bez přiřazení globálního správce nebo jiné role správce. Přiřaďte roli pozvánky hosta jednotlivcům. Pak se ujistěte, že jste nastavili **správce a uživatelé v roli pozvání hosta, který může pozvat** na **Ano**.

Tady je příklad, který ukazuje, jak pomocí PowerShellu přidat uživatele do role pozvat hosta:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidání uživatelů typu Host pro spolupráci B2B bez pozvánky](add-user-without-invite.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)


