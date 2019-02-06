---
title: Kurz – přidání zprostředkovatele identity do vašich aplikací – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přidat zprostředkovatelů identity pro vaše aplikace v Azure Active Directory B2C na webu Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.openlocfilehash: 2a1843f941c6abc46928b38a66025fa87c4bcea5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757645"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C

Ve svých aplikacích můžete umožnit uživatelům přihlášení pomocí poskytovatelů jiná identita. *Zprostředkovatele identity* vytváří, udržuje a spravuje informace o identitě současně ověřovací služby do aplikací. Můžete přidat zprostředkovatelů identity, které jsou podporovány službou Azure Active Directory (Azure AD) B2C k vaší [toky uživatelů](active-directory-b2c-reference-policies.md) pomocí webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytváření aplikací zprostředkovatele identity
> * Přidat zprostředkovatele identity do vašeho tenanta
> * Přidat zprostředkovatele identity do vašeho toku uživatele

Obvykle používají pouze jeden poskytovatel identity ve svých aplikacích, ale máte možnost pro přidání dalších. V tomto kurzu se dozvíte, jak přidat do svojí aplikace zprostředkovatele identity Azure AD a zprostředkovatele identity Facebook. Přidání oba z těchto zprostředkovatelů identity pro vaše aplikace je nepovinné. Můžete také přidat jiných zprostředkovatelů identity, jako například [Amazon](active-directory-b2c-setup-amzn-app.md), [Githubu](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), nebo [Twitter](active-directory-b2c-setup-twitter-app.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořit tok uživatele](tutorial-create-user-flows.md) umožňují uživatelům registraci a přihlášení do vaší aplikace. 

## <a name="create-applications"></a>Vytváření aplikací

Aplikace zprostředkovatele identity poskytují identifikátor a klíč k povolení komunikace pomocí vašeho tenanta Azure AD B2C. V této části kurzu vytvoříte aplikaci Azure AD a aplikace Facebook, ze kterého získáte identifikátory a klíče pro přidání poskytovatele identit pro vašeho tenanta. Pokud přidáváte některou ze zprostředkovatelů identity, stačí vytvořit aplikaci pro daného poskytovatele.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pokud chcete povolit přihlášení pro uživatele ze služby Azure AD, budete muset zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako vašeho tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje váš tenant Azure AD kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant Azure AD.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
4. Vyberte **Registrace nové aplikace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Pro **typ aplikace**vyberte `Web app / API`.
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL malými písmeny, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Všechny adresy URL by měla nyní být pomocí [b2clogin.com](b2clogin.md).

8. Klikněte na možnost **Vytvořit**. Kopírovat **ID aplikace** pro pozdější použití.
9. Vyberte aplikaci a pak vyberte **nastavení**.
10. Vyberte **klíče**, zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Takže ho můžete použít později v tomto kurzu, zkopírujte hodnotu klíče.

### <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook

Použití účtu sítě Facebook jako zprostředkovatele identity v Azure AD B2C, budete muset vytvořit aplikaci na Facebooku. Pokud ještě nemáte účet na Facebooku, získáte ji na [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přihlaste se k [Facebook pro vývojáře](https://developers.facebook.com/) pomocí svých přihlašovacích údajů účtu sítě Facebook.
2. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář sítě Facebook. Pokud chcete zaregistrovat, vyberte **zaregistrovat** v pravém horním rohu stránky, přijmout zásady na Facebooku a dokončete registraci.
3. Vyberte **Moje aplikace** a potom klikněte na tlačítko **přidejte novou aplikaci**. 
4. Zadejte **zobrazovaný název** a platný **E-mail kontaktu**.
5. Klikněte na tlačítko **vytvoření ID aplikace**. Může být nutné přijmout zásady platformy Facebooku a proveďte kontrolu zabezpečení online.
6. Vyberte **nastavení** > **základní**.
7. Zvolte **kategorie**, například `Business and Pages`. Tato hodnota je potřeba pomocí Facebooku, ale není možné použít u Azure AD B2C.
8. V dolní části stránky vyberte **přidat platformy**a pak vyberte **webu**.
9. V **adresa URL webu**, zadejte `https://your-tenant-name.b2clogin.com/` nahrazení `your-tenant-name` s názvem vašeho tenanta. Zadejte adresu URL, **adresa URL zásad ochrany osobních údajů**, například `http://www.contoso.com`. Adresa URL zásad je stránka, které spravujete, k poskytnutí informací o ochraně osobních údajů pro vaši aplikaci.
10. Vyberte **uložit změny**.
11. V horní části stránky, zkopírujte hodnotu **ID aplikace**. 
12. Klikněte na tlačítko **zobrazit** a zkopírujte hodnotu **tajný kód aplikace**. Obou z nich použijete ke konfiguraci sítě Facebook jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód aplikace** je důležitým bezpečnostním pověřením.
13. Vyberte **produkty**a pak vyberte **nastavení** pod **přihlášení k Facebooku**.
14. Vyberte **nastavení** pod **přihlášení k Facebooku**.
15. V **identifikátory URI pro přesměrování OAuth platný**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Klikněte na tlačítko **uložit změny** v dolní části stránky.
16. K zajištění dostupnosti sítě Facebook aplikace Azure AD B2C, klikněte na tlačítko **stav** selektoru v horní části stránky a nastavte ho na **na**. Klikněte na **Confirm** (Potvrdit). V tomto okamžiku by měl stav změnit z **vývoj** k **Live**.

## <a name="add-the-identity-providers"></a>Přidat zprostředkovatele identity

Po vytvoření aplikace pro zprostředkovatele identity, které chcete přidat, přidejte zprostředkovatele identity do vašeho tenanta.

### <a name="add-the-azure-active-directory-identity-provider"></a>Přidat zprostředkovatele identity Azure Active Directory

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje vašeho tenanta Azure AD B2C.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
4. Zadejte **název**. Zadejte například *Contoso Azure AD*.
5. Vyberte **typ zprostředkovatele identit**vyberte **Open ID Connect (verze Preview)** a potom klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
7. Pro **adresa url metadat**, zadejte následující adresu URL nahrazení `your-AD-tenant-domain` s názvem domény vašeho tenanta Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Pro **ID klienta**, zadejte ID aplikace, které jste si dříve poznamenali a **tajný kód klienta**, zadejte hodnotu klíče, který jste si předtím poznamenali.
9. Volitelně můžete zadat hodnotu pro **Domain_hint**. Například, `ContosoAD`. 
10. Klikněte na **OK**.
11. Vyberte **mapování deklarací od zprostředkovatele identity** a nastavte následující deklarace:
    
    - Pro **ID uživatele**, zadejte `oid`.
    - Pro **zobrazovaný název**, zadejte `name`.
    - Pro **křestní jméno**, zadejte `given_name`.
    - Pro **příjmení**, zadejte `family_name`.
    - Pro **e-mailu**, zadejte `unique_name`.

12. Klikněte na tlačítko **OK**a potom klikněte na tlačítko **vytvořit** uložte konfiguraci.

### <a name="add-the-facebook-identity-provider"></a>Přidat zprostředkovatele identity Facebook

1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
2. Zadejte **název**. Zadejte například *Facebook*.
3. Vyberte **typ zprostředkovatele identit**vyberte **Facebook**a klikněte na tlačítko **OK**.
4. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta**. Zadejte tajný kód aplikace, které jste si poznamenali jako **tajný kód klienta**.
5. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci sítě Facebook.

## <a name="update-the-user-flow"></a>Aktualizovat tok uživatele.

V tomto kurzu jste dokončili jako součást požadavků vytvořili tok uživatele pro registraci a přihlašování s názvem *B2C_1_signupsignin1*. V této části přidáte zprostředkovatelů identity pro *B2C_1_signupsignin1* tok uživatele.

1. Vyberte **toky uživatelů (zásady)** a pak vyberte *B2C_1_signupsignin1* tok uživatele.
2. Vyberte **zprostředkovatelé Identity**, vyberte **Facebook** a **Contoso Azure AD** zprostředkovatelů identity, které jste přidali.
3. Vyberte **Uložit**.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **spustit tok uživatele**.
2. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na tlačítko **spustit tok uživatele**a potom Přihlaste se pomocí zprostředkovatele identity, který jste přidali dříve.
4. Opakujte kroky 1 až 3 pro ostatní poskytovatele identit, které jste přidali.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytváření aplikací zprostředkovatele identity
> * Přidat zprostředkovatele identity do vašeho tenanta
> * Přidat zprostředkovatele identity do vašeho toku uživatele

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní svých aplikací v Azure Active Directory B2C](tutorial-customize-ui.md)