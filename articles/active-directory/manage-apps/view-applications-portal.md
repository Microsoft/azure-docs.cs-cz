---
title: Rychlý Start – zobrazení klientských aplikací pomocí Azure Active Directory
description: V tomto rychlém startu použijte Azure Portal k zobrazení aplikací v tenantovi Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421818"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Rychlý Start: zobrazení klientských aplikací Azure Active Directory

V tomto rychlém startu se pomocí webu Azure Portal zobrazí aplikace v tenantovi Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Před zahájením

Abyste viděli výsledky, musí váš tenant Azure AD obsahovat alespoň jednu aplikaci. Informace o přidání aplikace najdete v rychlém startu [Přidání aplikace](add-application-portal.md).

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="find-the-list-of-tenant-applications"></a>Vyhledání seznamu aplikací tenanta

Aplikace vašeho tenanta Azure AD můžete zobrazit na webu Azure Portal v části **Podnikové aplikace**.

Vyhledání aplikací tenanta:

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**.
1. Z rozevírací nabídky **Typ aplikace** vyberte **všechny aplikace**a zvolte **použít**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.
1. Pokud chcete zobrazit víc aplikací, vyberte na konci seznamu **načíst další** . V závislosti na počtu aplikací ve vašem tenantovi může být místo procházení seznamu jednodušší [vyhledat konkrétní aplikaci](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Výběr možností zobrazení

Vyberte možnosti podle toho, co hledáte.

1. Můžete zobrazit aplikace podle **typu aplikace**, **stavu aplikace**a **viditelnosti aplikace**.
1. V části **Typ aplikace** zvolte jednu z těchto možností:

    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.

1. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.
1. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. Možnost **skryté** zobrazuje aplikace, které jsou v tenantovi, ale nejsou viditelné pro uživatele.
1. Po výběru požadovaných možností vyberte **použít**.

## <a name="search-for-a-tenant-application"></a>Vyhledání aplikace tenanta

Hledání konkrétní aplikace:

1. V nabídce **Typ aplikace** vyberte možnost **všechny aplikace**a klikněte na tlačítko **použít**.
1. Zadejte název aplikace, kterou chcete vyhledat. Pokud byla aplikace přidána do tenanta služby Azure AD, zobrazí se ve výsledcích hledání. Tento příklad ukazuje, že GitHub nebyl přidán do klientských aplikací.

    ![Příklad ukazuje, že aplikace nebyla přidána do tenanta.](media/view-applications-portal/search-for-tenant-application.png)

1. Zkuste zadat několik počátečních písmen názvu aplikace. Tento příklad ukazuje všechny aplikace začínající na **Sales**.

    ![Příklad zobrazí všechny aplikace, které začínají na prodej.](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak zobrazit aplikace v tenantovi Azure AD. Zjistili jste, jak filtrovat seznam aplikací podle typu aplikace, stavu a viditelnosti. Také jste zjistili, jak vyhledat konkrétní aplikaci.

Teď, když jste našli aplikaci, kterou jste hledali, můžete pokračovat ve [přidávání dalších aplikací do svého tenanta](add-application-portal.md). Nebo můžete vybrat aplikaci pro zobrazení nebo úpravu vlastností a možností konfigurace. Například můžete nakonfigurovat jednotné přihlašování.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-non-gallery-applications.md)
