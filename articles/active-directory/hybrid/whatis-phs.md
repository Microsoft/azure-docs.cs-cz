---
title: Co je synchronizace hodnot hash hesel v Azure AD? | Dokumenty Microsoft
description: Popisuje synchronizaci hodnot hash hesel.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d11759fbc3354364af3f78599ff9cfb25674c612
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175138"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co je synchronizace hodnot hash hesel v Azure AD?
Synchronizace hodnot hash hesel je jedna z metod přihlašování používat k provádění hybridní identita. Azure AD Connect synchronizuje hodnoty hash,-the-hash, hesla uživatelů z místní instance Active Directory do služby Azure AD se cloudové instance.

Synchronizace hodnot hash hesel je rozšíření funkce synchronizace adresáře implementované synchronizace Azure AD Connect. Tuto funkci můžete použít pro přihlášení ke službám Azure AD, jako je Office 365. Přihlášení ke službě s použitím stejné heslo, které používáte pro přihlášení k vaší místní instancí Active Directory.

![Co je služba Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizace hodnot hash hesel pomáhá snížením počtu hesel, vaši uživatelé potřebují k údržbě pouze k jedné. Synchronizace hodnot hash hesel můžete:

* Zvýšení produktivity uživatelů.
* Snižte náklady na helpdesk.  

Volitelně můžete nastavit synchronizaci hodnot hash hesel jako záložní Pokud se rozhodnete použít [federace se službou Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) jako metodu přihlašování.

Pokud chcete použít synchronizaci hodnot hash hesel ve vašem prostředí, budete muset:

* Instalace služby Azure AD Connect.  
* Konfigurace synchronizace adresářů mezi vaší místní služby Active Directory instanci a instanci služby Azure Active Directory.
* Povolte synchronizaci hodnot hash hesel.



Další informace najdete v tématu [co je hybridní identita?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](whatis-phs.md)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je předávací ověřování (PTA)?](how-to-connect-pta.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlašování na?](how-to-connect-sso.md)
- [Jak funguje synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)
