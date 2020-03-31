---
title: Co je federace s Azure AD? | Dokumentace Microsoftu
description: Popisuje federaci se službou Azure AD.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049375"
---
# <a name="what-is-federation-with-azure-ad"></a>Co je federace s Azure AD?

Federace je kolekce domén, které vytvořily vztah důvěryhodnosti. Úroveň důvěryhodnosti se může lišit, ale obvykle zahrnuje ověřování a téměř vždy zahrnuje autorizaci. Typická federace může zahrnovat několik organizací, které vytvořily vztah důvěryhodnosti pro sdílený přístup k sadě prostředků.

Můžete federovat místní prostředí s Azure AD a použít tuto federaci pro ověřování a autorizaci.  Tato metoda přihlášení zajišťuje, že všechny ověřování uživatelů dochází v místním prostředí.  Tato metoda umožňuje správcům implementovat přísnější úrovně řízení přístupu. Federace s AD FS a PingFederate je k dispozici.

![Federovaná identita](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Pokud se rozhodnete využít federaci pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní řešení pro případ, že vaše infrastruktura AD FS selže.


## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je synchronizace hodnot hash hesel?](whatis-phs.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlášení?](how-to-connect-sso.md)
- [Jak funguje federace](how-to-connect-fed-whatis.md)
- [Federace s PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
