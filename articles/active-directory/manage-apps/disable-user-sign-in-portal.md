---
title: Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Jak zakázat podniková aplikace tak, aby žádní uživatelé můžou přihlásit k němu ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90000f34ff247fdd5939dc19971c170aa4b70386
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824660"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Zakázat přihlášení uživatele pro podnikové aplikace v Azure Active Directory
Je snadné zakázat podniková aplikace, aby žádní uživatelé můžou přihlásit k němu ve službě Azure Active Directory (Azure AD). Budete potřebovat příslušná oprávnění ke správě podnikové aplikace. A vy musíte být globální správce adresáře.

## <a name="how-do-i-disable-user-sign-ins"></a>Jak zakázat přihlášení uživatelů?
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
1. Vyberte **všechny služby**, zadejte **Azure Active Directory** v textovém poli a pak vyberte **Enter**.
1. Na **Azure Active Directory** -  ***NazevAdresare*** podokno (to znamená Azure AD Directory spravujete), vyberte **podnikové aplikace**.
1. Na **podnikové aplikace – všechny aplikace** podokně se zobrazí seznam aplikací můžete spravovat. Vyberte aplikaci.
1. Na ***appname*** podokno (to znamená, s názvem vybranou aplikaci v názvu), vyberte **vlastnosti**.
1. Na ***appname*** - **vlastnosti** vyberte **ne** pro **mohou se uživatelé přihlásit?**.
1. Vyberte **Uložit** příkazu.

## <a name="next-steps"></a>Další postup
* [Zobrazit všechny moje skupiny](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Změnit název nebo loga podnikové aplikace](change-name-or-logo-portal.md)
