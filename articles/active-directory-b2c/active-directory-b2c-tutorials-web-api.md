---
title: Kurz – poskytnutí přístupu k webovému rozhraní API ASP.NET – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API ASP.NET a jeho volání z webové aplikace ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cc4db0f2fe8f5db41f6e8332a398029bd105f3af
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756338"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní ASP.NET API pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat chráněné webové rozhraní API prostředku v Azure Active Directory (Azure AD) B2C z webové aplikace ASP.NET.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Konfigurace ukázky aplikace k používání aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Dokončete kroky a požadavky [kurzu: Povolit ověření ve webové aplikaci pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Přidání webového rozhraní API aplikaci

Webové rozhraní API prostředky musí být zaregistrovaní ve vašem tenantovi předtím, než může přijímat a reagovat na požadavky na chráněný prostředek v klientských aplikacích, které se přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
7. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:44332`.
8. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

1. Vyberte **aplikací**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **oboru**, zadejte `Hello.Read`a popis, zadejte `Read access to hello`.
4. Pro **oboru**, zadejte `Hello.Write`a popis, zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory je možné klientovi udělit oprávnění k aplikaci pro webové rozhraní API.

## <a name="grant-permissions"></a>Udělení oprávnění

Pokud chcete volat chráněné webové rozhraní API z aplikace, budete muset udělit oprávnění aplikace rozhraní API. V požadovaném kurzu vytvořili webovou aplikaci v Azure AD B2C s názvem *webapp1*. Volání webového rozhraní API pomocí této aplikace.

1. Vyberte **aplikací**a pak vyberte *webapp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **přidat**.
3. V **vybrat rozhraní API** rozevíracím seznamu vyberte *webapi1*.
4. V **vyberte obory** rozevírací seznam, vyberte **Hello.Read** a **Hello.Write** obory, které jste dříve definovali.
5. Klikněte na **OK**.

Vaše aplikace je zaregistrovaná a může volat chráněné webové rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C k používání aplikace. Aplikace obdrží udělení autorizace z Azure AD B2C pro přístup k chráněné webové rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

Teď, když je webové rozhraní API zaregistrované a máte definované obory, nakonfigurujte webové rozhraní API pro použití vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API. Ukázkové webové rozhraní API je zahrnutý v projektu, který jste stáhli v požadovaném kurzu.

Ukázkové řešení obsahuje dva projekty:

V ukázkovém řešení jsou tyto dva projekty:

- **TaskWebApp** – vytvoření a úpravy seznamu úkolů. Ukázka používá **registrace nebo přihlašování** tok uživatele k registraci nebo přihlašování uživatelů.
- **TaskService** – podporuje vytvoření, čtení, aktualizaci a odstranění seznamu úkolů. Rozhraní API je chráněné službou Azure AD B2C a volané TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurace webové aplikace

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
2. Otevřete soubor **Web.config** v projektu **TaskWebApp**.
3. Pokud chcete rozhraní API spustit místně, jako **api:TaskServiceUrl** použijte nastavení pro localhost. Změňte soubor Web.config následujícím způsobem: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Nakonfigurujte identifikátor URI rozhraní API. Toto je identifikátor URI, webová aplikace používá k odeslání požadavku rozhraní API. Nakonfigurujte také požadovaná oprávnění.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor **Web.config** v projektu **TaskService**.
2. Nakonfigurujte rozhraní API tak, aby používalo vašeho tenanta.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Nastavte ID klienta na ID aplikace zaregistrované pro vaše rozhraní API.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Aktualizace uživatele tok nastavení s názvem znaménko nahoru a přihlašování uživatelů toku.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Nakonfigurujte nastavení oborů tak, aby odpovídaly oborům, které jste vytvořili na portálu.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Je potřeba spustit jak projekt **TaskWebApp**, tak i projekt **TaskService**. 

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Nastavit projekty po spuštění**. 
2. Vyberte **více projektů po spuštění**.
3. U obou projektů změňte hodnotu **Akce** na **Spustit**.
4. Nastavení uložíte kliknutím na **OK**.
5. Stisknutím klávesy **F5** spusťte obě aplikace. Obě aplikace se otevřou na vlastní kartě prohlížeče `https://localhost:44316/` je webová aplikace.
    Na adrese `https://localhost:44332/` je webové rozhraní API.

6. Ve webové aplikaci, klikněte na tlačítko **registrace / přihlášení** pro přihlášení k webové aplikaci. Použijte účet, který jste předtím vytvořili. 
7. Jakmile se přihlásíte, klikněte na tlačítko **seznam úkolů** a vytvořte položku seznamu úkolů.

Když vytvoříte položku seznamu úkolů, webová aplikace odešle požadavek do webového rozhraní API pro generování položky seznamu úkolů. Vaše chráněná webová aplikace je volat chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Konfigurace ukázky aplikace k používání aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C](tutorial-add-identity-providers.md)
