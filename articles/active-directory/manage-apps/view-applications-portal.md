---
title: Quickstart - View tenant applications using Azure Active Directory
description: In this Quickstart, use the Azure portal to view the applications in your Azure Active Directory (Azure AD) tenant.
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
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: View your Azure Active Directory tenant applications

V tomto rychlém startu se pomocí webu Azure Portal zobrazí aplikace v tenantovi Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Než začnete

Abyste viděli výsledky, musí váš tenant Azure AD obsahovat alespoň jednu aplikaci. Informace o přidání aplikace najdete v rychlém startu [Přidání aplikace](add-application-portal.md).

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="find-the-list-of-tenant-applications"></a>Vyhledání seznamu aplikací tenanta

Aplikace vašeho tenanta Azure AD můžete zobrazit na webu Azure Portal v části **Podnikové aplikace**.

Vyhledání aplikací tenanta:

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and choose **Apply**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.
1. To view more applications, select **Load more** at the bottom of the list. V závislosti na počtu aplikací ve vašem tenantovi může být místo procházení seznamu jednodušší [vyhledat konkrétní aplikaci](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Výběr možností zobrazení

Select options according to what you're looking for.

1. You can view the applications by **Application Type**, **Application Status**, and **Application visibility**.
1. V části **Typ aplikace** zvolte jednu z těchto možností:

    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.

1. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.
1. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. The **Hidden** option shows applications that are in the tenant, but aren't visible to users.
1. After choosing the options you want, select **Apply**.

## <a name="search-for-a-tenant-application"></a>Vyhledání aplikace tenanta

To search for a particular application:

1. In the **Application Type** menu, select **All applications**, and choose **Apply**.
1. Zadejte název aplikace, kterou chcete vyhledat. If the application has been added to your Azure AD tenant, it appears in the search results. This example shows that GitHub hasn't been added to the tenant applications.

    ![Example shows an app hasn't been added to the tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Zkuste zadat několik počátečních písmen názvu aplikace. Tento příklad ukazuje všechny aplikace začínající na **Sales**.

    ![Example shows all apps that start with Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další kroky

In this quickstart, you learned how to view the applications in your Azure AD tenant. You learned how to filter the list of applications by application type, status, and visibility. Také jste zjistili, jak vyhledat konkrétní aplikaci.

Now that you've found the application you were looking for, you can continue to [Add more applications to your tenant](add-application-portal.md). Or, you can select the application to view or edit properties and configuration options. Například můžete nakonfigurovat jednotné přihlašování.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-non-gallery-applications.md)
