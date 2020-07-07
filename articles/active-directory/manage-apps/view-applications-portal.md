---
title: 'Rychlý Start: zobrazení seznamu aplikací, které používají klienta služby Azure Active Directory (Azure AD) pro správu identit'
description: V tomto rychlém startu použijte Azure Portal k zobrazení seznamu aplikací, které jsou zaregistrované pro použití vašeho tenanta Azure Active Directory (Azure AD) pro správu identit.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d04953789dc3fc864dd2809783b0d214a91738f
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983114"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Rychlý Start: zobrazení seznamu aplikací, které používají klienta služby Azure Active Directory (Azure AD) pro správu identit

Začněte používat Azure AD jako systém pro správu identit a autorizace (IAM) pro aplikace, které vaše organizace používá. V tomto rychlém startu zobrazíte aplikace označované také jako aplikace, které už jsou nastavené tak, aby jako zprostředkovatele identity používaly tenanta Azure AD.

## <a name="prerequisites"></a>Požadavky

K zobrazení aplikací, které jsou zaregistrované ve vašem tenantovi Azure AD, potřebujete:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu doporučujeme použít jiné než produkční prostředí.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Vyhledání seznamu aplikací ve vašem tenantovi

Aplikace, které jsou zaregistrované ve vašem tenantovi Azure AD, se můžou zobrazit v Azure Portal v části **podnikové aplikace** .

Zobrazení aplikací zaregistrovaných ve vašem tenantovi:

1. Přihlaste se ke svému [Azure Portal](https://portal.azure.com).
2. V levém navigačním panelu vyberte **Azure Active Directory**.
3. V podokně **Azure Active Directory** vyberte možnost **podnikové aplikace**.
4. Z rozevírací nabídky **Typ aplikace** vyberte **všechny aplikace**a zvolte **použít**. Zobrazí se náhodný vzorek aplikací vašeho tenanta.
5. Pokud chcete zobrazit víc aplikací, vyberte na konci seznamu **načíst další** . Pokud je ve vašem tenantovi mnoho aplikací, může být snazší vyhledat konkrétní aplikaci místo procházení seznamu. Hledání konkrétní aplikace je popsáno dále v tomto rychlém startu.

## <a name="select-viewing-options"></a>Výběr možností zobrazení

Vyberte možnosti podle toho, co hledáte.

1. Můžete zobrazit aplikace podle **typu aplikace**, **stavu aplikace**a **viditelnosti aplikace**.
2. V části **Typ aplikace** zvolte jednu z těchto možností:
    - **Podnikové aplikace:** Zobrazí se aplikace jiných výrobců než Microsoftu.
    - **Aplikace Microsoftu:** Zobrazí se aplikace Microsoftu.
    - **Všechny aplikace:** Zobrazí se aplikace Microsoftu i jiných výrobců.
3. V části **Stav aplikace** zvolte **Jakýkoli**, **Zakázáno** nebo **Povoleno**. Možnost **Jakýkoli** zahrnuje zakázané i povolené aplikace.
4. V části **Viditelnost aplikace** zvolte **Jakákoli** nebo **Skrytá**. Možnost **skryté** zobrazuje aplikace, které jsou v tenantovi, ale nejsou viditelné pro uživatele.
5. Po výběru požadovaných možností vyberte **použít**.

## <a name="search-for-an-application"></a>Vyhledání aplikace

Hledání konkrétní aplikace:

1. V nabídce **Typ aplikace** vyberte možnost **všechny aplikace**a klikněte na tlačítko **použít**.
2. Zadejte název aplikace, kterou chcete vyhledat. Pokud byla aplikace přidána do tenanta služby Azure AD, zobrazí se ve výsledcích hledání. Tento příklad ukazuje, že GitHub nebyl přidán do klientských aplikací.
    ![Příklad ukazuje, že aplikace nebyla přidána do tenanta.](media/view-applications-portal/search-for-tenant-application.png)
3. Zkuste zadat několik počátečních písmen názvu aplikace. Tento příklad ukazuje všechny aplikace začínající na **Sales**.
    ![Příklad zobrazí všechny aplikace, které začínají na prodej.](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak zobrazit aplikace v tenantovi Azure AD. Zjistili jste, jak filtrovat seznam aplikací podle typu aplikace, stavu a viditelnosti. Také jste zjistili, jak vyhledat konkrétní aplikaci.

- [Přidat aplikaci](add-application-portal.md)
- [Konfigurace aplikace](add-application-portal-configure.md)
- [Nastavení jednotného přihlašování](add-application-portal-setup-sso.md)
- [Odstranění aplikace](delete-application-portal.md)
