---
title: 'Rychlý Start: odstranění aplikace z klienta služby Azure Active Directory (Azure AD)'
description: Tento rychlý Start používá Azure Portal k odstranění aplikace z klienta služby Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 659d136695943d846fe57986d4b64a37f0d8f30e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89300115"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Rychlý Start: odstranění aplikace z klienta služby Azure Active Directory (Azure AD)

V tomto rychlém startu se pomocí Azure Portal odstraní aplikace, která se přidala do tenanta Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete odstranit aplikaci z tenanta Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md)
- Volitelné: dokončení [Přidání aplikace](add-application-portal.md)
- Volitelné: dokončení [Konfigurace aplikace](add-application-portal-configure.md)
- Volitelné: dokončování [přiřazování uživatelů k aplikaci](add-application-portal-assign-users.md).
- Volitelné: dokončení [nastavení jednotného přihlašování](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte neprodukční prostředí.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Odstranění aplikace z tenanta Azure AD

Odstranění aplikace z tenanta Azure AD:

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete odstranit. V tomto případě jsme odstranili aplikaci **GitHub_test** , kterou jsme přidali v předchozím rychlém startu.
1. V části **Spravovat** v levém podokně vyberte možnost **vlastnosti**.
1. Vyberte **Odstranit**a potom kliknutím na **Ano** potvrďte, že chcete aplikaci odstranit z tenanta Azure AD.

> [!TIP]
> Správu aplikací můžete automatizovat pomocí Graph API, přečtěte si téma [Automatizace správy aplikací pomocí rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s touto řadou rychlý Start hotovi, zvažte odstranění aplikace a vyčištění svého testovacího tenanta. Odstranění aplikace se pokrylo v tomto rychlém startu.

## <a name="next-steps"></a>Další kroky

Dokončili jste řadu rychlý Start. V dalším kroku si přečtěte informace o osvědčených postupech při správě aplikací.
> [!div class="nextstepaction"]
> [Osvědčené postupy pro správu aplikací](application-management-fundamentals.md)
