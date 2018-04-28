---
title: Udělení partnera místně spravované účty přístup k prostředkům cloudu jako uživatelé Azure AD B2B | Microsoft Docs
description: Místně spravované externími partnery poskytněte přístup k i místních a cloudových prostředků pomocí stejných přihlašovacích údajů s spolupráce Azure AD B2B.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/24/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 9abbc5576708468abdda3efeeae4f28376257e32
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udělení partnera místně spravované účty přístupu k cloudových prostředků pomocí spolupráce Azure AD B2B

Organizace s místních systémů identit před Azure Active Directory (Azure AD), mají tradičně spravované partnera účty v jejich místního adresáře. Chcete v této organizace, když spustíte přesunutí aplikace do služby Azure AD, ujistěte se, že vaši partneři mají přístup k prostředkům, které potřebují. Neměli podstatné, jestli prostředky jsou místní nebo v cloudu. Navíc chcete partnera uživatelé mohli používat stejné přihlašovací údaje pro místní i prostředků Azure AD. 

Pokud vytvoříte účty pro externí partnery ve vašem adresáři na místě (například vytvoříte účet s názvem "wmoran" pro externí uživatele s názvem Wendy Moran ve vaší doméně partners.contoso.com přihlášení), teď můžou synchronizovat tyto účty do cloud. Konkrétně Azure AD Connect můžete synchronizovat účty partnera do cloudu jako uživatele Azure AD s B2B (to znamená, uživatelé s UserType = hosta). To umožňuje uživatelům přístup k prostředkům cloudu pomocí stejných přihlašovacích údajů jako místní účty, aniž by jim přístup k více než potřebují partnera. 

## <a name="identify-unique-attributes-for-usertype"></a>Určit jedinečné atributy pro UserType

Než povolíte synchronizaci atribut UserType nastavený na, musíte nejprve rozhodnout, jak do atribut UserType nastavený na odvozena z místní služby Active Directory. Jinými slovy jaké parametry v místním prostředí jsou jedinečné pro externími spolupracovníky? Určete parametr, který se odlišuje od členy vaší vlastní organizaci tyto externími spolupracovníky.

Dva přístupy společné pro tento je:

- Určete nepoužívané místní atributů služby Active Directory (například extensionAttribute1) chcete použít jako zdrojový atribut. 
- Hodnotu pro atribut UserType Alternativně odvozen od dalších vlastností. Například chcete synchronizovat všechny uživatele jako hosta, pokud jejich atribut Active Directory UserPrincipalName v místě skončí s doménou *@partners.contoso.com*.
 
Atribut podrobné požadavky najdete v tématu [povolení synchronizace UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurovat Azure AD Connect uživatelům synchronizace do cloudu

Po identifikaci jedinečný atribut, můžete nakonfigurovat synchronizaci těchto uživatelů do cloudu jako uživatele Azure AD s B2B Azure AD Connect (to znamená, uživatelé s UserType = hosta). Tito uživatelé ze autorizace hlediska, se neodlišuje od B2B uživatelé vytvoření pozvánky procesem spolupráce Azure AD B2B.

Implementace pokyny najdete v tématu [povolení synchronizace UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Další postup

- [Spolupráce Azure Active Directory s B2B pro hybridní organizace](active-directory-b2b-hybrid-organizations.md)
- [Uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- Přehled služby Azure AD Connect, najdete v tématu [integraci místních adresářů se službou Azure Active Directory](connect/active-directory-aadconnect.md).

