---
title: Rozpoznávání řeči kognitivní služby SDK řešení potíží s | Microsoft Docs
description: Řešení problémů čistá kognitivní služeb řeči SDK
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343420"
---
# <a name="troubleshooting-speech-services-sdk"></a>Řešení potíží s řeči služby SDK

Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat při používání sady SDK řeči.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Chyba `WebSocket Upgrade failed with an authentication error (403).`

Můžete mít nesprávný koncový bod pro oblast nebo službě. Zkontrolujte identifikátor URI a ujistěte se, zda že jsou správná. Také najdete v další části, jak může se také jednat o problém s klíč předplatného nebo autorizační token.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Chyba `HTTP 403 Forbidden` nebo Chyba `HTTP 401 Unauthorized`

Tato chyba je často způsobeno problémy s ověřením. Požadavky na připojení bez platné `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví se odmítne kvůli stavu 401 nebo 403.

* Pokud používáte klíč předplatného pro ověřování, může být příčinou:

    - klíč předplatného je chybí nebo je neplatný
    - došlo k překročení kvóty využití vaše předplatné

* Pokud používáte autorizační token pro ověřování, může být příčinou:

    - autorizační token je neplatný
    - vypršela platnost tokenu autorizace

### <a name="validate-your-subscription-key"></a>Ověřit svůj klíč předplatného

Můžete ověřit, zda že máte platné předplatné klíč spuštěním jednoho z níže uvedených příkazů.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` a `YOUR_REGION` s vlastní klíč předplatného a přidružené oblasti, v uvedeném pořadí.

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

### <a name="validate-an-authorization-token"></a>Ověření autorizační token

Pokud používáte autorizační token pro ověřování, spusťte jeden z následujících příkazů ověřte, zda autorizační token je stále platný. Tokeny jsou platné po dobu 10 minut.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` s cestou k souboru připraveného obsahu zvuk `YOUR_ACCESS_TOKEN` autorizačním tokenem, vrátí se v předchozím kroku, a `YOUR_REGION` s správnou oblast.

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

Této chybě obvykle dochází, když text požadavku obsahuje neplatná data zvuk. Pouze `WAV` formát je podporován. Také zkontrolujte hlavičky žádosti a ujistěte se, určíte odpovídající `Content-Type` a `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Chyba `HTTP 408 Request Timeout`

Chyba je pravděpodobně, protože služba je odesílána žádná zvuková data. Tato chyba může být způsobeno problémy se sítí.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` Je v odpovědi `InitialSilenceTimeout`

Zvuk dat je obvykle z důvodu příčiny problému. Příklad:

* Je dlouhá úseku nečinnosti na začátku zvukovém souboru. Služba zastaví rozpoznávání za několik sekund a vrátí `InitialSilenceTimeout`.
* Zvuk používá nepodporovaný kodek formátu, což způsobí, že zvuk data, která mají být považované za nečinnosti.

## <a name="next-steps"></a>Další postup

* [Poznámky k verzi](releasenotes.md)

