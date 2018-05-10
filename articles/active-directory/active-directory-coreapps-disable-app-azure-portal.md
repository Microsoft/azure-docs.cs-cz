---
title: Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory | Microsoft Docs
description: Zakázání podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu v Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: be0c8ede4a99c532aecbfbb4ee0fc51657f96886
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory
Je snadné zakázat podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu v Azure Active Directory (Azure AD). Musí mít příslušná oprávnění ke správě firemní aplikace a musí být globální správce adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Jakým způsobem vypnout uživatelská přihlášení?
1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2. Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a potom vyberte **Enter**.
3. Na **Azure Active Directory** -  ***directoryname*** podokno (který je Azure AD pro adresář spravujete), vyberte **podnikové aplikace, které**.

    ![Otevírání podnikové aplikace](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace, které** podokně, vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** podokně, vyberte aplikaci.
6. Na ***appname*** (to znamená, v podokně s názvem vybranou aplikaci v názvu), vyberte **vlastnosti**.

    ![Výběr příkaz všechny aplikace](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Na ***appname*** - **vlastnosti** podokně, vyberte **ne** pro **povolit pro uživatele přihlásit?**.
8. Vyberte **Uložit** příkaz.

## <a name="next-steps"></a>Další postup
* [Zobrazení všech Moje skupin](active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny do aplikace enterprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace.](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Změna názvu nebo logo aplikace enterprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
