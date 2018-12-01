---
title: Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Twitteru ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f0f0b8e0cbb5fbab81a07a28a9d4a2c264be6545
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719857"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete použít Twitteru jako zprostředkovatele identity v Azure AD B2C, budete muset vytvoření aplikace Twitter. Pokud ještě nemáte účet na Twitteru, můžete získat na [ https://twitter.com/signup ](https://twitter.com/signup).

1. Přihlaste se k [Twitteru vývojáři](https://developer.twitter.com/en/apps) pomocí svých přihlašovacích údajů účtu Twitter.
2. Vyberte **vytvořit aplikaci**.
3. Zadejte **název aplikace** a **popis aplikace**.
4. V **adresu URL webu**, zadejte `https://your-tenant.b2clogin.com`. Nahraďte `your-tenant` s názvem vašeho tenanta. Například, https://contosob2c.b2clogin.com.
5. Pro **adresu URL zpětného volání**, zadejte `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Nahraďte `your-tenant` s názvem název tenanta a `your-user-flow-Id` s identifikátorem tok uživatele. Například, `b2c_1A_signup_signin_twitter`. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C.
6. V dolní části stránky, přečtěte si a přijměte podmínky a pak vyberte **vytvořit**.
7. Na **podrobností o aplikaci** stránce **Upravit > Upravit podrobnosti**, zaškrtněte políčko u **povolit Přihlaste se pomocí Twitteru**a pak vyberte **Uložit**.
8. Vyberte **klávesy a tokeny** a zaznamenejte **klíč rozhraní API příjemců** a **tajný klíč rozhraní API pro příjemce** hodnoty pro pozdější použití.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Twitter*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Twitter**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte klíč rozhraní API pro **ID klienta** a tajný klíč rozhraní API pro **tajný kód klienta**.
8. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Twitter.