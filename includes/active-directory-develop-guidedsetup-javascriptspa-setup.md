---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502766"
---
## <a name="setting-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Stáhněte si tento ukázkový projekt místo toho raději?
> - [Stáhnout soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pro místní webový server, jako je například uzel
>
> nebo
> - [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> A potom přejděte ke [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před jeho provedením.

## <a name="prerequisites"></a>Požadavky
Místní webový server jako [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), nebo integrace služby IIS Express s [Visual Studio 2017](https://www.visualstudio.com/downloads/) se vyžaduje pro spuštění v tomto kurzu.

Pokud se používá ke spuštění projektu Node.js, nainstalovat integrované vývojové prostředí, jako je [Visual Studio Code](https://code.visualstudio.com/download) k úpravám souborů projektu.

Pokyny v této příručce jsou založené na Node.js a sadě Visual Studio 2017, ale můžete použít jiné prostředí pro vývoj nebo webového serveru.

## <a name="create-your-project"></a>Vytvoření projektu

> ### <a name="option-1-node-other-web-servers"></a>Option 1: Uzel nebo jiné webové servery
> Ujistěte se, že jste si nainstalovali [Node.js](https://nodejs.org/en/download/), postupujte podle níže uvedených kroků:
> - Vytvořte složku pro hostování vaší aplikace.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Option 2: Visual Studio
> Pokud používáte Visual Studio a vytvořit nový projekt, postupujte podle pokynů můžete vytvořit nové řešení sady Visual Studio:
> 1.    V sadě Visual Studio:  **Soubor > Nový > Projekt**
> 2.    V části **Visual C# \Web**vyberte **webová aplikace ASP.NET (.NET Framework)**
> 3.    Zadejte název pro vaši aplikaci a vyberte **OK**
> 4.    V části **nová webová aplikace ASP.NET**vyberte **prázdný**

## <a name="create-your-single-page-applications-ui"></a>Vytvoření uživatelského rozhraní jednostránkové aplikace
1. Vytvoření `index.html` soubor pro vaše aplikace SPA v JavaScriptu. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem myši a vyberte: **Přidat > Nová položka > stránka HTML** a pojmenujte ho index.html.

2. Přidejte následující kód na stránku:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```
