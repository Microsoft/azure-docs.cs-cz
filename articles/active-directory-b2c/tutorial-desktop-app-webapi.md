---
title: 'Kurz: poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace'
description: Kurz týkající se použití Active Directory B2C k ochraně Node.js webového rozhraní API a jeho volání z desktopové aplikace .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555605"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat Node.js webového rozhraní API chráněného Azure Active Directory B2C (Azure AD B2C) z desktopové aplikace Windows Presentation Foundation (WPF), která je chráněná také Azure AD B2C.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a požadavky v [kurzu: ověřování uživatelů v nativním klientském počítači](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Poznamenejte si hodnotu  `demo.read` rozsah, který se má použít v pozdějším kroku při konfiguraci aplikace klasické pracovní plochy. Úplná hodnota oboru je podobná `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z nativní klientské aplikace, je nutné udělit zaregistrovaným nativním klientským aplikacím oprávnění k webovému rozhraní API, které jste zaregistrovali v Azure AD B2C.

V kurzu požadavků jste zaregistrovali nativní klientskou aplikaci s názvem *nativeapp1*. Následující postup nakonfiguruje registraci nativní aplikace pomocí oborů rozhraní API, které jste vystavili pro *webapi1* v předchozí části. To umožňuje aplikaci klasické pracovní plochy získat přístupový token z Azure AD B2C, kterou může webové rozhraní API použít k ověření a poskytování vymezeného přístupu k prostředkům. V tomto kurzu nakonfigurujete a spustíte i ukázky kódu desktopové aplikace i webové rozhraní API.

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Vyberte **Registrace aplikací** a pak vyberte nativní klientskou aplikaci, která by měla mít přístup k rozhraní API. Například *nativeapp1*.
1. V části **Spravovat** vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění** vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** .
1. Vyberte rozhraní API, ke kterému má být přístupná nativní klientská aplikace udělena. Například *webapi1*.
1. V části **oprávnění** rozbalte **Ukázka** a pak vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **Přidat oprávnění**. Jak je směrované, počkejte několik minut, než budete pokračovat k dalšímu kroku.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)**.
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v Azure AD B2C tenantovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat** a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav** pro oba obory. Rozšíření oprávnění může trvat několik minut.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Vyberte **aplikace (starší verze)** a pak vyberte *nativeapp1*.
1. Vyberte **přístup přes rozhraní API** a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

* * *

Uživatel se ověří v Azure AD B2C, aby mohl použít desktopovou aplikaci WPF. Desktopová aplikace získá z Azure AD B2C udělení autorizace pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-samples"></a>Konfigurace ukázek

Když je teď webové rozhraní API zaregistrované a máte nastavené obory a oprávnění, nakonfigurujete ukázky aplikace klasické pracovní plochy a webového rozhraní API tak, aby používaly svého tenanta Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aktualizace desktopové aplikace

V předpokladech tohoto článku jste upravili [desktopovou aplikaci WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) , která umožňuje přihlášení pomocí toku uživatele v Azure AD B2C tenantovi. V této části aktualizujete stejnou aplikaci tak, aby odkazovala na webové rozhraní API, které jste předtím zaregistrovali, *webapi1*.

1. Otevřete řešení **Active-Directory-B2C-WPF** ( `active-directory-b2c-wpf.sln` ) v aplikaci Visual Studio.
1. V projektu **Active-Directory-B2C-WPF** otevřete soubor *App. XAML. cs* a vyhledejte následující definice proměnných.
    1. Nahraďte hodnotu proměnné hodnotou `ApiScopes` , kterou jste si poznamenali dříve, když jste definovali obor **demo. Read** .
    1. Nahraďte hodnotu `ApiEndpoint` proměnné **identifikátorem URI přesměrování** , který jste si poznamenali dříve, když jste zaregistrovali webové rozhraní API (například *webapi1*) ve vašem tenantovi.

    Tady je příklad:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Získání a aktualizace ukázky rozhraní Node.js API

V dalším kroku získáte ukázku kódu Node.js webového rozhraní API z GitHubu a nakonfigurujete ho tak, aby používalo webové rozhraní API, které jste zaregistrovali ve vašem tenantovi Azure AD B2C.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Ukázka webového rozhraní API Node.js s využitím knihovny Passport.js umožňuje ochranu volání do tohoto rozhraní API pomocí služby Azure AD B2C.

1. Otevřete soubor `index.js`.
1. Aktualizujte tyto definice proměnných s následujícími hodnotami. Přejděte `<web-API-application-ID>` do **ID aplikace (klienta)** webového rozhraní API, které jste předtím zaregistrovali (*webapi1*). Změňte `<your-b2c-tenant>` název vašeho tenanta Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Vzhledem k tomu, že používáte rozhraní API místně, aktualizujte cestu v trase pro metodu GET na `/` místo umístění ukázkové aplikace `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Spuštění ukázek

### <a name="run-the-nodejs-web-api"></a>Spuštění Node.js webového rozhraní API

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

1. Otevřete řešení **Active-Directory-B2C-WPF** v aplikaci Visual Studio.
2. Stisknutím klávesy **F5** spusťte desktopovou aplikaci.
3. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [kurzu Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](tutorial-desktop-app.md).
4. Vyberte tlačítko **rozhraní API pro volání** .

Aplikace klasické pracovní plochy vytvoří požadavek na místně běžící webové rozhraní API a při ověřování platného přístupového tokenu se zobrazí zobrazované jméno uživatele, který je přihlášený.

![Zobrazovaný název zobrazený v horním podokně desktopové aplikace WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Vaše desktopová aplikace chráněná nástrojem Azure AD B2C volá místně běžící webové rozhraní API, které je také chráněné pomocí Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C](add-identity-provider.md)