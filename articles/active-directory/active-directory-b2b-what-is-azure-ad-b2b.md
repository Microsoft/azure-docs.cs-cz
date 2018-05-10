---
title: Co je spolupráce Azure Active Directory B2B? | Dokumenty Microsoft
description: Spolupráce Azure Active Directory s B2B podporuje vaše vztahy povolením obchodním partnerům umožní selektivní přístup k podnikovým aplikacím.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1eff04cf43260a12a50a08b9145e1975b51af0cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Co je spolupráce B2B ve službě Azure AD?

Možnosti (B2B) spolupráce Azure Active Directory (Azure AD) business-to-business povolit všechny organizace pomocí služby Azure AD pro práci s uživatele z jakékoli jiné organizaci, malý nebo velký bezpečně a bezpečně. Tyto organizace můžou být bez Azure AD a nepotřebujete i oddělení IT.

Organizace pomocí služby Azure AD může poskytnout přístup k dokumentům, prostředky a aplikace na jejich partnery, při zachování plnou kontrolu nad své vlastní firemní data. Vývojáři mohou pomocí Azure AD business-to-business rozhraní API pro psaní aplikací, u kterých se dvě organizace v další bezpečně. Navíc je snadné pro koncové uživatele pro navigaci.

Následující video obsahuje užitečné Přehled.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Klíčové výhody spolupráce Azure AD B2B

### <a name="work-with-any-user-from-any-partner"></a>Práce s všechny uživatele z partnerských

- Partneři používat svoje vlastní přihlašovací údaje
- Nevyžaduje se pro partnery používat Azure AD
- Žádné externí adresáře nebo komplexní nastavení vyžaduje

### <a name="simple-and-secure-collaboration"></a>Jednoduché a bezpečné spolupráce

- Poskytnout přístup k žádné podnikové aplikace nebo data, při použití sofistikované, Azure AD zapnuté autorizační zásady
- Snadné pro uživatele
- Podnikové úrovni zabezpečení pro aplikace a data

### <a name="no-management-overhead"></a>Bez režie na správu

- Externí správu účet nebo heslo
- Žádná synchronizace nebo ruční správa životního cyklu účtu
- Žádné externí režijní náklady na správu

## <a name="easily-add-b2b-collaboration-users"></a>Snadno přidat uživatele spolupráce B2B

Jako správce, můžete snadno přidat uživatele (Host) spolupráce B2B k vaší organizaci [portál Azure](https://portal.azure.com).

![Přidat uživatele typu Host](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Povolit vaší spolupracovníci aby své vlastní identity

B2B spolupracovníci se můžete přihlásit pomocí identity si sami vyberou. Pokud uživatel nemá účet Microsoft nebo účtu Azure AD – jedna se vytvoří pro ně bezproblémově v době pro uplatnění nabídka.

### <a name="delegate-to-application-and-group-owners"></a>Delegovat na aplikace a vlastníků skupiny

Jako aplikace nebo vlastník skupiny můžete přidat uživatele B2B přímo do jakékoli aplikace, která se zajímáte o, zda je aplikace Microsoft, nebo ne. Správci můžou delegovat oprávnění k přidání uživatele B2B do jiným uživatelům než správcům. Všichni uživatelé můžete použít [Azure AD Application přístupový Panel](https://myapps.microsoft.com) k přidání uživatele spolupráce B2B do skupiny nebo aplikace.

![panel přístupu](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![Přidat člena](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Zásady autorizace chránit vaše firemní obsah

Zásady podmíněného přístupu, jako je například služba Multi-Factor authentication, je možné vynutit:
- Na úrovni klienta
- Na úrovni aplikace
- Pro konkrétní uživatele chránit podnikové aplikace a data

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Pomocí rozhraní API a ukázkový kód snadno vytvářet aplikace zařadit do provozu

Přepněte externími partnery na palubě způsoby přizpůsobit potřebám vaší organizace.

Použití [pozvánku spolupráce B2B rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) k přizpůsobení vaší registrace vyskytne, včetně vytváření samoobslužné portály registrace. Poskytujeme ukázkový kód pro samoobslužný portál [na Githubu](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registrační portál](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

S spolupráce Azure AD B2B můžete získat úplné power Azure AD k ochraně vaše vztahy s partnery tak, aby koncoví uživatelé najít snadný a intuitivní.

## <a name="next-steps"></a>Další postup

- [Jak Azure Active Directory správci přidat uživatele spolupráce B2B?](active-directory-b2b-admin-add-users.md)
- [Jak informační pracovníci přidat uživatele spolupráce B2B?](active-directory-b2b-iw-add-users.md)
- [Uplatnění pozvánku spolupráce B2B](active-directory-b2b-redemption-experience.md)
- [Licencování Azure AD s B2B spolupráce](active-directory-b2b-licensing.md)
- A jako vždy připojit s produktový tým pro všechny zpětnou vazbu, diskusí a návrhy prostřednictvím našich [Microsoft technická komunita](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).