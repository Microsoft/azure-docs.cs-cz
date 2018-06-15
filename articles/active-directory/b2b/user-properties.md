---
title: Vlastnosti uživatele spolupráce Azure Active Directory s B2B | Microsoft Docs
description: Vlastnosti uživatelů služby Active Directory s B2B spolupráce se dají konfigurovat
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 65fd23ecf81aebdb1fe74f800d20df6414c18317
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260004"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Vlastnosti uživatele spolupráce Azure Active Directory s B2B

Uživatelé Azure Active Directory (Azure AD) (B2B) business-to-business spolupráce je uživatel s UserType = hosta. Tento uživatel guest obvykle je od organizace partnera a má omezená oprávnění v adresáři pozváním ve výchozím nastavení.

V závislosti na potřebách organizace pozváním uživatelé spolupráce Azure AD B2B může být v jednom z následujících stavů účet:

- Stav 1: Adresami v externí instanci Azure AD a reprezentována jako uživatel guest v pozváním organizaci. V takovém případě B2B uživatel přihlásí pomocí účtu Azure AD, který patří do pozvané klienta. Pokud partnerské organizace nepoužívá Azure AD, vytvoří se stále uživatele guest ve službě Azure AD. Požadavky se, že jejich uplatnit své pozvánky a Azure AD ověřuje e-mailové adresy. Toto uspořádání se také nazývá klientů za běhu (JIT) nebo "virální" klientů.

- Stav 2: Adresami v účtu Microsoft a reprezentována jako uživatel guest v organizaci hostitele. V takovém případě uživatele guest přihlásí pomocí účtu Microsoft. Sociální identity pozvané uživatele (google.com nebo podobné), což není účet Microsoft, se vytvoří během nabídka uplatnění jako účet Microsoft.

- Stav 3: Adresami v organizaci hostitele v místní službě Active Directory a synchronizované s hostiteli organizace Azure AD. Během této verze musíte použít PowerShell ruční změna UserType takových uživatelů v cloudu.

- Stav 4: Adresami v hostiteli organizace Azure AD s UserType = hosta a přihlašovací údaje, které spravuje organizace hostitele.

  ![Zobrazení pozvánky iniciály](media/user-properties/redemption-diagram.png)


Teď se podíváme, jak uživatelé spolupráce Azure AD B2B ve stavu 1 vypadá ve službě Azure AD.

### <a name="before-invitation-redemption"></a>Před pozvánku k uplatnění.

![Před nabídka uplatnění.](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Po uplatnění Pozvánka

![Po uplatnění nabídka](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Klíčové vlastnosti uživatele spolupráce Azure AD B2B
### <a name="usertype"></a>UserType
Tato vlastnost určuje relace uživatele na hostiteli klientů. Tato vlastnost může mít dvě hodnoty:
- Člen: Tato hodnota označuje zaměstnanec hostitele organizace a uživatele v mzdy organizace. Například tohoto uživatele očekává, že mají přístup k jen interní weby. Tohoto uživatele nebyla považována za externí spolupracovníka.

- Host: Tato hodnota označuje uživatele, který není považována za interní do společnosti, jako je například externí spolupracovník, partnera, zákazník nebo podobné uživatele. Takový uživatel nebude očekává přijímat interní memo generálního ředitele, nebo přijmout společnosti výhod, třeba.

  > [!NOTE]
  > UserType nemá žádný vztah k tom, jak se uživatel přihlásí, roli adresáře uživatele a tak dále. Tato vlastnost jednoduše označuje vztah uživatele organizaci hostitele a umožňuje organizace k vynucení zásad, které jsou závislé na této vlastnosti.

### <a name="source"></a>Zdroj
Tato vlastnost určuje, jak se uživatel přihlásí.

- Pozvat uživatele: Tento uživatel má dostali pozvánku, ale nebyla ještě uplatněny Pozvánka.

- Externí služby Active Directory: Tento uživatel je adresami v organizaci externí a provede ověření pomocí účtu Azure AD, který patří do jiné organizaci. Tento typ přihlášení odpovídá stavu 1.

- Účet Microsoft: Tento uživatel je adresami v účtu Microsoft a provede ověření pomocí účtu Microsoft. Tento typ přihlášení odpovídá stavu 2.

- Windows Server Active Directory: Tento uživatel je přihlášený z místní služby Active Directory, který patří do této organizace. Tento typ přihlášení odpovídá stavu 3.

- Azure Active Directory: Tento uživatel se ověřuje pomocí účtu Azure AD, který patří do této organizace. Tento typ přihlášení odpovídá stavu 4.
  > [!NOTE]
  > Zdroj a UserType jsou nezávislé vlastnosti. Hodnota zdroje neznamená pro UserType určitou hodnotu.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Může uživatele Azure AD s B2B přidáni jako členové místo hosté?
Obvykle je shodný služby Azure AD s B2B uživatele a uživatele typu Host. Proto uživatelé spolupráce Azure AD B2B je přidána jako uživatel s UserType = hosta ve výchozím nastavení. V některých případech je partnerské organizace členem větší organizaci, ke kterému se organizace hostitele také patří. Pokud ano, hostitele organizace by měly být považovány uživatelé v organizaci partnera jako členy místo hosty. Pomocí rozhraní API Azure AD s B2B pozvánku Manager přidejte nebo pozvat uživatele organizaci hostitele jako člen od organizace partnera.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtr pro uživatele typu Host v adresáři

![Filtrovat uživatele typu Host](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Převést UserType
V současné době je možné uživatelům převést UserType člena na hosta a naopak pomocí prostředí PowerShell. Vlastnost UserType však by měla představovat relace uživatele organizaci. Hodnota této vlastnosti proto změnit pouze v případě, že změny relace uživatele organizaci. Pokud se změní relace uživatele, měli problémy, jako jestli by se měl změnit hlavní název uživatele (UPN), řešeny? Uživatel získat přístup k prostředkům stejné? By měla být přiřazená poštovní schránky? Změna UserType pomocí prostředí PowerShell jako atomic aktivita proto nedoporučujeme. Kromě toho v případě, že tato vlastnost bude neměnné pomocí prostředí PowerShell, nedoporučujeme trvá závislost na této hodnotě.

## <a name="remove-guest-user-limitations"></a>Odeberte omezení uživatele guest
Můžou nastat případy, ve které chcete udělit vaše uživatele typu Host vyšší oprávnění. Můžete přidávat uživatele guest k žádné roli a dokonce odebírat výchozí omezení uživatele guest v adresáři přidělení uživateli stejná práva jako členy.

Je možné vypnout výchozí omezení uživatele guest, tak, aby uživatel guest v adresáři společnosti stejná oprávnění jako uživatel člen.

![Odeberte omezení uživatele guest](media/user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Další postup

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Tokeny uživatele spolupráce B2B](user-token.md)
* [Deklarace uživatele spolupráce B2B mapování](claims-mapping.md)
