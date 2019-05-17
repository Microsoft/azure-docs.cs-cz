---
title: Zobrazení aplikací tenanta – Azure Active Directory | Microsoft Docs
description: Pomocí webu Azure Portal můžete zobrazit aplikace ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 537916ef14a7cd2748ad453e2d0d91e3b5c936b1
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826052"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Zobrazení aplikací tenanta Azure Active Directory

V tomto rychlém startu se pomocí webu Azure Portal zobrazí aplikace v tenantovi Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Než začnete

Abyste viděli výsledky, musí váš tenant Azure AD obsahovat alespoň jednu aplikaci. Informace o přidání aplikace najdete v rychlém startu [Přidání aplikace](add-application-portal.md).

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="find-the-list-of-tenant-applications"></a>Vyhledání seznamu aplikací tenanta

Aplikace vašeho tenanta Azure AD můžete zobrazit na webu Azure Portal v části **Podnikové aplikace**.

Vyhledání aplikací tenanta:

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu vyberte **Azure Active Directory**. 

1. V **Azure Active Directory** vyberte **podnikové aplikace**. 

1. Z **typ aplikace** rozevírací nabídky vyberte **všechny aplikace**a zvolte **použít**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.
   
1. Pokud chcete zobrazit další aplikace, vyberte **načíst další** v dolní části seznamu. V závislosti na počtu aplikací ve vašem tenantovi může být místo procházení seznamu jednodušší [vyhledat konkrétní aplikaci](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Výběr možností zobrazení

Vyberte možnosti podle co hledáte.

1. Můžete zobrazit aplikace podle **typ aplikace**, **stav aplikace**, a **viditelnost aplikace**. 

1. V části **Typ aplikace** zvolte jednu z těchto možností:

    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.

1. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.

1. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. **Skryté** možnost zobrazí aplikace, které jsou v tenantovi, ale nejsou viditelné pro uživatele.

1. Po zvolení požadované možnosti, vyberte **použít**.
 

## <a name="search-for-a-tenant-application"></a>Vyhledání aplikace tenanta

K vyhledání konkrétní aplikaci:

1. V **typ aplikace** nabídce vyberte možnost **všechny aplikace**a zvolte **použít**.

1. Zadejte název aplikace, kterou chcete vyhledat. Pokud aplikace se přidala do svého tenanta Azure AD, zobrazí se ve výsledcích hledání. Tento příklad ukazuje, že GitHub nebyl přidán do aplikací tenanta.

    ![Vyhledání aplikace](media/view-applications-portal/search-for-tenant-application.png)

1. Zkuste zadat několik počátečních písmen názvu aplikace. Tento příklad ukazuje všechny aplikace začínající na **Sales**.

    ![Hledání s použitím předpony](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak zobrazit aplikace ve vašem tenantovi Azure AD. Jste zjistili, jak filtrovat seznam aplikací podle typu aplikace, stavu a viditelnost. Také jste zjistili, jak vyhledat konkrétní aplikaci.

Teď, když jste nalezené aplikace vám byly hledáte, můžete nadále [přidat další aplikace do svého tenanta](add-application-portal.md). Nebo můžete vybrat aplikace umožňuje zobrazit nebo upravit vlastnosti a možnosti konfigurace. Například můžete nakonfigurovat jednotné přihlašování. 

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-portal.md)


