---
title: Vlastnosti uživatele spolupráce Azure Active Directory B2B | Dokumentace Microsoftu
description: Je možné konfigurovat Azure Active Directory s B2B spolupráce uživatelské vlastnosti
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059638"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Vlastnosti uživatele spolupráce Azure Active Directory s B2B

Azure Active Directory (Azure AD) business-to-business (B2B) spolupráce uživatele je uživatel s UserType = hosta. Tento uživatel typu Host obvykle je z partnerské organizace a ve výchozím nastavení má omezená oprávnění v adresáři pozvání.

V závislosti na potřebách zvoucí organizaci uživatele spolupráce B2B ve službě Azure AD může být v jednom z následujících stavů účet:

- Stav 1: Uložená v externí instanci Azure AD, vyjádřena jako uživatel typu Host ve zvoucí organizaci. V tomto případě uživatel B2B přihlásí pomocí účtu služby Azure AD, který patří k tenantovi pozvaného. Pokud partnerské organizace nepoužívá Azure AD, je stále vytváření uživatele typu Host ve službě Azure AD. Požadavky se, že se uplatňovat svou pozvánku a Azure AD ověří jejich e-mailovou adresu. Toto uspořádání se také nazývá tenantů just-in-time (JIT) nebo "virálního" tenantů.

- Stav 2: Uložená v účtu Microsoft a vyjádřena jako uživatel typu Host v organizaci hostitele. V tomto případě uživatel typu Host přihlásí pomocí účtu Microsoft. Pozvaný uživatel sociálních identit (google.com nebo podobné), který není účtem Microsoft, se vytvoří během nabídka uplatnění jako účet Microsoft.

- Stav 3: Adresami v organizaci hostitele v místní službě Active Directory a synchronizují s hostiteli organizace Azure AD. Během této verze musíte ručně změnit UserType tyto uživatele v cloudu pomocí Powershellu.

- Stav 4: Adresami v hostiteli organizace Azure AD s UserType = hosta a přihlašovací údaje, které spravuje organizace hostitele.

  ![Zobrazení iniciály odesílatel pozvánky.](media/user-properties/redemption-diagram.png)


Teď se podívejme se, jak uživatele spolupráce B2B ve službě Azure AD ve stavu 1 vypadá ve službě Azure AD.

### <a name="before-invitation-redemption"></a>Před uplatnění pozvánky

![Před uplatnění nabídky](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po uplatnění pozvánky

![Po uplatnění nabídky](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Vlastnosti klíče uživatele spolupráce B2B ve službě Azure AD
### <a name="usertype"></a>UserType
Tato vlastnost určuje vztah mezi uživateli tenantů hostitele. Tato vlastnost může mít dvě hodnoty:
- Člen: Tato hodnota označuje zaměstnance organizace hostitele a uživatele v mezd organizace. Například tento uživatel očekává, že mají přístup k webům jenom interních. Tento uživatel nebude považovat za externího spolupracovníka.

- Host: Tato hodnota označuje uživatel, který není považováno za interní společnosti, jako je například externího spolupracovníka, partner, zákazníka nebo podobné uživatele. Přijímat interním sdělení svou podrobnou generální ředitel, nebo získání výhod společnosti, například by očekávat takový uživatel.

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
V současné době je možné pro uživatele pro převod UserType z členů na hosta (a naopak) pomocí prostředí PowerShell. Nicméně vlastnosti UserType by měl k vyjádření vztahu uživatele v organizaci. Hodnota této vlastnosti by proto změnit pouze v případě, že změny relace uživatele v organizaci. Pokud se změní vztah mezi uživatelem, by měla problémy, jako je, zda by měl změnit hlavní název uživatele (UPN), řešit? Uživatel získat přístup ke stejným prostředkům? By mělo být přiřazeno poštovní schránky? Změna UserType pomocí prostředí PowerShell jako aktivita atomické proto nedoporučujeme. Kromě toho v případě, že tato vlastnost se stane neměnné pomocí prostředí PowerShell, nedoporučujeme tak závislosti na této hodnotě.

## <a name="remove-guest-user-limitations"></a>Odeberte omezení uživatele typu Host
Můžou nastat případy, ve které chcete dát uživatelům typu Host vyšší oprávnění. Můžete přidat uživatele typu Host k žádné roli a dokonce i odstraňovat výchozí omezení uživatele typu Host v adresáři přiřadit uživatele stejná oprávnění jako členové.

Je možné vypnout výchozí omezení uživatele typu Host, tak, aby uživatel typu Host do adresáře společnosti stejná oprávnění jako uživatel členem.

![Odeberte omezení uživatele typu Host](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Můžete mi zviditelnit uživatele typu Host do globálního seznamu adres Exchange?
Ano. Ve výchozím nastavení objekty typu Host, se nezobrazí v seznamu globálního adresáře vaší organizace, ale můžete použít Azure Active Directory PowerShell je Pokud chcete zviditelnit. Podrobnosti najdete v tématu **můžete udělám hostů objekty viditelné v globálním?** v [přístup hosta v Office 365 Groups](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Tokeny uživatele spolupráce B2B](user-token.md)
* [Mapování deklarací uživatele spolupráce B2B](claims-mapping.md)
