---
title: Úvodní příručka – zobrazení aplikací klienta pomocí služby Azure Active Directory
description: V tomto rychlém startu použijte portál Azure k zobrazení aplikací v tenantovi Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74421818"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Úvodní příručka: Zobrazení aplikací klienta Azure Active Directory

V tomto rychlém startu se pomocí webu Azure Portal zobrazí aplikace v tenantovi Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Než začnete

Abyste viděli výsledky, musí váš tenant Azure AD obsahovat alespoň jednu aplikaci. Informace o přidání aplikace najdete v rychlém startu [Přidání aplikace](add-application-portal.md).

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="find-the-list-of-tenant-applications"></a>Vyhledání seznamu aplikací tenanta

Aplikace vašeho tenanta Azure AD můžete zobrazit na webu Azure Portal v části **Podnikové aplikace**.

Vyhledání aplikací tenanta:

1. Na **[webu Azure Portal](https://portal.azure.com)** na levém navigačním panelu vyberte **Azure Active Directory**.
1. V podokně **Služby Azure Active Directory** vyberte **podnikové aplikace**.
1. V rozevírací nabídce **Typ aplikace** vyberte **Všechny aplikace**a zvolte **Použít**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.
1. Chcete-li zobrazit více aplikací, vyberte **Načíst více** v dolní části seznamu. V závislosti na počtu aplikací ve vašem tenantovi může být místo procházení seznamu jednodušší [vyhledat konkrétní aplikaci](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Výběr možností zobrazení

Vyberte možnosti podle toho, co hledáte.

1. Aplikace můžete zobrazit podle **typu aplikace**, **stavu aplikace**a **viditelnosti aplikace**.
1. V části **Typ aplikace** zvolte jednu z těchto možností:

    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.

1. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.
1. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. Možnost **Skryté** zobrazuje aplikace, které jsou v tenantovi, ale nejsou viditelné pro uživatele.
1. Po výběru požadovaných možností vyberte **Použít**.

## <a name="search-for-a-tenant-application"></a>Vyhledání aplikace tenanta

Chcete-li vyhledat konkrétní aplikaci:

1. V nabídce **Typ aplikace** vyberte **Všechny aplikace**a zvolte **Použít**.
1. Zadejte název aplikace, kterou chcete vyhledat. Pokud byla aplikace přidána do vašeho klienta Azure AD, zobrazí se ve výsledcích hledání. Tento příklad ukazuje, že GitHub nebyl přidán do aplikací klienta.

    ![Příklad ukazuje, že aplikace nebyla přidána do tenanta.](media/view-applications-portal/search-for-tenant-application.png)

1. Zkuste zadat několik počátečních písmen názvu aplikace. Tento příklad ukazuje všechny aplikace začínající na **Sales**.

    ![Příklad ukazuje všechny aplikace, které začínají prodejem](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak zobrazit aplikace ve vašem tenantovi Azure AD. Zjistili jste, jak filtrovat seznam aplikací podle typu aplikace, stavu a viditelnosti. Také jste zjistili, jak vyhledat konkrétní aplikaci.

Teď, když jste našli aplikaci, kterou jste hledali, můžete pokračovat v [přidávání dalších aplikací do tenanta](add-application-portal.md). Nebo můžete vybrat aplikaci, která chcete zobrazit nebo upravit vlastnosti a možnosti konfigurace. Například můžete nakonfigurovat jednotné přihlašování.

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-non-gallery-applications.md)
