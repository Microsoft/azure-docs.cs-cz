---
title: Kurz – udělení přístupu k webovému rozhraní API ASP.NET – Azure Active Directory B2C
description: Kurz týkající se použití Active Directory B2C k ochraně webového rozhraní API ASP.NET a jeho volání z webové aplikace v ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 77cd720ffd2763b2ad3d73559a5363989f9e3e3a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679290"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Kurz: poskytnutí přístupu k webovému rozhraní API ASP.NET pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat chráněný prostředek webového rozhraní API v Azure Active Directory B2C (Azure AD B2C) z webové aplikace ASP.NET.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a požadavky v [kurzu: povolení ověřování ve webové aplikaci pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:44332`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Pro vás se vygeneruje úplný identifikátor URI, včetně domény. Například `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. K implementaci řízení přístupu na základě oboru používá webové rozhraní API obory. Například uživatelé webového rozhraní API můžou mít přístup pro čtení i zápis, nebo můžou mít uživatelé webového rozhraní API jenom přístup pro čtení. V tomto kurzu použijete obory k definování oprávnění ke čtení a zápisu pro webové rozhraní API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělit oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci použijete pro volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte *WebApp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete webové rozhraní API tak, aby používalo vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API. Ukázkové webové rozhraní API je součástí projektu, který jste si stáhli v kurzu požadavků.

Ukázkové řešení obsahuje dva projekty:

Následující dva projekty jsou v ukázkovém řešení:

* **TaskWebApp** – vytvoření a úprava seznamu úkolů V ukázce se k registraci nebo přihlašování uživatelů používá tok uživatelů **registrace nebo přihlašování** .
* **TaskService** – podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Rozhraní API je chráněno pomocí Azure AD B2C a volá ho TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurace webové aplikace

1. Otevřete řešení **B2C-WebApi-dotnet** v aplikaci Visual Studio.
1. V projektu **TaskWebApp** otevřete soubor **Web. config**.
1. Pokud chcete rozhraní API spustit lokálně, použijte nastavení localhost pro **rozhraní API: TaskServiceUrl**. Změňte soubor Web. config následujícím způsobem:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Nakonfigurujte identifikátor URI rozhraní API. Toto je identifikátor URI, který webová aplikace používá k vytvoření požadavku rozhraní API. Nakonfigurujte také požadovaná oprávnění.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. V projektu **TaskService** otevřete soubor **Web. config**.
1. Nakonfigurujte rozhraní API tak, aby používalo vašeho tenanta.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Nastavte ID klienta na ID aplikace vaší registrované aplikace webového rozhraní API, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Aktualizujte nastavení toku uživatelů pomocí názvu vašeho uživatelského toku pro registraci a přihlašování, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Nakonfigurujte nastavení oborů tak, aby odpovídalo nastavením, které jste vytvořili na portálu.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Musíte spustit projekty **TaskWebApp** i **TaskService** .

1. V Průzkumník řešení klikněte pravým tlačítkem na řešení a vyberte **nastavit projekty po spuštění...** .
1. Vyberte **více projektů po spuštění**.
1. Změňte **akci** pro oba projekty na **Spustit**.
1. Konfiguraci uložíte kliknutím na **OK** .
1. Stisknutím klávesy **F5** spusťte obě aplikace. Každá aplikace se otevře v samostatném okně prohlížeče.
    * `https://localhost:44316/` je webová aplikace.
    * `https://localhost:44332/` je webové rozhraní API.

1. Ve webové aplikaci vyberte možnost **Registrace/přihlášení** a přihlaste se k webové aplikaci. Použijte účet, který jste vytvořili dříve.
1. Po přihlášení vyberte **seznam úkolů** a vytvořte položku seznamu úkolů.

Když vytvoříte položku seznamu úkolů, Webová aplikace odešle požadavek na webové rozhraní API, aby vygeneroval položku seznamu úkolů. Vaše chráněná webová aplikace volá webové rozhraní API chráněné Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidání zprostředkovatelů identit do aplikací v Azure Active Directory B2C](tutorial-add-identity-providers.md)
