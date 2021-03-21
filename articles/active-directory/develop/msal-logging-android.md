---
title: Protokolování chyb a výjimek v MSAL pro Android.
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL pro Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954880"
---
# <a name="logging-in-msal-for-android"></a>Protokolování v MSAL pro Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Přihlášení MSAL pro Android pomocí Java

Zapněte při vytváření aplikace přihlášení vytvořením zpětného volání protokolování. Zpětné volání přebírá tyto parametry:

- `tag` je řetězec předaný do zpětného volání knihovny. Je spojena s položkou protokolu a lze ji použít k řazení zpráv protokolování.
- `logLevel` umožňuje rozhodnout, jakou úroveň protokolování chcete. Podporované úrovně protokolu jsou: `Error` , `Warning` , a `Info` `Verbose` .
- `message` je obsah položky protokolu.
- `containsPII` Určuje, jestli se mají protokolovat zprávy obsahující osobní údaje nebo data organizace. Ve výchozím nastavení je tato hodnota nastavená na false, aby vaše aplikace neprotokoloval osobní údaje. `containsPII`V případě `true` , tato metoda obdrží zprávy dvakrát: jednou s `containsPII` parametrem nastaveným na `false` a `message` bez osobních údajů a podruhé s `containsPii` parametrem nastaveným na `true` a zpráva může obsahovat osobní údaje. V některých případech (Pokud zpráva neobsahuje osobní údaje), bude zpráva stejná.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Protokolovací nástroj MSAL ve výchozím nastavení nezachytí žádné osobní údaje ani organizační údaje, které by se vám mohly identifikovat.
Postup povolení protokolování osobních identifikovatelných informací nebo organizačních informací:

```java
Logger.getInstance().setEnablePII(true);
```

Postup zakázání protokolování osobních údajů a dat organizace:

```java
Logger.getInstance().setEnablePII(false);
```

Ve výchozím nastavení je protokolování do Logcat zakázané. Povolení:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).