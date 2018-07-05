---
title: Konfigurace zprostředkovatele identity Githubu v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účtů GitHub ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443337"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: Registrace a poskytněte přihlášení zákazníky prostřednictvím účtů GitHub

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele s účtem GitHub.

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace OAuth Githubu

S githubem jako zprostředkovatele identity v Azure AD B2C, budete muset vytvořit aplikace s OAuth Githubu a zadejte správné parametry.

1. Přejděte [nastavení pro vývojáře Githubu](https://github.com/settings/developers) po přihlášení do Githubu.
1. Klikněte na tlačítko **nové aplikace OAuth**
1. Zadejte **název_aplikace** a **adresa URL domovské stránky**.
1. Pro **adresu URL zpětného volání autorizace**, zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Nahraďte **{klient}** s názvem vašeho tenanta Azure AD B2C (například contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Hodnota pro "tenant" musí být malými písmeny v **přihlašovací adresa URL**.

1. Klikněte na tlačítko **zaregistrovat aplikaci**.
1. Uložit hodnoty **ID klienta** a **tajný kód klienta**. Budete potřebovat v další části.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Konfigurace Githubu jako zprostředkovatele identity ve vašem tenantovi Azure AD B2C

1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
1. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
1. Klikněte na **Přidat** v horní části okna.
1. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Githubu".
1. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Githubu**a klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta a tajný kód klienta aplikace OAuth Githubu, který jste si zkopírovali dříve.
1. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Githubu.

## <a name="next-steps"></a>Další postup

Vytvoření nebo úprava [integrované zásady](active-directory-b2c-reference-policies.md) a přidejte Githubu jako zprostředkovatele identity.