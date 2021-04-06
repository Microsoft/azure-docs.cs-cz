---
title: Protokolování chyb a výjimek v MSAL pro iOS/macOS
titleSuffix: Microsoft identity platform
description: Naučte se protokolovat chyby a výjimky v MSAL pro iOS/macOS.
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763402"
---
# <a name="logging-in-msal-for-iosmacos"></a>Protokolování v MSAL pro iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL pro iOS a macOS protokolování – ObjC

Nastavte zpětné volání pro zachycení protokolování MSAL a zahrňte ho do protokolování vlastní aplikace. Signatura zpětného volání vypadá takto:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Například:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Osobní údaje

Ve výchozím nastavení MSAL nezachytí ani neprotokoluje žádná osobní data. Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím vlastnosti ve třídě MSALLogger. Když zapnete `pii.Enabled` , aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a následujících zákonných požadavků.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Úrovně protokolování

Pokud chcete nastavit úroveň protokolování při protokolování pomocí MSAL pro iOS a macOS, použijte jednu z následujících hodnot:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat všechna protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi řetězce klíčů |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Například:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formát zprávy protokolu

Část zprávy protokolu MSAL je ve formátu `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Například:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování ID korelace a časových razítek je užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediným spolehlivým místem, kde se dají načíst, je ze zpráv protokolu MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL pro iOS a protokolování macOS – SWIFT

Nastavte zpětné volání pro zachycení protokolování MSAL a zahrňte ho do protokolování vlastní aplikace. Signatura (reprezentovaná v cíli-C) pro zpětné volání vypadá takto:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Například:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Osobní údaje

Ve výchozím nastavení MSAL nezachytí ani neprotokoluje žádná osobní data. Knihovna umožňuje vývojářům aplikací tuto možnost zapínat prostřednictvím vlastnosti ve třídě MSALLogger. Když zapnete `pii.Enabled` , aplikace vezme zodpovědnost za bezpečné zpracování vysoce citlivých dat a následujících zákonných požadavků.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Úrovně protokolování

Pokud chcete nastavit úroveň protokolování při protokolování pomocí MSAL pro iOS a macOS, použijte jednu z následujících hodnot:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Zakázat všechna protokolování |
| `MSALLogLevelError` | Výchozí úroveň, vytiskne informace pouze v případě, že dojde k chybám |
| `MSALLogLevelWarning` | Upozornění |
| `MSALLogLevelInfo` |  Vstupní body knihovny s parametry a různými operacemi řetězce klíčů |
|`MSALLogLevelVerbose`     |  Trasování rozhraní API |

Například:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formát zprávy protokolu

Část zprávy protokolu MSAL je ve formátu `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Například:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Poskytování ID korelace a časových razítek je užitečné pro sledování problémů. Informace o časovém razítku a ID korelace jsou k dispozici ve zprávě protokolu. Jediným spolehlivým místem, kde se dají načíst, je ze zpráv protokolu MSAL.

---

## <a name="next-steps"></a>Další kroky

Další ukázky kódu najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md).