---
title: Tutorial - Enable authentication in a single-page application - Azure Active Directory B2C
description: In this tutorial, learn how to use Azure Active Directory B2C to provide user login for a single page application (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8ece6401484d8f32483d4673384fcce6d7afac4a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420352"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Tutorial: Enable authentication in a single-page application using Azure Active Directory B2C (Azure AD B2C)

This tutorial shows you how to use Azure Active Directory B2C (Azure AD B2C) to sign in and sign up users in a single-page application (SPA). Azure AD B2C enables your applications to authenticate to social accounts, enterprise accounts, and Azure Active Directory accounts using open standard protocols.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Update the application in Azure AD B2C
> * Configure the sample to use the application
> * Sign up using the user flow

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

You need the following Azure AD B2C resources in place before continuing with the steps in this tutorial:

* [Azure AD B2C tenant](tutorial-create-tenant.md)
* [Application registered](tutorial-register-applications.md) in your tenant
* [User flows created](tutorial-create-user-flows.md) in your tenant

Additionally, you need the following in your local development environment:

* Code editor, for example [Visual Studio Code](https://code.visualstudio.com/) or [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) or later
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

In the second tutorial that you completed as part of the prerequisites, you registered a web application in Azure AD B2C. To enable communication with the sample in the tutorial, you need to add a redirect URI to the application in Azure AD B2C.

You can use the current **Applications** experience or our new unified **App registrations (Preview)** experience to update the application. [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Make sure you're using the directory that contains your Azure AD B2C tenant by selecting the **Directory + subscription** filter in the top menu and choosing the directory that contains your tenant.
1. Select **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
1. Select **Applications**, and then select the *webapp1* application.
1. Under **Reply URL**, add `http://localhost:6420`.
1. Vyberte **Save** (Uložit).
1. On the properties page, record the **Application ID**. You use the app ID in a later step when you update the code in the single-page web application.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App registrations (Preview)](#tab/app-reg-preview/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Select the **Directory + subscription** filter in the top menu, and then select the directory that contains your Azure AD B2C tenant.
1. In the left menu, select **Azure AD B2C**. Or, select **All services** and search for and select **Azure AD B2C**.
1. Select **App registrations (Preview)** , select the **Owned applications** tab, and then select the *webapp1* application.
1. Select **Authentication**, then select **Try out the new experience** (if shown).
1. Under **Web**, select the **Add URI** link, enter `http://localhost:6420`, and then select **Save**.
1. Vyberte **Přehled**.
1. Record the **Application (client) ID** for use in a later step when you update the code in the single-page web application.

* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

In this tutorial, you configure a code sample that you download from GitHub. The sample demonstrates how a single-page application can use Azure AD B2C for user sign-up and sign-in, and to call a protected web API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Update the sample

Now that you've obtained the sample, update the code with your Azure AD B2C tenant name and the application ID you recorded in an earlier step.

1. Open the `index.html` file in the root of the sample directory.
1. In the `msalConfig` definition, modify the **clientId** value with the Application ID you recorded in an earlier step. Next, update the **authority** URI value with your Azure AD B2C tenant name. Also update the URI with the name of the sign-up/sign-in user flow you created in one of the prerequisites (for example, *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    The name of the user flow used in this tutorial is **B2C_1_signupsignin1**. If you're using a different user flow name, specify that name in the `authority` value.

## <a name="run-the-sample"></a>Spuštění ukázky

1. Open a console window and change to the directory containing the sample. Například:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Spusťte následující příkazy:

    ```
    npm install && npm update
    node server.js
    ```

    The console window displays the port number of the locally running Node.js server:

    ```
    Listening on port 6420...
    ```

1. Go to `http://localhost:6420` in your browser to view the application.

The sample supports sign-up, sign-in, profile editing, and password reset. This tutorial highlights how a user signs up using an email address.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

> [!WARNING]
> After sign-up or sign-in, you might see an [insufficient permissions error](#error-insufficient-permissions). Due to the code sample's current implementation, this error is expected. This issue will be resolved in a future version of the code sample, at which time this warning will be removed.

1. Select **Login** to initiate the *B2C_1_signupsignin1* user flow you specified in an earlier step.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Since you don't yet have an account, select the **Sign up now** link.
1. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. The sign-up workflow also collects the user's password and the requested attributes defined in the user flow.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Sign-up page presented by the sign-in/sign-up user flow](./media/active-directory-b2c-tutorials-spa/azure-ad-b2c-sign-up-workflow.png)

1. Select **Create** to create a local account in the Azure AD B2C directory.

When you select **Create**, the sign up page closes and the sign in page reappears.

You can now use your email address and password to sign in to the application.

### <a name="error-insufficient-permissions"></a>Error: insufficient permissions

After you sign in, the application may return an insufficient permissions error:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

You receive this error because the web application is attempting to access a web API protected by the demo directory, *fabrikamb2c*. Because your access token is valid only for your Azure AD directory, the API call is unauthorized.

To fix this error, continue on to the next tutorial in the series (see [Next steps](#next-steps)) to create a protected web API for your directory.

## <a name="next-steps"></a>Další kroky

In this article, you learned how to:

> [!div class="checklist"]
> * Update the application in Azure AD B2C
> * Configure the sample to use the application
> * Sign up using the user flow

Now move on to the next tutorial in the series to grant access to a protected web API from the SPA:

> [!div class="nextstepaction"]
> [Tutorial: Grant access to an ASP.NET Core web API from an SPA using Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
