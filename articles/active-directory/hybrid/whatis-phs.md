---
title: Co je synchronizace hodnot hash hesel pomocí Azure AD? | Dokumentace Microsoftu
description: Popisuje synchronizaci hodnot hash hesel.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f88a783ca257090fc607cc9a16ec457c7e9c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016584"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co je synchronizace hodnot hash hesel pomocí Azure AD?
Synchronizace hodnot hash hesel je jednou z metod přihlášení, které se používají k provádění hybridní identity. Azure AD Connect synchronizuje hodnotu hash hesla uživatele z místní instance služby Active Directory s využitím cloudové instance služby Azure AD.

Synchronizace hodnot hash hesel je rozšířením funkce synchronizace adresáře implementované Azure AD Connect synchronizace. Tuto funkci můžete použít pro přihlášení ke službám Azure AD, jako je Microsoft 365. Ke službě se přihlašujete pomocí stejného hesla, které používáte k přihlášení k místní instanci služby Active Directory.

![Co je služba Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizace hodnot hash hesel pomáhá snižovat počet hesel, takže je potřeba, aby se uživatelé zachovali jenom k jednomu. Synchronizace hodnot hash hesel může:

* Zvyšte produktivitu vašich uživatelů.
* Snižte náklady na helpdesk.  

Synchronizace hodnot hash hesel taky umožňuje [zjišťování nevrácených přihlašovacích údajů](../identity-protection/concept-identity-protection-risks.md#user-risk) pro vaše hybridní účty. Společnost Microsoft spolupracuje s tmavými webovými výzkumníky a orgány činnými v oblasti a hledá veřejně dostupné páry uživatelského jména a hesla. Pokud se některé z těchto párů shodují s našimi uživateli, je přidružený účet přesunutý na vysoké riziko. 

>[!NOTE]
> Po povolení KOSMETICE se budou zpracovávat jenom nové nevrácené přihlašovací údaje pro vašeho tenanta. Nepovedlo se ověřit předchozí nalezené páry přihlašovacích údajů.


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
