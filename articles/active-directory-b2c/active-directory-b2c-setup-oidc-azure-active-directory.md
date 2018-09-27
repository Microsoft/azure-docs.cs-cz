---
title: Nastavení přihlášení účtů Azure Active Directory předdefinovaných zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Nastavení přihlášení účtů služby Azure Active Directory zásadu integrované v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 99e9edf0bb31ec0eab96e22e866d9602a26c2693
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47179914"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Nastavení přihlášení účtů Azure Active Directory předdefinovaných zásad v Azure Active Directory B2C

>[!NOTE]
> Tato funkce je ve verzi public preview. Nepoužívejte tuto funkci v produkčním prostředí.

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure Active Directory (Azure AD) pomocí předdefinovaných zásad v Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD.

>[!NOTE]
>`Contoso.com` slouží k organizační tenanta Azure AD a `fabrikamb2c.onmicrosoft.com` slouží jako tenant Azure AD B2C v následujících pokynech.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje organizace Azure AD tenant (contoso.com) kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
4. Vyberte **Registrace nové aplikace**.
5. Zadejte název pro vaši aplikaci. Například, `Azure AD B2C App`.
6. Pro **typ aplikace**vyberte `Web app / API`.
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL malými písmeny, kde `your-tenant` se nahradí názvem vašeho tenanta Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Klikněte na možnost **Vytvořit**. Kopírovat **ID aplikace** pro pozdější použití.
9. Vyberte aplikaci a pak vyberte **nastavení**.
10. Vyberte **klíče**, zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zkopírujte hodnotu klíče, který se zobrazí pro pozdější použití.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurace služby Azure AD jako zprostředkovatele identity ve vašem tenantovi

1. Ujistěte se, že používáte adresáře, který obsahuje organizace Azure AD tenant (contoso.com) kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
4. Zadejte **název**. Zadejte například "Contoso Azure AD".
5. Vyberte **typ zprostředkovatele identit**vyberte **Open ID Connect**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Pro **adresa url metadat**, zadejte následující adresu URL nahrazení `your-tenant` s názvem vašeho tenanta Azure AD. Např. contoso.com:

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```

8. Volitelně můžete zadat hodnotu pro **domény** (třeba `ContosoAD`). Jedná se o hodnotu použít k odkazování na tento zprostředkovatelů identity pomocí *domain_hint* v požadavku. 
9. Klikněte na **OK**.
10. Vyberte **mapování deklarací od zprostředkovatele identity** a nastavte následující deklarace:
    
    - Pro **ID uživatele**, zadejte `oid`.
    - Pro **zobrazovaný název**, zadejte `name`.
    - Pro **křestní jméno**, zadejte `given_name`.
    - Pro **příjmení**, zadejte `family_name`.
    - Pro **e-mailu**, zadejte `unique_name`.

11. Klikněte na tlačítko **OK**a potom **vytvořit** uložte konfiguraci.
