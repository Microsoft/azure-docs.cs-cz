---
title: Postup přiřazení uživatelů k aplikacím | Dokumentace Microsoftu
description: Vysvětlení, jak přiřadit uživatele k aplikaci ve vašem tenantovi
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.openlocfilehash: 84e341b4ec63bd0cd038e1f010e4f09c7ccceffd
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961461"
---
# <a name="how-to-assign-users-to-applications"></a>Přiřazení uživatelů k aplikacím

Tento článek pomůže pochopit, jak přiřadit uživatele k aplikaci ve vašem tenantovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak uživatelé získat přidružených k aplikaci ve službě Azure AD?

Pro uživatele pro přístup k aplikaci je třeba nejprve je přiřadit k ní nějakým způsobem. Přiřazení může provést správce, obchodní delegáta nebo v některých případech uživatel sami. Níže popisuje způsoby, které uživatelé mohou přiřadit k aplikacím:

1.  Správce [přiřadí uživateli](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) přímo do aplikace

2.  Správce [přiřadí skupinu](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , že uživatel je členem skupiny k aplikaci, včetně:

    * Skupiny, které se synchronizovaly z místní

    * Skupiny zabezpečení statické se vytvářejí v cloudu

    * A [dynamické zabezpečení skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) se vytvářejí v cloudu

    * Skupiny Office 365 se vytvářejí v cloudu

    * [Všichni uživatelé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) skupiny

3.  Správce povolí [samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umožňující uživateli přidat aplikace pomocí [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **přidat aplikaci** funkce **bez schválení**

4.  Správce povolí [samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umožňující uživateli přidat aplikace pomocí [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **přidat aplikaci** funkce, ale pouze w**i-tý předchozí schválení od vybraná sada firemními schvalovateli**

5.  Správce povolí [Samoobslužná správa skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) umožňující uživateli připojení ke skupině, která je přiřazena aplikace **bez schválení**

6.  Správce povolí [Samoobslužná správa skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) umožňující uživateli připojení ke skupině přiřazené aplikaci položky, ale pouze **s předchozí schválení od vybraná sada firemními schvalovateli**

7.  Správce přiřadí licenci pro uživatele přímo v aplikaci první strany, jako je třeba [Microsoft Office 365](https://products.office.com/)

8.  Správce přiřadí licenci ke skupině, že uživatel je členem k první aplikaci stran, jako je třeba [Microsoft Office 365](https://products.office.com/)

9.  [Správce vyjádří souhlas aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) využívat všichni uživatelé a pak uživatel přihlásí k aplikaci

10. Uživatel [vyjádří souhlas aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) sami přihlášením k aplikaci

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
