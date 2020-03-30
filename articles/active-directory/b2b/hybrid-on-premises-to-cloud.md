---
title: Synchronizace místních partnerských účtů do cloudu jako uživatelé B2B – Azure AD
description: Udělit místně spravované externí partnery přístup k místním i cloudovým prostředkům pomocí stejných přihlašovacích údajů s Azure AD B2B spolupráce.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcc8c0538bb3362818a4172dd42905fd72b19812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272605"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udělte místně spravované partnerské účty přístup ke cloudovým prostředkům pomocí spolupráce Azure AD B2B

Před Azure Active Directory (Azure AD) mají organizace s místními systémy identit tradičně spravované partnerské účty ve svém místním adresáři. V takové organizaci, když začnete přesouvat aplikace do Azure AD, chcete se ujistit, že vaši partneři mají přístup k prostředkům, které potřebují. Nemělo by záležet na tom, jestli jsou prostředky místní nebo v cloudu. Také chcete, aby vaši partneři mohli používat stejná přihlašovací pověření pro místní prostředky i prostředky Azure AD. 

Pokud vytvoříte účty pro externí partnery v místním adresáři (například vytvoříte účet s přihlašovacím jménem "wmoran" pro externího uživatele jménem Wendy Moran ve vaší partners.contoso.com doméně), můžete nyní tyto účty synchronizovat s partners.contoso.com doménou), můžete nyní tyto účty synchronizovat s Cloud. Konkrétně můžete použít Azure AD Connect k synchronizaci partnerských účtů do cloudu jako uživatelé Azure AD B2B (to znamená, že uživatelé s UserType = Guest). To umožňuje partnerským uživatelům přístup ke cloudovým prostředkům pomocí stejných přihlašovacích údajů jako jejich místní účty, aniž by jim poskytli větší přístup, než potřebují. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifikovat jedinečné atributy pro UserType

Před povolením synchronizace atributu UserType se musíte nejprve rozhodnout, jak odvodit atribut UserType z místní služby Active Directory. Jinými slovy, jaké parametry v místním prostředí jsou jedinečné pro vaše externí spolupracovníky? Určete parametr, který odlišuje tyto externí spolupracovníky od členů vaší vlastní organizace.

Dva společné přístupy k tomuto:

- Určete nepoužívaný místní atribut služby Active Directory (například extensionAttribute1), který se použije jako zdrojový atribut. 
- Alternativně odvodit hodnotu pro UserType atribut z jiných vlastností. Chcete například synchronizovat všechny uživatele jako hosta, pokud jejich místní atribut UserPrincipalName služby Active Directory končí * \@partners.contoso.com*domény .
 
Podrobné požadavky na atributy naleznete v [tématu Povolení synchronizace typu UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurace služby Azure AD Connect pro synchronizaci uživatelů s cloudem

Po identifikaci jedinečného atributu můžete nakonfigurovat Azure AD Connect tak, aby tyto uživatele synchronizoval s cloudem jako uživatele Azure AD B2B (to znamená uživatele s Typem Uživatele = Guest). Z hlediska autorizace jsou tito uživatelé k nerozeznání od uživatelů B2B vytvořených prostřednictvím procesu pozvání ke spolupráci Azure AD B2B.

Pokyny k implementaci naleznete v [tématu Povolení synchronizace typu UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Další kroky

- [Spolupráce Azure Active Directory B2B pro hybridní organizace](hybrid-organizations.md)
- [Udělení přístupu uživatelům B2B ve službě Azure AD k místním aplikacím](hybrid-cloud-to-on-premises.md)
- Přehled služby Azure AD Connect najdete v [tématu Integrace místních adresářů pomocí Služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

