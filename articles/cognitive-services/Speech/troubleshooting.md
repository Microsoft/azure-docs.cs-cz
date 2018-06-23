---
title: Řešení potíží s | Microsoft Docs
description: Jak řešit problémy při použití Microsoft řeči Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 04f3da19939d523d201d357b2b0293db1508431d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342632"
---
# <a name="troubleshooting"></a>Řešení potíží

## <a name="error-http-403-forbidden"></a>Chyba `HTTP 403 Forbidden`

Při použití funkce rozpoznávání řeči rozhraní API, vrátí `HTTP 403 Forbidden` chyby.

### <a name="cause"></a>Příčina

Tato chyba je často způsobeno problémy s ověřením. Požadavky na připojení bez platné `Ocp-Apim-Subscription-Key` nebo `Authorization` záhlaví odmítnula službu s `HTTP 403 Forbidden` odpovědi.

Pokud používáte klíč předplatného pro ověřování, může být z důvodu

- klíč předplatného je chybí nebo je neplatný
- dojde k překročení kvóty využití klíče předplatného
- `Ocp-Apim-Subscription-Key` pole není nastavena v hlavičce požadavku, při volání rozhraní REST API

Pokud používáte autorizační token pro ověřování, z následujících důvodů může způsobit chybu.

- `Authorization` chybí záhlaví v žádosti o při používání REST
- autorizační token uvedená v hlavičce autorizace je neplatný
- vypršela platnost tokenu autorizace. Přístupový token má vypršela platnost 10 minut

Další informace o ověřování najdete v tématu [ověřování](How-to/how-to-authentication.md) stránky.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

#### <a name="verify-that-your-subscription-key-is-valid"></a>Ověřte, zda je platný svůj klíč předplatného

Spuštěním následujícího příkazu pro ověření. Poznámka: Chcete-li nahradit *YOUR_SUBSCRIPTION_KEY* s svůj vlastní klíč předplatného. Pokud svůj klíč předplatného je platný, zobrazí se v odpovědi autorizační token jako token JSON Web Token (JWT). V opačném případě dojde k chybě v odpovědi.

> [!NOTE]
> Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj vlastní klíč předplatného.

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

Tento příklad používá curl v systému Linux s bash. Pokud není k dispozici na vaši platformu, musíte nainstalovat curl. V příkladu by měla fungovat taky na emulaci ve Windows, Git Bash, zsh a další součásti pro.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Ujistěte se, že používáte stejný klíč předplatného ve vaší aplikaci nebo v požadavku REST jako, který se používá výše.

#### <a name="verify-the-authorization-token"></a>Ověření tokenu autorizace

Tento krok je nutný pouze, pokud použijete autorizační token pro ověřování. Spusťte následující příkaz pro ověření, že autorizační token je stále platný. Příkaz odešle požadavek POST do služby a očekává zprávu odpovědi ze služby. Pokud se pořád zobrazí HTTP `403 Forbidden` chyby, zkontrolujte přístup token je správně nastavena ani s prošlou platností.

> [!IMPORTANT]
> Token má vypršela platnost 10 minut.
> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` s cestou k souboru připraveného obsahu zvuk a `YOUR_ACCESS_TOKEN` autorizačním tokenem, vrátí se v předchozím kroku.

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

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Chyba `HTTP 400 Bad Request`

Z tohoto důvodu je obvykle, že text žádosti obsahuje neplatná data zvuk. Momentálně podporujeme jenom WAV souboru.

## <a name="error-http-408-request-timeout"></a>Chyba `HTTP 408 Request Timeout`

Chyba je pravděpodobně vzhledem k tomu, že žádná zvuková data se odešle do služby a služby vrátí tuto chybu po vypršení časového limitu. Rozhraní API REST zvuková data měly být umístěny v textu požadavku.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` Je v odpovědi `InitialSilenceTimeout`

Zvuk dat je obvykle z důvodu příčiny problému. Například:

- Zvuk má dlouhou ticho čas na začátku. Služba se zastaví rozpoznávání po některé počet sekund a vrátí `InitialSilenceTimeout`.
- zvuk používá nepodporovaný kodek formátu, který umožňuje zvuková data považována za nečinnosti.
