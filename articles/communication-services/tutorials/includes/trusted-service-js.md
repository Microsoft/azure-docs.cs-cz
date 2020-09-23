---
title: JavaScript důvěryhodné služby
description: Toto je verze JavaScriptu pro vytvoření důvěryhodné služby pro komunikační služby.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d4ef8baa123f805d380b14fa24abff65903cb41d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946840"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) na některé z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), LTS a verze LTS údržby (doporučeno 10.14.1). Pomocí `node --version` příkazu ověřte svou verzi. 
- [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. 
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Přehled

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagram pro architekturu důvěryhodné služby":::

Pro tento kurz vytvoříme funkci Azure, která bude sloužit jako služba pro zajištění důvěryhodného tokenu. Tento kurz můžete použít k zavedení vlastní služby pro zřizování tokenů.

Tato služba zodpovídá za ověřování uživatelů v komunikačních službách Azure. Uživatelé vašich aplikací komunikačních služeb budou vyžadovat, aby se `Access Token` účastnili zřetězení vláken a volání VoIP. Funkce Azure Functions bude fungovat jako důvěryhodný střední uživatel mezi uživatelem a komunikačními službami. To vám umožní zřídit přístupové tokeny bez vystavení připojovacího řetězce prostředků vašim uživatelům.

Další informace najdete v Koncepční dokumentaci k architektuře a ověřování na [straně klienta](../../concepts/client-and-server-architecture.md) a [autorizaci](../../concepts/authentication.md) .

## <a name="setting-up"></a>Nastavení

### <a name="azure-functions-set-up"></a>Nastavení Azure Functions

Pojďme nejdřív nastavit základní strukturu pro naši funkci Azure Functions. Podrobné pokyny k nastavení najdete tady: [Vytvoření funkce pomocí Visual Studio Code](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)

Naše funkce Azure vyžaduje následující konfiguraci:

- Jazyk: JavaScript
- Šablona: Trigger HTTP
- Úroveň autorizace: anonymní (tuto možnost lze zapnout později, pokud dáváte přednost jinému autorizačnímu modelu)
- Název funkce: uživatelsky definované

Po provedení následujících [pokynů Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript) s výše uvedenou konfigurací byste měli mít projekt v Visual Studio Code pro funkci Azure Functions se `index.js` souborem, který obsahuje funkci. Kód uvnitř tohoto souboru by měl být následující:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Nyní budeme pokračovat k instalaci knihoven služby Azure Communication Services.

### <a name="install-communication-services-libraries"></a>Nainstalovat knihovny komunikačních služeb

`Administration`K vygenerování budeme použít knihovnu `User Access Tokens` .

Pomocí `npm install` příkazu nainstalujte klientskou knihovnu pro správu služby Azure Communication Services pro JavaScript.

```console

npm install @azure/communication-administration --save

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

V horní části `index.js` souboru importujte rozhraní pro `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>Generování přístupového tokenu

Abychom mohli službě Azure Functions, aby vygenerovala `User Access Tokens` , nejdřív musíte použít připojovací řetězec pro náš prostředek služby Communications Services.

Další informace o načtení připojovacího řetězce najdete v [rychlém startu k zřizování prostředků](../../quickstarts/create-communication-resource.md) .

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Dále upravíte naši původní funkci k vygenerování `User Access Tokens` . 

`User Access Tokens` jsou generovány vytvořením uživatele z `createUser` metody. Po vytvoření uživatele můžeme použít `issueToken` metodu k vygenerování tokenu pro tohoto uživatele, který vrátí funkce Azure.

V tomto příkladu nastavíme rozsah tokenu na `voip` . Pro vaši aplikaci můžou být nutné další obory. Další informace o [oborech](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

U existujících komunikačních služeb `CommunicationUser` můžete přeskočit krok vytvoření a jednoduše vygenerovat přístupový token. Další podrobnosti najdete v [rychlém startu vytvoření tokenů přístupu uživatele](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Testování funkce Azure Functions

Spusťte funkci Azure místně pomocí `F5` . Tím se funkce Azure inicializuje místně a zpřístupní se prostřednictvím: `http://localhost:7071/api/FUNCTION_NAME` . Projděte si další dokumentaci k [místnímu spuštění](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-locally)

Otevřete adresu URL v prohlížeči a měli byste vidět text odpovědi s ID uživatele komunikace, tokenem a vypršením platnosti tokenu.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Snímek obrazovky znázorňující příklad odpovědi pro vytvořenou funkci Azure Functions.":::

## <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

Pokud chcete nasadit funkci Azure Functions, můžete postupovat podle podrobných [pokynů](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#sign-in-to-azure) .

Obecně platí, že budete potřebovat:
1. Přihlášení k Azure ze sady Visual Studio
2. Publikujte projekt do svého účtu Azure. Tady budete muset zvolit stávající předplatné.
3. Vytvořte nový prostředek funkce Azure Functions pomocí Průvodce pro Visual Studio nebo použijte existující prostředek. V případě nového prostředku ho budete muset nakonfigurovat do požadované oblasti, modulu runtime a jedinečného identifikátoru.
4. Počkat na dokončení nasazení
5. Spusťte funkci 🎉

## <a name="run-azure-function"></a>Spustit funkci Azure Functions

Spuštění funkce Azure pomocí adresy URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Adresu URL najdete tak, že kliknete pravým tlačítkem na funkci na Visual Studio Code a zkopírujete adresu URL funkce.

Další informace o [spuštění funkce Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-in-azure)
