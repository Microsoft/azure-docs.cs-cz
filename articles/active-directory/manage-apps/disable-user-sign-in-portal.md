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
ms.openlocfilehash: 86ff34bb0780811663d437a9183d0b688b69073d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303305"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory
Je snadné zakázat podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu v Azure Active Directory (Azure AD). Musí mít příslušná oprávnění ke správě firemní aplikace a musí být globální správce adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Jakým způsobem vypnout uživatelská přihlášení?
1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2. Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a potom vyberte **Enter**.
3. Na **Azure Active Directory** -  ***directoryname*** podokno (který je Azure AD pro adresář spravujete), vyberte **podnikové aplikace, které**.

    ![Otevírání podnikové aplikace](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace, které** podokně, vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** podokně, vyberte aplikaci.
6. Na ***appname*** (to znamená, v podokně s názvem vybranou aplikaci v názvu), vyberte **vlastnosti**.

    ![Výběr příkaz všechny aplikace](./media/disable-user-sign-in-portal/select-app.png)
7. Na ***appname*** - **vlastnosti** podokně, vyberte **ne** pro **povolit pro uživatele přihlásit?**.
8. Vyberte **Uložit** příkaz.

## <a name="next-steps"></a>Další postup
* [Zobrazení všech Moje skupin](../active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny do aplikace enterprise](assign-user-or-group-access-portal.md)
* [Odebrat uživatele nebo skupinu přiřazení z podnikové aplikace.](remove-user-or-group-access-portal.md)
* [Změna názvu nebo logo aplikace enterprise](change-name-or-logo-portal.md)
