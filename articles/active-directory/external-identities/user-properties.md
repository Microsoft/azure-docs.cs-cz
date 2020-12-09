---
title: Vlastnosti uživatele typu Host B2B-Azure Active Directory | Microsoft Docs
description: Azure Active Directory vlastnosti a stavy uživatele hosta B2B před a po uplatnění pozvánky
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c97958add682a4c49d7832843c0b95ffd2663bf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859627"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Vlastnosti Azure Active Directoryho uživatele spolupráce B2B

Tento článek popisuje vlastnosti a stavy objektu uživatele hosta B2B v Azure Active Directory (Azure AD) před a po uplatnění pozvánky. Uživatel spolupráce B2B (Business-to-Business) Azure AD je uživatel s UserType = Guest. Tento uživatel typu Host obvykle pochází z partnerské organizace a má v adresáři pro pozvání omezená oprávnění, a to ve výchozím nastavení.

V závislosti na potřebách zvát organizace může být uživatel spolupráce Azure AD B2B v jednom z následujících stavů:

- Stav 1: domů v externí instanci Azure AD a reprezentovaný jako uživatel typu Host v hostitelské organizaci. V tomto případě se uživatel B2B přihlásí pomocí účtu Azure AD, který patří k pozvanému tenantovi. Pokud partnerská organizace nepoužívá službu Azure AD, je uživatel typu Host ve službě Azure AD stále vytvořen. Požadavky jsou v tom, že uplatní svou pozvánku a Azure AD ověří svou e-mailovou adresu. Toto uspořádání se také nazývá nájem JIT (just-in-time) nebo "virová".

   > [!IMPORTANT]
   > **Od 31. března 2021** přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří nespravované účty a klienty Azure AD. V přípravě doporučujeme zákazníkům, aby se přihlásili k [e-mailu ověřování jednorázovým heslem](one-time-passcode.md). Uvítáme vaše názory na tuto funkci Public Preview a zajímáme si vytváření ještě více způsobů, jak spolupracovat.

- Stav 2: domů v Microsoft nebo jiném účtu, který je reprezentován jako uživatel typu Host v organizaci hostitele. V takovém případě se uživatel typu Host přihlásí pomocí účet Microsoft nebo účtu v sociální síti (google.com nebo podobný). Identita pozvaného uživatele je vytvořena jako účet Microsoft v adresáři rezvaníing vaší organizace během uplatnění nabídky.

- Stav 3: doma v místní službě Active Directory v hostitelské organizaci a synchronizovaný s Azure AD v hostitelské organizaci. Pomocí Azure AD Connect můžete synchronizovat partnerské účty s cloudem jako uživatelé Azure AD B2B s UserType = Host. Viz [udělení přístupu k prostředkům cloudu prostřednictvím místně spravovaných partnerských účtů](hybrid-on-premises-to-cloud.md).

- Stav 4: Domů ve službě Azure AD v hostitelské organizaci s oprávněním pro hostování = host a přihlašovací údaje, které spravuje hostitelská organizace.

  ![Diagram znázorňující čtyři stavy uživatele](media/user-properties/redemption-diagram.png)


Teď se podívejme, co v Azure AD vypadá jako uživatel spolupráce Azure AD B2B.

### <a name="before-invitation-redemption"></a>Před uplatněním pozvánky

Účty stavu 1 a 2 jsou výsledkem pozvání uživatelů typu Host ke spolupráci pomocí přihlašovacích údajů uživatelů typu Host. Když se Pozvánka poprvé pošle uživateli typu Host, vytvoří se ve vašem adresáři účet. K tomuto účtu nejsou přidružené žádné přihlašovací údaje, protože zprostředkovatel identity uživatele typu Host provádí ověřování. Vlastnost **source** účtu uživatele hosta ve vašem adresáři je nastavená na **pozvaného uživatele**. 

![Snímek obrazovky se zobrazením vlastností uživatele před uplatněním nabídky](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po uplatnění pozvánky

Jakmile uživatel typu Host přijme pozvání, aktualizuje se vlastnost **zdroj** na základě zprostředkovatele identity uživatele typu Host.

Pro uživatele typu Host ve stavu 1 je **zdrojem** **externí Azure Active Directory**.

![Stav 1 uživatel typu Host po uplatnění nabídky](media/user-properties/after-redemption-state1.png)

Pro uživatele typu Host ve stavu 2 je **zdrojem** **účet Microsoft**.

![Stav 2 uživatel typu Host po uplatnění nabídky](media/user-properties/after-redemption-state2.png)

Pro uživatele typu Host ve stavu 3 a 4 je vlastnost **source** nastavena na **Azure Active Directory** nebo **Windows Server Active Directory**, jak je popsáno v následující části.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Klíčové vlastnosti uživatele spolupráce Azure AD B2B
### <a name="usertype"></a>UserType
Tato vlastnost označuje vztah uživatele k hostitelské architektuře. Tato vlastnost může mít dvě hodnoty:
- Člen: Tato hodnota označuje zaměstnance hostitelské organizace a uživatele v rámci výplaty organizace. Tento uživatel například očekává přístup k interním webům. Tento uživatel není považován za externího spolupracovníka.

- Host: Tato hodnota označuje uživatele, který není považován za interního pro společnost, jako je externí spolupracovníka, partner nebo zákazník. Takový uživatel se neočekává, že obdrží interní penále generálního ředitele nebo obdrží výhody společnosti, například.

  > [!NOTE]
  > UserType nemá žádný vztah k tomu, jak se uživatel přihlásí, role adresáře uživatele atd. Tato vlastnost jednoduše označuje vztah uživatele k hostitelské organizaci a umožňuje organizaci vyhovět zásadám, které jsou závislé na této vlastnosti.

Podrobnosti o cenách najdete v referenčních [Azure Active Directory cenách](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Zdroj
Tato vlastnost určuje, jak se uživatel přihlásí.

- Pozvaný uživatel: Tento uživatel byl pozván, ale dosud neodkazoval na pozvánku.

- Externí Azure Active Directory: Tento uživatel je doma v externí organizaci a ověřuje se pomocí účtu Azure AD, který patří do jiné organizace. Tento typ přihlášení odpovídá stavu 1.

- Účet Microsoft: Tento uživatel je doma v účet Microsoft a ověřuje se pomocí účet Microsoft. Tento typ přihlášení odpovídá stavu 2.

- Windows Server Active Directory: Tento uživatel je přihlášený z místní služby Active Directory, která patří do této organizace. Tento typ přihlášení odpovídá stavu 3.

- Azure Active Directory: Tento uživatel se ověřuje pomocí účtu Azure AD, který patří do této organizace. Tento typ přihlášení odpovídá stavu 4.
  > [!NOTE]
  > Zdroj a UserType jsou nezávislé vlastnosti. Hodnota zdroje nezahrnuje konkrétní hodnotu pro UserType.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Můžou být uživatelé Azure AD B2B přidáni jako členové místo hostů?
Typicky se jedná o synonymum uživatele Azure AD B2B a uživatele typu Host. Proto se uživatel spolupráce Azure AD B2B přidá jako uživatel s UserType = Host ve výchozím nastavení. V některých případech je ale partnerská organizace členem větší organizace, ke které patří i organizace hostitele. Pokud ano, hostitelská organizace může chtít považovat uživatele v partnerské organizaci za členy místo hostů. Pomocí rozhraní API Správce oznámení B2B v Azure AD můžete přidat nebo pozvat uživatele z partnerské organizace do hostitelské organizace jako člen.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrovat uživatele typu Host v adresáři

![Snímek obrazovky zobrazující filtr pro uživatele typu Host](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Převést UserType
Je možné převést UserType ze členů na hosta a naopak pomocí PowerShellu. Vlastnost UserType však představuje vztah uživatele k organizaci. Proto byste tuto vlastnost měli změnit jenom v případě, že se změní vztah uživatele k organizaci. Pokud se vztah uživatele změní, měl by se změnit hlavní název uživatele (UPN)? Má uživatel nadále mít přístup ke stejným prostředkům? Měla by být poštovní schránka přiřazená? Nedoporučujeme měnit UserType pomocí PowerShellu jako atomickou aktivitu. V případě, že se tato vlastnost v případě neproměnlivého prostředí PowerShellu nemění, nedoporučujeme u této hodnoty používat závislost.

## <a name="remove-guest-user-limitations"></a>Odebrat omezení uživatelů typu Host
Můžou nastat případy, kdy chcete uživatelům typu Host poskytnout vyšší oprávnění. Uživatele typu Host můžete přidat do libovolné role a dokonce i odebrat výchozí uživatelská omezení hostů v adresáři, aby měl uživatel stejná oprávnění jako členové.

Je možné vypnout výchozí omezení, aby uživatel typu Host v adresáři společnosti měl stejná oprávnění jako uživatel, který je členem.

![Snímek obrazovky s možností externích uživatelů v uživatelském nastavení](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Je možné uživatele typu Host zobrazit v globálním seznamu adres systému Exchange?
Ano. Ve výchozím nastavení nejsou objekty hosta viditelné v globálním seznamu adres vaší organizace, ale pomocí Azure Active Directory PowerShellu je můžete zobrazit. Podrobnosti najdete v tématu zobrazení **objektů hosta v globálním seznamu adres?** v tématu [Správa přístupu hostů v Microsoft 365ch skupinách](/office365/admin/create-groups/manage-guest-access-in-groups).

## <a name="can-i-update-a-guest-users-email-address"></a>Můžu aktualizovat e-mailovou adresu uživatele typu Host?

Pokud uživatel typu Host přijme vaši pozvánku a následně změní svou e-mailovou adresu, nový e-mail se automaticky nesynchronizuje s objektem uživatele hosta v adresáři. Vlastnost mail se vytvoří prostřednictvím [rozhraní Microsoft Graph API](/graph/api/resources/user). Vlastnost mail můžete aktualizovat prostřednictvím rozhraní Microsoft Graph API, centra pro správu Exchange nebo [prostředí PowerShell pro Exchange Online](/powershell/module/exchange/users-and-groups/set-mailuser). Tato změna se projeví v objektu uživatele hosta Azure AD.

## <a name="next-steps"></a>Další kroky

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Tokeny uživatelů spolupráce B2B](user-token.md)
* [Mapování deklarací identity uživatelů spolupráce B2B](claims-mapping.md)
