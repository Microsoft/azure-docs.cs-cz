---
title: Použít Internet Explorer (Microsoft Authentication Library pro JavaScript)
titleSuffix: Microsoft identity platform
description: Přečtěte si o používání knihovny Microsoft Authentication Library pro JavaScript (MSAL. js) v prohlížeči Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c79717b00cd9a4b5da00496bf2f1822f7f77032
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802990"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Známé problémy v prohlížečích Internet Explorer a Microsoft Edge pomocí MSAL. js

Knihovna Microsoft Authentication Library pro JavaScript (MSAL. js) se vygeneruje pro [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , aby ji bylo možné spustit v Internet Exploreru. Existuje však několik věcí, které je třeba znát.

## <a name="run-an-app-in-internet-explorer"></a>Spuštění aplikace v Internet Exploreru
Pokud máte v úmyslu používat MSAL. js v aplikacích, které lze spustit v aplikaci Internet Explorer, bude nutné před odkazování na skript MSAL. js přidat odkaz na doplňování příslib.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Je to proto, že Internet Explorer nativně nepodporuje příslibů JavaScriptu.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Ladění aplikace spuštěné v aplikaci Internet Explorer

### <a name="running-in-production"></a>Běžící v produkčním prostředí
Nasazení aplikace do produkčního prostředí (například ve službě Azure Web Apps) normálně funguje bez problémů, pokud koncový uživatel přijal místní nabídky. Otestovali jsme ji pomocí aplikace Internet Explorer 11.

### <a name="running-locally"></a>Místní spuštění
Pokud chcete místně spustit a ladit aplikaci spuštěnou v aplikaci Internet Explorer, je třeba mít na paměti následující skutečnosti (Předpokládejme, že chcete aplikaci spustit jako *http://localhost:1234* ):

- Internet Explorer má bezpečnostní mechanismus s názvem chráněný režim, který brání správnému fungování MSAL. js. V rámci příznaků se po přihlášení může stránka přesměrovat na http://localhost:1234/null.

- Chcete-li aplikaci spustit a ladit místně, je nutné zakázat tento chráněný režim. Pro toto:

    1. Klikněte na **nástroje** Internet Explorer (ikona ozubeného kolečka).
    1. Vyberte **Možnosti Internetu** a pak kartu **zabezpečení** .
    1. Klikněte na zónu **Internetu** a zrušte políčko **Povolit chráněný režim (vyžaduje restart aplikace Internet Explorer)** . Internet Explorer upozorňuje, že váš počítač už není chráněný. Klikněte na **OK**.
    1. Restartujte aplikaci Internet Explorer.
    1. Spusťte a ladit aplikaci.

Až budete hotovi, obnovte nastavení zabezpečení aplikace Internet Explorer.  Vyberte **nastavení** -> **Možnosti internetu** -> **zabezpečení** -> **resetovat všechny zóny na výchozí úroveň**.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [známých problémech při používání MSAL. js v Internet Exploreru](msal-js-use-ie-browser.md).