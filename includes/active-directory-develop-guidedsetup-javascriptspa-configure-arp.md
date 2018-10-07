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
ms.openlocfilehash: c2b86e79f0364ee84e01fee5e9837db5a6b618a2
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842940"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace

V tomto kroku budete muset nakonfigurovat adresy URL přesměrování registračních informací vaší aplikace a pak ID aplikace přidejte do aplikace SPA v JavaScriptu.

### <a name="configure-redirect-url"></a>Nakonfigurujte adresu URL pro přesměrování

Konfigurace `Redirect URL` polí s adresou URL pro stránku index.html založené na vašem webovém serveru a potom klikněte na *aktualizace*.


> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio pokyny pro získání adresy URL pro přesměrování
> Postupujte podle těchto kroků k získání adresy URL pro přesměrování:
> 1.    V **Průzkumníka řešení**, vyberte projekt a podívejte se na **vlastnosti** okna. Pokud se nezobrazí **vlastnosti** okna, stisknutím klávesy **F4**.
> 2.    Zkopírujte hodnotu z **URL** do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Vložte tuto hodnotu jako **adresy URL pro přesměrování** horní části této stránky, klikněte na **aktualizace**

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Nastavení adresy URL pro přesměrování pro uzel
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale můžete použít další dostupný port. Postupujte podle níže uvedených pokynů a nastavte adresu URL pro přesměrování v informace o registraci aplikace:<br/>
> Nastavit `http://localhost:30662/` jako **adresy URL pro přesměrování** horní části této stránky, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (ve kterém *[port]* je vlastní číslo portu TCP) a potom klikněte na tlačítko  **Aktualizace**

### <a name="configure-your-javascript-spa-application"></a>Konfigurace aplikace SPA v JavaScriptu

1.  V `index.html` soubor vytvořen během instalace projektu, přidejte informace o registraci aplikace. Přidejte následující kód v horní části v rámci `<script></script>` značky v těle vaše `index.html` souboru:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
