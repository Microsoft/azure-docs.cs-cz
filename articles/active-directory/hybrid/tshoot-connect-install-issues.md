---
title: Řešení potíží s Azure AD Connect instalaci | Dokumentace Microsoftu
description: Toto téma popisuje kroky pro řešení potíží s instalací Azure AD Connect.
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
ms.openlocfilehash: 56fd4be85fabebcdde5ca271630e1c5b83c398b9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773215"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Řešení potíží: Problémy instalace služby Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**
Zkontrolujte prosím, která [typ instalace služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) je vhodný pro vás. Při splnění kritérií Expresní instalace, pak důrazně doporučujeme pro expresní instalace. Expresní instalace poskytuje minimální možnosti, které jsou potřebné k dokončení instalace, proto je menší pravděpodobnost, že případné problémy. 

Ale pokud nesplňují kritéria Expresní instalace a musíte provést vlastní instalaci, pak tady jsou některé osvědčené postupy můžete použít, aby běžné problémy. Z důvodu zjednodušení jenom selektivní možností uvedených zde:

* Ujistěte se, že máte oprávnění správce na počítači, na který instalujete, AAD Connect. Přihlaste se počítači pomocí stejných přihlašovacích údajů správce.

* Umožní všechny možnosti jako výchozí na následující stránce, s výjimkou "Použití existujícího serveru SQL Server", pokud chcete použít existující systém SQL Server. Tady jsou [podrobnosti](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) o tom, jak používat možnosti vlastní instalace. 

    ![Use Existing SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na následující stránce vyberte možnost "Vytvořit nové reklamy účet", vyhněte se všechna oprávnění, problémy s existujícím účtem.

    ![AD doménové struktury účtu](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Běžné problémy**

* [Problémy s připojením pomocí místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problémy s připojením pomocí online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problémům s oprávněním pomocí místní služby Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Požadavky pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Výběr typu instalace pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Začínáme se službou Azure AD Connect s použitím expresního nastavení](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Vlastní instalace služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Co je pracovní server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co je modul PowerShellu ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Další postup
- [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co je hybridní identitu? ](whatis-hybrid-identity.md).





