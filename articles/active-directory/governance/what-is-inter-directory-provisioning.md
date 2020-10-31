---
title: Co je mezi adresářovým zřizováním Azure Active Directory? | Dokumentace Microsoftu
description: Popisuje přehled zřizování identit mezi adresáři.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135320"
---
# <a name="what-is-inter-directory-provisioning"></a>Co je mezi adresářovým zřizováním?

Adresář je sdílená informační infrastruktura, která se používá k vyhledání, správě, správě a uspořádání položek a síťových prostředků.  Příklady aplikací, které používají adresářové služby, jsou Microsoft Active Directory a Azure AD.  Identity v adresářových systémech usnadňují určení, jako je třeba to, kdo má přístup k čemu a kdo smí používat konkrétní prostředky.

Mezi adresářovým zřizováním se zřizuje identita mezi dvěma různými systémy adresářových služeb.   Nejběžnějším scénářem pro zřizování mezi adresáři je, že uživatel, který je už ve službě Active Directory, se zřídí do služby Azure AD. Toto zřizování můžou provést agenti, jako je Azure AD Connect synchronizace nebo Azure AD Connect zřizování cloudu.

Mezi adresářovým zřizováním můžeme vytvořit hybridní prostředí [identit](../hybrid/whatis-hybrid-identity.md) .


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Jaké typy meziadresářových zřizování podporuje Azure AD

Azure AD aktuálně podporuje tři metody pro zajištění meziadresářového zřizování. Mezi tyto metody patří:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) – nástroj Microsoftu navržený tak, aby splňoval a splnil vaši hybridní identitu, včetně zřizování mezi adresáři z Active Directory do Azure AD.

- [Zřizování cloudu Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) – nový agent Microsoftu, který je navržený tak, aby splňoval a splnil vaše cíle hybridní identity.  Nabízí vysoce náročné prostředí pro zřizování mezi službami Active Directory a Azure AD.

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) – místní řešení pro správu identit a přístupu od Microsoftu, které pomáhá spravovat uživatele, přihlašovací údaje, zásady a přístup v rámci vaší organizace. Kromě toho MIM nabízí pokročilou zřizování mezi adresáři, aby bylo možné využívat hybridní prostředí identit pro služby Active Directory, Azure AD a další adresáře.

### <a name="key-benefits"></a>Klíčové výhody

Tato schopnost meziadresářového zřizování nabízí tyto významné obchodní výhody:

- [Synchronizace hodnot hash hesel](../hybrid/whatis-phs.md) – Metoda přihlašování, která synchronizuje hash hesla místních uživatelů služby AD pomocí Azure AD.
- [Předávací ověřování](../hybrid/how-to-connect-pta.md) – Metoda přihlašování, která umožňuje uživatelům používat stejné heslo místně a v cloudu, ale nevyžaduje další infrastrukturu federovaného prostředí.
- [Integrace federace](../hybrid/how-to-connect-fed-whatis.md) – dá se použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury AD FS. Poskytuje taky AD FS možnosti správy, jako je například obnovení certifikátu a další AD FS nasazení serveru.
- [Synchronizace](../hybrid/how-to-connect-sync-whatis.md) – zodpovídá za vytváření uživatelů, skupin a dalších objektů.  A zároveň se ujistěte, že informace o identitě místních uživatelů a skupin odpovídají cloudu.  Tato synchronizace zahrnuje taky hodnoty hash hesel.
- [Monitorování stavu](../hybrid/whatis-hybrid-identity-health.md) – může poskytovat robustní monitorování a poskytovat centrální umístění v Azure Portal k zobrazení této aktivity. 


## <a name="next-steps"></a>Další kroky 
- [Co je Správa životního cyklu identit](what-is-identity-lifecycle-management.md)
- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování na základě lidských zdrojů?](what-is-hr-driven-provisioning.md)
- [Co je zřizování aplikací?](what-is-app-provisioning.md)
