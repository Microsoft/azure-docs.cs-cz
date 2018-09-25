---
title: Řešení potíží pro zpracování řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Jak řešit problémy při použití pro zpracování řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 532916106f62e0236b8dd53cf7988a648355aef4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991857"
---
# <a name="troubleshooting-bing-speech"></a>Řešení potíží pro zpracování řeči Bingu

## <a name="error-http-403-forbidden"></a>Chyba `HTTP 403 Forbidden`

Při použití rozhraní API pro rozpoznávání řeči, vrátí `HTTP 403 Forbidden` chyby.

### <a name="cause"></a>Příčina

Tato chyba je často způsobeno problémy s ověřováním. Požadavky na připojení bez platné `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví odmítnula této služby s využitím `HTTP 403 Forbidden` odpovědi.

Pokud používáte klíč předplatného pro ověřování, může být důvod

- klíč předplatného je chybějící nebo neplatný
- překročení kvóty využití klíč předplatného
- `Ocp-Apim-Subscription-Key` není nastavené pole v hlavičce žádosti při volání rozhraní REST API

Pokud ověřovací token, který používáte pro ověřování, z následujících důvodů může způsobit chybu.

- `Authorization` chybí hlavička v požadavku při používání REST
- autorizační token zadaný v autorizační hlavičce je neplatný.
- ověřovací token, který je neplatný. Přístupový token má vypršení platnosti 10 minut

Další informace o ověřování najdete v článku [ověřování](How-to/how-to-authentication.md) stránky.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

#### <a name="verify-that-your-subscription-key-is-valid"></a>Ověřte, zda je platný klíč předplatného.

Můžete spustit následující příkaz k ověření. Poznámka: Chcete-li nahradit *YOUR_SUBSCRIPTION_KEY* s klíči předplatného. Pokud váš klíč předplatného je platný, zobrazí se v odpovědi ověřovací token, který jako JSON Web Token (token JWT). V opačném případě dojde k chybě v odpovědi.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného.

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

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

V příkladu používá curl v Linuxu pomocí prostředí bash. Pokud není dostupný na vaší platformě, budete muset nainstalovat curl. V příkladu by měl také pracovat na Cygwin ve Windows, Git Bash, zsh a jiné prostředí.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Ujistěte se, že používáte stejný klíč předplatného ve vaší aplikaci nebo v požadavku REST, který je použitý výše.

#### <a name="verify-the-authorization-token"></a>Ověřit token autorizace

Tento krok je nutný pouze, pokud používáte autorizační token pro ověření. Spusťte následující příkaz k ověření, že ověřovací token, který je stále platný. Příkaz odešle požadavek POST do služby a očekává, že odpověď ze služby. Pokud se stále zobrazí HTTP `403 Forbidden` chyba, zkontrolujte přístup k tokenu je správně nastavena ani s prošlou platností.

> [!IMPORTANT]
> Token, který má vypršení platnosti 10 minut.
> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku a `YOUR_ACCESS_TOKEN` autorizačním tokenem vrácené v předchozím kroku.

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

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

## <a name="error-http-400-bad-request"></a>Chyba `HTTP 400 Bad Request`

Z tohoto důvodu je obvykle, že text požadavku obsahuje neplatnou zvuková data. Momentálně podporujeme pouze soubor WAV.

## <a name="error-http-408-request-timeout"></a>Chyba `HTTP 408 Request Timeout`

Chyba je pravděpodobně vzhledem k tomu, že žádná zvuková data se odesílají službě a službě vrátí tuto chybu po vypršení časového limitu. Rozhraní REST API zvuková data měly být umístěny v textu požadavku.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` v odpovědi je `InitialSilenceTimeout`

Zvukových dat je obvykle důvodem příčiny problému. Například:

- Zvuk má čas nečinnosti dlouho na začátku. Služba se zastaví rozpoznávání po několika sekundách a vrátí `InitialSilenceTimeout`.
- zvuk používá nepodporovaný kodek formátu, který umožňuje zvukových dat považovat za nečinnosti.
