---
title: Problémy v aplikaci Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js) v prohlížeči Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695853"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Známé problémy v prohlížečích Internet Explorer a Microsoft Edge (MSAL.js)

Microsoft Authentication Library pro JavaScript (MSAL.js) je generován pro [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) tak, aby mohl běžet v aplikaci Internet Explorer. Existuje však několik věcí, které je třeba vědět.

## <a name="run-an-app-in-internet-explorer"></a>Spuštění aplikace v Internet Exploreru
Pokud máte v úmyslu použít soubor MSAL.js v aplikacích, které lze spustit v aplikaci Internet Explorer, budete muset před odkazováním na skript MSAL.js přidat odkaz na polyvýplit příslib.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Důvodem je, že aplikace Internet Explorer nepodporuje javascriptové sliby nativně.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Ladění aplikace spuštěné v aplikaci Internet Explorer

### <a name="running-in-production"></a>Běh ve výrobě
Nasazení aplikace do produkčního prostředí (například ve webových aplikacích Azure) obvykle funguje dobře za předpokladu, že koncový uživatel přijal vyskakovací okno. Testovali jsme to s Internet Explorer 11.

### <a name="running-locally"></a>Místní spuštění
Pokud chcete spustit a ladit místně aplikace spuštěná v aplikaci Internet Explorer, musíte si být vědomi následujících *http://localhost:1234*aspektů (předpokládejme, že chcete spustit aplikaci jako ):

- Aplikace Internet Explorer má mechanismus zabezpečení s názvem "chráněný režim", který brání správnému fungování souboru MSAL.js. Mezi příznaky po přihlášení může být stránka přesměrována na http://localhost:1234/null.

- Chcete-li spustit a ladit aplikaci místně, budete muset zakázat tento "chráněný režim". Za tímto cílem:

    1. Klikněte na **Nástroje** aplikace Internet Explorer (ikona ozubeného kola).
    1. Vyberte **Možnosti Internetu** a potom na kartu **Zabezpečení.**
    1. Klikněte na zónu **Internet** a zrušte zaškrtnutí **políčka Povolit chráněný režim (vyžaduje restartování aplikace Internet Explorer).** Aplikace Internet Explorer varuje, že počítač již není chráněn. Klikněte na tlačítko **OK**.
    1. Restartujte aplikaci Internet Explorer.
    1. Spusťte a laděte aplikaci.

Po dokončení obnovte nastavení zabezpečení aplikace Internet Explorer.  Vyberte **Nastavení** -> **Možnosti** -> **Internetu: Zabezpečení** -> **Obnovte výchozí úroveň všech zón**.

## <a name="next-steps"></a>Další kroky
Další informace o [známých problémech při používání souboru MSAL.js v aplikaci Internet Explorer](msal-js-use-ie-browser.md).