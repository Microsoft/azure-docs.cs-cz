---
title: Konfigurace služby Twitter pro Azure AD B2C | Microsoft Docs
description: Zadejte registrace a přihlášení pro zákazníky s účty služby Twitter v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Zadejte registrace a přihlášení k příjemce s účty služby Twitter pomocí Azure AD B2C

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud chcete používat služby Twitter jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci služby Twitter a zadat se správné parametry. Účet služby Twitter. k tomu potřebujete. Pokud nemáte, můžete ho na získat [ https://twitter.com/signup ](https://twitter.com/signup).

1. Přejděte na [Twitter aplikace](https://apps.twitter.com/) a přihlaste se pomocí přihlašovacích údajů.
2. Klikněte na tlačítko **vytvořte novou aplikaci**. 
3. Ve formuláři, zadejte hodnotu **název**, **popis**, a **webu**.
4. Pro **adresu URL zpětné volání**, zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Nezapomeňte nahradit **{klient}** s názvem vašeho klienta (například contosob2c.onmicrosoft.com).
5. Zaškrtněte políčko s **vývojáře smlouvy** a klikněte na tlačítko **vytvořit aplikaci služby Twitter**.
6. Po vytvoření aplikace, klikněte na tlačítko **klíče a přístupové tokeny**.
7. Zkopírujte hodnotu **uživatelský klíč** a **uživatelský tajný klíč**. Budete potřebovat oba dva ke konfiguraci služby Twitter jako zprostředkovatel identity ve vašem klientovi.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace služby Twitter jako zprostředkovatele identity ve vašem klientovi
1. Přihlaste se k [portál Azure](https://portal.azure.com/) jako globální správce klienta Azure AD B2C. 
2. Přejděte ke klientovi Azure AD B2C, vyberte adresář Azure AD B2C v pravém horním rohu portálu.
3. Klikněte na tlačítko **všechny služby**a potom vyberte **Azure AD B2C** ze seznamu služeb v rámci **zabezpečení a identita**.
4. Klikněte na tlačítko **zprostředkovatelů Identity**.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Twitter".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Twitter (Preview)** a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte Twitteru **uživatelský klíč** pro **id klienta** a Twitteru **uživatelský tajný klíč** pro **tajný klíč klienta**.
7. Klikněte na tlačítko **OK**a potom klikněte na **vytvořit** uložte konfiguraci služby Twitter.

## <a name="next-steps"></a>Další postup

Vytvořit nebo upravit [předdefinovaných zásad](active-directory-b2c-reference-policies.md) a přidejte Twitter jako zprostředkovatele identity.