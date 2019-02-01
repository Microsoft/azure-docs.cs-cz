---
title: Připojení Active Directory s Azure Active Directory | Dokumenty Microsoft
description: Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro aplikace Office 365, Azure a SaaS integrované s Azure AD.
keywords: Úvod k Azure AD Connect, přehled Azure AD Connect, co je Azure AD Connect, instalace active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fc18353ff89f8613847c977066e51620fb3e834f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494660"
---
# <a name="what-is-hybrid-identity"></a>Co je hybridní identita? 

Dnes, podniky a společnosti jsou stává čím dál tím víc směs místních a cloudových aplikací.  Uživatelé, kteří potřebují přístup k těmto aplikacím v místním prostředí i v cloudu. Tento požadavek se stal náročné scénáře. 

Řešení identit společnosti Microsoft zahrnují místní a cloudové funkce.  Tato řešení vytvořit společnou uživatelskou identitu pro ověřování a autorizaci ke všem prostředkům, bez ohledu na umístění. To označujeme **hybridní identita**.

K dosažení hybridní identity, jedním ze tří způsobů ověřování je možné, v závislosti na scénáře.   Jsou tři metody: 

- **[Synchronizace hodnot hash hesel (PHS)](whatis-phs.md)**  
- **[Předávací ověřování (PTA)](how-to-connect-pta.md)**  
- **[Federace](whatis-fed.md)** 

Tyto metody ověřování také poskytují [jednotného přihlašování](how-to-connect-sso.md) možnosti.  Jednotného přihlašování automaticky přihlásí uživatele v případě, že nejsou v jejich firemní zařízení připojených k podnikové síti.

Další informace najdete v tématu [zvolte správné ověřování pro vaše řešení hybridní identity Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení 

Tady najdete několik běžných scénářů souvisejících s hybridní identitou a správou přístupu a doporučení, jaká možnost (nebo možnosti) hybridní identity by mohla být pro každý z nich nejvhodnější. 

|Požadavky:|PHS a SSO<sup>1</sup>| PTA a SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatická synchronizace nových uživatelských, kontaktních a skupinových účtů vytvořených v místní službě Active Directory do cloudu|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Nastavení tenanta pro hybridní scénáře Office 365|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolení přihlašování a přístupu uživatelů ke cloudovým službám pomocí místního hesla|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementace jednotného přihlašování pomocí podnikových přihlašovacích údajů|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučené](./media/whatis-hybrid-identity/ic195031.png) |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|  
|Zajištění, aby se žádné hodnoty hash hesel neukládaly v cloudu| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolení cloudových řešení vícefaktorového ověřování| |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)|![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolení místních řešení vícefaktorového ověřování| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Podpora ověřování uživatelů pomocí čipové karty<sup>4</sup>| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 
|Zobrazování oznámení o vypršení platnosti hesla na portálu Office a na ploše Windows 10| | |![Doporučené](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronizace hodnot hash hesel a jednotné přihlašování. 
> 
> <sup>2</sup> Předávací ověřování a jednotné přihlašování.  
> 
> <sup>3</sup> Federované jednotné přihlašování pomocí AD FS.  
>  
> <sup>4</sup> AD FS je možné integrovat s podnikovou infrastrukturou veřejných klíčů a umožnit tak přihlašování pomocí certifikátů. Těmito certifikáty můžou být softwarové certifikáty nasazené přes důvěryhodné kanály zřizování, jako MDM nebo GPO, nebo certifikáty čipových karet (včetně karet PIV/CAC) nebo Hello pro firmy (důvěryhodnost certifikátu). Další informace o podpoře ověřování pomocí čipové karty najdete v [tomto blogovém příspěvku](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md) 
- [Co je synchronizace hodnot hash hesel (PHS)?](whatis-phs.md) 
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md) 
- [Co je federace?](whatis-fed.md) 
- [Co je jednotné přihlašování na?](how-to-connect-sso.md) 

