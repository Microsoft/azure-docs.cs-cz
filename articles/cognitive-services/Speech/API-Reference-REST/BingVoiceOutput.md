---
title: rozhraní API pro převod textu na řeč služby Microsoft Speech Service | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Použití rozhraní API pro převod textu na řeč k zajištění převodu textu na řeč v reálném čase v různých hlasů a jazycích
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ee9b0b47fb88cba948bc06db6eb83fe9c076fe40
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966868"
---
# <a name="bing-text-to-speech-api"></a>ROZHRANÍ Bing pro převod textu na řeč

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Úvod

S textem Bingu pro Speech API může vaše aplikace odesílat požadavky HTTP na cloudový server, kde se text rychle vysyntetizuje pomocí lidského zvukového rozpoznávání řeči a vrátí se jako zvukový soubor. Toto rozhraní API je možné použít v mnoha různých kontextech k zajištění převodu textu na řeč v reálném čase v nejrůznějších různých hlasů a jazycích.

## <a name="VoiceSynReq"></a>Požadavek na hlasovou syntézu

### <a name="Subscription"></a>Autorizační token

Každá žádost o zpracování hlasové syntézy vyžaduje přístupový token JSON Web Token (JWT). Přístupový token JWT se předává v hlavičce žádosti o řeč. Platnost tokenu vypršela po dobu 10 minut. Informace o přihlášení k odběru a získání klíčů rozhraní API, které se používají k načtení platných přístupových tokenů JWT, najdete v tématu [Cognitive Services Subscription](https://azure.microsoft.com/try/cognitive-services/).

Klíč rozhraní API se předává službě tokenů. Příklad:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Následující informace hlavičky pro přístup k tokenu jsou následující.

Name| Formát | Popis
----|----|----
OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného

Tokenová služba vrátí přístupový token JWT jako `text/plain`. Pak se token JWT předává jako `Base64 access_token` koncový bod řeči jako autorizační hlavička s předponou a řetězcem. `Bearer` Příklad:

`Authorization: Bearer [Base64 access_token]`

Klienti musí pro přístup ke službě převod textu na řeč použít následující koncový bod:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Dokud nezískáte přístupový token s klíčem předplatného, jak je popsáno dříve, tento odkaz `403 Forbidden` vygeneruje chybu odpovědi.

### <a name="Http"></a>Hlavičky protokolu HTTP

Následující tabulka uvádí hlavičky protokolu HTTP, které se používají pro požadavky hlasové syntézy.

Záhlaví |Value |Komentáře
----|----|----
Typ obsahu | Application/SSML + XML | Vstupní typ obsahu.
X-Microsoft-OutputFormat | **1.** SSML-16khz-16bitový-mono-TTS <br> **2.** RAW-16khz-16bitový-mono-PCM <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** RIFF-16khz-16bitový-mono-PCM <br> **6.** audio-16khz-128kbitrate-mono-MP3 <br> **7.** audio-16khz-64kbitrate-mono-MP3 <br> **8.** audio-16khz-32kbitrate-mono-MP3 | Výstupní zvukový formát.
X-Search-AppId | GUID (jenom šestnáctkově, žádné pomlčky) | ID, které jedinečně identifikuje klientskou aplikaci. Může to být ID obchodu pro aplikace. Pokud není k dispozici, ID může být pro aplikaci vygenerované uživatelem.
X-Search-ClientID | GUID (jenom šestnáctkově, žádné pomlčky) | IDENTIFIKÁTOR, který jednoznačně identifikuje instanci aplikace pro každou instalaci.
User-Agent | Název aplikace | Název aplikace je povinný a musí mít méně než 255 znaků.
Authorization | Autorizační token |  Podívejte se na část <a href="#Subscription">autorizační token</a> .

### <a name="InputParam"></a>Vstupní parametry

Požadavky na rozhraní Bing text na Speech API se provádějí pomocí volání HTTP POST. Hlavičky jsou uvedené v předchozí části. Tělo obsahuje vstup SSML (Speech Shrnutí Markup Language), který představuje text, který se má syntetizovat. Popis značek používaných k řízení aspektů řeči, jako je například jazyk a pohlaví mluvčího, najdete v článku [specifikace W3C pro SSML](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maximální velikost podporovaného vstupu SSML je 1 024 znaků, včetně všech značek.

###  <a name="SampleVoiceOR"></a>Příklad: požadavek na výstup hlasu

Příkladem požadavku na výstup hlasu je následující:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Reakce na výstup hlasu

Rozhraní text Bingu API pro rozpoznávání řeči používá HTTP POST k odesílání zvuku zpátky klientovi. Odpověď rozhraní API obsahuje zvukový stream a kodek a odpovídá požadovanému výstupnímu formátu. Zvuk vrácený pro daný požadavek nesmí být delší než 15 sekund.

### <a name="SuccessfulRecResponse"></a>Příklad: úspěšná syntéza – odpověď

Následující kód je příkladem odpovědi JSON na úspěšný požadavek na syntézu hlasu. Komentáře a formátování kódu jsou pouze pro účely tohoto příkladu a jsou vynechány ze skutečné odpovědi.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Příklad: Chyba syntézy

Následující příklad kódu ukazuje odpověď JSON na selhání dotazu na hlasovou syntézu:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Chybové odpovědi

Chyba | Popis
----|----
Chybný požadavek HTTP/400 | Povinný parametr chybí, je prázdný nebo má hodnotu null nebo hodnota předaná buď požadovanému nebo volitelnému parametru není platná. Jedním z důvodů, proč je získání "neplatné" odpovědi předání řetězcové hodnoty delší než povolená délka. K dispozici je stručný popis problematického parametru.
HTTP/401 Neautorizováno | Požadavek není autorizovaný.
HTTP/413 RequestEntityTooLarge  | Vstup SSML je větší, než je podporováno.
HTTP/502 BadGateway | Došlo k potížím souvisejícím se sítí nebo k problému na straně serveru.

Příklad chybové odpovědi je následující:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Změna hlasového výstupu prostřednictvím SSML

ROZHRANÍ Microsoft pro převod textu na řeč podporuje SSML 1,0, jak je definované ve [verzi SSML (W3C Speech Markup Language) 1,0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). V této části jsou uvedeny příklady měnící se určité charakteristiky vygenerovaného hlasového výstupu, jako je například míra speaking, výslovnost atd. pomocí značek SSML.

1. Přidávání přerušení

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Mluvy frekvence změny

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Výslovnost

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Změnit svazek

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Změnit výšku

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Změnit Prosody Rozvrh

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Všimněte si, že zvuková data musí být 8k nebo 16. wav zaznamenaná v následujícím formátu: **Kód CRC** (CRC-32): 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0xFFFFFFFF; **Příznak zvukového formátu**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0xFFFFFFFF; **Počet vzorků**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0x7FFFFFFF; **Velikost binárního textu**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0x7FFFFFFF; **Binární tělo**: n bajtů.

## <a name="SampleApp"></a>Ukázková aplikace

Podrobné informace o implementaci najdete v [tématu C#ukázková aplikace pro převod textu na řeč v aplikaci Visual .NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Podporovaná místní a hlasová písma

Následující tabulka uvádí některé z podporovaných národních prostředí a související hlasová písma.

Národní prostředí | Pohlaví | Název mapování služby
---------|--------|------------
ar – EG * | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)"
ar-SA | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)"
bg-BG | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)"
ES certifikační autority | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)"
cs-CZ | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)"
da-DK | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)"
de-AT | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)"
de-CH | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)"
de-DE | Žena | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Hedda)
de-DE | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)"
de-DE | Muž | Hlasový Převod textu na řeč pro Microsoft Server Speech (de-DE, Stefan, Apollo)
el-GR | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)"
cs AU | Žena | Microsoft Server Speech Převod textu na řeč Voice (EN-AU, Catherine)
cs AU | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, HayleyRUS)"
cs CA | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, Lenka)"
cs CA | Žena | "Microsoft serveru řeči Text na řeč hlas (en-CA, HeatherRUS)"
en-GB | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, Susan, Apollo)"
en-GB | Žena | "Microsoft serveru řeči Text na řeč hlas (en-GB, HazelRUS)"
en-GB | Muž | "Microsoft serveru řeči Text na řeč hlas (en-GB, George, Apollo)"
cs IE | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IE, Sean)"
en-IN | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, Heera, Apollo)"
en-IN | Žena | "Microsoft serveru řeči Text na řeč hlas (en-IN, PriyaRUS)"
en-IN | Muž | "Microsoft serveru řeči Text na řeč hlas (en-IN, Ravi, Apollo)"
en-US | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)"
en-US | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)"
en-US | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)"
es-ES | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)"
es-ES | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)"
es-ES | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)"
es-MX | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)"
es-MX | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)"
fi-FI | Žena | "Microsoft serveru řeči Text na řeč hlas (fi-FI, HeidiRUS)"
fr-CA | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)"
fr-CA | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)"
FR-CH | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)"
fr-FR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)"
fr-FR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)"
fr-FR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)"
he-IL| Muž| "Microsoft serveru řeči Text na řeč hlas (he-IL, Asaf)"
Dobrý den – v | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana, Apollo)"
Dobrý den – v | Žena | "Microsoft serveru řeči Text na řeč hlas (hi v, Kalpana)"
Dobrý den – v | Muž | "Microsoft serveru řeči Text na řeč hlas (hi v, Hemant)"
hr-HR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)"
hu-HU | Muž | "Microsoft serveru řeči Text na řeč hlas (hu-HU, Szabolcs)"
ID ID | Muž | "Microsoft serveru řeči Text na řeč hlas (id-ID, Andika)"
IT-IT | Muž | "Microsoft serveru řeči Text hlas řeči (it-IT, Cosimo, Apollo)"
IT-IT | Žena | "Microsoft serveru řeči Text na řeč hlas (it-IT, LuciaRUS)"
ja-JP | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)"
ja-JP | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)"
ja-JP | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)"
ko-KR | Žena | "Microsoft serveru řeči Text na řeč hlas (ko-KR, HeamiRUS)"
Moje MS | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)"
nb-NO | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)"
NL-NL | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)"
pl-PL | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)"
pt-BR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)"
pt-BR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)"
pt-PT | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)"
ro RO | Muž | "Microsoft serveru řeči Text na řeč hlas (ro-RO, Andrei)"
ru-RU | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)"
ru-RU | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)"
ru-RU | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, EkaterinaRUS)"
sk-SK | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)"
sl SI | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl Incidentech Lado)"
sv-SE | Žena | "Microsoft serveru řeči Text na řeč hlas (sv-SE, HedvigRUS)"
ta v | Muž | "Microsoft serveru řeči Text na řeč hlas, (ta v, Valluvar)"
th TH | Muž | "Microsoft Server řeči převod textu na řeč hlasové (th TÝ, Pattara)"
tr-TR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)"
vi-VN | Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)"
zh-CN | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, HuihuiRUS)"
zh-CN | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Yaoyao, Apollo)"
zh-CN | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-CN, Kangkang, Apollo)"
zh-HK | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Tracy, Apollo)"
zh-HK | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-HK, TracyRUS)"
zh-HK | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-HK, Danny, Apollo)"
zh-TW | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Yating, Apollo)"
zh-TW | Žena | "Microsoft serveru řeči Text na řeč hlas (zh-TW, HanHanRUS)"
zh-TW | Muž | "Microsoft serveru řeči Text na řeč hlas (zh-TW, Zhiwei, Apollo)"

 \* ar-EG podporuje moderní standardní arabštinu (MSA).

> [!NOTE]
> Všimněte si, že předchozí služba pojmenování **Microsoft Server speech převod textu na řeč Voice (cs-cz, vit)** a **microsoft Server Speech převod textu na řeč Voice (EN-IE, Shaun)** se po 3/31/2018 zastaralá za účelem optimalizace rozhraní API pro zpracování řeči Bingu vestavěn. Aktualizujte prosím svůj kód s aktualizovanými názvy.

## <a name="TrouNSupport"></a>Řešení potíží a podpora

Publikujte všechny otázky a problémy do fóra [služby zpracování řeči Bingu Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) na webu MSDN. Uveďte kompletní podrobnosti, jako například:

* Příklad úplného řetězce požadavku
* V případě potřeby úplný výstup neúspěšné žádosti, včetně ID protokolů.
* Procento neúspěšných požadavků.
