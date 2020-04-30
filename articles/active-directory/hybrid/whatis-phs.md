---
title: Co je synchronizace hodnot hash hesel pomocí Azure AD? | Dokumentace Microsoftu
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "66253863"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co je synchronizace hodnot hash hesel pomocí Azure AD?
Synchronizace hodnot hash hesel je jednou z metod přihlášení, které se používají k provádění hybridní identity. Azure AD Connect synchronizuje hodnotu hash hesla uživatele z místní instance služby Active Directory s využitím cloudové instance služby Azure AD.

Synchronizace hodnot hash hesel je rozšířením funkce synchronizace adresáře implementované Azure AD Connect synchronizace. Tuto funkci můžete použít pro přihlášení ke službám Azure AD, jako je například Office 365. Ke službě se přihlašujete pomocí stejného hesla, které používáte k přihlášení k místní instanci služby Active Directory.

![Co je služba Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizace hodnot hash hesel pomáhá snižovat počet hesel, takže je potřeba, aby se uživatelé zachovali jenom k jednomu. Synchronizace hodnot hash hesel může:

* Zvyšte produktivitu vašich uživatelů.
* Snižte náklady na helpdesk.  

V případě potřeby můžete nastavit synchronizaci hodnot hash hesel jako zálohu, pokud se rozhodnete jako metodu přihlašování použít [federaci s Active Directory Federation Services (AD FS) (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) .

Pokud chcete použít synchronizaci hodnot hash hesel ve vašem prostředí, musíte:

* Nainstalujte Azure AD Connect.  
* Nakonfigurujte synchronizaci adresářů mezi vaší místní instancí služby Active Directory a vaší instancí Azure Active Directory.
* Povolte synchronizaci hodnot hash hesel.



Další informace najdete v tématu [co je hybrid identity?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](whatis-hybrid-identity.md)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlašování?](how-to-connect-sso.md)
- [Jak funguje synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)
