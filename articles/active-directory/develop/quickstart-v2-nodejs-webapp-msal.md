---
title: 'Rychlý Start: Přidání ověřování do webové aplikace v uzlu pomocí uzlu MSAL | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak implementovat ověřování pomocí Node.js webové aplikace a knihovny Microsoft Authentication Library (MSAL) pro Node.js.
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: e860a33ba44743dcc5a96d7656f22fd952a1d0b8
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762563"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Rychlý Start: přihlášení uživatelů a získání přístupového tokenu v rámci webové aplikace v uzlu pomocí toku kódu ověřování

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která demonstruje, jak se může webová aplikace Node.js přihlašovat uživatelům pomocí toku autorizačního kódu. Ukázka kódu také ukazuje, jak získat přístupový token pro volání rozhraní Microsoft Graph API. 

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

V tomto rychlém startu se používá knihovna ověřování Microsoft pro Node.js (uzel MSAL) s tokem autorizačního kódu.

> [!IMPORTANT]
> Uzel MSAL [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [vytvoření předplatného Azure zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) nebo jiný Editor kódu

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrace a stažení aplikace pro rychlý Start
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Nastavte hodnotu **identifikátoru URI přesměrování** na `http://localhost:3000/redirect` .
> 1. Vyberte **Zaregistrovat**. 
> 1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V části **Spravovat** vyberte **certifikáty & tajných klíčů**  >  **nový tajný klíč klienta**.  Ponechte Popis prázdné a výchozí doba platnosti a pak vyberte **Přidat**.
> 1. Poznamenejte si **hodnotu** **tajného klíče klienta** pro pozdější použití.

#### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

> [!div renderon="docs"]
> Chcete-li spustit projekt s webovým serverem pomocí Node.js, [Stáhněte si základní soubory projektu](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Spustit projekt s webovým serverem pomocí Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>Krok 3: Konfigurace aplikace Node
>
> Rozbalte projekt a otevřete složku *MS-identity-Node-Main* a pak otevřete soubor *index.js* .
> Nastavte `clientID` s **ID aplikace (klienta)**.
> Nastavte `clientSecret` s **hodnotou** **tajného klíče klienta**.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> Upravte hodnoty v `config` části, jak je popsáno zde:
>
> - `Enter_the_Application_Id_Here` je **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here` je **hodnota** **tajného klíče klienta** pro zaregistrovanou aplikaci.
>
> Výchozí `authority` hodnota představuje hlavní (globální) cloud Azure:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Pokud chcete zjistit hodnotu **ID aplikace (klienta)**, na stránce s přehledem registrace aplikace se podívejte na stránku Azure Portal s **přehledem** registrace aplikace. Pokud chcete načíst nebo vygenerovat nový **tajný klíč klienta**, vyhledejte v části **certifikáty & tajných klíčů** .
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>Krok 4: spuštění projektu

Spusťte projekt pomocí Node.js:

1. Chcete-li spustit server, spusťte v adresáři projektu následující příkazy:
    ```console
    npm install
    npm start
    ```
1. Přejděte na adresu `http://localhost:3000/`.

1. Vyberte **Přihlásit** se a spusťte proces přihlášení.

    Při prvním přihlášení se zobrazí výzva k zadání vašeho souhlasu, který aplikaci umožní přístup k vašemu profilu a bude vás přihlašovat. Po úspěšném přihlášení se zobrazí zpráva protokolu na příkazovém řádku.

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje

Ukázka, pokud je spuštěna, je hostitelem webového serveru na hostiteli localhost, portu 3000. Když webový prohlížeč přistupuje k tomuto webu, ukázka okamžitě přesměruje uživatele na ověřovací stránku Microsoftu. Z tohoto důvodu vzorek neobsahuje žádné *HTML* nebo zobrazované elementy. Po úspěšném ověření se zobrazí zpráva "OK".

### <a name="msal-node"></a>Uzel MSAL

Knihovna uzlů MSAL podepisuje uživatele a požádá o tokeny, které se používají pro přístup k rozhraní API, které je chráněno platformou Microsoft identity. Nejnovější verzi si můžete stáhnout pomocí Správce balíčků Node.js (npm):

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání ověřování do existující webové aplikace – ukázka kódu na GitHubu >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
