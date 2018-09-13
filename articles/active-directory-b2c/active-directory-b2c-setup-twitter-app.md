---
title: Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Twitteru ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5732293510a75a3c40df5cf3d31978c5ce599791
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720153"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Twitteru pomocí Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter

Použití účtu na Twitteru jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet na Twitteru, můžete získat na [ https://twitter.com/signup ](https://twitter.com/signup).

1. Přihlaste se k [Twitteru aplikace](https://apps.twitter.com/) pomocí svých přihlašovacích údajů Twitter.
2. Vyberte **vytvořit aplikaci**.
3. Zadejte **název aplikace**, **popis aplikace**, a **adresu URL webu**.
4. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy-name/oauth1/authresp` v **adresy URL zpětného volání**. Nahraďte `your-tenant-name` s názvem vašeho tenanta a `your-policy-name` s názvem zásady. Například, `b2c_1_signupsignin`. Budete muset použít jenom malá písmena. při zadávání názvu klienta a název zásady i v případě, že byly definovány pomocí velká písmena v Azure AD B2C.
5. Souhlas **smlouva k vývojářským** a vyberte **vytvořit**.
7. Vyberte **klíče a přístupové tokeny** kartu.
8. Zkopírujte hodnoty **klíč rozhraní API** a **tajný klíč rozhraní API**. Je nutné jejich konfigurace účtu na Twitteru jako zprostředkovatele identity ve vašem tenantovi.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako zprostředkovatele identity ve vašem tenantovi

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.  

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Twitter*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Twitter**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte klíč rozhraní API pro **ID klienta** a tajný klíč rozhraní API pro **tajný kód klienta**.
8. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Twitter.