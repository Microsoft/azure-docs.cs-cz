---
title: Zpracování chyb a výjimek v MSAL4J
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat chyby a výjimky, problémy s deklarací identity podmíněného přístupu a opakované pokusy v aplikacích MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760705"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Zpracování chyb a výjimek v MSAL pro Javu

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Zpracování chyb v MSAL pro Java

V MSAL pro jazyk Java existují tři typy výjimek: `MsalClientException` , `MsalServiceException` , a `MsalInteractionRequiredException` ; všechny, které dědí z `MsalException` .

- `MsalClientException` je vyvolána, když dojde k chybě, která je místní pro knihovnu nebo zařízení.
- `MsalServiceException` je vyvolána, když služba tokenů zabezpečení (STS) vrátí chybovou odpověď nebo dojde k jiné chybě sítě.
- `MsalInteractionRequiredException` je vyvolána, pokud je k úspěšnému ověření nutná interakce uživatelského rozhraní.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` zveřejňuje hlavičky HTTP vrácené v požadavcích na službu STS. Přístup k nim přes `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Jeden z běžných stavových kódů vrácených z MSAL pro Java při volání `AcquireTokenSilently()` je `InvalidGrantError` . To znamená, že před vydáním ověřovacího tokenu je nutné provést další zásahy uživatele. Vaše aplikace by měla znovu zavolat knihovnu ověřování, ale v interaktivním režimu odesláním `AuthorizationCodeParameters` nebo `DeviceCodeParameters` pro veřejné klientské aplikace.

Většinou v době, kdy se chyba `AcquireTokenSilently` nezdařila, je to proto, že mezipaměť tokenů nemá token, který by odpovídal vaší žádosti. Přístupové tokeny vyprší za jednu hodinu a `AcquireTokenSilently` pokusí se o získání nového na základě aktualizačního tokenu. V OAuth2 se jedná o tok obnovovacího tokenu. Tento tok může selhat také z různých důvodů, například když správce tenanta nakonfiguruje přísnější zásady přihlašování.

Některé podmínky, jejichž výsledkem je tato chyba, jsou pro uživatele snadno vyřešené. Například může být nutné přijmout podmínky použití nebo požadavek nelze splnit aktuální konfigurací, protože počítač se musí připojit ke konkrétní podnikové síti.

MSAL zpřístupňuje `reason` pole, které můžete použít k zajištění lepšího uživatelského prostředí. `reason`Pole může například vést k tomu, abyste uživateli oznámili, že platnost hesla vypršela nebo že budou muset vyjádřit souhlas s používáním některých prostředků. Podporované hodnoty jsou součástí  `InteractionRequiredExceptionReason` výčtu:

| Důvod | Význam | Doporučené zpracování |
|---------|-----------|-----------------------------|
| `BasicAction` | Podmínku lze vyřešit interakcí uživatele během interaktivního toku ověřování. | Zavolejte `acquireToken` pomocí interaktivních parametrů. |
| `AdditionalAction` | Podmínku lze vyřešit pomocí další nápravné interakce se systémem mimo tok interaktivního ověřování. | Voláním `acquireToken` interaktivních parametrů zobrazíte zprávu s vysvětlením nápravné akce, která má být provedena. Volající aplikace se může rozhodnout skrýt toky, které vyžadují další akci, pokud uživatel pravděpodobně nedokončí akci nápravy. |
| `MessageOnly` | V tuto chvíli nelze vyřešit podmínku. Spustit tok interaktivního ověřování, který zobrazí zprávu s vysvětlením podmínky. | Voláním `acquireToken` interaktivních parametrů zobrazíte zprávu, která vysvětluje podmínku. `acquireToken` Vrátí `UserCanceled` chybu poté, co uživatel přečte zprávu a zavře okno. Aplikace může zvolit, že se mají skrýt toky, které mají za následek zprávu, pokud se uživateli pravděpodobně nebudete moci vytěžit ze zprávy. |
| `ConsentRequired`| Chybí souhlas uživatele nebo byl odvolán. |Zavolejte `acquireToken` pomocí interaktivních parametrů, aby uživatel mohl udělit souhlas. |
| `UserPasswordExpired` | Vypršela platnost hesla uživatele. | Volání `acquireToken` s interaktivním parametrem, aby uživatel mohl resetovat heslo. |
| `None` |  Další podrobnosti jsou k dispozici. Tuto podmínku může vyřešit interakce uživatele během toku interaktivního ověřování. | Zavolejte `acquireToken` pomocí interaktivních parametrů. |

### <a name="code-example"></a>Příklad kódu

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Další kroky

Zvažte možnost povolit [protokolování v MSAL pro Java](msal-logging-java.md) , které vám pomůžou diagnostikovat a ladit problémy.
