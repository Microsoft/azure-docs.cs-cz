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
ms.openlocfilehash: fc06da3b1ad66aa15237a25d2f50374043c860ba
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293706"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace

V tomto kroku budete muset nakonfigurovat adresy URL přesměrování registračních informací vaší aplikace a pak ID aplikace přidejte do aplikace SPA v JavaScriptu.

### <a name="configure-redirect-url"></a>Nakonfigurujte adresu URL pro přesměrování

Konfigurace `Redirect URL` polí s adresou URL pro stránku index.html založené na vašem webovém serveru a potom klikněte na *aktualizace*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio pokyny pro získání adresy URL pro přesměrování
> Získat adresu URL přesměrování:
> 1.    V *Průzkumníka řešení*, vyberte projekt a podívejte se na `Properties` okna (Pokud se nezobrazí okno Vlastnosti, stiskněte klávesu `F4`)
> 2.    Zkopírujte hodnotu z `URL` do schránky:<br/> ![Vlastnosti projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Vložte tuto hodnotu jako `Redirect URL` horní části této stránky, klikněte na `Update`

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Nastavení adresy URL pro přesměrování pro uzel
> Pro Node.js, můžete nastavit na webu port serveru v *server.js* souboru. Tento kurz používá port 30662 pro odkaz, ale teď můžete použít jakýkoli jiný port, které jsou k dispozici. Chcete-li nastavit adresu URL pro přesměrování v informace o registraci aplikace v každém případě použijte následující pokyny:<br/>
> Nastavte `http://localhost:30662/` jako `Redirect URL` horní části této stránky, nebo použijte `http://localhost:[port]/` Pokud používáte vlastní port TCP (ve kterém *[port]* je vlastní číslo portu TCP) a potom kliknutím na tlačítko 'Aktualizovat'

### <a name="configure-your-javascript-spa-application"></a>Konfigurace aplikace SPA v JavaScriptu

1.  Vytvořte soubor s názvem `msalconfig.js` obsahující informace o registraci aplikace. Pokud používáte Visual Studio, vyberte projekt (Kořenová složka projektu), klikněte pravým tlačítkem a vyberte: `Add`  >  `New Item`  >  `JavaScript File`. Pojmenujte ji `msalconfig.js`
2.  Přidejte následující kód, který vaše `msalconfig.js` souboru:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
