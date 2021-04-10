---
title: Povolit externí nastavení spolupráce B2B – Azure AD
description: Naučte se, jak povolit externí spolupráci služby Active Directory B2B a spravovat, kdo může pozvat uživatele typu Host. Delegovat pozvánky pomocí role pozvat hosta
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747fa3005930414832878757664f4787157302d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645819"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Povolení externí spolupráce B2B a správa toho, kdo může zvát hosty

Tento článek popisuje, jak povolit spolupráci B2B v Azure Active Directory (Azure AD), určit, kdo může pozvat hosty a určit oprávnění, která mají uživatelé typu Host ve službě Azure AD. 

Ve výchozím nastavení mohou všichni uživatelé a hosté ve vašem adresáři pozvat hosty i v případě, že nejsou přiřazeni k roli správce. Nastavení externí spolupráce vám umožní zapnout nebo vypnout pozvánky hostů pro různé typy uživatelů ve vaší organizaci. Můžete také delegovat pozvánky na jednotlivé uživatele přiřazením rolí, které jim umožní pozvat hosty.

Azure AD umožňuje omezit, co můžou externí uživatelé typu Host zobrazit v adresáři služby Azure AD. Ve výchozím nastavení jsou uživatelé typu Host nastaveni na úroveň omezené úrovně oprávnění, která je blokuje při vytváření výčtu uživatelů, skupin nebo jiných prostředků adresáře, ale umožňuje zobrazit členství neskrytých skupin. Nové nastavení ve verzi Preview umožňuje omezit přístup hostů ještě dál, aby mohli hosté zobrazit jenom svoje vlastní informace o profilu. Podrobnosti najdete v tématu [omezení oprávnění přístupu hosta (Preview)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce B2B

Díky spolupráci Azure AD B2B může správce tenanta nastavit následující zásady pro pozvánky:

- Vypnout pozvánky
- Povolit zvaní jenom správcům a uživatelům s rolí Odesílatel pozvánek hostů.
- Povolit zvaní jenom správcům, roli Odesílatel pozvánky hostů a členům.
- Povolit zvaní všem uživatelům, včetně hostů.

Ve výchozím nastavení mohou všichni uživatelé, včetně hostů, pozvat uživatele typu Host.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce tenanta.
2. Vyberte **Azure Active Directory**.
3. Vyberte externí **identity** externí  >  **nastavení spolupráce**.

4. V části **omezení přístupu uživatele typu Host (Preview)** vyberte úroveň přístupu, kterou mají mít uživatelé typu Host:
  
   - **Uživatelé typu Host mají stejný přístup jako členové (nejvíc včetně)**: Tato možnost dává hostům stejný přístup k prostředkům a datům Azure AD jako členské uživatele.

   - **Uživatelé typu Host mají omezený přístup k vlastnostem a členstvím objektů adresáře**: (výchozí) Toto nastavení blokuje hosty z určitých úloh adresáře, jako je vytváření výčtu uživatelů, skupin nebo jiných prostředků adresáře. Hosté můžou zobrazit členství všech neskrytých skupin.

   - **Přístup uživatelů typu Host je omezený na vlastnosti a členství svých objektů adresáře (nejvíce omezující)**: s tímto nastavením můžou hosté přistupovat jenom k vlastním profilům. Hosté nemají povoleno zobrazovat profily, skupiny nebo členství v jiných uživatelích.


5. V části **nastavení pozvánky hosta** vyberte odpovídající nastavení:

    ![Nastavení pozvánky hosta](./media/delegate-invitations/guest-invite-settings.png)

   - **Správci a uživatelé v roli Pozvánka hosta může pozvat**: Pokud chcete správcům a uživatelům v roli "pozvání hosta" umožnit pozvat hosty, nastavte tuto zásadu na **Ano**.

   - **Členové můžou pozvat**: Pokud chcete, aby členové adresáře bez správce mohli pozvat hosty, nastavte tuto zásadu na **Ano**.

   - **Hosté můžou pozvat**: Pokud chcete, aby hosty mohli pozvat další hosty, nastavte tuto zásadu na **Ano**.

   > [!NOTE]
   > Pokud je možné, že **členové můžou pozvat** na možnost **ne** a **Správci a uživatelé v roli pozvat pozvánky hosta** je nastavená na **Ano**, uživatelé v roli **pozvání hosta** budou mít i nadále možnost pozvat hosty.

6. V části **e-mail jednorázového hesla pro hosty** vyberte příslušné nastavení (Další informace najdete v tématu [ověřování e-mailu s jednorázovým heslem](one-time-passcode.md)):

   - **Automaticky povolit jednorázové e-mailové heslo pro hosty v říjnu 2021**. Výchozí Pokud pro vašeho tenanta ještě není povolená funkce pro jednorázové e-mailové heslo, bude automaticky zapnutá v říjnu 2021. Pokud chcete povolit funkci v daném čase, není nutná žádná další akce. Pokud jste tuto funkci již povolili nebo zakázali, tato možnost nebude k dispozici.

   - **Povolte jednorázové e-mailové heslo pro hosty**, které jsou teď platné. Zapne pro vašeho tenanta funkci jednorázového hesla pro e-mail.

   - **Zakažte jednorázové e-mailové heslo pro hosty**. Vypne funkci jednorázového hesla e-mailu pro vašeho tenanta a zabrání funkci v zapnutí v říjnu 2021.

   > [!NOTE]
   > Místo výše uvedených možností se zobrazí následující přepínač, pokud jste tuto funkci povolili nebo zakázali, nebo pokud jste se dříve přihlásili k verzi Preview:
   >
   >![Povolit přístup k jednorázovému heslu pro odesílání e-mailů](media/delegate-invitations/enable-email-otp-opted-in.png)

7. V části **Povolit samoobslužnou registraci hosta prostřednictvím toků uživatelů** vyberte **Ano** , pokud chcete mít možnost vytvářet toky uživatelů, které umožňují uživatelům zaregistrovat se k aplikacím. Další informace o tomto nastavení najdete v tématu [Přidání uživatelského toku samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md).

    ![Samoobslužná registrace pomocí nastavení toků uživatelů](./media/delegate-invitations/self-service-sign-up-setting.png)

7. V části **omezení spolupráce** můžete zvolit, zda chcete povolit nebo odepřít pozvánky k zadaným doménám a zadat konkrétní názvy domén v textových polích. V případě více domén zadejte každou doménu na nový řádek. Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](allow-deny-list.md).

    ![Nastavení omezení spolupráce](./media/delegate-invitations/collaboration-restrictions.png)
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

