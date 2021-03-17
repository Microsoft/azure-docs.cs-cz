---
title: Synchronizace místních partnerských účtů s cloudem jako uživatelé B2B – Azure AD
description: Udělte místně spravovaným externím partnerům přístup k místním i cloudovým prostředkům pomocí stejných přihlašovacích údajů ve spolupráci s Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575919"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udělení přístupu ke cloudovým prostředkům místně spravovaným partnerským účtům s využitím spolupráce Azure AD s B2B

Než Azure Active Directory (Azure AD), organizace s místními systémy identit mají tradičně spravované partnerské účty ve svém místním adresáři. V takové organizaci, když začnete přesouvat aplikace do služby Azure AD, se chcete ujistit, že partneři budou mít přístup k potřebným prostředkům. Nezáleží na tom, jestli jsou prostředky místní nebo v cloudu. Také chcete, aby mohli vaši partneři používat stejné přihlašovací údaje pro místní i prostředky Azure AD. 

Pokud vytvoříte účty pro externí partnery v místním adresáři (například vytvoříte účet s přihlašovacím jménem "wmoran" pro externího uživatele s názvem Wendy Moran v doméně partners.contoso.com), můžete nyní tyto účty synchronizovat s cloudem. Konkrétně můžete použít Azure AD Connect k synchronizaci partnerských účtů do cloudu, který vytvoří uživatelský účet s UserType = Guest. Díky tomu budou mít vaši partneři přístup k prostředkům cloudu pomocí stejných přihlašovacích údajů, jako jsou jejich místní účty, a to bez toho, aby k nim měli přístup.

> [!NOTE]
> Viz také postup pro [pozvání interních uživatelů ke spolupráci B2B](invite-internal-users.md). Pomocí této funkce můžete pozvat interní uživatele typu Host, aby mohli používat spolupráci B2B, bez ohledu na to, jestli jste své účty synchronizoval z místního adresáře do cloudu. Jakmile uživatel přijme pozvání k používání spolupráce B2B, bude moci používat vlastní identity a přihlašovací údaje pro přihlášení k prostředkům, ke kterým mají přístup. Nebudete muset spravovat hesla ani spravovat životní cyklus účtů.

## <a name="identify-unique-attributes-for-usertype"></a>Identifikujte jedinečné atributy pro UserType

Než povolíte synchronizaci atributu UserType, musíte se nejdřív rozhodnout, jak se má odvodit atribut UserType z místní služby Active Directory. Jinými slovy, jaké parametry v místním prostředí jsou jedinečné pro vaše externí spolupracovníky? Určete parametr, který rozlišuje tyto externí spolupracovníci od členů vaší organizace.

K tomu patří dva běžné přístupy:

- Určete nepoužitý místní atribut služby Active Directory (například extensionAttribute1), který se použije jako zdrojový atribut. 
- Alternativně je odvozena hodnota atributu UserType z jiných vlastností. Například chcete synchronizovat všechny uživatele jako hosta, pokud jejich místní atribut Active Directory UserPrincipalName končí na *\@ Partners.contoso.com* domény.
 
Podrobné požadavky atributů najdete v tématu [povolení synchronizace usertype](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurace Azure AD Connect pro synchronizaci uživatelů s cloudem

Po identifikaci jedinečného atributu můžete nakonfigurovat Azure AD Connect pro synchronizaci těchto uživatelů s cloudem, který vytvoří uživatelský účet s UserType = Host. Z autorizačního bodu jsou tito uživatelé nerozlišovat od uživatelů B2B vytvořených prostřednictvím procesu pozvání spolupráce B2B v Azure AD.

Pokyny k implementaci najdete v tématu [povolení synchronizace usertype](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Další kroky

- [Azure Active Directory spolupráce B2B pro hybridní organizace](hybrid-organizations.md)
- [Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](hybrid-cloud-to-on-premises.md)
- Přehled Azure AD Connect najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

