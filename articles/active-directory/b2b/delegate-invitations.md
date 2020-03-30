---
title: Povolení nastavení externí spolupráce B2B – Azure AD
description: Přečtěte si, jak povolit externí spolupráci služby Active Directory B2B a jak spravovat, kdo může pozvat uživatele typu Host. K delegování pozvánek použijte roli Pozvaní hosta.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272899"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Povolit externí spolupráci B2B a spravovat, kdo může pozvat hosty

Tento článek popisuje, jak povolit spolupráci Služby Azure Active Directory (Azure AD) B2B a určit, kdo může pozvat hosty. Ve výchozím nastavení mohou všichni uživatelé a hosté ve vašem adresáři pozvat hosty, i když nejsou přiřazeni k roli správce. Nastavení externí spolupráce umožňuje zapnout nebo vypnout pozvánky pro hosty pro různé typy uživatelů ve vaší organizaci. Pozvánky můžete delegovat také jednotlivým uživatelům přiřazením rolí, které jim umožňují pozvat hosty.

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce B2B

Díky spolupráci azure ad b2b může správce tenanta nastavit následující zásady pozvání:

- Vypnutí pozvánek
- Povolit zvaní jenom správcům a uživatelům s rolí Odesílatel pozvánek hostů.
- Povolit zvaní jenom správcům, roli Odesílatel pozvánky hostů a členům.
- Povolit zvaní všem uživatelům, včetně hostů.

Ve výchozím nastavení mohou všichni uživatelé, včetně hostů, pozvat uživatele typu Host.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce klienta.
2. Vyberte**nastavení uživatele****služby** >  **Azure Active Directory** > .
3. V části **Externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
   > [!NOTE]
   > Nastavení **Externí spolupráce** jsou k dispozici také na stránce Vztahy **organizace.** Ve službě Azure Active Directory přejděte v části **Správa**na**nastavení** **organizačních vztahů** > .
4. Na stránce **Nastavení externí spolupráce** zvolte zásady, které chcete povolit.

   ![Nastavení externí spolupráce](./media/delegate-invitations/control-who-to-invite.png)

  - **Oprávnění uživatelů typu Host jsou omezena**: Tato zásada určuje oprávnění pro hosty ve vašem adresáři. Výběrem **možnosti Ano** zablokujete hosty z určitých úloh adresáře, jako je výčet uživatelů, skupin nebo jiných adresářových prostředků. Výběrem **možnosti Ne** pořte hostům stejný přístup k datům adresáře jako běžným uživatelům ve vašem adresáři.
   - **Správci a uživatelé v roli pozvaní hosta mohou pozvat:** Chcete-li správcům a uživatelům v roli Pozvaní hosta pozvat pozvání hostů, nastavte tyto zásady na **Ano**.
   - **Členové mohou pozvat:** Chcete-li povolit členům vašeho adresáře, kteří nejsou správci, aby pozvali hosty, nastavte tuto zásadu na **Ano**.
   - **Hosté mohou pozvat**: Chcete-li hostům umožnit pozvat další hosty, nastavte tyto podmínky na **Ano**.
   - **Povolení jednorázového hesla e-mailu pro hosty (náhled):** Další informace o funkci jednorázového přístupového kódu najdete v [tématu E-mail s ověřením jednorázového hesla (preview).](one-time-passcode.md)
   - **Omezení spolupráce**: Další informace o povolení nebo blokování pozvánek do určitých domén naleznete v tématu [Povolit nebo blokovat pozvánky uživatelům B2B z konkrétních organizací](allow-deny-list.md).

## <a name="assign-the-guest-inviter-role-to-a-user"></a>Přiřazení role Pozvaní hosta uživateli

Pomocí role Pozvaní hosté můžete jednotlivým uživatelům poskytnout možnost pozvat hosty, aniž byste jim přiřadili globální roli správce nebo jiného správce. Přiřaďte roli pozvaní hosta jednotlivcům. Pak se ujistěte, že jste **nastavili správce a uživatelé v roli pozvaní hosta mohou pozvat** na **Ano**.

Tady je příklad, který ukazuje, jak pomocí PowerShellu přidat uživatele do role Pozvaní hosta:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Přidání uživatelů typu Host pro spolupráci B2B bez pozvání](add-user-without-invite.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)


