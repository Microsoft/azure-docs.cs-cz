---
title: 'Kurz: poskytnutí přístupu k webovému rozhraní API ASP.NET'
titleSuffix: Azure AD B2C
description: Kurz týkající se použití Active Directory B2C k ochraně webového rozhraní API ASP.NET a jeho volání z webové aplikace v ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ada4226500437e7733605a29988ee7cacae40761
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994302"
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

Proveďte kroky a požadavky v [kurzu: povolení ověřování ve webové aplikaci pomocí Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:44332` .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. Pro **Zahrnout webovou aplikaci nebo webové rozhraní API** vyberte **Ano**.
7. V případě **adresy URL odpovědi** Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:44332` .
8. Pro **identifikátor URI ID aplikace** zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

* * *

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci použijete pro volání webového rozhraní API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete webové rozhraní API tak, aby používalo vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API. Ukázkové webové rozhraní API je součástí projektu, který jste si stáhli v kurzu požadavků.

Ukázkové řešení obsahuje dva projekty:

* **TaskWebApp** – vytvoření a úprava seznamu úkolů V ukázce se k registraci nebo přihlašování uživatelů používá tok uživatelů **registrace nebo přihlašování** .
* **TaskService** – podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Rozhraní API je chráněno pomocí Azure AD B2C a volá ho TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurace webové aplikace

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
1. V projektu **TaskWebApp** otevřete **Web.config**.
1. Pokud chcete rozhraní API spustit místně, jako **api:TaskServiceUrl** použijte nastavení pro localhost. Změňte soubor Web.config následujícím způsobem:

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

1. V projektu **TaskService** otevřete **Web.config**.
1. Nakonfigurujte rozhraní API tak, aby používalo vašeho tenanta.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Nastavte ID klienta na ID aplikace vaší registrované aplikace webového rozhraní API, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Aktualizujte nastavení toku uživatelů pomocí názvu vašeho uživatelského toku pro registraci a přihlašování *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Nakonfigurujte nastavení oborů tak, aby odpovídalo nastavením, které jste vytvořili na portálu.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Je potřeba spustit jak projekt **TaskWebApp**, tak i projekt **TaskService**.

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Nastavit projekty po spuštění**.
1. Vyberte **více projektů po spuštění**.
1. U obou projektů změňte hodnotu **Akce** na **Spustit**.
1. Konfiguraci uložíte kliknutím na **OK** .
1. Stisknutím klávesy **F5** spusťte obě aplikace. Každá aplikace se otevře v samostatném okně prohlížeče.
    * `https://localhost:44316/` je webová aplikace.
    * Na adrese `https://localhost:44332/` je webové rozhraní API.

1. Ve webové aplikaci vyberte možnost **Registrace/přihlášení** a přihlaste se k webové aplikaci. Použijte účet, který jste vytvořili dříve.
1. Po přihlášení vyberte **seznam úkolů** a vytvořte položku seznamu úkolů.

Když vytvoříte položku seznamu úkolů, Webová aplikace odešle požadavek na webové rozhraní API, aby vygeneroval položku seznamu úkolů. Vaše chráněná webová aplikace volá webové rozhraní API chráněné Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidání zprostředkovatelů identit do aplikací v Azure Active Directory B2C](tutorial-add-identity-providers.md)