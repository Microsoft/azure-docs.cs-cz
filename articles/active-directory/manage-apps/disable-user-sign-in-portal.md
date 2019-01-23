---
title: Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Jak zakázat podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 4355b1a17f3cf9c8d25b97d63a39cdf8343181b8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476270"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory
Je snadné zakázat podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu ve službě Azure Active Directory (Azure AD). Musí mít příslušná oprávnění ke správě podnikové aplikace a musíte být globální správce adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak zakázat přihlášení uživatelů?
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a pak vyberte **Enter**.
3. Na **Azure Active Directory** -  ***NazevAdresare*** podokno (to znamená Azure AD Directory spravujete), vyberte **podnikové aplikace**.

    ![Otevření podnikové aplikace](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. Na **podnikové aplikace** vyberte **všechny aplikace**. Zobrazí seznam aplikací, které můžete spravovat.
5. Na **podnikové aplikace – všechny aplikace** podokně, vyberte aplikaci.
6. Na ***appname*** podokno (to znamená, s názvem vybranou aplikaci v názvu), vyberte **vlastnosti**.

    ![Výběrem příkazu všechny aplikace](./media/disable-user-sign-in-portal/select-app.png)
7. Na ***appname*** - **vlastnosti** vyberte **ne** pro **mohou se uživatelé přihlásit?**.
8. Vyberte **Uložit** příkazu.

## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)
