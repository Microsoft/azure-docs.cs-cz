---
title: Řešení potíží s Zpracování řeči Bingu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Řešení potíží při použití Zpracování řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f222d9d4cf6c56dea0832938dcb132cf711491bc
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934060"
---
# <a name="troubleshooting-bing-speech"></a>Řešení potíží s Zpracování řeči Bingu

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Chyba`HTTP 403 Forbidden`

Při použití rozhraní API pro rozpoznávání řeči se vrátí `HTTP 403 Forbidden` chyba.

### <a name="cause"></a>Příčina

Tato chyba je často způsobena problémy s ověřováním. Služba s `Ocp-Apim-Subscription-Key` `Authorization` odpovědíodmítlapožadavkynapřipojeníbez`HTTP 403 Forbidden` platného nebo hlavičky.

Pokud pro ověřování používáte klíč předplatného, důvod může být

- klíč předplatného chybí nebo není platný.
- překročila se kvóta využití klíče předplatného.
- `Ocp-Apim-Subscription-Key` pole není nastavené v hlavičce požadavku, když se zavolá REST API.

Pokud pro ověřování používáte autorizační token, může to způsobit následující důvody.

- v požadavku chybí záhlaví při použití REST. `Authorization`
- autorizační token zadaný v autorizační hlavičce je neplatný.
- platnost autorizačního tokenu vypršela. Platnost přístupového tokenu vypršela 10 minut.

Další informace o ověřování najdete na stránce [ověřování](How-to/how-to-authentication.md) .

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

#### <a name="verify-that-your-subscription-key-is-valid"></a>Ověřte, jestli je klíč předplatného platný.

Pro ověření můžete spustit následující příkaz. Poznámka: Nahraďte *YOUR_SUBSCRIPTION_KEY* vlastním klíčem předplatného. Pokud je klíč předplatného platný, dostanete v odpovědi autorizační token jako JSON Web Token (JWT). V opačném případě se zobrazí chyba v reakci.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` vlastním klíčem předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

V příkladu se používá oblé v systému Linux s bash. Pokud není na vaší platformě k dispozici, bude pravděpodobně nutné nainstalovat oblé. Příklad by měl fungovat taky na Cygwin ve Windows, Gitu bash, zsh a dalších prostředích.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Ujistěte se, že používáte stejný klíč předplatného v aplikaci nebo v žádosti REST, jak se používá výše.

#### <a name="verify-the-authorization-token"></a>Ověření autorizačního tokenu

Tento krok je potřeba jenom v případě, že pro ověřování používáte autorizační token. Spusťte následující příkaz a ověřte, zda je autorizační token stále platný. Příkaz odešle službě požadavek POST a očekává ze služby zprávu s odpovědí. Pokud se stále zobrazuje chyba `403 Forbidden` protokolu HTTP, dvakrát ověřte, že je přístupový token nastavený správně a že nevypršela jeho platnost.

> [!IMPORTANT]
> Platnost tokenu vypršela 10 minut.
> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k zadanému zvukovému souboru a `YOUR_ACCESS_TOKEN` pomocí autorizačního tokenu vráceného v předchozím kroku.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Chyba`HTTP 400 Bad Request`

Důvodem je obvykle to, že tělo žádosti obsahuje neplatná zvuková data. V současné době podporujeme jenom soubor WAV.

## <a name="error-http-408-request-timeout"></a>Chyba`HTTP 408 Request Timeout`

Tato chyba je pravděpodobně způsobena tím, že se službě neodesílají žádná zvuková data a služba vrátí tuto chybu po vypršení časového limitu. V případě REST API by se měla zvuková data umístit do textu žádosti.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` V odpovědi je`InitialSilenceTimeout`

Zvuková data jsou obvykle příčinou problému. Například

- zvuk má na začátku dlouhou dobu ticha. Služba zastaví rozpoznávání po určitém počtu sekund a vrátí `InitialSilenceTimeout`.
- zvuk používá nepodporovaný formát kodeku, což způsobuje, že zvuková data budou považována za netichá.
