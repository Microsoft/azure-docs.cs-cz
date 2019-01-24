---
title: Nastavení přihlášení pro organizace Azure Active Directory v Azure Active Directory B2C | Dokumentace Microsoftu
description: Nastavení přihlášení pro konkrétní organizace Azure Active Directory v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 06630d93925875a2c9a492d018f5dd211f437430
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846485"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizace Azure Active Directory v Azure Active Directory B2C

>[!NOTE]
> Tato funkce je ve verzi Public Preview. Nepoužívejte tuto funkci v produkčním prostředí.

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure Active Directory (Azure AD) pomocí tok uživatele v Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD, který není stejný jako vašeho tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje klikněte na filtr adresářů a předplatných v horní nabídce vyberte adresář, který obsahuje váš tenant Azure AD a vašeho tenanta Azure AD.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
4. Vyberte **Registrace nové aplikace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Pro **typ aplikace**vyberte `Web app / API`.
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL malými písmeny, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Všechny adresy URL by měla nyní být pomocí [b2clogin.com](b2clogin.md).

8. Klikněte na možnost **Vytvořit**. Kopírovat **ID aplikace** pro pozdější použití.
9. Vyberte aplikaci a pak vyberte **nastavení**.
10. Vyberte **klíče**, zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zkopírujte hodnotu klíče, který se zobrazí pro pozdější použití.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace služby Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresáře tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje vašeho tenanta Azure AD B2C.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
4. Zadejte **název**. Zadejte například "Contoso Azure AD".
5. Vyberte **typ zprostředkovatele identit**vyberte **Open ID Connect (verze Preview)** a potom klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Pro **adresa url metadat**, zadejte následující adresu URL nahrazení `your-AD-tenant-domain` s názvem domény vašeho tenanta Azure AD. Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Pro **id klienta**, zadejte ID aplikace, které jste si dříve poznamenali a **tajný kód klienta**, zadejte hodnotu klíče, který jste si předtím poznamenali.
9. Volitelně můžete zadat hodnotu pro **Domain_hint**. Například, `ContosoAD`. Jedná se o hodnotu použít k odkazování na tento zprostředkovatelů identity pomocí *domain_hint* v požadavku. 
10. Klikněte na **OK**.
11. Vyberte **mapování deklarací od zprostředkovatele identity** a nastavte následující deklarace:
    
    - Pro **ID uživatele**, zadejte `oid`.
    - Pro **zobrazovaný název**, zadejte `name`.
    - Pro **křestní jméno**, zadejte `given_name`.
    - Pro **příjmení**, zadejte `family_name`.
    - Pro **e-mailu**, zadejte `unique_name`.

12. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci.
