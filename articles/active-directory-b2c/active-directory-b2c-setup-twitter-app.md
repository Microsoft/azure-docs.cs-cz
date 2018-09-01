---
title: Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Twitteru ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6d8e9245e95c08aad69cd05f338b6260e554469b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337786"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Použití účtu na Twitteru jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet na Twitteru, můžete získat na [ https://twitter.com/signup ](https://twitter.com/signup).

1. Přihlaste se k [Twitteru aplikace](https://apps.twitter.com/) pomocí svých přihlašovacích údajů Twitter.
2. Vyberte **vytvořte novou aplikaci**.
3. Zadejte **název**, **popis**, a **webu**.
4. Zadejte `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp` v **adresy URL zpětného volání**. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c) a **{policyId}** se svým ID zásad (například b2c_1_policy). Měli byste přidat adresu URL zpětného volání pro všechny zásady, které se pomocí účtu sítě Twitter. 
5. Souhlas **smlouva k vývojářským** a vyberte **vytvoření aplikace Twitter**.
7. Vyberte **klíče a přístupové tokeny** kartu.
8. Zkopírujte hodnotu **uživatelský klíč** a **uživatelský tajný klíč**. Je nutné jejich konfigurace účtu na Twitteru jako zprostředkovatele identity ve vašem tenantovi.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Twitter*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Twitter**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte uživatelský klíč pro **ID klienta** a **uživatelský tajný klíč** pro **tajný kód klienta**.
8. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Twitter.