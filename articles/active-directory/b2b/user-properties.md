---
title: Vlastnosti uživatele spolupráce Azure Active Directory B2B | Dokumentace Microsoftu
description: Je možné konfigurovat Azure Active Directory s B2B spolupráce uživatelské vlastnosti
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/5/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 01693f16b0af59881c22fefb6ec8abe0c4fb3874
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52996628"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Vlastnosti uživatele spolupráce Azure Active Directory s B2B

Azure Active Directory (Azure AD) business-to-business (B2B) spolupráce uživatele je uživatel s UserType = hosta. Tento uživatel typu Host obvykle je z partnerské organizace a ve výchozím nastavení má omezená oprávnění v adresáři pozvání.

V závislosti na potřebách zvoucí organizaci uživatele spolupráce B2B ve službě Azure AD může být v jednom z následujících stavů účet:

- Stav 1: Uložená v externí instanci Azure AD, vyjádřena jako uživatel typu Host ve zvoucí organizaci. V tomto případě uživatel B2B přihlásí pomocí účtu služby Azure AD, který patří k tenantovi pozvaného. Pokud partnerské organizace nepoužívá Azure AD, je stále vytváření uživatele typu Host ve službě Azure AD. Požadavky se, že se uplatňovat svou pozvánku a Azure AD ověří jejich e-mailovou adresu. Toto uspořádání se také nazývá tenantů just-in-time (JIT) nebo "virálního" tenantů.

- Stav 2: Uložená v Microsoftu nebo jiného účtu, vyjádřena jako uživatele typu Host v organizaci hostitele. V tomto případě uživatel typu Host přihlásí pomocí účtu Microsoft nebo účtu na sociální síti (google.com nebo podobnou). Identita pozvaného uživatele je vytvořen jako účet Microsoft v adresáři zvoucí organizaci při využití nabídky.

- Stav 3: Adresami v organizaci hostitele v místní službě Active Directory a synchronizují s hostiteli organizace Azure AD. Můžete použít Azure AD Connect pro synchronizaci partnerské účty v cloudu jako Azure AD B2B uživatelé s UserType = hosta. Zobrazit [udělit partnera místně spravované účty přístup ke cloudovým prostředkům](hybrid-on-premises-to-cloud.md).

- Stav 4: Adresami v organizaci hostitele Azure AD s UserType = hosta a přihlašovací údaje, které spravuje organizace hostitele.

  ![Zobrazení iniciály odesílatel pozvánky.](media/user-properties/redemption-diagram.png)


Teď se podívejme se, jak uživatele spolupráce s B2B ve službě Azure AD funguje ve službě Azure AD.

### <a name="before-invitation-redemption"></a>Před uplatnění pozvánky

Stav 1 a 2 stavu účty jsou výsledkem pozvání uživatelů typu Host pro spolupráci s použitím přihlašovacích údajů uživatelů vlastní hosta. Pokud na uživatele typu Host se nejdřív pošle e-mailové pozvánce, účet se vytvoří ve vašem adresáři. Tento účet nemá žádné přihlašovací údaje s ním spojená, protože ověřování provádí zprostředkovatel identity uživatele typu Host. **Zdroj** pro uživatelský účet guest ve vašem adresáři je nastavena na **Invited uživatele**. 

![Před uplatnění nabídky](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po uplatnění pozvánky

Poté, co uživatel typu Host přijme pozvánku, **zdroj** vlastností se aktualizuje podle poskytovatele identity uživatele typu Host.

Pro uživatele typu Host ve stavu 1 **zdroj** je **externí Azure Active Directory**.

![Stav 1 uživatel typu Host po uplatnění nabídky](media/user-properties/after-redemption-state1.png)

Pro uživatele typu Host ve stavu 2 **zdroj** je **Account Microsoft**.

![Uživatel typu Host stavu 2 po uplatnění nabídky](media/user-properties/after-redemption-state2.png)

Pro uživatele typu Host do stavu 3 a 4 stavu **zdroj** je nastavena na **Azure Active Directory** nebo **Windows Server Active Directory**, jak je popsáno v další části.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Vlastnosti klíče uživatele spolupráce B2B ve službě Azure AD
### <a name="usertype"></a>UserType
Tato vlastnost určuje vztah mezi uživateli tenantů hostitele. Tato vlastnost může mít dvě hodnoty:
- Člen: Tato hodnota označuje zaměstnance organizace hostitele a uživatele v mezd organizace. Například tento uživatel očekává, že mají přístup k webům jenom interních. Tento uživatel není považováno za externího spolupracovníka.

- Host: Tato hodnota označuje uživatel, který není považováno za interní společnosti, například externího spolupracovníka, zákazníka nebo partnera. Přijímání generální ředitel interním sdělení svou podrobnou nebo získání výhod společnosti, například neočekává se tohoto uživatele.

  > [!NOTE]
  > UserType nemá žádný vztah k tom, jak se uživatel přihlásí, role adresáře uživatele a tak dále. Tato vlastnost jednoduše označuje vztah uživatele v organizaci hostiteli a umožňuje organizaci k vynucování zásad, které jsou závislé na této vlastnosti.

### <a name="source"></a>Zdroj
Tato vlastnost určuje, jak se uživatel přihlásí.

- Pozvaný uživatel: Tento uživatel byl pozván, ale nebyl dosud uplatnit pozvání.

- Externí služby Active Directory: Tento uživatel je uložená v externí organizace a ověří pomocí účtu služby Azure AD, který patří do jiné organizace. Tento typ přihlášení odpovídá stavu 1.

- Účet Microsoft: Tento uživatel je uložená v účtu Microsoft a provede ověření pomocí účtu Microsoft. Tento typ přihlášení odpovídá stavu 2.

- Windows Server Active Directory: Tento uživatel je přihlášený z místní služby Active Directory, který patří do této organizace. Tento typ přihlášení odpovídá stavu 3.

- Azure Active Directory: Tento uživatel se ověřuje pomocí účtu služby Azure AD, který patří do této organizace. Tento typ přihlášení odpovídá stavu 4.
  > [!NOTE]
  > Zdroj a UserType jsou nezávislé vlastnosti. Hodnota zdroje pro UserType neznamená určitou hodnotu.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Uživatele Azure AD B2B se dá přidat jako členy místo hosté?
Azure AD B2B uživatele a uživatele typu Host jsou obvykle shodný. Proto je uživatele spolupráce B2B ve službě Azure AD přihlášení jako uživatel s UserType = hostovaného ve výchozím nastavení. V některých případech však partnerské organizace je členem větší organizace, ke kterému také organizace hostitele patří. Pokud ano, může být žádoucí hostitele přistupovat ke všem uživatelům v organizaci partnera poskytujícího jako členy místo hosty. Pomocí rozhraní API služby Azure AD B2B pozvánku správce můžete přidat nebo pozvat uživatele v organizaci partnera poskytujícího organizaci hostitele jako člena.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr pro uživatele typu Host do adresáře

![Filtrovat uživatele typu Host](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Převést UserType
Je možné převést UserType od člena, u hosta (a naopak) pomocí prostředí PowerShell. Vlastnosti UserType však představuje relaci uživatele v organizaci. Proto byste měli změnit tuto vlastnost pouze tehdy, pokud relace uživatele na změny organizace. Pokud se změní vztah uživatel by měl změnit hlavní název uživatele (UPN)? Uživatel získat přístup ke stejným prostředkům? By mělo být přiřazeno poštovní schránky? Nedoporučujeme ale, změna UserType pomocí prostředí PowerShell jako aktivita atomické. Také v případě, že tato vlastnost se stane neměnné pomocí prostředí PowerShell, nedoporučujeme tak závislosti na této hodnotě.

## <a name="remove-guest-user-limitations"></a>Odeberte omezení uživatele typu Host
Můžou nastat případy, ve které chcete dát uživatelům typu Host vyšší oprávnění. Můžete přidat uživatele typu Host k žádné roli a dokonce i odstraňovat výchozí omezení uživatele typu Host v adresáři přiřadit uživatele stejná oprávnění jako členové.

Je možné vypnout výchozí omezení, tak, aby uživatel typu Host do adresáře společnosti má stejná oprávnění jako uživatel členem.

![Odeberte omezení uživatele typu Host](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Můžete mi zviditelnit uživatele typu Host do globálního seznamu adres Exchange?
Ano. Ve výchozím nastavení objekty typu Host nejsou viditelné v seznamu globálního adresáře vaší organizace, ale můžete použít Azure Active Directory PowerShell je Pokud chcete zviditelnit. Podrobnosti najdete v tématu **můžete udělám hostů objekty viditelné v globálním?** v [přístup hosta v Office 365 Groups](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Tokeny uživatele spolupráce B2B](user-token.md)
* [Mapování deklarací uživatele spolupráce B2B](claims-mapping.md)
