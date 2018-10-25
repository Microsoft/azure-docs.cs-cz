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
ms.openlocfilehash: b3d46e10facdef26b36c910a5c23b40a415a2894
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988396"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Zaregistrovat aplikaci několika způsoby. Vyberte možnost, která nejlépe vyhovuje vašim potřebám:
* [Expresní režim – konfigurace aplikace pomocí rychlého startu SPA](#option-1-register-your-application-express-mode)
* [Rozšířený režim - ruční konfigurace nastavení aplikace](#option-2-register-your-application-advanced-mode)

### <a name="option-1-register-your-application-express-mode"></a>Možnost 1: Registrace vaší aplikace (expresní režim)

1. Přihlaste se k [registrace aplikace Azure portal (preview)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) pro registraci aplikace.
1. Na **zaregistrovat aplikaci** stránky, zadejte název pro vaši aplikaci.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
1. Až budete hotovi, vyberte **zaregistrovat**.
1. Postupujte podle pokynů rychlý start ke stažení a automaticky jedním kliknutím nakonfigurujte novou aplikaci za vás.

### <a name="option-2-register-your-application-advanced-mode"></a>Možnost 2: Registrace vaší aplikace (rozšířený režim)

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pro registraci aplikace.
1. Pokud váš účet umožňuje přístup k více než jednoho tenanta, vyberte svůj účet v pravém horním rohu a nastavení portálu relace k požadované službě Azure AD tenanta.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby a pak vyberte **registrace aplikací (Preview) > Nový registrační**.
1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
1. V části **identifikátor URI pro přesměrování** vyberte **webové** platformy a nastavte hodnotu na adresu URL aplikace založené na vašem webovém serveru. Naleznete níže uvedených částech pokyny o tom, jak nastavit a získat adresu URL pro přesměrování ve Visual Studiu a uzel.
1. Až budete hotovi, vyberte **zaregistrovat**.
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
        clientID: "[Enter the application Id here]",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Nahraďte <code>Enter the application Id here</code> s ID aplikace, který jste právě zaregistrovali.
</li>
</ol>
