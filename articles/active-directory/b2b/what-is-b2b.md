---
title: Co je spolupráce Azure Active Directory s B2B? | Dokumenty Microsoft
description: Spolupráce Azure Active Directory s B2B podporuje vaše vztahy s ostatními společnostmi tím, že obchodním partnerům umožní selektivní přístup k vašim podnikovým aplikacím.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 51a969ae215583a0be8d75ff1de11173e0696a22
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643366"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Co je spolupráce B2B ve službě Azure AD?

Azure Active Directory (Azure AD) pro mezifiremní (B2B) možnosti spolupráce, které umožňují jakékoli organizaci pomocí služby Azure AD pro práci s uživateli z jakékoli jiné organizace, malé nebo velké bezpečné a spolehlivé. Tyto organizace můžou být i bez Azure AD a dokonce ani není potřeba mít IT oddělení.

Organizace, které používají Azure AD poskytuje přístup k dokumentům, prostředky a aplikace pro své partnery, a přitom plnou kontrolu nad své vlastní firemní data. Vývojáři mohou využít služby Azure AD business-to-business rozhraní API pro psaní aplikací, které spojí dvě organizace ve více bezpečně. Je také snadné pro koncové uživatele pro navigaci.

Následující video přináší přehled užitečné.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Klíčové výhody spolupráce B2B ve službě Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Práce s jakýmkoli uživatelem z partnerských

- Partneři používat svoje vlastní přihlašovací údaje
- Nevyžaduje pro partnery pomocí služby Azure AD
- Bez externích adresářů nebo složité nastavování vyžaduje

### <a name="simple-and-secure-collaboration"></a>Jednoduchá a bezpečná spolupráce

- Poskytnout přístup na všechny podnikové aplikace nebo data, při použití sofistikované, Azure AD s využitím autorizační zásady
- Snadné pro uživatele
- Zabezpečení na podnikové úrovni pro aplikace a data

### <a name="no-management-overhead"></a>Bez režie na správu

- Externí správu účtu nebo hesla
- Žádná synchronizace nebo ruční správa životního cyklu účtu
- Žádné externí režijní náklady na správu

## <a name="easily-add-b2b-collaboration-users"></a>Snadno přidat uživatele spolupráce B2B

Jako správce, můžete snadno přidat uživatele B2B spolupráce (Host) k vaší organizaci [webu Azure portal](https://portal.azure.com).

![Přidat uživatele typu Host](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Povolit vaši spolupracovníci přenést svoji vlastní identitu

Spolupracovníci B2B se můžete přihlásit pomocí identity podle vlastní volby. Pokud uživatel nemá účet Microsoft nebo účet Azure AD – vytvoří se pro ně bez problémů v době pro uplatnění nabídky.

### <a name="delegate-to-application-and-group-owners"></a>Delegovat na aplikaci a vlastníci skupiny

Jako aplikace nebo vlastník skupiny můžete přidat uživatele B2B přímo do jakékoli aplikace, která vás zajímají, zda je aplikace Microsoft, nebo ne. Správci mohou delegovat oprávnění k přidání uživatelů B2B bez oprávnění správce. Non správci můžou použít [Azure AD aplikace přístupový Panel](https://myapps.microsoft.com) přidat uživatele spolupráce B2B do aplikace nebo skupiny.

![Přístupový panel](media/what-is-b2b/access-panel.png)

![Přidat člena](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Zásady ověřování ochrana vašeho firemního obsahu

Zásady podmíněného přístupu, jako je ověřování službou Multi-Factor Authentication, je možné vynutit:
- Na úrovni tenanta
- Na úrovni aplikace
- Pro konkrétní uživatele chránit podnikové aplikace a data

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Použití rozhraní API a ukázkový kód umožňují snadno vytvářet aplikace k připojení

Přeneste externí partnery na palubě způsoby přizpůsobit podle potřeb vaší organizace.

Použití [pozvánku spolupráce B2B rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) přizpůsobení připojujeme prostředí, včetně vytváření samoobslužné portály pro registraci. Poskytujeme ukázkový kód pro samoobslužný portál [na Githubu](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registrační portál](media/what-is-b2b/sign-up-portal.png)

Se spoluprací Azure AD B2B můžete získat plný výkon služby Azure AD k ochraně vašeho vztahů s partnery tak, aby koncoví uživatelé najdou jednoduchou a intuitivní.

## <a name="next-steps"></a>Další postup

- [Jak správci služby Azure Active Directory přidat uživatele spolupráce B2B?](add-users-administrator.md)
- [Jak informačních pracovníků vynutit přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- [Uplatnění pozvání spolupráce B2B](redemption-experience.md)
- [Licencování spolupráce v Azure AD B2B](licensing-guidance.md)
- A jako vždy, spojte se s týmem pro všechny názory, diskuse a návrhy prostřednictvím našich [technické komunitě Microsoftu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).