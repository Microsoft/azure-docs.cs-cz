---
title: Poradce při potížích s instalací služby Azure AD Connect | Dokumenty společnosti Microsoft
description: Toto téma obsahuje postup řešení problémů s instalací služby Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211842"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Poradce při potížích: Problémy s instalací Služby Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**
Zkontrolujte, který [typ instalace služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) je pro vás vhodný. Pokud splňujete kritéria expresní instalace, doporučujeme vám, abyste se s expresní instalací schovali. Expresní instalace vám dává minimální možnosti potřebné k dokončení instalace, proto je menší pravděpodobnost jakýchkoli problémů. 

Pokud však nesplňujete kritéria expresní instalace a musíte provést vlastní instalaci, pak zde jsou některé doporučené postupy, které můžete dodržovat, abyste se vyhnuli běžným problémům. V zájmu jednoduchosti jsou zde uvedeny pouze selektivní možnosti:

* Ujistěte se, že jste správcem počítače, do kterého instalujete službu AAD Connect. Přihlaste se k počítači se stejnými přihlašovacími údaji správce.

* Nechť všechny možnosti, které mají být výchozí na následující stránce, s výjimkou "Použít existující SQL Server", pokud chcete použít existující SQL Server. Zde jsou [další podrobnosti](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) o použití vlastních možností instalace. 

    ![Použití existujícího serveru SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na následující stránce vyberte možnost "Vytvořit nový účet ad", abyste se vyhnuli problémům s oprávněními u stávajícího účtu.

    ![Účet doménové struktury služby AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Běžné problémy**

* [Problémy s připojením k místnímu službě Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problémy s připojením s online službou Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problémy s oprávněním k místnímu službě Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Požadavky pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Výběr typu instalace pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Začínáme se službou Azure AD Connect s použitím expresního nastavení](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Vlastní instalace služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Co je pracovní server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co je modul PowerShellu ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Další kroky
- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md)





