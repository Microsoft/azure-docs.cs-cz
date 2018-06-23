---
title: Převod textu na řeč rozhraní API služby Microsoft řeči | Microsoft Docs
description: Použít převod textu na řeč rozhraní API vám umožňuje v reálném čase je převod v různých hlasy a jazyky
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342743"
---
# <a name="bing-text-to-speech-api"></a>Převod Bing textu na řeč rozhraní API

## <a name="Introduction"></a>Úvod

S Bing převod textu na řeč rozhraní API vaší aplikace odesílat požadavky HTTP cloudu server, kde text okamžitě syntetizován do lidské zní řeči a vrácena jako zvukový soubor. Toto rozhraní API můžete použít v mnoha různých kontextech zajistit v reálném čase je převod v různých různé hlasy a jazyky.

## <a name="VoiceSynReq"></a>Hlasové souhrnnou požadavku

### <a name="Subscription"></a>Autorizační token

Každý požadavek souhrnnou hlasové vyžaduje přístupový token JSON Web Token (JWT). Přístupový token JWT předána v hlavičce požadavku řeči. Token má čas vypršení platnosti 10 minut. Informace o odběru a získání klíče rozhraní API, které slouží k načtení platný přístupové tokeny JWT najdete v tématu [kognitivní předplatné služby](https://azure.microsoft.com/try/cognitive-services/).

Klíč rozhraní API je předán do tokenu služby. Příklad:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Požadovaná hlavička informace o tokenu přístupu je následující.

Název| Formát | Popis
----|----|----
OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného

Služba tokenu vrátí přístupový token JWT jako `text/plain`. Pak token JWT se předá jako `Base64 access_token` ke koncovému bodu řeči jako hlavičku autorizace řetězec s předponou `Bearer`. Příklad:

`Authorization: Bearer [Base64 access_token]`

Přístup ke službě převod textu na řeč, musí klienti používat následující koncový bod:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Dokud jste získali přístupový token s svůj klíč předplatného, jak je popsáno výše, vyvolá tento odkaz `403 Forbidden` Chyba odpovědi.

### <a name="Http"></a>Hlavičky protokolu HTTP

V následující tabulce jsou uvedeny hlavičky protokolu HTTP, které se používají pro hlasové souhrnnou požadavky.

Záhlaví |Hodnota |Komentáře
----|----|----
Typ obsahu | aplikace nebo ssml + xml | Vstupní typ obsahu.
X-Microsoft-OutputFormat | **1.** ssml-16 khz-16bitové-mono-převod textu na řeč <br> **2.** nezpracovaná-16 khz-16bitové-mono-pcm <br>**3.** zvuk-16 khz-16 kb/s-mono-siren <br> **4.** riff-16 khz-16 kb/s-mono-siren <br> **5.** riff-16 khz-16bitové-mono-pcm <br> **6.** zvuk-16 khz-128kbitrate-mono-mp3 <br> **7.** zvuk-16 khz-64kbitrate-mono-mp3 <br> **8.** zvuk-16 khz-32kbitrate-mono-mp3 | Zvuk formát výstupu.
ID aplikace X vyhledávání | Identifikátor GUID (šestnáctkově pouze, bez pomlček) | ID, které jedinečně identifikují klientské aplikace. To může být ID úložiště pro aplikace. Pokud není k dispozici, Identifikátor může být uživatelem generovaný pro aplikaci.
X-vyhledávání ClientID | Identifikátor GUID (šestnáctkově pouze, bez pomlček) | ID, které jednoznačně identifikuje instance aplikace pro každou instalaci.
Uživatelský Agent | Název aplikace | Název aplikace je vyžadován a musí být kratší než 255 znaků.
Autorizace | Autorizační token |  Najdete v článku <a href="#Subscription">autorizační token</a> části.

### <a name="InputParam"></a>Vstupní parametry

Požadavky na převod Bing textu na řeč rozhraní API jsou vytvářeny pomocí volání HTTP POST. Hlavičky jsou určené v předchozí části. Vstup řeči souhrnnou Markup Language (SSML), který reprezentuje text, který má být syntetizován obsahuje text. Popis kód použít k řízení aspektů řeči například jazyk a pohlaví mluvčího najdete v tématu [SSML W3C specifikace](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Maximální velikost vstupní SSML, která je podporována je 1 024 znaků, včetně všechny značky.

###  <a name="SampleVoiceOR"></a>Příklad: hlasové výstup požadavku

Příklad výstupu požadavku hlasové vypadá takto:

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

Převod Bing textu na řeč rozhraní API používá HTTP POST k odesílání zvuk zpět do klienta. Odpověď rozhraní API obsahuje zvuk datového proudu a kodek a odpovídá požadovaný výstupní formát. Zvuk vrátil pro daný požadavek nesmí být delší než 15 sekund.

### <a name="SuccessfulRecResponse"></a>Příklad: souhrnnou úspěšné odpovědi

Následující kód je příklad JSON odpovědi na žádost o souhrnnou úspěšné hlasu. Komentáře a formátování kódu jsou pro účely tohoto příkladu pouze a byly vynechány z skutečné odpovědi.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Příklad: souhrnnou selhání

Následující příklad kódu ukazuje JSON odpovědí na selhání hlasové souhrnnou dotazu:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Chybové odpovědi

Chyba | Popis
----|----
Chybný požadavek HTTP/400 | Požadovaný parametr je chybějící, prázdná nebo null, nebo hodnotu předanou buď požadované nebo volitelné parametr je neplatný. Jedním z důvodů pro získání "neplatná" odpověď je předávání řetězcovou hodnotu, která přesahuje povolenou délku. Stručný popis problematické parametru je zahrnuta.
HTTP/401 neoprávněný přístup | Požadavek není ověřen.
HTTP/413 RequestEntityTooLarge  | SSML vstupu je větší než co je podporováno.
HTTP/502 BadGateway | Existuje problém související se sítí nebo problém na straně serveru.

Příkladem chybnou odpověď je následující:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Změna hlasové výstup prostřednictvím SSML

Rozhraní API služby Microsoft převod textu na řeč podporuje SSML 1.0, jak jsou definovány v W3C [řeči souhrnnou Markup Language (SSML) verze 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Tato část uvádí příklady změna některých vlastností výstupu generovaného hlasové jako hovořícího hodnocení, výslovnosti atd. pomocí SSML značek.

1. Přidání přerušení

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Hovořícího míru změn

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Výslovnosti

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Změnit svazku

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Změna výšky

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Změna prosody obrysem

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Poznámka: zvuková data musí být 8 kB nebo 16 kB wav selhala v následujícím formátu: **CRC kód** (CRC-32): 4 bajty (DWORD) s platný rozsah 0x00000000 ~ 0xFFFFFFFF; **Zvuk formátu příznak**: 4 bajty (DWORD) s platný rozsah 0x00000000 ~ 0xFFFFFFFF; **Počet vzorků**: 4 bajty (DWORD) s platný rozsah 0x00000000 ~ 0x7FFFFFFF; **Velikosti binární**: 4 bajty (DWORD) s platný rozsah 0x00000000 ~ 0x7FFFFFFF; **Binární textu**: n bajtů.

## <a name="SampleApp"></a>Ukázkové aplikace

Podrobnosti implementace najdete v tématu [Visual C# .NET je ukázková aplikace](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Podporované národní prostředí a hlasové písem

V následující tabulce jsou uvedeny některé z podporovaných národních prostředí a související hlasové písem.

Národní prostředí | Pohlaví | Mapování názvu služby
---------|--------|------------
ar např * | Žena | "Microsoft Server řeči Text hlasové rozpoznávání řeči (ar např, Hoda)"
ar (SA) | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (ar-SA, Naayf)"
bg-BG | Muž | "Microsoft Server řeči převod textu na řeč hlasové (bg-BG, Ivan)"
ES certifikační autority | Žena | "Microsoft Server řeči převod textu na řeč hlasové (certifikační autority ES, HerenaRUS)"
cs-CZ | Muž | "Microsoft Server řeči Text hlasové rozpoznávání řeči (cs-CZ, Jakub)"
da-DK | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (da-DK, HelleRUS)"
de-AT | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (de-AT, Michael)"
de-CH | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (de-CH, Karsten)"
de-DE | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (de-DE, Hedda)"
de-DE | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (de-DE, HeddaRUS)"
de-DE | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (de-DE, Stefan, Apollo)"
el-GR | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (el-GR, Stefanos)"
en-AU | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs AU, Catherine)"
en-AU | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs AU, HayleyRUS)"
en-CA | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs CA, Linda)"
en-CA | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs CA, HeatherRUS)"
en-GB | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs GB, Susan, Apollo)"
en-GB | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs GB, HazelRUS)"
en-GB | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs GB, George, Apollo)"
en-IE | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs IE, pracovníka)"
en-IN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs IN, Heera, Apollo)"
en-IN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs IN, PriyaRUS)"
en-IN | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (cs IN, Ravi, Apollo)"
cs-CZ | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (en US, ZiraRUS)"
cs-CZ | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (en US, JessaRUS)"
cs-CZ | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (en US, BenjaminRUS)"
ES-ES | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, Laura, Apollo)"
ES-ES | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, HelenaRUS)"
ES-ES | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-ES, tablet Pablo, Apollo)"
es-MX | Žena | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, HildaRUS)"
es-MX | Muž | "Microsoft Server řeči převod textu na řeč hlasové (es-MX, Raul, Apollo)"
fi-FI | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (fi-FI, HeidiRUS)"
fr-CA | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, Caroline)"
fr-CA | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-CA, HarmonieRUS)"
FR-CH | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-CH, Guillaume)"
fr-FR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Julie, Apollo)"
fr-FR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, HortenseRUS)"
fr-FR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (fr-FR, Paul, Apollo)"
he-IL| Muž| "Microsoft Server řeči Text na hlas rozpoznávání řeči (he-IL, Asaf)"
Dobrý den IN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (hi-IN, Kalpana, Apollo)"
Dobrý den IN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (hi-IN, Kalpana)"
Dobrý den IN | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (hi-IN, Hemant)"
hr-HR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (hr-HR, Matej)"
hu-HU | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (hu-HU, Szabolcs)"
ID ID | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (id-ID, Andika)"
IT-IT | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (it-IT, Cosimo, Apollo)"
ja-JP | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ayumi, Apollo)"
ja-JP | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, Ichiro, Apollo)"
ja-JP | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, HarukaRUS)"
ja-JP | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, LuciaRUS)"
ja-JP | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ja-JP, EkaterinaRUS)"
ko-KR | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (ko-KR, HeamiRUS)"
Moje MS | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ms Moje, Rizwan)"
nb-NO | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nb-NO HuldaRUS)"
NL-NL | Žena | "Microsoft Server řeči převod textu na řeč hlasové (nl-NL, HannaRUS)"
pl-PL | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pl-PL, PaulinaRUS)"
pt-BR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, HeloisaRUS)"
pt-BR | Muž | "Microsoft Server řeči převod textu na řeč hlasové (pt-BR, ADAM, Apollo)"
pt-PT | Žena | "Microsoft Server řeči převod textu na řeč hlasové (pt-PT, HeliaRUS)"
ro-RO | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (ro-RO, Andrei)"
ru-RU | Žena | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Irina, Apollo)"
ru-RU | Muž | "Microsoft Server řeči převod textu na řeč hlasové (ru-RU, Pavel, Apollo)"
sk-SK | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sk-SK, Filip)"
sl-SI | Muž | "Microsoft Server řeči převod textu na řeč hlasové (sl-SI, Lado)"
sv-SE | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (sv-SE, HedvigRUS)"
tových IN | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (tových IN, Valluvar)"
TH TÝ | Muž | "Microsoft Server řeči převod textu na řeč hlasové (th-TH, Pattara)"
tr-TR | Žena | "Microsoft Server řeči převod textu na řeč hlasové (tr-TR, SedaRUS)"
vi-VN | Muž | "Microsoft Server řeči převod textu na řeč hlasové (vi VN)"
zh-CN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-CN, HuihuiRUS)"
zh-CN | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-CN, Yaoyao, Apollo)"
zh-CN | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-CN, Kangkang, Apollo)"
zh-HK | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-HK Tracy, Apollo)"
zh-HK | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-HK TracyRUS)"
zh-HK | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-HK Danny, Apollo)"
zh-TW | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-TW, Yating, Apollo)"
zh-TW | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-TW, HanHanRUS)"
zh-TW | Muž | "Microsoft Server řeči Text na hlas rozpoznávání řeči (zh-TW, Zhiwei, Apollo)"
 * ar např podporuje moderní standardní Arabština (MSA).

> [!NOTE]
> Všimněte si, že předchozí názvy služby **Microsoft Server řeči Text hlasové rozpoznávání řeči (cs-CZ, Vit)** a **Microsoft Server řeči Text hlasové rozpoznávání řeči (cs IE, Shauna)** přestanou po 3/31/2018 v pořadí k optimalizaci funkce rozhraní API řeči Bing. Aktualizujte prosím kód s aktualizované názvy.

## <a name="TrouNSupport"></a>Řešení potíží a podpora

Odeslat všechny otázky a problémy na [služby Bing řeči](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) fórum MSDN. Kompletní informace, jako například patří:

* Příkladem řetězce úplné žádosti.
* Pokud je k dispozici, přihlaste se úplný výstup chybné žádosti, která zahrnuje ID.
* Procento neúspěšných požadavků.
