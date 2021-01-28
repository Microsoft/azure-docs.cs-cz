---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954951"
---
## <a name="enable-logging"></a>Povolit protokolování

Pro pomoc při řešení potíží při ladění a selhání ověřování poskytuje knihovna Microsoft Authentication Library integrovanou podporu protokolování. Protokolování je každá knihovna zahrnuta v následujících článcích:

:::row:::
    :::column:::
        - [Protokolování v MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Protokolování v MSAL pro Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Protokolování v MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Protokolování v MSAL pro iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Protokolování v MSAL pro Javu](../articles/active-directory/develop/msal-logging-java.md)
        - [Protokolování v MSAL pro Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Zde jsou některé návrhy pro shromažďování dat:

- Uživatelé můžou požádat o pomoc, když budou mít problémy. Osvědčeným postupem je zaznamenat a dočasně ukládat protokoly. Zadejte umístění, kam mohou uživatelé nahrávat protokoly. MSAL poskytuje rozšíření protokolování pro zachycení podrobných informací o ověřování.

- Pokud je telemetrie k dispozici, povolte ji prostřednictvím MSAL, abyste mohli shromažďovat data o tom, jak se uživatelé přihlásí k vaší aplikaci.


## <a name="validate-your-integration"></a>Ověření integrace

Otestujte integraci podle [kontrolního seznamu integrace Microsoft Identity Platform](../articles/active-directory/develop/identity-platform-integration-checklist.md).
