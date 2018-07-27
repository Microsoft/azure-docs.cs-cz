---
title: Rozpoznávání řeči služeb cognitive Services SDK Poradce při potížích
description: Řešení potíží Cognitive Services řeči SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284118"
---
# <a name="troubleshooting-speech-services-sdk"></a>Řešení potíží s hlasové služby SDK

Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat při použití sady SDK řeči.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Chyba `WebSocket Upgrade failed with an authentication error (403).`

Možná Chybný koncový bod pro oblast nebo službu. Zkontrolujte identifikátor URI, abyste měli jistotu, že je správný. Také naleznete v oddílu, protože může to být i problém s klíč předplatného nebo autorizační token.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Chyba `HTTP 403 Forbidden` nebo chyby `HTTP 401 Unauthorized`

Tato chyba je často způsobeno problémy s ověřováním. Požadavky na připojení bez platného `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví se odmítne kvůli stavu 401 nebo 403.

* Pokud používáte klíč předplatného pro ověření, příčinou může být:

    - klíč předplatného je chybějící nebo neplatný
    - Překročili jste kvótu využití vašeho předplatného

* Pokud používáte autorizační token pro ověření, příčinou může být:

    - autorizační token je neplatný.
    - platnost autorizačního tokenu

### <a name="validate-your-subscription-key"></a>Ověřte váš klíč předplatného

Můžete ověřit, abyste měli jistotu, že máte klíč platné předplatné spuštěním jednoho z níže uvedených příkazů.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` a `YOUR_REGION` s klíč předplatného a související oblasti, v uvedeném pořadí.

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

### <a name="validate-an-authorization-token"></a>Ověření tokenu autorizace

Pokud používáte autorizační token pro ověření, spusťte jeden z následujících příkazů ověřte, že ověřovací token, který je stále platný. Tokeny jsou platné po dobu 10 minut.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku `YOUR_ACCESS_TOKEN` autorizačním tokenem, který je vrácený v předchozím kroku, a `YOUR_REGION` s správné oblastí.

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
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Chyba `HTTP 400 Bad Request`

K této chybě obvykle dochází, když text požadavku obsahuje neplatnou zvuková data. Pouze `WAV` formát je podporován. Také zkontrolovat hlavičky žádosti, abyste měli jistotu, že zadáváte odpovídající `Content-Type` a `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Chyba `HTTP 408 Request Timeout`

Chyba je pravděpodobně, protože žádné zvukové dat je odesíláno do služby. Tato chyba může být způsobeno problémy se sítí.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` v odpovědi je `InitialSilenceTimeout`

Zvukových dat je obvykle důvodem příčiny problému. Příklad:

* Existuje dlouhý roztažení nečinnosti na začátku zvuku. Služba zastaví uznání za několik sekund a vrátit `InitialSilenceTimeout`.
* Zvuk používá nepodporovaný kodek formátu, což způsobí, že zvukových dat jsou považovány za nečinnosti.

## <a name="next-steps"></a>Další postup

* [Poznámky k verzi](releasenotes.md)

