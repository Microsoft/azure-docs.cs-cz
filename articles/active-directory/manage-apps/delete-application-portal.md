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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223903"
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
- Volitelné: dokončení [nastavení jednotného přihlašování](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte nevýrobní prostředí.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Odstranění aplikace z tenanta Azure AD

Odstranění aplikace z tenanta Azure AD:

1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete odstranit. V tomto případě jsme odstranili aplikaci **GitHub_test** , kterou jsme přidali v předchozím rychlém startu.
1. V části **Spravovat** v levém podokně vyberte možnost **vlastnosti**.
1. Vyberte **Odstranit**a potom kliknutím na **Ano** potvrďte, že chcete aplikaci odstranit z tenanta Azure AD.


## <a name="next-steps"></a>Další kroky

- [Osvědčené postupy pro správu aplikací](application-management-fundamentals.md)
- [Běžné scénáře správy aplikací](common-scenarios.md)
- [Viditelnost a řízení správy aplikací](cloud-app-security.md)