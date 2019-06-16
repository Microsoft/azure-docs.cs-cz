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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133685"
---
## <a name="set-up-your-web-server-or-project"></a>Nastavení webového serveru nebo projektu

> Stáhněte si tento ukázkový projekt místo toho raději? Proveďte jednu z následujících akcí:
> 
> - Spustit projekt s místní webový server, jako je například Node.js, [stáhnout soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Volitelné) Spustit projekt s server služby IIS [stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> A potom nakonfigurovat vzorového kódu před spuštěním, přeskočte k [potřeba provádět krok konfigurace](#register-your-application).

## <a name="prerequisites"></a>Požadavky

* Spustit v tomto kurzu, budete potřebovat místní webový server, jako například [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core), nebo integrace služby IIS Express s [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Používáte-li spustit projekt Node.js, nainstalovat integrované vývojové prostředí (IDE), jako například [Visual Studio Code](https://code.visualstudio.com/download), chcete-li upravit soubory projektu.

* Pokyny v této příručce jsou založené na Node.js a sadě Visual Studio 2017, ale můžete použít jiné prostředí pro vývoj nebo webový server.

## <a name="create-your-project"></a>Vytvoření projektu

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Option 1: Node.js nebo jiné webové servery
> Ujistěte se, že jste si nainstalovali [Node.js](https://nodejs.org/en/download/), a potom postupujte takhle:
> - Vytvořte složku pro hostování vaší aplikace.
>
> ### <a name="option-2-visual-studio"></a>Option 2: Visual Studio
> Pokud používáte Visual Studio a vytvořit nový projekt, postupujte takto:
> 1. V sadě Visual Studio, vyberte **souboru** > **nový** > **projektu**.
> 1. V části **Visual C#\Web** vyberte **Webová aplikace ASP.NET (.NET Framework)** .
> 1. Zadejte název pro vaši aplikaci a pak vyberte **OK**.
> 1. V části **nová webová aplikace ASP.NET**vyberte **prázdný**.

## <a name="create-the-spa-ui"></a>Vytvoření uživatelského rozhraní aplikace SPA
1. Vytvoření *index.html* soubor pro vaše aplikace SPA v JavaScriptu. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem a vyberte **přidat** > **nová položka** > **stránku HTML**, a Název souboru *index.html*.

1. V *index.html* přidejte následující kód:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
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
   > Verze MSAL.js v předchozím skriptu můžete nahradit nejnovější vydanou verzi v části [uvolní MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
