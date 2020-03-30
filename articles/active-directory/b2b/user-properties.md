---
title: Vlastnosti uživatele typu Host B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Vlastnosti a stavy uživatelů služby Azure Active Directory B2B před a po uplatnění pozvánky
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f5002e361653614c966dc43301afa83eb7b200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050798"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Vlastnosti uživatele spolupráce služby Azure Active Directory B2B

Tento článek popisuje vlastnosti a stavy objektu hosta B2B ve službě Azure Active Directory (Azure AD) před a po uplatnění pozvánky. Uživatel spolupráce Azure AD business-to-business (B2B) je uživatel s UserType = Guest. Tento uživatel typu Host obvykle pochází z partnerské organizace a má ve výchozím nastavení v adresáři pozvání omezená oprávnění.

V závislosti na potřebách zvoucí organizace může být uživatel spolupráce Azure AD B2B v jednom z následujících stavů účtu:

- Stav 1: Homed v externí instanci Azure AD a reprezentované jako uživatel hosta v zvoucí organizace. V takovém případě se uživatel B2B přihlásí pomocí účtu Azure AD, který patří do pozvat klienta. Pokud partnerská organizace nepoužívá Azure AD, uživatel hosta ve službě Azure AD se stále vytvoří. Požadavky jsou, že uplatní pozvánku a Azure AD ověří svou e-mailovou adresu. Toto uspořádání se také nazývá just-in-time (JIT) nájem nebo "virové" nájmu.

   > [!IMPORTANT]
   > **března 2021**, Microsoft již nebude podporovat uplatnění pozvánek vytvořením nespravované účty Azure AD a tenanty pro scénáře spolupráce B2B. V rámci přípravy doporučujeme zákazníkům, aby se přihlásili k [jednorázovému ověření hesla e-mailem](one-time-passcode.md). Vítáme vaši zpětnou vazbu k této funkci veřejného náhledu a jsme nadšeni, že můžeme vytvořit ještě více způsobů spolupráce.

- Stav 2: Homed v účtu Microsoft nebo jiný a reprezentované jako uživatel typu Host v hostitelské organizaci. V takovém případě se uživatel typu Host přihlásí pomocí účtu Microsoft nebo účtu na sociální síti (google.com nebo podobně). Identita pozvaného uživatele se vytvoří jako účet Microsoft v adresáři zvoucí organizace během uplatnění nabídky.

- Stav 3: Homed v hostitelské organizace v místním active directory a synchronizovány s Azure AD hostitelské organizace. Azure AD Connect můžete použít k synchronizaci partnerských účtů do cloudu jako uživatelé Azure AD B2B s UserType = Guest. Viz [Udělení místně spravovaných partnerských účtů přístup ke cloudovým prostředkům](hybrid-on-premises-to-cloud.md).

- Stav 4: Homed v azure ad hostitelské organizace s UserType = Guest a pověření, které spravuje hostitelská organizace.

  ![Diagram znázorňující čtyři stavy uživatelů](media/user-properties/redemption-diagram.png)


Teď se podívejme, jak vypadá uživatel spolupráce Azure AD B2B ve službě Azure AD.

### <a name="before-invitation-redemption"></a>Před uplatněním pozvánky

Účty state 1 a State 2 jsou výsledkem pozvání uživatelů typu Host ke spolupráci pomocí vlastních přihlašovacích údajů uživatelů typu Host. Při počátečním odeslání pozvánky uživateli typu Host je ve vašem adresáři vytvořen účet. Tento účet nemá žádné přihlašovací údaje s ním spojené, protože ověřování provádí zprostředkovatel identity uživatele hosta. Vlastnost **Source** pro uživatelský účet hosta v adresáři je nastavena na **pozvaný uživatel**. 

![Snímek obrazovky s vlastnostmi uživatele před uplatněním nabídky](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po uplatnění pozvánky

Poté, co uživatel typu Host přijme pozvání, **Source** vlastnost je aktualizován a na základě poskytovatele identity uživatele typu hosta.

Pro uživatele typu Host ve stavu 1 je **zdrojem** **externí služba Azure Active Directory**.

![Stát 1 uživatel hosta po uplatnění nabídky](media/user-properties/after-redemption-state1.png)

Pro uživatele typu Host ve státě 2 je **zdrojem** **účet Microsoft**.

![Stát 2 uživatel hosta po uplatnění nabídky](media/user-properties/after-redemption-state2.png)

Pro uživatele typu Host ve stavu 3 a stavu 4 je vlastnost **Source** nastavena na **Azure Active Directory** nebo Windows Server Active **Directory**, jak je popsáno v další části.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Klíčové vlastnosti uživatele spolupráce Azure AD B2B
### <a name="usertype"></a>UserType
Tato vlastnost označuje vztah uživatele k hostiteli nájmu. Tato vlastnost může mít dvě hodnoty:
- Člen: Tato hodnota označuje zaměstnance hostitelské organizace a uživatele na výplatní listině organizace. Tento uživatel například očekává, že bude mít přístup k interním webům. Tento uživatel není považován za externího spolupracovníka.

- Host: Tato hodnota označuje uživatele, který není považován za interního ve společnosti, například externího spolupracovníka, partnera nebo zákazníka. Od takového uživatele se neočekává, že obdrží interní poznámku generálního ředitele nebo obdrží výhody společnosti.

  > [!NOTE]
  > UserType nemá žádný vztah k jak se uživatel připíše, role adresáře uživatele a tak dále. Tato vlastnost jednoduše označuje vztah uživatele k hostitelské organizaci a umožňuje organizaci vynutit zásady, které závisí na této vlastnosti.

### <a name="source"></a>Zdroj
Tato vlastnost označuje, jak se uživatel přihlásí.

- Pozvaný uživatel: Tento uživatel byl pozván, ale ještě pozvánku neuplatnil.

- Externí služba Azure Active Directory: Tento uživatel je doma v externí organizaci a ověřuje se pomocí účtu Azure AD, který patří do jiné organizace. Tento typ přihlášení odpovídá stavu 1.

- Účet Microsoft: Tento uživatel je vyhotovený v účtu Microsoft a ověřuje se pomocí účtu Microsoft. Tento typ přihlášení odpovídá stavu 2.

- Služba Active Directory systému Windows Server: Tento uživatel je přihlášen z místní služby Active Directory, která patří této organizaci. Tento typ přihlášení odpovídá stavu 3.

- Azure Active Directory: Tento uživatel se ověřuje pomocí účtu Azure AD, který patří do této organizace. Tento typ přihlášení odpovídá stavu 4.
  > [!NOTE]
  > Zdroj a UserType jsou nezávislé vlastnosti. Hodnota Source neznamená konkrétní hodnotu usertype.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Můžou být uživatelé Azure AD B2B přidáni jako členové místo jako hosté?
Obvykle azure AD B2B uživatele a uživatele typu Host jsou synonyma. Proto je uživatel spolupráce Azure AD B2B přidán jako uživatel s UserType = Guest ve výchozím nastavení. V některých případech je však partnerská organizace členem větší organizace, do které hostitelská organizace také patří. Pokud ano, hostitelská organizace může chtít zacházet s uživateli v partnerské organizaci jako s členy namísto hostů. Pomocí rozhraní API správce pozvánek Azure AD B2B můžete přidat nebo pozvat uživatele z partnerské organizace do hostitelské organizace jako člena.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrování pro uživatele typu Host v adresáři

![Snímek obrazovky s filtrem pro uživatele typu Host](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Převést typ uživatele
Je možné převést UserType z člena hosta a naopak pomocí prostředí PowerShell. Vlastnost UserType však představuje vztah uživatele k organizaci. Proto byste měli změnit tuto vlastnost pouze v případě, že se změní vztah uživatele k organizaci. Pokud se změní vztah uživatele, má se změnit hlavní jméno uživatele (UPN)? Má mít uživatel i nadále přístup ke stejným prostředkům? Má být přiřazena poštovní schránka? Nedoporučujeme měnit UserType pomocí Prostředí PowerShell jako atomické aktivity. Také v případě, že tato vlastnost stane neměnné pomocí prostředí PowerShell, nedoporučujeme mít závislost na této hodnotě.

## <a name="remove-guest-user-limitations"></a>Odebrání omezení pro uživatele typu Host
Mohou napovědět případy, kdy chcete uživatelům typu Host udělit vyšší oprávnění. Můžete přidat uživatele typu Host do libovolné role a dokonce odebrat výchozí omezení uživatele typu Host v adresáři, abyste uživateli poskytli stejná oprávnění jako členům.

Je možné vypnout výchozí omezení tak, aby uživatel typu Host v adresáři společnosti měl stejná oprávnění jako členský uživatel.

![Snímek obrazovky s možností Externí uživatelé v nastavení uživatele](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Mohu uživatele typu Host zviditelnit v globálním seznamu adres serveru Exchange?
Ano. Ve výchozím nastavení nejsou objekty hosta viditelné v globálním seznamu adres vaší organizace, ale můžete je zviditelnit pomocí prostředí Azure Active Directory PowerShell. Podrobnosti najdete v tématu **Můžu objekty hosta zviditelnit v globálním seznamu adres?** [Manage guest access in Office 365 Groups](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#add-guests-to-the-global-address-list) 

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Uživatelské tokeny spolupráce B2B](user-token.md)
* [Mapování deklarací identity uživatelů spolupráce B2B](claims-mapping.md)
