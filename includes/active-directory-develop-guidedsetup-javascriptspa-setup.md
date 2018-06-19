---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 24a5baade2168df603f5505baeaaf8e1c3ea2411
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205223"
---
## <a name="setting-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Stáhněte si tento ukázkový projekt místo dávají přednost? 
> - [Stáhněte si projekt sady Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> nebo
> - [Stažení souborů projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) pro místní webový server, jako je například Python
>
> A potom přejděte na [krok konfigurace](#register-your-application) konfigurace ukázka kódu před jeho provedením.

## <a name="prerequisites"></a>Požadavky
Místní webový server jako [Python http.server](https://www.python.org/downloads/), [http server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core), nebo služba IIS Express integrace s [Visual Studio 2017](https://www.visualstudio.com/downloads/) je potřeba spustit tuto s průvodcem instalací. 

Pokyny v této příručce jsou založené na Python a Visual Studio 2017, ale Nebojte se použít jakékoli jiné vývojové prostředí nebo webový Server.

## <a name="create-your-project"></a>Vytvoření projektu 

> ### <a name="option-1-visual-studio"></a>Možnost 1: Visual Studio 
> Pokud používáte Visual Studio a vytvořit nový projekt, postupujte podle následujících kroků a vytvořte nové řešení sady Visual Studio:
> 1.    V sadě Visual Studio:  `File` > `New` > `Project`
> 2.    V části `Visual C#\Web`, vyberte možnost `ASP.NET Web Application (.NET Framework)`
> 3.    Název aplikace a klikněte na tlačítko *OK*
> 4.    V části `New ASP.NET Web Application`, vyberte možnost `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Možnost 2: Python / jiné webové servery
> Ujistěte se, že máte nainstalovanou [Python](https://www.python.org/downloads/), postupujte podle níže uvedených kroků:
> - Vytvořte složku pro hostování vaší aplikace.


## <a name="create-your-single-page-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace jediné stránce
1.  Vytvoření *index.html* soubor pro vaše SPA JavaScript. Pokud používáte Visual Studio, vyberte projekt (projektu do kořenové složky), klikněte pravým tlačítkem a vyberte: `Add`  >  `New Item`  >  `HTML page` a pojmenujte ji index.html
2.  Přidejte následující kód na stránku:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
