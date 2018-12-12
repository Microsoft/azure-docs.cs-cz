---
title: Řešení potíží s rozhraním Speech SDK – hlasové služby
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat, když použijete služby SDK řeči.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 04a1f3222b17d91889eb580d9d4e8206d8156d37
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095479"
---
# <a name="troubleshoot-the-speech-service-sdk"></a>Řešení potíží se službou Speech SDK

Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat, když použijete služby SDK řeči.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Chyba: Upgrade objektu websocket na straně se nezdařilo s chybou ověřování (403)

Může být nesprávné koncový bod pro oblast nebo službu. Zkontrolujte identifikátor URI, abyste měli jistotu, že je správný.

Také, pravděpodobně došlo k potížím s klíč předplatného nebo autorizační token. Další informace najdete v další části.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Chyba: HTTP 403 Zakázáno nebo HTTP 401 Neautorizováno

Tato chyba často způsobuje problémy s ověřováním. Požadavky na připojení bez platného `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví odmítají se stavem 403 nebo 401.

* Pokud používáte klíč předplatného pro ověřování, může zobrazit chyba, protože:

    - klíč předplatného je chybějící nebo neplatný
    - Překročili jste kvótu využití vašeho předplatného

* Pokud používáte autorizační token pro ověření, může zobrazit chyba, protože:

    - autorizační token je neplatný.
    - platnost autorizačního tokenu

### <a name="validate-your-subscription-key"></a>Ověřte váš klíč předplatného

Můžete ověřit, že máte klíč platné předplatné spuštěním jednoho z následujících příkazů.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` a `YOUR_REGION` s klíč předplatného a přidruženou oblastí.

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
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku. Nahraďte `YOUR_ACCESS_TOKEN` autorizačním tokenem vrácené v předchozím kroku. Nahraďte `YOUR_REGION` s správné oblastí.

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

---

## <a name="error-http-400-bad-request"></a>Chyba: HTTP 400 Chybný požadavek

K této chybě obvykle dochází, když text požadavku obsahuje neplatnou zvuková data. Pouze WAV formát je podporován. Zkontrolujte taky, ujistěte se, že jste zadejte příslušné hodnoty pro záhlaví požadavku `Content-Type` a `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Chyba: HTTP 408 žádosti vypršel časový limit

Chyba pravděpodobně dochází, protože žádné zvukové dat je odesíláno do služby. Tato chyba může být také způsobena problémy se sítí.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" v odpovědi je "InitialSilenceTimeout"

Tento problém je obvykle způsobeno zvuková data. Tato chyba může zobrazit, protože:

* Existuje dlouhý roztažení nečinnosti na začátku zvuku. V takovém případě služba zastaví uznání za několik sekund a vrátí `InitialSilenceTimeout`.

* Zvuk používá nepodporovaný kodek formátu, což způsobí, že zvukových dat jsou považovány za nečinnosti.

## <a name="next-steps"></a>Další postup

* [Přečtěte si poznámky k verzi](releasenotes.md)
