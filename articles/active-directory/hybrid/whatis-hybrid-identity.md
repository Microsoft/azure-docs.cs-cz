---
title: Co je hybridní identita ve službě Azure Active Directory?
description: Hybridní identita má společnou identitu uživatele pro ověřování a autorizaci jak místně, tak v cloudu.
keywords: Úvod k Azure AD Connect, přehled Azure AD Connect, co je Azure AD Connect, instalace active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d681dd06f07f6174e31b59cccf42df5dc16a1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "68779854"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Co je hybridní identita ve službě Azure Active Directory?

Dnes se firmy a korporace stávají stále více směsicí místních a cloudových aplikací.  Uživatelé vyžadují přístup k těmto aplikacím místně i v cloudu. Správa uživatelů v místním prostředí i v cloudu představuje náročné scénáře. 

Řešení identit společnosti Microsoft zahrnuje místní a cloudové funkce.  Tato řešení vytvořit společnou identitu uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění. Nazýváme tuto **hybridní identitu**.

S hybridní identitou azure ad a správou hybridníidentity jsou tyto scénáře možné.

K dosažení hybridní identity s Azure AD, lze použít jednu ze tří metod ověřování, v závislosti na scénářích.   Tyto tři metody jsou: 

- **[Synchronizace hash hesel (PHS)](whatis-phs.md)**  
- **[Předávací ověřování (PTA)](how-to-connect-pta.md)**  
- **[Federace (AD FS)](whatis-fed.md)** 

Tyto metody ověřování také poskytují možnosti [jednotného přihlášení.](how-to-connect-sso.md)  Jednotné přihlašování automaticky přihlásí uživatele, když jsou na svých podnikových zařízeních připojených k podnikové síti.

Další informace najdete [v tématu Výběr správné metody ověřování pro řešení hybridní identity služby Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). 

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení 

Tady najdete několik běžných scénářů souvisejících s hybridní identitou a správou přístupu a doporučení, jaká možnost (nebo možnosti) hybridní identity by mohla být pro každý z nich nejvhodnější. 

|Požadavky:|PHS a SSO<sup>1</sup>| PTA a SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatická synchronizace nových uživatelských, kontaktních a skupinových účtů vytvořených v místní službě Active Directory do cloudu|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Nastavte si klienta pro hybridní scénáře Office 365.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Umožněte uživatelům přihlásit se ke cloudovým službám a přistupovat k nim pomocí místního hesla.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementujte jednotné přihlašování pomocí podnikových přihlašovacích údajů.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|  
|Ujistěte se, že v cloudu nejsou uloženy žádné hashe hesla.| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolte cloudová vícefaktorová ověřování.|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolte místní řešení vícefaktorového ověřování.| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Podpora ověřování pomocí čipových karet pro uživatele <sup>4.</sup>| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Zobrazte oznámení o vypršení platnosti hesla na portálu Office Portal a na ploše Windows 10.| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronizace hodnot hash hesel a jednotné přihlašování. 
> 
> <sup>2</sup> Předávací ověřování a jednotné přihlašování.  
> 
> <sup>3</sup> Federované jednotné přihlašování pomocí AD FS.  
>  
> <sup>4</sup> AD FS je možné integrovat s podnikovou infrastrukturou veřejných klíčů a umožnit tak přihlašování pomocí certifikátů. Těmito certifikáty můžou být softwarové certifikáty nasazené přes důvěryhodné kanály zřizování, jako MDM nebo GPO, nebo certifikáty čipových karet (včetně karet PIV/CAC) nebo Hello pro firmy (důvěryhodnost certifikátu). Další informace o podpoře ověřování pomocí čipové karty najdete v [tomto blogovém příspěvku](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenční požadavky pro používání služby Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md) 
- [Co je synchronizace hash hesla (PHS)?](whatis-phs.md) 
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md) 
- [Co je federace?](whatis-fed.md) 
- [Co je jednotné přihlášení?](how-to-connect-sso.md) 

