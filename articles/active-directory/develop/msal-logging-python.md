---
title: Protokolování chyb a výjimek v MSAL pro Python
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL pro Python.
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
ms.openlocfilehash: f660bd17954afb4ae02da77e93d4d9cc36f3d355
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763420"
---
# <a name="logging-in-msal-for-python"></a>Protokolování v MSAL pro Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL pro protokolování v Pythonu

Přihlášení v MSAL Pythonu používá standardní mechanismus protokolování Pythonu, například `logging.info("msg")` můžete nakonfigurovat protokolování MSAL následujícím způsobem (a zobrazit ho v akci v [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Povolit protokolování ladění pro všechny moduly

Ve výchozím nastavení je protokolování v jakémkoli skriptu Pythonu vypnuté. Pokud chcete povolit protokolování ladění pro všechny moduly v celém skriptu Pythonu, použijte:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Protokolování pouze tichého MSAL

Chcete-li pouze tiché protokolování MSAL knihovny a povolit protokolování ladění ve všech ostatních modulech skriptu Python, vypněte protokolovací nástroj používaný MSAL Pythonem:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Osobní a organizační data v Pythonu

MSAL for Python neprotokoluje osobní data ani organizační data. Neexistuje žádná vlastnost pro zapnutí nebo vypnutí protokolování osobních nebo organizačních dat.

Pomocí standardního protokolování Pythonu se můžete přihlásit cokoli, co potřebujete, ale zodpovídáte za bezpečné zpracování citlivých dat a následujících zákonných požadavků.

Další informace o protokolování v Pythonu najdete v tématu protokolování v Pythonu  [: postupy](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Další kroky

Další ukázky kódu naleznete v tématu [Microsoft Identity Platform Code Samples)](sample-v2-code.md).

---