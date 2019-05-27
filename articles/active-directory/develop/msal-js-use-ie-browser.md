---
title: Pomocí aplikace Internet Explorer (Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o použití knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) s prohlížeč Internet Explorer.
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
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873912"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Známé problémy v prohlížeče Internet Explorer a Microsoft Edge s MSAL.js

Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) je generován pro [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) tak, aby mohly běžet v aplikaci Internet Explorer. Existují však byste měli vědět pár věcí.

## <a name="run-an-app-in-internet-explorer"></a>Spuštění aplikace v aplikaci Internet Explorer
Pokud máte v úmyslu používat MSAL.js v aplikacích, které můžou běžet v aplikaci Internet Explorer, musíte přidat odkaz na kód promise před odkazující na skript MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Je to proto, že aplikace Internet Explorer JavaScript příslibů nativně nepodporuje.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Ladění aplikace běžící v aplikaci Internet Explorer

### <a name="running-in-production"></a>Spouštění v produkčním prostředí
Nasazení aplikace do produkčního prostředí (například ve službě Azure Web apps) běžně funguje bez problémů, za předpokladu, že koncový uživatel přijal automaticky otevíraná okna. Otestovali jsme ho s aplikací Internet Explorer 11.

### <a name="running-locally"></a>Místně spuštěná
Pokud chcete spustit místní a ladění aplikace běžící v aplikaci Internet Explorer, budete muset mít na paměti následující aspekty (Předpokládejme, že chcete spustit vaši aplikaci ve formě *http://localhost:1234*):

- Aplikace Internet Explorer nabízí mechanismus zabezpečení s názvem "chráněný režim", což zabrání MSAL.js podle vašich představ. Mezi příznaky, po přihlášení, na stránce je možné přesměrovat do http://localhost:1234/null.

- Ke spuštění a ladění vaší aplikace v místním prostředí, budete muset zakázat tento "chráněný režim". Pro toto:

    1. Klikněte na aplikaci Internet Explorer **nástroje** (ikona ozubeného kolečka).
    1. Vyberte **Možnosti Internetu** a pak **zabezpečení** kartu.
    1. Klikněte na **Internet** zóny a zrušte zaškrtnutí políčka **Povolit chráněný režim (vyžaduje restartování aplikace Internet Explorer)**. Aplikace Internet Explorer vás upozorní, že je počítač už chráněný. Klikněte na **OK**.
    1. Restartujte aplikaci Internet Explorer.
    1. Spuštění a ladění vaší aplikace.

Jakmile budete hotovi, obnovení nastavení zabezpečení aplikace Internet Explorer.  Vyberte **nastavení** -> **Možnosti Internetu** -> **zabezpečení** -> **obnovíte výchozí úroveňvšechnyzóny**.

## <a name="next-steps"></a>Další postup
Další informace o [– známé problémy při použití v aplikaci Internet Explorer MSAL.js](msal-js-use-ie-browser.md).