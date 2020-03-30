---
title: Co je synchronizace hash hesla s Azure AD? | Dokumentace Microsoftu
description: Popisuje synchronizaci hodnot hash hesel.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "66253863"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co je synchronizace hash hesla s Azure AD?
Synchronizace hodnot hash hesla je jednou z metod přihlášení používaných k dosažení hybridní identity. Azure AD Connect synchronizuje hash, hash, hesla uživatelů z místní instance služby Active Directory na cloudovou instanci Azure AD.

Synchronizace hodnot hash hesla je rozšíření funkce synchronizace adresářů implementované synchronizací Azure AD Connect. Pomocí této funkce se můžete přihlásit ke službám Azure AD, jako je Office 365. Ke službě se přihlašujete pomocí stejného hesla, které používáte k přihlášení k místní instanci služby Active Directory.

![Co je služba Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizace hash hesel pomáhá snížením počtu hesel, uživatelé musí udržovat pouze jeden. Synchronizace hodnot hash hesla může:

* Zlepšete produktivitu svých uživatelů.
* Snižte náklady na helpdesk.  

Volitelně můžete nastavit synchronizaci hodnot hash hesel jako zálohu, pokud se rozhodnete použít [federaci se službou AD FS (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) jako metodu přihlášení.

Chcete-li používat synchronizaci hodnot hash hesel ve vašem prostředí, je třeba:

* Nainstalujte Azure AD Connect.  
* Nakonfigurujte synchronizaci adresářů mezi místní instancí služby Active Directory a instancí služby Azure Active Directory.
* Povolte synchronizaci hodnot hash hesel.



Další informace naleznete v tématu [Co je hybridní identita?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](whatis-hybrid-identity.md)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlášení?](how-to-connect-sso.md)
- [Jak funguje synchronizace hash hesel](how-to-connect-password-hash-synchronization.md)
