---
title: 'Kurz: Udělení přístupu k webovému rozhraní API ASP.NET'
titleSuffix: Azure AD B2C
description: Návod, jak používat službu Active Directory B2C k ochraně webového rozhraní API pro ASP.NET a volat jej z webové aplikace ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 23531bd4c53dc2fc4851a1e4718fca0e9c3bfc1c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78187419"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní API ASP.NET pomocí služby Azure Active Directory B2C

Tento kurz ukazuje, jak volat prostředek chráněného webového rozhraní API ve službě Azure Active Directory B2C (Azure AD B2C) z webové aplikace ASP.NET.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

V kurzu proveďte kroky a [předpoklady: Povolte ověření ve webové aplikaci pomocí služby Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být registrovány ve vašem tenantovi, než budou moci přijímat a reagovat na požadavky chráněných prostředků klientskými aplikacemi, které představují přístupový token.

Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **Aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V **možnosti Zahrnout webové aplikace/ webové rozhraní API**vyberte **Ano**.
7. Pro **adresu URL odpovědi**zadejte koncový bod, kde azure AD B2C by měl vrátit všechny tokeny, které vaše aplikace požaduje. V tomto kurzu ukázka spustí místně `https://localhost:44332`a naslouchá na .
8. Do **identifikátoru URI ID aplikace**zadejte identifikátor použitý pro webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **Vytvořit**.
10. Na stránce vlastností zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **Přesměrování identifikátoru URI**vyberte **web**a zadejte koncový bod, ve kterém by měl Azure AD B2C vrátit všechny tokeny, které vaše aplikace požaduje. V tomto kurzu ukázka spustí místně `https://localhost:44332`a naslouchá na .
1. Vyberte **Zaregistrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

* * *

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte udělit oprávnění aplikace k rozhraní API. V kurzu předpokladu jste vytvořili webovou aplikaci v Azure AD B2C s názvem *webapp1*. Tato aplikace slouží k volání webového rozhraní API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Vaše aplikace je registrována pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C k použití aplikace. Aplikace získá udělení autorizace od Azure AD B2C pro přístup k chráněné webové rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Teď, když je webové rozhraní API registrované a máte definované obory, nakonfigurujete webové rozhraní API tak, aby používalo vašeho klienta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API. Ukázkové webové rozhraní API je součástí projektu, který jste stáhli v kurzu předpokladu.

Ukázkové řešení obsahuje dva projekty:

* **TaskWebApp** - Vytvoření a úprava seznamu úkolů. Ukázka používá **tok registrace nebo přihlášení** uživatele k registraci nebo přihlášení uživatelů.
* **TaskService** - Podporuje vytvoření, čtení, aktualizaci a odstranění funkce seznamu úkolů. Rozhraní API je chráněné Azure AD B2C a voláno TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurace webové aplikace

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
1. V projektu **TaskWebApp** otevřete **soubor Web.config**.
1. Pokud chcete rozhraní API spustit místně, jako **api:TaskServiceUrl** použijte nastavení pro localhost. Změňte soubor Web.config následujícím způsobem:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Nakonfigurujte identifikátor URI rozhraní API. Toto je identifikátor URI, který webová aplikace používá k provedení požadavku rozhraní API. Nakonfigurujte také požadovaná oprávnění.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. V projektu **TaskService** otevřete **soubor Web.config**.
1. Nakonfigurujte rozhraní API tak, aby používalo vašeho tenanta.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Nastavte ID klienta na ID aplikace registrované webové aplikace API *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Aktualizujte nastavení toku uživatele názvem toku uživatele registrace a *přihlášení, B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Nakonfigurujte nastavení oborů tak, aby odpovídalo nastavení, které jste vytvořili na portálu.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Je potřeba spustit jak projekt **TaskWebApp**, tak i projekt **TaskService**.

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Nastavit projekty po spuštění**.
1. Vyberte **více projektů při spuštění**.
1. U obou projektů změňte hodnotu **Akce** na **Spustit**.
1. Chcete-li uložit konfiguraci, klepněte na tlačítko **OK.**
1. Stisknutím klávesy **F5** spusťte obě aplikace. Každá aplikace se otevře ve vlastním okně prohlížeče.
    * `https://localhost:44316/`je webová aplikace.
    * Na adrese `https://localhost:44332/` je webové rozhraní API.

1. Ve webové aplikaci vyberte **registraci / přihlášení** a přihlaste se k webové aplikaci. Použijte účet, který jste dříve vytvořili.
1. Po přihlášení vyberte **seznam úkolů** a vytvořte položku seznamu úkolů.

Když vytvoříte položku seznamu úkolů, webová aplikace podá požadavek webového rozhraní API na generování položky seznamu úkolů. Chráněná webová aplikace volá webové rozhraní API chráněné Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidání poskytovatelů identit do aplikací ve službě Azure Active Directory B2C](tutorial-add-identity-providers.md)
