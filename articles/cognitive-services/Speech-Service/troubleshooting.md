---
title: Řešení potíží se službou Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Tento článek poskytuje informace, které vám pomohou při řešení problémů, se kterými se můžete setkat při používání sady Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815410"
---
# <a name="troubleshoot-the-speech-sdk"></a>Řešení potíží se sadou Speech SDK

Tento článek poskytuje informace, které vám pomohou při řešení problémů, se kterými se můžete setkat při používání sady Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Chyba: upgrade protokolu WebSocket se nezdařil s chybou ověřování (403)

Je možné, že máte nesprávný koncový bod pro vaši oblast nebo službu. Zkontrolujte identifikátor URI a ujistěte se, že je správný.

Může se také jednat o problém s klíčem předplatného nebo autorizačním tokenem. Další informace naleznete v následujícím oddílu.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Chyba: HTTP 403 zakázáno nebo HTTP 401 Neautorizováno

Tato chyba je často způsobena problémy při ověřování. Požadavky na připojení bez platného `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví jsou odmítnuty se stavem 403 nebo 401.

* Pokud pro ověřování používáte klíč předplatného, může se zobrazit chyba z těchto důvodů:

    - Klíč předplatného chybí nebo není platný.
    - Překročili jste kvótu využití vašeho předplatného.

* Pokud pro ověřování používáte autorizační token, může se zobrazit chyba z těchto důvodů:

    - Autorizační token je neplatný.
    - Platnost autorizačního tokenu vypršela.

### <a name="validate-your-subscription-key"></a>Ověření klíče předplatného

Spuštěním jednoho z následujících příkazů můžete ověřit, zda máte platný klíč předplatného.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` a `YOUR_REGION` vlastním klíčem předplatného a přidruženou oblastí.

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

Pokud jste zadali platný klíč předplatného, příkaz vrátí autorizační token, jinak se vrátí chyba.

### <a name="validate-an-authorization-token"></a>Ověření autorizačního tokenu

Pokud pro ověřování používáte autorizační token, spusťte jeden z následujících příkazů a ověřte, zda je autorizační token stále platný. Tokeny jsou platné po dobu 10 minut.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k vašemu předsdílenému zvukovému souboru. Nahraďte `YOUR_ACCESS_TOKEN` autorizačním tokenem vráceným v předchozím kroku. Nahraďte `YOUR_REGION` správnou oblastí.

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

Pokud jste zadali platný autorizační token, příkaz vrátí přepis pro váš zvukový soubor, jinak se vrátí chyba.

---

## <a name="error-http-400-bad-request"></a>Chyba: Chybná žádost HTTP 400

K této chybě obvykle dochází, když tělo požadavku obsahuje neplatná zvuková data. Podporován je pouze formát WAV. Zkontrolujte také hlavičky žádosti a ujistěte se, že jste zadali odpovídající hodnoty pro `Content-Type` a `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Chyba: časový limit žádosti HTTP 408

K chybě pravděpodobně dochází, protože do služby nejsou odesílána žádná zvuková data. Tato chyba může být také způsobena problémy se sítí.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" v odpovědi je "InitialSilenceTimeout"

K tomuto problému obvykle dochází v důsledku zvukových dat. Tato chyba se může zobrazit z těchto důvodů:

* Na začátku zvukového zařízení je dlouhé roztažení tichého. V takovém případě služba zastaví rozpoznávání po několika sekundách a vrátí `InitialSilenceTimeout` .

* Zvuk používá nepodporovaný formát kodeku, což způsobí, že zvuková data budou považována za netichá.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si poznámky k verzi.](releasenotes.md)
