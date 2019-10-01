---
title: Kurz – udělení přístupu k webovému rozhraní API Node. js z desktopové aplikace – Azure Active Directory B2C | Microsoft Docs
description: Kurz týkající se použití Active Directory B2C k ochraně webového rozhraní API Node. js a jeho volání z desktopové aplikace .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679327"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: poskytnutí přístupu k webovému rozhraní API Node. js z desktopové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API typu Azure Active Directory B2C (Azure AD B2C) z desktopové aplikace Windows Presentation Foundation (WPF).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

Dokončení kroků a požadavků v [kurzu: povolení ověřování desktopových aplikací pomocí účtů pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. K implementaci řízení přístupu na základě oboru používá webové rozhraní API obory. Někteří uživatelé například můžou mít přístup pro čtení i zápis, zatímco jiní uživatelé mohou mít oprávnění jen pro čtení. V tomto kurzu definujete pro webové rozhraní API oprávnění ke čtení.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělit oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *app1*. Tuto aplikaci použijete pro volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte *nativeapp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

Uživatel se ověřuje pomocí Azure AD B2C pro použití desktopové aplikace WPF. Desktopová aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci Node. js, kterou si můžete stáhnout z GitHubu.

[Stáhněte si soubor zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Ukázka webového rozhraní API Node. js používá knihovnu Passport. js k povolení Azure AD B2C k ochraně volání rozhraní API.

1. Otevřete soubor `index.js`.
2. Nakonfigurujte ukázku s informacemi o registraci klienta Azure AD B2C. Změňte následující řádky kódu:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

1. Spusťte příkazový řádek Node. js.
2. Přejděte do adresáře, který obsahuje ukázku Node. js. Například `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Spusťte následující příkazy:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Spuštění desktopové aplikace

1. Otevřete řešení **Active-Directory-B2C-WPF** v aplikaci Visual Studio.
2. Stisknutím klávesy **F5** spusťte aplikaci klasické pracovní plochy.
3. Přihlaste se pomocí e-mailové adresy a hesla použitého v [kurzu ověřování uživatelů pomocí Azure Active Directory B2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).
4. Klikněte na tlačítko **zavolat rozhraní API** .

Aplikace klasické pracovní plochy vytvoří požadavek na webové rozhraní API a získá odpověď s zobrazovaným jménem přihlášeného uživatele. Chráněná desktopová aplikace volá chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidání zprostředkovatelů identit do aplikací v Azure Active Directory B2C](tutorial-add-identity-providers.md)
