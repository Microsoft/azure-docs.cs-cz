---
title: Co je federace se službou Azure AD? | Dokumentace Microsoftu
description: Popisuje federaci s Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7a5b186ebc98babe1baacc9f56f39dc8fbc093
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80049375"
---
# <a name="what-is-federation-with-azure-ad"></a>Co je federace se službou Azure AD?

Federace je kolekce domén, které mají zavedený vztah důvěryhodnosti. Úroveň důvěryhodnosti se může lišit, ale obvykle zahrnuje ověřování a téměř vždy zahrnuje autorizaci. Typická federace může zahrnovat řadu organizací, které navázaly důvěryhodnost pro sdílený přístup k sadě prostředků.

Můžete federovat své místní prostředí pomocí Azure AD a použít tuto federaci k ověřování a autorizaci.  Tato metoda přihlašování zajišťuje, že veškeré ověření uživatele probíhá místně.  Tato metoda umožňuje správcům implementovat přísnější úrovně řízení přístupu. K dispozici je federace s AD FS a PingFederate.

![Federovaná identita](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Pokud se rozhodnete využít federaci pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní řešení pro případ, že vaše infrastruktura AD FS selže.


## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je synchronizace hodnot hash hesel?](whatis-phs.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlašování?](how-to-connect-sso.md)
- [Jak funguje federace](how-to-connect-fed-whatis.md)
- [Federace s PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
