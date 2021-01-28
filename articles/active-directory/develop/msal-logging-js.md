---
title: Protokolování chyb a výjimek v MSAL.js
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL.js
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954813"
---
# <a name="logging-in-msaljs"></a>Protokolování v MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Konfigurovat přihlašování MSAL.js

Povolit protokolování v MSAL.js (JavaScript) předáním objektu protokolovacího nástroje během konfigurace pro vytvoření `UserAgentApplication` instance. Tento objekt protokolovacího nástroje má následující vlastnosti:

- `localCallback`: instance zpětného volání, kterou může vývojář poskytnout pro využívání a publikování protokolů vlastním způsobem. Implementujte metodu localCallback v závislosti na tom, jak chcete přesměrovat protokoly.
- `level` (volitelné): konfigurovatelná úroveň protokolu. Podporované úrovně protokolu jsou: `Error` , `Warning` , a `Info` `Verbose` . Výchozí formát je `Info`.
- `piiLoggingEnabled` (volitelné): Pokud je nastavené na true, protokoluje osobní a organizační data. Ve výchozím nastavení je to false, aby vaše aplikace nehlásila osobní údaje. Protokoly osobních údajů se nikdy nezapisují do výchozích výstupů, jako je konzola, Logcat nebo NSLog.
- `correlationId` (volitelné): jedinečný identifikátor, který slouží k mapování požadavku na odezvu pro účely ladění. Ve výchozím nastavení se RFC4122 identifikátor GUID verze 4 (128 bitů).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).