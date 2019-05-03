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
ms.openlocfilehash: 805b773795e7c8a96fe1b94abecd860475a4c7a1
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993289"
---
## <a name="setting-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Stáhněte si tento ukázkový projekt místo toho raději?
> - [Stáhnout soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pomocí místního webového serveru, jako je například uzel
>
> nebo
> - (Volitelné) [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip) pomocí serveru služby IIS
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
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0-preview.4/js/msal.js"></script>
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

   > [!TIP]
   > Verze MSAL.js v nad skriptu můžete nahradit nejnovější vydanou verzi v části [uvolní MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
