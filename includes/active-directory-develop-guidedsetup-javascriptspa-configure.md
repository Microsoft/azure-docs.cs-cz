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
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843194"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Chcete-li vytvořit aplikaci, vyberte jednu z nich několika způsoby:

### <a name="option-1-register-your-application-express-mode"></a>Možnost 1: Registrace vaší aplikace (expresní režim)
Teď musíte zaregistrovat svoji aplikaci *portál pro registraci aplikací Microsoft*:

1.  Registrace vaší aplikace prostřednictvím [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Zadejte název vaší aplikace a e-mailu.
3.  Ujistěte se, že možnost **instalační program s asistencí** je zaškrtnuté políčko.
4.  Postupujte podle pokynů k získání ID aplikace a vložte ho do svého kódu.

### <a name="option-2-register-your-application-advanced-mode"></a>Možnost 2: Registrace vaší aplikace (rozšířený režim)

1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Zadejte název vaší aplikace a e-mailu.
3. Ujistěte se, že možnost **instalační program s asistencí** není zaškrtnuta.
4.  Klikněte na tlačítko **přidat platformy**a pak vyberte **webové**.
5. Přidat **adresy URL pro přesměrování** , který odpovídá na adresu URL vaší aplikace na webovém serveru. Naleznete níže uvedených částech pokyny o tom, jak nastavit a získat adresu URL pro přesměrování ve Visual Studiu a uzel.
6. Vyberte **Uložit**.

> #### <a name="setting-redirect-url-for-node"></a>Nastavení adresy URL pro přesměrování pro uzel
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale teď můžete použít jakýkoli jiný port, které jsou k dispozici. V každém případě postupujte podle níže uvedených pokynů a nastavte adresu URL pro přesměrování v informace o registraci aplikace:<br/>
> - Přepněte zpět do *portál pro registraci aplikací* a nastavte `http://localhost:30662/` jako `Redirect URL`, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (ve kterém *[port]* je vlastní port TCP číslo) a klikněte na možnost "uložit.

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio pokyny pro získání adresy URL pro přesměrování
> Postupujte podle těchto kroků k získání adresy URL pro přesměrování:
> 1.    V **Průzkumníka řešení**, vyberte projekt a podívejte se na **vlastnosti** okna. Pokud se nezobrazí **vlastnosti** okna, stisknutím klávesy **F4**.
> 2.    Zkopírujte hodnotu z **URL** do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Přepněte zpět *portál pro registraci aplikací* a vložte tuto hodnotu jako **adresy URL pro přesměrování** a vyberte **uložit**


#### <a name="configure-your-javascript-spa"></a>Konfigurace vaší aplikace SPA v JavaScriptu

1.  V `index.html` soubor vytvořen během instalace projektu, přidejte informace o registraci aplikace. Přidejte následující kód v horní části v rámci `<script></script>` značky v těle vaše `index.html` souboru:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Nahraďte <code>Enter the application Id here</code> s Id aplikace, který jste právě zaregistrovali.
</li>
</ol>
