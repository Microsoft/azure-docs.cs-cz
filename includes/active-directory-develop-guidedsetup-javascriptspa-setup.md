---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 724166d402f81fa3a2c977d107111f5a0c32571d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293471"
---
## <a name="setting-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Stáhněte si tento ukázkový projekt místo toho raději?
> - [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> nebo
> - [Stáhnout soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) pro místní webový server, jako je například uzel
>
> A potom přejděte ke [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před jeho provedením.

## <a name="prerequisites"></a>Požadavky
Místní webový server jako [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), nebo integrace služby IIS Express s [Visual Studio 2017](https://www.visualstudio.com/downloads/) se vyžaduje pro spuštění v tomto kurzu.

Pokyny v této příručce jsou založené na Node.js a sadě Visual Studio 2017, ale můžete použít jiné prostředí pro vývoj nebo webového serveru.

## <a name="create-your-project"></a>Vytvoření projektu

> ### <a name="option-1-visual-studio"></a>Možnost 1: Visual Studio
> Pokud používáte Visual Studio a vytvořit nový projekt, postupujte podle pokynů můžete vytvořit nové řešení sady Visual Studio:
> 1.    V sadě Visual Studio:  `File` > `New` > `Project`
> 2.    V části `Visual C#\Web`vyberte `ASP.NET Web Application (.NET Framework)`
> 3.    Pojmenujte svoji aplikaci a klikněte na tlačítko *OK*
> 4.    V části `New ASP.NET Web Application`vyberte `Empty`

<p/><!-- -->

> ### <a name="option-2-node-other-web-servers"></a>Možnost 2: Uzel nebo jiné webové servery
> Ujistěte se, že jste si nainstalovali [Node.js](https://nodejs.org/en/download/), postupujte podle níže uvedených kroků:
> - Vytvořte složku pro hostování vaší aplikace.


## <a name="create-your-single-page-applications-ui"></a>Vytvoření uživatelského rozhraní jednostránkové aplikace
1.  Vytvoření *index.html* soubor pro vaše aplikace SPA v JavaScriptu. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem myši a vyberte: `Add`  >  `New Item`  >  `HTML page` a pojmenujte ho index.html
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
