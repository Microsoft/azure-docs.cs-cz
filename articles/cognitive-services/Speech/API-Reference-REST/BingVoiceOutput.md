---
title: Převod textu na řeč rozhraní API služby Microsoft řeči | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Převod textu na řeč rozhraní API používat k poskytování převodu převod textu na řeč v reálném čase v různých hlasy a jazyky
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ms.openlocfilehash: db69a9e3beb819600109603a8c0129547db57fa5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343023"
---
# <a name="bing-text-to-speech-api"></a>Převod textu na řeč z Bingu rozhraní API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Úvod

Vaše aplikace se Bingu převod textu na řeč rozhraní API, můžete odeslat požadavky HTTP cloudovými servery, kde text okamžitě syntetizovat do mluvené řeči lidských to nezní a vrátí jako zvukový soubor. Toto rozhraní API můžete použít v mnoha různých kontextech kvůli převodu převod textu na řeč v reálném čase v řadě různých hlasy a jazyky.

## <a name="VoiceSynReq"></a>Žádost o syntézu hlasu

### <a name="Subscription"></a>Autorizační token

Každý hlas syntézu požadavek vyžaduje přístupového tokenu JSON Web Token (JWT). Přístupový token JWT se předává v hlavičce žádosti řeči. Token, který má čas vypršení platnosti 10 minut. Informace o odběru a získání klíče rozhraní API, které slouží k načtení platné přístupové tokeny JWT, naleznete v tématu [předplatné služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Klíč rozhraní API je předán do tokenu služby. Příklad:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Informace o požadované záhlaví pro přístup pomocí tokenu je následujícím způsobem.

Název| Formát | Popis
----|----|----
OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného

Vrátí token služby přístupový token JWT jako `text/plain`. Pak tokenů JWT je předán jako `Base64 access_token` ke koncovému bodu řeči jako hlavičku autorizace řetězec s předponou `Bearer`. Příklad:

`Authorization: Bearer [Base64 access_token]`

Následující koncový bod musí ho klienti používat pro přístup k převod textu na řeč služby:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Dokud jste získali přístupový token s klíči předplatného, jak je popsáno výše, generuje tento odkaz `403 Forbidden` Chyba odpovědi.

### <a name="Http"></a>Hlavičky protokolu HTTP

V následující tabulce jsou uvedeny hlavičky protokolu HTTP, které se používají pro hlasové požadavky syntézu.

Hlavička |Hodnota |Komentáře
----|----|----
Typ obsahu | aplikace/ssml + xml | Vstupní typ obsahu.
X-Microsoft-OutputFormat | **1.** ssml – 16 khz – 16 bitů-mono – převod textu na řeč <br> **2.** nezpracovaná – 16 khz – 16 bitů-mono-pcm <br>**3.** zvuk – 16 khz – 16 kb/s – mono-siren <br> **4.** riff – 16 khz – 16 kb/s – mono-siren <br> **5.** riff – 16 khz – 16 bitů-mono-pcm <br> **6.** zvuk – 16 khz-128kbitrate-mono-mp3 <br> **7.** zvuk – 16 khz-64kbitrate-mono-mp3 <br> **8.** zvuk – 16 khz-32kbitrate-mono-mp3 | Zvukový formát výstupu.
X-Search-AppId | Identifikátor GUID (pouze hex, pomlčky) | ID, které jednoznačně identifikuje klientskou aplikaci. To může být ID úložiště pro aplikace. Pokud není k dispozici, může být ID uživatele vygenerované aplikace.
X-Search-ClientID | Identifikátor GUID (pouze hex, pomlčky) | ID, které jednoznačně identifikuje instance aplikace pro každou instalaci.
Uživatelský Agent | Název aplikace | Název aplikace je povinné a musí být kratší než 255 znaků.
Autorizace | Autorizační token |  Zobrazit <a href="#Subscription">autorizační token</a> oddílu.

### <a name="InputParam"></a>Vstupní parametry

Požadavky na textu na řeč Bingu rozhraní API se provedou pomocí volání HTTP POST. Hlavičky jsou určené v předchozí části. Vstup řeči syntézu Markup Language (SSML), který představuje text, který se má syntetizovat obsahuje text. Popis kódu použít k řízení aspektů řeči, jako je například jazyk a pohlaví mluvčího, najdete v článku [specifikaci W3C SSML](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maximální velikost, která je podporována vstup SSML je 1 024 znaků, včetně všech značek.

###  <a name="SampleVoiceOR"></a>Příklad: hlasový výstup žádosti

Příklad žádosti hlasový výstup vypadá takto:

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

## <a name="VoiceOutResponse"></a>Hlasový výstup odezvy

Text na řeč Bingu rozhraní API používá HTTP POST k odesílání zvuku zpět do klienta. Odpověď rozhraní API obsahuje zvukový datový proud a kodek a odpovídá požadovaný výstupní formát. Zvuk vrátil pro danou žádost nesmí překročit 15 sekund.

### <a name="SuccessfulRecResponse"></a>Příklad: syntézu úspěšné odpovědi

Následující kód je příkladem odpověď ve formátu JSON na žádost o syntézu úspěšné hlasu. Komentáře a formátování kódu jsou pro účely tohoto příkladu pouze a jsou vynechány z skutečné odpovědi.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Příklad: syntézu selhání

Následující příklad kódu ukazuje odpověď ve formátu JSON k selhání dotazu syntézu hlasu:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Chybové odpovědi

Chyba | Popis
----|----
Chybný požadavek HTTP/400 | Je povinný parametr chybí, je prázdný nebo null, nebo hodnotu předanou buď povinný nebo volitelný parametr je neplatný. Jedním z důvodů pro získání "neplatná" odpověď je předání hodnotu řetězce, která přesahuje povolenou délku. Stručný popis problematické parametru je součástí.
HTTP/zobrazuje chyba 401 Neautorizováno | Požadavek není autorizovaný.
HTTP/413 RequestEntityTooLarge  | Vstup SSML je větší, než co je podporováno.
HTTP/502 BadGateway | Existuje problém související se sítí nebo o problém na straně serveru.

Příklad chybová odpověď vypadá takto:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Změna hlasového výstup prostřednictvím SSML

Rozhraní API pro převod textu na řeč k Microsoft podporuje SSML 1.0 podle W3C [řeči syntézu Markup Language (SSML) verze 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). V této části jsou uvedeny příklady změny některých vlastností výstupu generovaného hlasové, jako jsou mluvený přenosové rychlosti, výslovnost atd. pomocí značek SSML.

1. Přidání přerušení

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Mluvy frekvence změny

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Výslovnost

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Změnit svazek

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Změnit výšku

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Změna prosody obrysu

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Poznámka: zvukových dat musí být 8 kB nebo 16 kB wav zaznamenaná v následujícím formátu: **CRC kód** (CRC-32): 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0xFFFFFFFF; **Zvuk formátu příznak**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0xFFFFFFFF; **Počet vzorků**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0x7FFFFFFF; **Velikost binární tělo**: 4 bajty (DWORD) s platným rozsahem 0x00000000 ~ 0x7FFFFFFF; **Binární tělo**: n bajtů.

## <a name="SampleApp"></a>Ukázkové aplikace

Podrobnosti implementace, najdete v článku [Visual C# .NET převod textu na řeč ukázkovou aplikaci](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Podporovaná národní prostředí a hlasová písma

V následující tabulce jsou uvedeny některé z podporovaných národních prostředí a související hlasová písma.

Národní prostředí | Pohlaví | Název mapování služby
---------|--------|------------
ar – třeba * | Žena | "Microsoft Server řeči Text na řeč hlas (ar např Hoda)"
ar-SA | Muž | "Microsoft serveru řeči Text na řeč hlas (ar-SA, Naayf)"
bg-BG | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg – BG, Ivan)"
ES certifikační autority | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ca-ES, HerenaRUS)"
cs-CZ | Muž | "Microsoft Server řeči Text na řeč hlas (cs-CZ, Jakub)"
da-DK | Žena | "Microsoft serveru řeči Text na řeč hlas (da-DK HelleRUS)"
de-AT | Muž | "Microsoft Server řeči Text na řeč hlas (de-AT, musí Michael)"
de-CH | Muž | "Microsoft serveru řeči Text na řeč hlas (de-CH, Karsten)"
de-DE | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, Hedda)"
de-DE | Žena | "Microsoft serveru řeči Text na řeč hlas (de-DE, HeddaRUS)"
de-DE | Muž | "Microsoft serveru řeči Text na řeč hlas (de-DE, Stefan, Apollo)"
el-GR | Muž | "Microsoft serveru řeči Text na řeč hlas (el-GR, Stefanos)"
cs AU | Žena | "Microsoft serveru řeči Text na řeč hlas (en-AU, Catherine)"
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
cs-CZ | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, ZiraRUS)"
cs-CZ | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, JessaRUS)"
cs-CZ | Muž | "Microsoft serveru řeči Text na řeč hlas (en US, BenjaminRUS)"
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
 * ar – třeba podporuje moderní standardní Arabština (MSA).

> [!NOTE]
> Všimněte si, že předchozí názvy služeb **Microsoft Server řeči Text na řeč hlas (cs-CZ, Vit)** a **Microsoft serveru řeči Text na řeč hlas (en-IE, Shaun)** se přestanou používat po 3/31. ledna 2018, v pořadí pro optimalizaci funkcí rozhraní Bing Speech API. Aktualizujte prosím váš kód s aktualizované názvy.

## <a name="TrouNSupport"></a>Řešení potíží a podpora

Publikujte všechny dotazy a problémy, které [Speech Service Bingu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) fórum na webu MSDN. Úplné podrobnosti, patří například:

* Příkladem řetězce úplná.
* Pokud je k dispozici, přihlaste se celý výstup z neúspěšného požadavku, která zahrnuje ID.
* Procentuální podíl neúspěšných požadavků.
