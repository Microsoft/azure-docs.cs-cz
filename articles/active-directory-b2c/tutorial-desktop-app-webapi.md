---
title: 'Kurz: Udělení přístupu k webovému rozhraní API Node.js z desktopové aplikace'
description: Návod, jak pomocí služby Active Directory B2C chránit webové rozhraní API Node.js a volat jej z desktopové aplikace .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183366"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C

Tento kurz ukazuje, jak volat webové rozhraní API Node.js chráněné Azure Active Directory B2C (Azure AD B2C) z desktopové aplikace Windows Presentation Foundation (WPF), chráněné také Azure AD B2C.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a předpoklady v [kurzu: Ověřování uživatelů v nativním desktopovém klientovi](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zaznamenejte hodnotu `demo.read` v rámci **scopes** pro obor, který se má použít v pozdějším kroku při konfiguraci desktopové aplikace. Hodnota celého oboru `https://contosob2c.onmicrosoft.com/api/demo.read`je podobná hodnotě .

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z nativní klientské aplikace, musíte udělit registrovaným nativním klientským aplikacím oprávnění webovému rozhraní API, které jste zaregistrovali v Azure AD B2C.

V požadovaném kurzu jste zaregistrovali nativní klientskou aplikaci s názvem *nativeapp1*. Následující kroky nakonfigurují registraci nativní aplikace s obory rozhraní API, které jste vystavili pro *webapi1* v předchozí části. To umožňuje desktopové aplikaci získat přístupový token z Azure AD B2C, který webové rozhraní API můžete použít k ověření a poskytnutí přístupu s vymezeným oborem k jeho prostředkům. Nakonfigurujete a spusťte ukázky kódu desktopové aplikace a webového rozhraní API později v kurzu.

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Vyberte **aplikace**a pak vyberte *nativní app1*.
1. Vyberte **přístup k rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím souboru **Vybrat rozhraní API** vyberte *webapi1*.
1. V rozevíracím souboru **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo.read* a *demo.write*.
1. Vyberte **OK**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Vyberte **registrace aplikací (Preview)** a pak vyberte nativní klientskou aplikaci, která by měla mít přístup k rozhraní API. Například *nativeapp1*.
1. V části **Manage**vyberte **oprávnění rozhraní API**.
1. V části **Nakonfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje api.**
1. Vyberte rozhraní API, ke kterému má být nativní klientská aplikace udělena. Například *webapi1*.
1. V části **Oprávnění**rozbalte **ukázku**a vyberte obory, které jste definovali dříve. Například *demo.read* a *demo.write*.
1. Vyberte **Přidat oprávnění**. Podle pokynů počkejte několik minut, než přejdete k dalšímu kroku.
1. Vyberte **Udělit souhlas správce (název vašeho klienta).**
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v tenantovi Azure AD B2C, kterému byla přiřazena alespoň role *správce cloudových aplikací.*
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a ověřte, zda je povoleno "Uděleno pro ...". zobrazí v části **Stav** pro oba obory. Může trvat několik minut, než se oprávnění rozšíří.

* * *

Uživatel se ověří v Azure AD B2C, aby mohl použít desktopovou aplikaci WPF. Desktopová aplikace získá z Azure AD B2C udělení autorizace pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-samples"></a>Konfigurace ukázek

Teď, když je webové rozhraní API registrované a máte nakonfigurované obory a oprávnění, nakonfigurujete ukázky desktopové aplikace a webového rozhraní API tak, aby používaly vašeho klienta Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aktualizace desktopové aplikace

V předpokladu pro tento článek jste [upravili desktopovou aplikaci WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) tak, aby umožňovala přihlášení s tokem uživatele v tenantovi Azure AD B2C. V této části aktualizujete stejnou aplikaci tak, aby odkazovala na webové rozhraní API, které jste zaregistrovali dříve, *webapi1*.

1. Otevřete řešení **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) v sadě Visual Studio.
1. V projektu **active-directory-b2c-wpf** otevřete soubor *App.xaml.cs* a vyhledejte následující definice proměnných.
    1. Nahraďte hodnotu `ApiScopes` proměnné hodnotou, kterou jste zaznamenali dříve při definování oboru **demo.read.**
    1. Nahraďte hodnotu `ApiEndpoint` proměnné **identifikátorem URI přesměrování,** který jste zaznamenali dříve při registraci webového rozhraní API (například *webapi1)* ve vašem tenantovi.

    Tady je příklad:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Získání a aktualizace ukázky rozhraní API Node.js

Dále získejte ukázku kódu webového rozhraní API Node.js z GitHubu a nakonfigurujte ji tak, aby používala webové rozhraní API, které jste zaregistrovali v tenantovi Azure AD B2C.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Ukázka webového rozhraní API Node.js s využitím knihovny Passport.js umožňuje ochranu volání do tohoto rozhraní API pomocí služby Azure AD B2C.

1. Otevřete soubor `index.js`.
1. Aktualizujte tyto definice proměnných následujícími hodnotami. Změňte `<web-API-application-ID>` na **ID aplikace (klienta)** webového rozhraní API, které jste zaregistrovali dříve *(webapi1*). Změňte `<your-b2c-tenant>` název vašeho klienta Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Vzhledem k tomu, že rozhraní API používáte místně, aktualizujte cestu v trase metody GET namísto `/` umístění ukázkové aplikace `/hello`:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Spuštění ukázek

### <a name="run-the-nodejs-web-api"></a>Spuštění webového rozhraní NODE.js

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Spusťte následující příkazy:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Spuštění desktopové aplikace

1. Otevřete řešení **active-directory-b2c-wpf** v sadě Visual Studio.
2. Stisknutím klávesy **F5** spusťte desktopovou aplikaci.
3. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [kurzu Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](tutorial-desktop-app.md).
4. Vyberte tlačítko **Rozhraní API pro volání.**

Desktopová aplikace podá požadavek na místně spuštěné webové rozhraní API a po ověření platného přístupového tokenu zobrazí zobrazované jméno přihlášeného uživatele.

![Zobrazovaný název zobrazený v horním podokně desktopové aplikace WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Vaše desktopová aplikace, chráněná Azure AD B2C, volá místně spuštěné webové rozhraní API, které je také chráněné Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidání poskytovatelů identit do aplikací ve službě Azure Active Directory B2C](tutorial-add-identity-providers.md)
