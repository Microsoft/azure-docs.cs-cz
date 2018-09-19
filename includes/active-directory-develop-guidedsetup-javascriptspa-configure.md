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
ms.openlocfilehash: 569a7e74df3016fae133066607fdc0bc32c0044d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293797"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Chcete-li vytvořit aplikaci, vyberte jednu z nich několika způsoby:

### <a name="option-1-register-your-application-express-mode"></a>Možnost 1: Registrace vaší aplikace (expresní režim)
Teď musíte zaregistrovat svoji aplikaci *portál pro registraci aplikací Microsoft*:

1.  Registrace vaší aplikace prostřednictvím [portál pro registraci aplikací Microsoftu](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Zadejte název vaší aplikace a e-mailu
3.  Ujistěte se, že možnost *instalační program s asistencí* je zaškrtnuté políčko
4.  Postupujte podle pokynů k získání ID aplikace a vložte ho do svého kódu

### <a name="option-2-register-your-application-advanced-mode"></a>Možnost 2: Registrace vaší aplikace (rozšířený režim)

1. Přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app) pro registraci aplikace
2. Zadejte název vaší aplikace a e-mailu 
3. Ujistěte se, že možnost *instalační program s asistencí* není zaškrtnuta
4.  Klikněte na tlačítko `Add Platform`a pak vyberte `Web`
5. Přidat `Redirect URL` , které odpovídají na adresu URL vaší aplikace na webovém serveru. Naleznete níže uvedených částech pokyny o tom, jak nastavit nebo získat adresu URL pro přesměrování v sadě Visual Studio a Pythonu.
6. Klikněte na *Uložit*.

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio pokyny pro získání adresy URL pro přesměrování
> Postupujte podle pokynů a získat adresu URL přesměrování:
> 1.    V *Průzkumníka řešení*, vyberte projekt a podívejte se na `Properties` okna (Pokud se nezobrazí okno Vlastnosti, stiskněte klávesu `F4`)
> 2.    Zkopírujte hodnotu z `URL` do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Přepněte zpět *portál pro registraci aplikací* a vložte tuto hodnotu jako `Redirect URL` a klikněte na možnost "uložit.

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Nastavení adresy URL pro přesměrování pro uzel
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale teď můžete použít jakýkoli jiný port, které jsou k dispozici. V každém případě postupujte podle níže uvedených pokynů a nastavte adresu URL pro přesměrování v informace o registraci aplikace:<br/>
> - Přepněte zpět do *portál pro registraci aplikací* a nastavte `http://localhost:30662/` jako `Redirect URL`, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (ve kterém *[port]* je vlastní port TCP číslo) a klikněte na možnost "uložit.


#### <a name="configure-your-javascript-spa"></a>Konfigurace vaší aplikace SPA v JavaScriptu

1.  Vytvořte soubor s názvem `msalconfig.js` obsahující informace o registraci aplikace. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem a vyberte: `Add`  >  `New Item`  >  `JavaScript File`. Pojmenujte ji `msalconfig.js`
2.  Přidejte následující kód, který vaše `msalconfig.js` souboru:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Nahraďte <code>Enter_the_Application_Id_here</code> s Id aplikace, který jste právě zaregistrovali
</li>
</ol>
