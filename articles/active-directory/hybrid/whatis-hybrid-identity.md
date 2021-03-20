---
title: Co je hybridní identita s Azure Active Directory?
description: Hybridní identita má běžnou identitu uživatelů pro ověřování a autorizaci v místním prostředí i v cloudu.
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
ms.openlocfilehash: 895355b5acebffe6ad24b15b0c709d21e8f20be4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90016644"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Co je hybridní identita s Azure Active Directory?

V současné době se podniky a společnosti stávají více a více než v různých místních i cloudových aplikacích.  Uživatelé vyžadují přístup k těmto aplikacím místně i v cloudu. Správa uživatelů v místním prostředí i v cloudu přináší náročné scénáře. 

Řešení identit od Microsoftu využívají místní a cloudové možnosti.  Tato řešení vytváří společnou identitu uživatelů pro ověřování a autorizaci pro všechny prostředky bez ohledu na jejich umístění. Tuto **hybridní identitu** voláme.

S hybridní identitou pro Azure AD a hybridní správu identit se tyto scénáře stanou možné.

Pro zajištění hybridní identity pomocí Azure AD se dá použít jedna ze tří metod ověřování v závislosti na vašich scénářích.   Tyto tři metody: 

- **[Synchronizace hodnot hash hesel (KOSMETICE)](whatis-phs.md)**  
- **[Předávací ověřování (PTA)](how-to-connect-pta.md)**  
- **[Federace (AD FS)](whatis-fed.md)** 

Tyto metody ověřování také poskytují možnosti [jednotného přihlašování](how-to-connect-sso.md) .  Jednotné přihlašování automaticky podepisuje uživatele v případě, že jsou na jejich firemních zařízeních, připojené k podnikové síti.

Další informace najdete v tématu [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](./choose-ad-authn.md). 

## <a name="common-scenarios-and-recommendations"></a>Běžné scénáře a doporučení 

Tady najdete několik běžných scénářů souvisejících s hybridní identitou a správou přístupu a doporučení, jaká možnost (nebo možnosti) hybridní identity by mohla být pro každý z nich nejvhodnější. 

|Požadavky:|PHS a SSO<sup>1</sup>| PTA a SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatická synchronizace nových uživatelských, kontaktních a skupinových účtů vytvořených v místní službě Active Directory do cloudu|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučeno](./media/whatis-hybrid-identity/ic195031.png) |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Nastavte svého tenanta pro Microsoft 365 hybridních scénářů.|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučeno](./media/whatis-hybrid-identity/ic195031.png) |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Umožněte uživatelům přihlášení a přístup ke cloudovým službám pomocí jejich místního hesla.|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučeno](./media/whatis-hybrid-identity/ic195031.png) |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementujte jednotné přihlašování pomocí podnikových přihlašovacích údajů.|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| ![Doporučeno](./media/whatis-hybrid-identity/ic195031.png) |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)|  
|Ujistěte se, že v cloudu nejsou uložené žádné hodnoty hash hesel.| |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolte cloudová řešení Multi-Factor Authentication.|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)|![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Povolte místní řešení Multi-Factor Authentication.| | |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Podpora ověřování SmartCard pro moje uživatele <sup>4</sup> .| | |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 
|Zobrazit oznámení o vypršení platnosti hesla na portálu Office a na desktopu Windows 10| | |![Doporučeno](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronizace hodnot hash hesel a jednotné přihlašování. 
> 
> <sup>2</sup> Předávací ověřování a jednotné přihlašování.  
> 
> <sup>3</sup> Federované jednotné přihlašování pomocí AD FS.  
>  
> <sup>4</sup> AD FS je možné integrovat s podnikovou infrastrukturou veřejných klíčů a umožnit tak přihlašování pomocí certifikátů. Těmito certifikáty můžou být softwarové certifikáty nasazené přes důvěryhodné kanály zřizování, jako MDM nebo GPO, nebo certifikáty čipových karet (včetně karet PIV/CAC) nebo Hello pro firmy (důvěryhodnost certifikátu). Další informace o podpoře ověřování pomocí čipové karty najdete v [tomto blogovém příspěvku](/archive/blogs/samueld/adfs-certauth-aad-o365). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenční požadavky pro použití Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Další kroky 

- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md) 
- [Co je synchronizace hodnot hash hesel (KOSMETICE)?](whatis-phs.md) 
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md) 
- [Co je federace?](whatis-fed.md) 
- [Co je jednotné přihlašování?](how-to-connect-sso.md)