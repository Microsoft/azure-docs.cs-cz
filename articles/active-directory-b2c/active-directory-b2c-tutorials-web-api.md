---
title: Kurz – udělení přístupu k webovému rozhraní API ASP.NET – Azure Active Directory B2C | Microsoft Docs
description: Kurz týkající se použití Active Directory B2C k ochraně webového rozhraní API ASP.NET a jeho volání z webové aplikace v ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ec6b667dfc554c037d9b0a56e52bc8f212812812
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064730"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní API ASP.NET pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat chráněný prostředek webového rozhraní API v Azure Active Directory B2C (Azure AD B2C) z webové aplikace ASP.NET.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a požadavky v [kurzu: Povolte ověřování ve webové aplikaci pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:44332`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

1. Vyberte **aplikace**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **Rozsah**zadejte `Hello.Read`, a pro Popis zadejte `Read access to hello`.
4. Pro **Rozsah**zadejte `Hello.Write`, a pro Popis zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory lze použít k udělení oprávnění klientské aplikace webovému rozhraní API.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci použijete pro volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte *WebApp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
3. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
4. V rozevíracím seznamu **Vybrat obory** vyberte obory **Hello. Read** a **Hello. Write** , které jste předtím definovali.
5. Klikněte na **OK**.

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete webové rozhraní API tak, aby používalo vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkové webové rozhraní API. Ukázkové webové rozhraní API je součástí projektu, který jste si stáhli v kurzu požadavků.

Ukázkové řešení obsahuje dva projekty:

Následující dva projekty jsou v ukázkovém řešení:

- **TaskWebApp** – vytvoření a úprava seznamu úkolů V ukázce se k registraci nebo přihlašování uživatelů používá tok uživatelů **registrace nebo přihlašování** .
- **TaskService** – podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Rozhraní API je chráněno pomocí Azure AD B2C a volá ho TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurace webové aplikace

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
2. Otevřete soubor **Web.config** v projektu **TaskWebApp**.
3. Pokud chcete rozhraní API spustit místně, jako **api:TaskServiceUrl** použijte nastavení pro localhost. Změňte soubor Web.config následujícím způsobem:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Nakonfigurujte identifikátor URI rozhraní API. Toto je identifikátor URI, který webová aplikace používá k vytvoření požadavku rozhraní API. Nakonfigurujte také požadovaná oprávnění.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor **Web.config** v projektu **TaskService**.
2. Nakonfigurujte rozhraní API tak, aby používalo vašeho tenanta.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Nastavte ID klienta na ID aplikace zaregistrované pro vaše rozhraní API.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Aktualizujte nastavení toku uživatelů pomocí názvu uživatelského toku registrace a přihlášení.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Nakonfigurujte nastavení oborů tak, aby odpovídaly oborům, které jste vytvořili na portálu.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Je potřeba spustit jak projekt **TaskWebApp**, tak i projekt **TaskService**.

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Nastavit projekty po spuštění**.
2. Vyberte **více projektů po spuštění**.
3. U obou projektů změňte hodnotu **Akce** na **Spustit**.
4. Nastavení uložíte kliknutím na **OK**.
5. Stisknutím klávesy **F5** spusťte obě aplikace. Každá aplikace se otevře na vlastní kartě prohlížeče. `https://localhost:44316/` je webová aplikace.
    Na adrese `https://localhost:44332/` je webové rozhraní API.

6. Ve webové aplikaci klikněte na **Registrace/přihlášení** a přihlaste se k webové aplikaci. Použijte účet, který jste vytvořili dříve.
7. Po přihlášení klikněte na **seznam úkolů** a vytvořte položku seznamu úkolů.

Když vytvoříte položku seznamu úkolů, Webová aplikace odešle požadavek na webové rozhraní API, aby vygeneroval položku seznamu úkolů. Chráněná webová aplikace volá chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C](tutorial-add-identity-providers.md)
