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
ms.topic: get-started-article
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 278935ae7d29680e4fa741d59370e75ee0e1e93d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197700"
---
# <a name="what-is-federation-with-azure-ad"></a>Co je federace se službou Azure AD?

Federace je kolekce domén, které mají vztah důvěryhodnosti. Úroveň důvěryhodnosti mohou lišit, ale obvykle zahrnuje ověřování a téměř vždy obsahuje autorizaci. Typické federace může obsahovat několik organizací, které mají vztah důvěryhodnosti pro sdílený přístup k sadu prostředků.

Můžete vytvořit federaci místní prostředí s využitím Azure AD a použít tuto federaci pro ověřování a autorizaci.  Tato metoda přihlášení zajišťuje, že všechny ověření uživatele probíhá místně.  Tato metoda umožňuje správci provádět přísnější úrovně řízení přístupu. Federace se službou AD FS a PingFederate je k dispozici.

![Federovaná identita](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Pokud se rozhodnete využít federaci pomocí služby Active Directory Federation Services (AD FS), můžete volitelně nastavit synchronizaci hodnot hash hesel jako záložní řešení pro případ, že vaše infrastruktura AD FS selže.


## <a name="next-steps"></a>Další kroky

- [Co je hybridní identita?](whatis-phs.md)
- [Co je Azure AD Connect a Connect Health?](whatis-azure-ad-connect.md)
- [Co je synchronizace hodnot hash hesel?](whatis-phs.md)
- [Co je federace?](whatis-fed.md)
- [Co je jednotné přihlašování na?](how-to-connect-sso.md)
- [Jak funguje federace](how-to-connect-fed-whatis.md)
- [Federace s PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
