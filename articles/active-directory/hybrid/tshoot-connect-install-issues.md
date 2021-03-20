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
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89275857"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Řešení potíží: Azure AD Connect problémy s instalací

## <a name="recommended-steps"></a>**Doporučené kroky**
Zkontrolujte prosím, který [typ instalace Azure AD Connect](./how-to-connect-install-select-installation.md) je vhodný pro vás. Pokud splňujete kritéria pro expresní instalaci, důrazně doporučujeme, abyste přešli na expresní instalaci. Expresní instalace poskytuje minimální možnosti potřebné k dokončení instalace, proto je méně pravděpodobné, že dojde k problémům. 

Pokud ale nesplňujete kritéria Expresní instalace a musíte provést vlastní instalaci, pak tady jsou některé osvědčené postupy, které vám pomůžou se vyhnout běžným problémům. V zájmu jednoduchosti jsou zde uvedeny pouze selektivní možnosti:

* Ujistěte se, že jste správce na počítači, na kterém instalujete AAD Connect. Přihlaste se k počítači se stejnými přihlašovacími údaji správce.

* Pokud chcete použít existující SQL Server, nechte všechny možnosti na následující stránce výchozí, s výjimkou použití existující SQL Server. Tady jsou [Další podrobnosti](./how-to-connect-install-custom.md) o tom, jak používat vlastní možnosti instalace. 

    ![Použít existující SQL Server](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na následující stránce vyberte možnost vytvořit nový účet služby AD, abyste se vyhnuli jakýmkoli problémům s oprávněním existujícímu účtu.

    ![Účet doménové struktury AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Běžné problémy**

* [Problémy s připojením k místní službě Active Directory](./reference-connect-adconnectivitytools.md).

* [Problémy s připojením online Azure Active Directory](./tshoot-connect-connectivity.md).

* [Problémy s oprávněními v místní službě Active Directory](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Doporučené dokumenty**
* [Požadavky pro Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Výběr typu instalace pro Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Začínáme se službou Azure AD Connect s použitím expresního nastavení](./how-to-connect-install-express.md)
* [Vlastní instalace služby Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: co je to přípravný Server?](./plan-connect-topologies.md#staging-server)
* [Co je modul PowerShellu ADConnectivityTools?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Další kroky
- [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md).
- [Co je hybridní identita?](whatis-hybrid-identity.md)