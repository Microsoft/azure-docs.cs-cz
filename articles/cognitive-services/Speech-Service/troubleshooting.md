---
title: Poradce při potížích se službou Speech SDK - Speech Service
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace, které vám pomohou vyřešit problémy, se kterými se můžete setkat při použití sady Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815410"
---
# <a name="troubleshoot-the-speech-sdk"></a>Řešení potíží se sadou Speech SDK

Tento článek obsahuje informace, které vám pomohou vyřešit problémy, se kterými se můžete setkat při použití sady Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Chyba: Upgrade websocketu se nezdařil s chybou ověřování (403)

Pravděpodobně máte nesprávný koncový bod pro vaši oblast nebo službu. Zkontrolujte identifikátor URI a ujistěte se, že je správný.

Také může být problém s klíčem předplatného nebo autorizační token. Další informace naleznete v následujícím oddílu.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Chyba: HTTP 403 Zakázáno nebo HTTP 401 Neautorizováno

Tato chyba je často způsobena problémy s ověřováním. Požadavky na připojení `Ocp-Apim-Subscription-Key` bez `Authorization` platné nebo záhlaví jsou odmítnuty se stavem 403 nebo 401.

* Pokud používáte klíč předplatného pro ověřování, může se chyba zobrazit, protože:

    - Klíč předplatného chybí nebo je neplatný.
    - Překročili jste kvótu využití předplatného.

* Pokud používáte autorizační token pro ověřování, může se chyba zobrazit, protože:

    - Autorizační token je neplatný.
    - Platnost autorizačního tokenu vypršela.

### <a name="validate-your-subscription-key"></a>Ověření klíče předplatného

Můžete ověřit, že máte platný klíč předplatného spuštěním jednoho z následujících příkazů.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` a `YOUR_REGION` s vlastním klíčem předplatného a přidružené oblasti.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Pokud jste zadali platný klíč předplatného, příkaz vrátí token autorizace, jinak je vrácena chyba.

### <a name="validate-an-authorization-token"></a>Ověření autorizačního tokenu

Pokud používáte autorizační token pro ověřování, spusťte jeden z následujících příkazů a ověřte, zda je autorizační token stále platný. Tokeny jsou platné po dobu 10 minut.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k předem nahranému zvukovému souboru. Nahradit `YOUR_ACCESS_TOKEN` autorizační token vrácený v předchozím kroku. Nahraďte `YOUR_REGION` správnou oblastí.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Pokud jste zadali platný autorizační token, příkaz vrátí přepis zvukového souboru, jinak je vrácena chyba.

---

## <a name="error-http-400-bad-request"></a>Chyba: Chybný požadavek PROTOKOLU HTTP 400

K této chybě obvykle dochází, pokud tělo požadavku obsahuje neplatná zvuková data. Podporován je pouze formát WAV. Zkontrolujte také záhlaví požadavku a ujistěte se, `Content-Type` že `Content-Length`zadáte příslušné hodnoty pro a .

## <a name="error-http-408-request-timeout"></a>Chyba: Časový výtok požadavku HTTP 408

K chybě s největší pravděpodobností dochází, protože do služby nejsou odesílána žádná zvuková data. Tato chyba může být také způsobena problémy se sítí.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" v odpovědi je "InitialSilenceTimeout"

Tento problém je obvykle způsoben zvukovými daty. Tato chyba se může zobrazit, protože:

* Na začátku zvuku je dlouhý úsek ticha. V takovém případě služba zastaví rozpoznávání po několika `InitialSilenceTimeout`sekundách a vrátí .

* Zvuk používá nepodporovaný formát kodeku, který způsobí, že zvuková data mají být považována za ticho.

## <a name="next-steps"></a>Další kroky

* [Kontrola poznámek k verzi](releasenotes.md)
