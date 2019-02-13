---
title: Partnerské místně spravované účty přístup ke cloudovým prostředkům jako uživatele Azure AD B2B udělit | Dokumentace Microsoftu
description: Místně spravované externím partnerům poskytněte přístup k i místních a cloudových prostředků pomocí stejných přihlašovacích údajů se spoluprací Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29f5deb8bd06d4001f1776765ea6824da9bd9802
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163780"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Udělit partnera místně spravované účty přístup ke cloudovým prostředkům využitím spolupráce Azure AD B2B

Organizace s místními systémy identit před Azure Active Directory (Azure AD), mají partnerské tradičně spravované účty v jejich místní adresář. Chcete v rámci organizace při spuštění přesun aplikací do Azure AD, ujistěte se, že vaši partneři mají přístup k prostředkům, které potřebují. By nemělo záležet, jestli jsou prostředky v místním nebo v cloudu. Navíc se mají vaši uživatelé partnera moct dál používat stejné přihlašovací údaje pro místní i prostředky služby Azure AD. 

Pokud jste vytvořili účty pro externí partnery v místním adresáři (například vytvoříte účet s názvem "wmoran" pro externí uživatele s názvem Wendy firem ve vaší doméně partners.contoso.com přihlášení), teď můžete synchronizovat ryto účty cloud. Konkrétně můžete použít Azure AD Connect pro synchronizaci partnerské účty v cloudu jako uživatele Azure AD B2B (to znamená, že uživatelé s UserType = hosta). To umožňuje uživatelům přístup ke cloudovým prostředkům pomocí stejných přihlašovacích údajů jako místní účty, aniž by jim přístup k více než potřebují partnera. 

## <a name="identify-unique-attributes-for-usertype"></a>Identifikuje jedinečné atributy UserType

Než povolíte synchronizaci atribut UserType, musíte nejdřív rozhodnout, jak odvodit atribut UserType z místní služby Active Directory. Jinými slovy jaké parametry se v místním prostředí jsou jedinečné pro externí spolupracovníky? Určete parametr, který odlišuje od členy vaší vlastní organizaci těmito externími spolupracovníky.

Jsou dvě běžné metody pro toto:

- Určete nevyužitých místních atributů služby Active Directory (například extensionAttribute1) použít jako zdrojový atribut. 
- Můžete také odvodit hodnotu pro atribut UserType z dalších vlastností. Například chcete synchronizujeme všechny uživatele jako hosta, pokud jejich místní atribut Active Directory UserPrincipalName skončí s doménou *@partners.contoso.com*.
 
Atribut podrobné požadavky najdete v tématu [povolení synchronizace hodnot UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurace služby Azure AD Connect pro synchronizaci uživatelů do cloudu

Po identifikaci jedinečný atribut, můžete nakonfigurovat synchronizaci tito uživatelé do cloudu jako uživatele Azure AD B2B Azure AD Connect (to znamená, že uživatelé s UserType = hosta). Z autorizační hlediska jsou tito uživatelé nerozeznatelná od uživatele B2B, které jsou vytvořené pomocí proces pozvánky spolupráce B2B ve službě Azure AD.

Pokyny k implementaci, najdete v části [povolení synchronizace hodnot UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Další postup

- [Spolupráce Azure Active Directory s B2B pro hybridní organizace](hybrid-organizations.md)
- [Uživatelům udělit B2B v Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md)
- Přehled služby Azure AD Connect, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

