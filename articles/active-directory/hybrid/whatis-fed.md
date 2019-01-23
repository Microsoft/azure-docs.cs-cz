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
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ac58992d81be8d3e275dc89a26ed4cb8b6a3242c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475964"
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