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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578749"
---
# <a name="logging-in-msal-for-python"></a>Protokolování v MSAL pro Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL pro protokolování v Pythonu

Přihlášení MSAL pro Python využívá [modul protokolování ve standardní knihovně Pythonu](https://docs.python.org/3/library/logging.html). Protokolování MSAL můžete nakonfigurovat následujícím způsobem (a zobrazit ho v akci v [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Povolit protokolování ladění pro všechny moduly

Ve výchozím nastavení je protokolování v jakémkoli skriptu Pythonu vypnuté. Pokud chcete povolit podrobné protokolování pro **všechny** moduly Pythonu ve skriptu, použijte `logging.basicConfig` s úrovní `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Tím se vytisknou všechny zprávy protokolu dané protokolovacímu modulu na standardní výstup.

### <a name="configure-msal-logging-level"></a>Konfigurace úrovně protokolování MSAL

Úroveň protokolování MSAL pro poskytovatele protokolu Pythonu můžete nakonfigurovat pomocí `logging.getLogger()` metody s názvem protokolovacího nástroje `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Konfigurace protokolování MSAL pomocí Azure App Insights

Protokoly Pythonu jsou předány obslužné rutině protokolu, která je ve výchozím nastavení `StreamHandler` . Pokud chcete odesílat protokoly MSAL do Application Insights pomocí klíče instrumentace, použijte `AzureLogHandler` poskytnutou `opencensus-ext-azure` knihovnu.

Pokud ho chcete nainstalovat, `opencensus-ext-azure` přidejte `opencensus-ext-azure` balíček ze PyPI do instalace závislostí nebo PIP:

```console
pip install opencensus-ext-azure
```

Pak změňte výchozí obslužnou rutinu `"msal"` poskytovatele protokolu na instanci `AzureLogHandler` s klíčem instrumentace nastaveným v `APP_INSIGHTS_KEY` proměnné prostředí:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Osobní a organizační data v Pythonu

MSAL for Python neprotokoluje osobní data ani organizační data. Neexistuje žádná vlastnost pro zapnutí nebo vypnutí protokolování osobních nebo organizačních dat.

Pomocí standardního protokolování Pythonu se můžete přihlásit cokoli, co potřebujete, ale zodpovídáte za bezpečné zpracování citlivých dat a následujících zákonných požadavků.

Další informace o protokolování v Pythonu najdete v tématu protokolování v Pythonu  [: postupy](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).
