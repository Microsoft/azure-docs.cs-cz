---
title: Nastavení přihlášení pro organizace Azure Active Directory – Azure Active Directory B2C
description: Nastavení přihlášení pro konkrétní organizace Azure Active Directory v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b867a7f9ffeab3d243c8c094830aa0984cffd04a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654202"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizace Azure Active Directory v Azure Active Directory B2C

>[!NOTE]
> Tato funkce je ve verzi Public Preview. Nepoužívejte tuto funkci v produkčním prostředí.

Použití služby Azure Active Directory (Azure AD) jako [zprostředkovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure AD B2C, budete muset vytvořit aplikaci, který ho zastupuje. V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní služby Azure AD organizace podle uživatele tok v Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD, který není stejný jako vašeho tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje váš tenant Azure AD. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje váš tenant Azure AD. Toto není stejného tenanta jako vašeho tenanta Azure AD B2C.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
4. Vyberte **registrace nové**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Přijmout výběr **účty v tomto adresáři organizace jenom** pro tuto aplikaci.
7. Pro **identifikátor URI pro přesměrování**, přijměte hodnotu **webové**a zadejte následující adresu URL malými písmeny, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Všechny adresy URL by měla nyní být pomocí [b2clogin.com](b2clogin.md).

8. Klikněte na tlačítko **zaregistrovat**. Kopírovat **ID aplikace (klient)** pro pozdější použití.
9. Vyberte **certifikáty a tajné kódy** v nabídce aplikace a pak vyberte **nový tajný kód klienta**.
10. Zadejte název pro tajný kód klienta. Například, `Azure AD B2C App Secret`.
11. Vyberte dobu platnosti. Pro tuto aplikaci přijmout výběr **v 1 rok**.
12. Vyberte **přidat** a zkopírujte hodnotu nový tajný kód klienta, který se zobrazí pro pozdější použití.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace služby Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresáře tenanta Azure AD B2C. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
4. Zadejte **název**. Zadejte například `Contoso Azure AD`.
5. Vyberte **typ zprostředkovatele identit**vyberte **Open ID Connect (verze Preview)** a potom klikněte na tlačítko **OK**.
6. Vyberte **nastavit tohoto zprostředkovatele identity**
7. Pro **adresa url metadat**, zadejte následující adresu URL nahrazení `your-AD-tenant-domain` s názvem domény vašeho tenanta Azure AD. Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Pro **ID klienta**, zadejte ID aplikace, které jste si dříve poznamenali a **tajný kód klienta**, zadejte tajný kód klienta, který jste si předtím poznamenali.
9. Volitelně můžete zadat hodnotu pro **Domain_hint**. Například, `ContosoAD`. Jedná se o hodnotu použít k odkazování na tento zprostředkovatelů identity pomocí *domain_hint* v požadavku.
10. Klikněte na **OK**.
11. Vyberte **mapování deklarací od zprostředkovatele identity** a nastavte následující deklarace:

    - Pro **ID uživatele**, zadejte `oid`.
    - Pro **zobrazovaný název**, zadejte `name`.
    - Pro **křestní jméno**, zadejte `given_name`.
    - Pro **příjmení**, zadejte `family_name`.
    - Pro **e-mailu**, zadejte `unique_name`.

12. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci.
