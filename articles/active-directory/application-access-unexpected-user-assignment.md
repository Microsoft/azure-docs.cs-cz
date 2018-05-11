---
title: Postup přiřazení uživatelů k aplikacím | Microsoft Docs
description: Pochopit, jak přiřadit uživatele k aplikaci v klientovi
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: eff24ddcb02f4aa4cac28d722e73222ca2e686e4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-assign-users-to-applications"></a>Přiřazení uživatelů k aplikacím

Tento článek vám pomůže lépe porozumět jak přiřadit uživatele k aplikaci v klientovi.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Jak uživatelé přiřazovány aplikace ve službě Azure AD?

Pro uživatele o přístup k aplikaci je třeba nejprve je přiřadit k němu nějakým způsobem. Přiřazení můžete provést pomocí správce, delegáta firmy nebo v některých případech uživatel sami. Níže popisuje způsoby, které uživatelé můžou přiřazovány aplikace:

1.  Správce [přiřadí uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) přímo do aplikace

2.  Správce [přiřadí skupinu](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , uživatel je členem do aplikace, včetně:

  * Skupinu, která byla synchronizované z místní

  * Skupiny zabezpečení statické vytvořeny v cloudu

  * A [skupiny dynamické zabezpečení](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) vytvořeny v cloudu

  * Skupiny Office 365 v cloudu

  * [Všichni uživatelé](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) skupiny

3.  Správce povolí [samoobslužného přístup k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umožňující uživateli přidat aplikace pomocí [Panel přístupu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **přidat aplikaci** funkce **bez obchodní schválení**

4.  Správce povolí [samoobslužného přístup k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) umožňující uživateli přidat aplikace pomocí [Panel přístupu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **přidat aplikaci** funkce, ale pouze w**i-té předchozí schválení od vybraná sada business schvalovatelů**

5.  Správce povolí [Samoobslužná správa skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) uživatele o připojení ke skupině přiřazený aplikace **bez obchodní schválení**

6.  Správce povolí [Samoobslužná správa skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) uživatele o připojení ke skupině, která aplikace je přiřazena k, ale pouze **s předchozí schválení od vybraná sada business schvalovatelů**

7.  Správce přiřadí licence pro uživatele přímo pro první strany aplikace, jako je třeba [Microsoft Office 365](http://products.office.com/)

8.  Správce přiřadí licence pro skupinu, uživatel je členem k první aplikaci strany, jako je třeba [Microsoft Office 365](http://products.office.com/)

9.  [Správce souhlasí k aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) má být používána všichni uživatelé a pak uživatel přihlásí k aplikaci

10. Uživatel [souhlasí k aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) sami po přihlášení k aplikaci

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md)
