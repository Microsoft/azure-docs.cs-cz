---
title: Konfigurace zprostředkovatele identity v Githubu – Azure AD B2C | Microsoft Docs
description: Zadejte registrace a přihlášení pro zákazníky s účty Githubu v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/06/2017
ms.author: davidmu
ms.openlocfilehash: 17ec4ebd8406f56c431666340ca8834c5ccf9670
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s účty Githubu

> [!NOTE]
> Tato funkce je ve verzi preview.
> 

Tento článek ukazuje, jak povolit přihlášení pro uživatele s účtem Githubu.

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace Githubu OAuth

Pokud chcete použít Githubu jako zprostředkovatele identity v Azure AD B2C, musíte vytvořit aplikaci Githubu OAuth a zadat se správné parametry.

1. Přejděte na [nastaveními vývojáře Githubu](https://github.com/settings/developers) po přihlášení k webu GitHub.
1. Klikněte na tlačítko **novou aplikaci OAuth**
1. Zadejte **název aplikace** a **URL domovské stránky**.
1. Pro **URL zpětného volání autorizace**, zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Nahraďte **{klient}** s názvem klienta Azure AD B2C (například contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Hodnota pro "klienta" musí být psaný malými písmeny v **přihlašovací adresa URL**.

1. Klikněte na tlačítko **registrace aplikace**.
1. Uložit hodnoty **ID klienta** a **tajný klíč klienta**. Budete potřebovat v další části.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Konfigurace Githubu jako zprostředkovatele identity ve vašem klientovi Azure AD B2C

1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
1. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
1. Klikněte na **Přidat** v horní části okna.
1. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Githubu".
1. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Githubu**a klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte ID klienta a tajný klíč klienta OAuth Githubu aplikace, který jste zkopírovali dříve.
1. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci Githubu.

## <a name="next-steps"></a>Další postup

Vytvořit nebo upravit [předdefinovaných zásad](active-directory-b2c-reference-policies.md) a přidejte Githubu jako zprostředkovatele identity.