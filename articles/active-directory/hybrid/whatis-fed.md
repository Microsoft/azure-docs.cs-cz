---
title: Co je federace se službou Azure AD? | Dokumenty Microsoft
description: Popisuje federace se službou Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f9832a4307ea9519df448f192eeca70043a6d6
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035169"
---
# <a name="what-is-federation-with-azure-ad"></a>Co je federace se službou Azure AD?

Federace je kolekce domén, které mají vztah důvěryhodnosti. Úroveň důvěryhodnosti mohou lišit, ale obvykle zahrnuje ověřování a téměř vždy obsahuje autorizaci. Typické federace může obsahovat několik organizací, které mají vztah důvěryhodnosti pro sdílený přístup k sadu prostředků.

Můžete vytvořit federaci místní prostředí s využitím Azure AD a použít tuto federaci pro ověřování a autorizaci.  Tato metoda přihlášení zajišťuje, že všechny ověření uživatele probíhá místně.  Tato metoda umožňuje správci provádět přísnější úrovně řízení přístupu. Federace se službou AD FS a PingFederate je k dispozici.

![Federovaná identita](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Pokud se rozhodnete využít federaci pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní řešení pro případ, že vaše infrastruktura AD FS selže.


## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je synchronizace hodnot hash hesel?](whatis-phs.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlašování na?](how-to-connect-sso.md)
- [Jak funguje federace](how-to-connect-fed-whatis.md)
- [Federace s PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
