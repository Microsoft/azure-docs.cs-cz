---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 135ee9f6b833165cd393b9c5ca582e0ee9499e0f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057364"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pro registraci aplikace.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview) > Nová registrace**.
1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
1. V části **identifikátor URI pro přesměrování** vyberte **webové** platformy a nastavte hodnotu na adresu URL aplikace založené na vašem webovém serveru. Naleznete níže uvedených částech pokyny o tom, jak nastavit a získat adresu URL pro přesměrování ve Visual Studiu a uzel.
1. Až budete hotovi, vyberte **Zaregistrovat**.
1. V aplikaci **přehled** stránce si poznamenejte **ID aplikace (klient)** hodnotu.
1. Tento rychlý start vyžaduje [implicitní tok poskytování](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) povolit. V levém navigačním podokně zaregistrovanou aplikaci vyberte **ověřování**.
1. V **upřesňující nastavení**v části **implicitní grant**, oboje povolili **tokeny typu ID** a **přístupové tokeny** zaškrtávací políčka. Tokeny typu ID a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

> #### <a name="setting-the-redirect-url-for-node"></a>Nastavení adresy URL pro přesměrování pro uzel
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale můžete použít další dostupný port. Postupujte podle níže uvedených pokynů a nastavte adresu URL pro přesměrování v informace o registraci aplikace:<br/>
> - Přepněte zpět do *registrace aplikace* a nastavte `http://localhost:30662/` jako `Redirect URL`, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (ve kterém *[port]* je vlastní číslo portu TCP).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio pokyny pro získání adresy URL pro přesměrování
> Postupujte podle těchto kroků k získání adresy URL pro přesměrování:
> 1. V **Průzkumníka řešení**, vyberte projekt a podívejte se na **vlastnosti** okna. Pokud se nezobrazí **vlastnosti** okna, stisknutím klávesy **F4**.
> 2. Zkopírujte hodnotu z **URL** do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Přepněte zpět *registrace aplikace* a nastavte tuto hodnotu jako **adresy URL pro přesměrování**.

#### <a name="configure-your-javascript-spa"></a>Konfigurace vaší aplikace SPA v JavaScriptu

1. V `index.html` soubor vytvořen během instalace projektu, přidejte informace o registraci aplikace. Přidejte následující kód v horní části v rámci `<script></script>` značky v těle vaše `index.html` souboru:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Nahraďte <code>Enter the application Id here</code> s ID aplikace, který jste právě zaregistrovali.
</li>
</ol>
