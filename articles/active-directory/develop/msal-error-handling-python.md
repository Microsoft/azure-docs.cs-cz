---
title: Zpracování chyb a výjimek v MSAL pro Python
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, problémy s deklarací identity podmíněného přístupu a opakované pokusy v MSAL pro aplikace Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761093"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Zpracování chyb a výjimek v MSAL pro Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Zpracování chyb v MSAL pro Python

V MSAL pro Python je většina chyb přenášena jako návratová hodnota z volání rozhraní API. Tato chyba je reprezentována jako slovník obsahující odpověď JSON od platformy Microsoft identity.

* Úspěšná odpověď obsahuje `"access_token"` klíč. Formát odpovědi je definován protokolem OAuth2. Další informace najdete v tématu [5,1 úspěšná odezva](https://tools.ietf.org/html/rfc6749#section-5.1) .
* Chybná odpověď obsahuje `"error"` a obvykle `"error_description"` . Formát odpovědi je definován protokolem OAuth2. Další informace najdete v tématu [5,2 chybová odezva](https://tools.ietf.org/html/rfc6749#section-5.2) .

Když se vrátí chyba, `"error_description"` klíč obsahuje zprávu, která je čitelná pro čtení, která zase obvykle obsahuje kód chyby platformy Microsoft identity. Podrobnosti o různých kódech chyb najdete v tématu [kódy chyb ověřování a autorizace](./reference-aadsts-error-codes.md).

V MSAL pro Python jsou výjimky zřídka, protože většina chyb je zpracována vrácením hodnoty chyby. `ValueError`Výjimka je vyvolána pouze v případě, že dojde k potížím s postupem, jakým se pokoušíte použít knihovnu, například pokud jsou parametry rozhraní API poškozeny.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Další kroky

Zvažte možnost povolit [protokolování v MSAL pro Python](msal-logging-python.md) , které vám pomůžou diagnostikovat a ladit problémy.
