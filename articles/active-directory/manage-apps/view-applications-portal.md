---
title: Zobrazení aplikací tenanta – Azure Active Directory | Microsoft Docs
description: Pomocí webu Azure Portal můžete zobrazit aplikace ve vašem tenantovi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec039beecdcfa6dacd19e5910209c58f13543fce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199880"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Zobrazení aplikací tenanta Azure Active Directory

V tomto rychlém startu se pomocí webu Azure Portal zobrazí aplikace v tenantovi Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Před zahájením

Abyste viděli výsledky, musí váš tenant Azure AD obsahovat alespoň jednu aplikaci. Informace o přidání aplikace najdete v rychlém startu [Přidání aplikace](add-application-portal.md).

Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="find-the-list-of-tenant-applications"></a>Vyhledání seznamu aplikací tenanta

Aplikace vašeho tenanta Azure AD můžete zobrazit na webu Azure Portal v části **Podnikové aplikace**.

Vyhledání aplikací tenanta:

1. Na webu **[Azure Portal](https://portal.azure.com)** klikněte na levém navigačním panelu na **Azure Active Directory**. 

2. V okně Azure Active Directory klikněte na **Podnikové aplikace**. 

3. V rozevírací nabídce **Typ aplikace** vyberte **Všechny aplikace** a klikněte na **Použít**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.

    ![Podnikové aplikace](media/view-applications-portal/open-enterprise-apps.png)
   
4. Pokud chcete zobrazit další aplikace, klikněte na **Zobrazit více** v dolní části seznamu. V závislosti na počtu aplikací ve vašem tenantovi může být místo procházení seznamu jednodušší [vyhledat konkrétní aplikaci](#search-for-a-tenant-application).

## <a name="select-viewing-options"></a>Výběr možností zobrazení

V této části vyberete příslušné možnosti podle toho, co hledáte.

1. K zobrazení aplikací můžete použít možnosti **Typ aplikace**, **Stav aplikace** a **Viditelnost aplikace**. 

    ![Možnosti hledání](media/view-applications-portal/search-options.png)

2. V části **Typ aplikace** zvolte jednu z těchto možností:

    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.

3. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.

4. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. Možnost **Skrytá** zobrazí aplikace, které se nacházejí v tenantovi, ale nejsou viditelné pro uživatele.

5. Jakmile zvolíte požadované možnosti, klikněte na **Použít**.
 

## <a name="search-for-a-tenant-application"></a>Vyhledání aplikace tenanta

Vyhledání konkrétní aplikace:

1. V nabídce **Typ aplikace** vyberte **Všechny aplikace** a klikněte na **Použít**.

2. Zadejte název aplikace, kterou chcete vyhledat. Pokud se aplikace přidala do vašeho tenanta Azure AD, zobrazí se ve výsledcích hledání. Tento příklad ukazuje, že GitHub není mezi přidanými aplikacemi tenanta.

    ![Vyhledání aplikace](media/view-applications-portal/search-for-tenant-application.png)

3. Zkuste zadat několik počátečních písmen názvu aplikace.  Tento příklad ukazuje všechny aplikace začínající na **Sales**.

    ![Hledání s použitím předpony](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se dozvěděli, jak zobrazit aplikace ve vašem tenantovi Azure AD a filtrovat seznam aplikací podle typu, stavu a viditelnosti aplikace. Také jste zjistili, jak vyhledat konkrétní aplikaci.

Teď, když jste našli aplikaci, kterou jste hledali, můžete pokračovat [přidáním dalších aplikací do vašeho tenanta](add-application-portal.md) nebo kliknout na aplikaci a zobrazit nebo upravit vlastnosti a možnosti konfigurace. Například můžete nakonfigurovat jednotné přihlašování. 

> [!div class="nextstepaction"]
> [Konfigurace jednotného přihlašování](configure-single-sign-on-portal.md)


