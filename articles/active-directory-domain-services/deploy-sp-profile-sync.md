---
title: 'Azure Active Directory Domain Services: Povolit službu profilů uživatelů služby SharePoint | Microsoft Docs'
description: Konfigurace Azure Active Directory Domain Services spravovaných domén pro podporu synchronizace profilů pro SharePointový Server
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: iainfou
ms.openlocfilehash: 4a9ee05b37a69927d70269dccef2b74a2c251722
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234101"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Konfigurace spravované domény pro podporu synchronizace profilů pro SharePoint Server
SharePoint Server obsahuje službu profilů uživatelů, která se používá pro synchronizaci profilů uživatelů. Chcete-li nastavit službu profilů uživatelů, je nutné udělit příslušná oprávnění v doméně služby Active Directory. Další informace najdete v tématu [udělení oprávnění Active Directory Domain Services pro synchronizaci profilů v sharepointovém serveru 2013](https://technet.microsoft.com/library/hh296982.aspx).

Tento článek vysvětluje, jak můžete nakonfigurovat Azure AD Domain Services spravované domény pro nasazení služby synchronizace profilů uživatelů serveru SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>Skupina účty služby AAD DC
Skupina zabezpečení s názvem**účty služby AAD DC**je k dispozici v rámci organizační jednotky uživatelů ve spravované doméně. Tuto skupinu můžete zobrazit ve spravované doméně v modulu snap-in **Uživatelé služby Active Directory a počítače** MMC.

![Skupina zabezpečení účty služby AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Členové této skupiny zabezpečení mají delegovaná tato oprávnění:
- Oprávnění replikovat změny adresáře u kořenového DSE spravované domény.
- Oprávnění replikovat změny adresáře pro názvový kontext konfigurace (CN = Container Configuration) spravované domény.

Tato skupina zabezpečení je zároveň členem předdefinované skupiny **Pre-Windows 2000 Compatible Access**.

![Skupina zabezpečení účty služby AAD DC](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Povolení, aby vaše spravovaná doména podporovala synchronizaci profilů uživatelů serveru SharePoint
Účet služby, který se používá pro synchronizaci profilů uživatelů služby SharePoint, můžete přidat do skupiny **účty služby AAD DC** . V důsledku toho účet synchronizace získá odpovídající oprávnění k replikaci změn do adresáře. Tento krok konfigurace umožňuje správné fungování synchronizace profilů uživatelů serveru SharePoint.

![Účty služby AAD DC – přidání členů](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Účty služby AAD DC – přidání členů](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Související obsah
* [Technické reference – udělení oprávnění Active Directory Domain Services pro synchronizaci profilů v SharePointovém serveru 2013](https://technet.microsoft.com/library/hh296982.aspx)
