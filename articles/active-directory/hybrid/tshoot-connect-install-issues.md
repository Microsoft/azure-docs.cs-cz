---
title: Řešení potíží s Azure AD Connect instalací | Microsoft Docs
description: V tomto tématu najdete postup řešení potíží při instalaci Azure AD Connect.
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
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211842"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Poradce Potíže s instalací služby Azure AD Connect

## <a name="recommended-steps"></a>**Doporučené kroky**
Zkontrolujte prosím, který [typ instalace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) je vhodný pro vás. Pokud splňujete kritéria pro expresní instalaci, důrazně doporučujeme, abyste přešli na expresní instalaci. Expresní instalace poskytuje minimální možnosti potřebné k dokončení instalace, proto je méně pravděpodobné, že dojde k problémům. 

Pokud ale nesplňujete kritéria Expresní instalace a musíte provést vlastní instalaci, pak tady jsou některé osvědčené postupy, které vám pomůžou se vyhnout běžným problémům. V zájmu jednoduchosti jsou zde uvedeny pouze selektivní možnosti:

* Ujistěte se, že jste správce na počítači, na kterém instalujete AAD Connect. Přihlaste se k počítači se stejnými přihlašovacími údaji správce.

* Pokud chcete použít existující SQL Server, nechte všechny možnosti na následující stránce výchozí, s výjimkou použití existující SQL Server. Tady jsou [Další podrobnosti](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) o tom, jak používat vlastní možnosti instalace. 

    ![Použít existující SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na následující stránce vyberte možnost vytvořit nový účet služby AD, abyste se vyhnuli jakýmkoli problémům s oprávněním existujícímu účtu.

    ![Účet doménové struktury AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Běžné problémy**

* [Problémy s připojením k místní službě Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problémy s připojením online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problémy s oprávněními v místní službě Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Požadavky pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Výběr typu instalace pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Začínáme se službou Azure AD Connect s použitím expresního nastavení](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Vlastní instalace služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Co je pracovní server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Co je modul PowerShellu ADConnectivityTools?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Další postup
- [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md)





