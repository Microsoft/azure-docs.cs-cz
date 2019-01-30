---
title: Protokol WebSocket pro zpracování řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Dokumentace k protokolu pro zpracování řeči Bingu podle WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 1d6c0a8ca04949216e6410ff81b15f79c7067522
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217284"
---
# <a name="bing-speech-websocket-protocol"></a>Protokol WebSocket pro zpracování řeči Bingu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pro zpracování řeči Bingu je Cloudová platforma, která nabízí nejpokročilejší algoritmy, které jsou k dispozici pro převod mluvené slovo zvuku na text. Protokol pro zpracování řeči Bingu definuje [nastavení připojení](#connection-establishment) mezi klientské aplikace a služby a zpráv rozpoznávání řeči vyměňovaných mezi jejich protějšky ([klientského vznikla zprávy](#client-originated-messages) a [zprávy služby pochází](#service-originated-messages)). Kromě toho [telemetrických zpráv](#telemetry-schema) a [zpracování chyb](#error-handling) jsou popsány.

## <a name="connection-establishment"></a>Navazování připojení

Speech Service protokol dodržuje standardní specifikace protokolu WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Připojení soketu WebSocket je na začátku jako požadavek HTTP, který obsahuje hlavičky protokolu HTTP, které označují klienta chce upgradovat připojení protokolu WebSocket namísto použití sémantikou HTTP. Server určuje jeho připravenost k účasti na připojení soketu WebSocket vrácením HTTP `101 Switching Protocols` odpovědi. Po výměně této metody handshake klient a služba ponechat otevřené soketu a začít používat protokol založenou na zprávách odesílat a přijímat informace.

Pokud chcete začít metodu handshake protokolu WebSocket, klientská aplikace odešle požadavek HTTPS GET ke službě. Obsahuje standardní záhlaví upgrade objektu websocket na straně společně s další hlavičky, které jsou specifické pro zpracování řeči.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Služba jako odpověď vrátí:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Vyžadovat všechny hlasové požadavky [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) šifrování. Použijte nezašifrované hlasové požadavky se nepodporuje. Následující verze TLS se nepodporuje:

* TLS 1.2

### <a name="connection-identifier"></a>Identifikátor připojení

Speech Service vyžaduje, že všichni klienti obsahovat jedinečné ID pro identifikaci připojení. Klienti *musí* zahrnout *X ConnectionId* záhlaví při spuštění metody handshake protokolu WebSocket. *X ConnectionId* záhlaví musí být [univerzálně jedinečným identifikátorem](https://en.wikipedia.org/wiki/Universally_unique_identifier) hodnotu (UUID). WebSocket upgradu požadavků, které neobsahují *X ConnectionId*, nezadávejte hodnotu pro *X ConnectionId* záhlaví, nebo neobsahují platná hodnota identifikátoru UUID odmítají službou pomocí protokolu HTTP `400 Bad Request` odpovědi.

### <a name="authorization"></a>Autorizace

Kromě standardní záhlaví handshake protokolu WebSocket, hlasové požadavky vyžadují *autorizace* záhlaví. Požadavky na připojení bez této hlavičky odmítají službou pomocí protokolu HTTP `403 Forbidden` odpovědi.

*Autorizace* záhlaví musí obsahovat přístupového tokenu JSON Web Token (JWT).

Informace o tom, jak odběru a získání klíče rozhraní API, které slouží k načtení platné přístupové tokeny JWT, najdete v článku [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky.

Klíč rozhraní API je předán do tokenu služby. Příklad:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Následující informace záhlaví jsou nezbytné k tokenu přístupu.

| Name | Formát | Popis |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného |

Vrátí token služby přístupový token JWT jako `text/plain`. Pak tokenů JWT je předán jako `Base64 access_token` k ověření typu handshake jako *autorizace* záhlaví s předponou řetězec `Bearer`. Příklad:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Soubory cookie

Klienti *musí* podporují soubory cookie protokolu HTTP, jak je uvedeno v [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Přesměrování protokolu HTTP

Klienti *musí* podporují standardní přesměrování mechanismy určené [specifikace protokolu HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Koncové body řeči

Klienti *musí* použít příslušný koncový bod služby řeči. Koncový bod se odvíjí režim rozpoznávání a jazyk. V tabulce jsou uvedeny některé příklady.

| Mode | Cesta | Identifikátor URI služby |
| -----|-----|-----|
| Interaktivní | /speech/recognition/interactive/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konverzace | /speech/recognition/conversation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktování | /speech/recognition/dictation/cognitiveservices/v1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Další informace najdete v tématu [identifikátor URI služby](../GetStarted/GetStartedREST.md#service-uri) stránky.

### <a name="report-connection-problems"></a>Problémy s připojením typu sestavy

Klienti měli okamžitě hlásí všechny problémy vzniklé při navazování připojení. Zpráva protokolu pro generování sestav se nezdařilo připojení je popsána v [telemetrie selhání připojení](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Doba trvání omezení připojení

Ve srovnání s běžné webové služby HTTP připojení, poslední připojení pomocí protokolu WebSocket *dlouhé* čas. Speech Service umístí omezení na době trvání připojení pomocí protokolu WebSocket ke službě:

 * Maximální doba trvání pro všechny aktivní připojení soketu WebSocket je 10 minut. Připojení je aktivní, pokud služba nebo klient odesílá zprávy pomocí protokolu WebSocket přes toto připojení. Služba ukončuje připojení bez upozornění při dosažení limitu. Klienti měli vytvořit uživatelské scénáře, které nevyžadují připojení k aktivní na nebo blízko ní životnost maximální připojení.

 * Maximální doba trvání pro všechny aktivní připojení soketu WebSocket je 180 sekund. Připojení je neaktivní, pokud služby ani klienta odeslal zprávu pomocí protokolu WebSocket přes dané připojení. Po dosažení maximální doba života neaktivní služba ukončuje neaktivní připojení soketu WebSocket.

## <a name="message-types"></a>Typy zpráv

Po vytvoření objektu websocket na straně připojení mezi klientem a službu Klient a služba odesílat zprávy. Tato část popisuje formátu tyto zprávy pomocí protokolu WebSocket.

[Sdružení IETF RFC 6455](https://tools.ietf.org/html/rfc6455) Určuje, že zprávy protokolu WebSocket můžou přenášet data pomocí textový nebo binární kódování. Dvě kódování používají různé formáty v přenosu. Každý formát je optimalizovaná pro efektivní kódování, přenosu a dekódování datovou část zprávy.

### <a name="text-websocket-messages"></a>Text zprávy protokolu WebSocket

Text zprávy protokolu WebSocket provádět datovou část textové informace, které se skládá z část záhlaví a text oddělené pár známé double návrat nového řádku pro zprávy HTTP. A stejně jako zprávy protokolu HTTP, zadejte text zprávy protokolu WebSocket záhlaví v *název: hodnota* formátu oddělené pár jeden CR znaku nového řádku. Jakýkoli text součástí textovou zprávu pomocí protokolu WebSocket *musí* použít [UTF-8](https://tools.ietf.org/html/rfc3629) kódování.

Text zprávy protokolu WebSocket musíte zadat cestu zprávu v hlavičce *cesta*. Hodnotu této hlavičky musí být jeden z typů zpráv protokolu řeči definované dále v tomto dokumentu.

### <a name="binary-websocket-messages"></a>Binární zprávy protokolu WebSocket

Binární zprávy protokolu WebSocket provádět binární datové části. V protokolu Speech Service je zvuk předány a přijal od služby pomocí binární zprávy protokolu WebSocket. Všechny ostatní zprávy jsou textové zprávy pomocí protokolu WebSocket. 

Stejně jako textové zprávy pomocí protokolu WebSocket binární zprávy protokolu WebSocket sestávají z hlavičky a tělo oddílu. Zadejte první 2 bajty binární zprávy protokolu WebSocket, [formát big-endian](https://en.wikipedia.org/wiki/Endianness) velikost 16bitové celé číslo oddílu záhlaví objednávky. Velikost oddílu minimální záhlaví je 0 bajtů. Maximální velikost je 8 192 bajtů. Text v záhlaví binární zprávy protokolu WebSocket *musí* použít [US-ASCII](https://tools.ietf.org/html/rfc20) kódování.

Záhlaví ve zprávě protokolu WebSocket binární jsou kódovány ve stejném formátu jako v textových zpráv protokolu WebSocket. *Názvu a hodnoty* formátu jsou oddělené oddělovačem CR jednoho znaku nového řádku pár. Binární zprávy protokolu WebSocket musí určovat cestu zprávy v hlavičce *cesta*. Hodnotu této hlavičky musí být jeden z typů zpráv protokolu řeči definované dále v tomto dokumentu.

Textové a binární zprávy protokolu WebSocket se používají v protokolu Speech Service. 

## <a name="client-originated-messages"></a>Vytvoří se klient zprávy

Po navázání připojení klienta a službu můžete spustit pro odesílání zpráv. Tato část popisuje formátu a datovou část zprávy, které klientské aplikace odesílají Speech Service. V části [zprávy služby pochází](#service-originated-messages) uvede zprávy, které pocházejí z Speech Service a odesílají do klientské aplikace.

Hlavní zprávy odeslané klientem služby jsou `speech.config`, `audio`, a `telemetry` zprávy. Před považujeme za každou zprávu podrobně, společné požadované záhlaví zpráv pro tyto zprávy jsou popsány.

### <a name="required-message-headers"></a>Požadovaná zpráva hlavičky

Následující hlavičky jsou požadovány pro všechny zprávy klienta pochází.

| Hlavička | Value |
|----|----|
| Cesta | Cesta zprávy, jak je uvedeno v tomto dokumentu |
| X-RequestId | Identifikátor UUID ve formátu "no-dash" |
| X-časové razítko | Časové razítko hodiny klienta UTC ve formátu ISO 8601 |

#### <a name="x-requestid-header"></a>Hlavička X-RequestId

Požadavky na klienta pochází se jednoznačně identifikují pomocí *X-RequestId* záhlaví zprávy. Tato hlavička se vyžaduje pro všechny zprávy klienta pochází. *X-RequestId* hodnota záhlaví musí UUID být v podobě "no-dash", například *123e4567e89b12d3a456426655440000*. To *nelze* být v kanonickém tvaru *123e4567-e89b-12d3-a456-426655440000*. Požadavky bez *X-RequestId* záhlaví nebo s hodnotu hlavičky, která používá má nesprávný formát pro identifikátory UUID způsobit služby k ukončení připojení soketu WebSocket.

#### <a name="x-timestamp-header"></a>Hlavička X-časové razítko

Každá zpráva odeslaná do Speech Service používá klientská aplikace *musí* patří *X časové razítko* záhlaví. Pro toto záhlaví hodnotu čas, kdy klient odešle zprávu. Požadavky bez *X časové razítko* záhlaví nebo s hodnotu hlavičky, která používá má nesprávný formát způsobit, že služba ukončit připojení soketu WebSocket.

*X časové razítko* hodnota záhlaví musí být ve formátu "yyyy'-'MM'-'dd'T' HH': 'mm':'ss '." fffffffZ "kde"fffffff"představuje zlomek sekundy. Například "12,5" znamená "12 + 5/10 sekund a"12.526"znamená, že 12 plus 526/1 000 sekund". Tento formát je v souladu s [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a na rozdíl od standardní HTTP *datum* záhlaví, může poskytnout překlad milisekund. Klientské aplikace může být zaokrouhlit časová razítka na nejbližší milisekundu. Potřeba zajistit, že hodiny zařízení přesně sleduje dobu pomocí klientské aplikace [Protokol NTP (Network Time) server](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Zpráva `speech.config`

Speech Service je potřeba vědět vlastnostech vaší aplikace zajistit nejlepší možné řeči. Požadované vlastnosti data zahrnují informace o zařízení a operační systém, který využívá aplikaci. Zadejte tyto informace `speech.config` zprávy.

Klienti *musí* odeslat `speech.config` zpráv okamžitě po navázání připojení k Speech Service a před odesláním žádné `audio` zprávy. Je nutné odeslat `speech.config` zprávy pouze jednou za připojení.

| Pole | Popis |
|----|----|
| Kódování zpráv protokolu WebSocket | Text |
| Tělo | Datová struktura JSON |

#### <a name="required-message-headers"></a>Požadovaná zpráva hlavičky

| Název hlavičky | Value |
|----|----|
| Cesta | `speech.config` |
| X-časové razítko | Časové razítko hodiny klienta UTC ve formátu ISO 8601 |
| Typ obsahu | Application/json; charset = utf-8 |

Stejně jako všechny zprávy v protokolu Speech Service klientského vznikla `speech.config` zpráva *musí* patří *X časové razítko* hlavičku, která zaznamenává klient UTC Čas odeslání zprávy ke službě. `speech.config` Zpráva *nemá* vyžadují *X-RequestId* záhlaví vzhledem k tomu, že tato zpráva není přidružený k žádosti o konkrétní řeči.

#### <a name="message-payload"></a>Datovou část zprávy
Datová část `speech.config` zprávy je struktura JSON, který obsahuje informace o aplikaci. Následující příklad zobrazuje tyto informace. Informace o kontextu klienta a zařízení je součástí *kontextu* element struktuře JSON. 

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>System – element

Prvek system.version `speech.config` zpráva obsahuje verzi řeči SDK software používaný klientská aplikace nebo zařízení. Hodnota je ve formě *major.minor.build.branch*. Můžete vynechat *větev* komponenty, pokud se nedá použít.

##### <a name="os-element"></a>OS element

| Pole | Popis | Využití |
|-|-|-|
| os.platform | Operační systém platformy, který je hostitelem aplikace, například, Windows, Android, iOS nebo Linuxu |Požaduje se |
| OS.Name | Název produktu operačního systému, například systému Debian nebo Windows 10 | Požaduje se |
| os.version | Verze operačního systému ve formě *major.minor.build.branch* | Požaduje se |

##### <a name="device-element"></a>Element zařízení

| Pole | Popis | Využití |
|-|-|-|
| device.manufacturer | Výrobce zařízení | Požaduje se |
| device.model | Model zařízení | Požaduje se |
| device.version | Verze softwaru zařízení dodané výrobcem zařízení. Tato hodnota určuje verzi, která lze sledovat výrobce zařízení. | Požaduje se |

### <a name="message-audio"></a>Zpráva `audio`

Podporou hlasových klientské aplikace odesílat zvuku Speech Service převedením zvukový datový proud do řady zvuku bloků. Každý blok zvuk přenáší segment mluveného slova, které má být přepsána službou. Maximální velikost jediný neodkazovaný blok zvuku je 8 192 bajtů. Zvukový datový proud zprávy jsou *zprávy protokolu WebSocket binární*.

Klienti používají `audio` zpráva k odeslání zvuku bloků dat do služby. Klienti číst zvuk, mikrofon v blocích a odesílat tyto bloky Speech Service pro přepis. První `audio` zpráva může obsahovat hlavičku ve správném formátu, který správně Určuje, že zvuk odpovídá jedné z kódování formátů podporovaných službou. Další `audio` zprávy obsahují pouze binární zvuk Streamovat data načtená z mikrofon.

Klienti mohou volitelně odesílají `audio` zprávu s nulovou délkou text. Tato zpráva, že služba, která klient ví, že uživatel zastavil mluvený, dokončení utterance a mikrofonu je vypnuté.

Speech Service používá první `audio` zprávu, která obsahuje identifikátor jedinečný požadavek na signál start nový cyklus žádostí a odpovědí nebo *zapnout*. Poté, co služba přijímá `audio` zprávu s identifikátorem novou žádost o zahodí zařazených do fronty nebo unsent zpráv, které jsou přidruženy žádné předchozí zapnout.

| Pole | Popis |
|-------------|----------------|
| Kódování zpráv protokolu WebSocket | Binární hodnota |
| Tělo | Binární data pro zvuk bloku. Maximální velikost je 8 192 bajtů. |

#### <a name="required-message-headers"></a>Požadovaná zpráva hlavičky

Následující hlavičky jsou potřebné pro všechny `audio` zprávy.

| Hlavička         |  Value     |
| ------------- | ---------------- |
| Cesta | `audio` |
| X-RequestId | Identifikátor UUID ve formátu "no-dash" |
| X-časové razítko | Časové razítko hodiny klienta UTC ve formátu ISO 8601 |
| Typ obsahu | Typ zvukové obsahu. Typ musí být buď *audio/x-wav* (PCM) nebo *audio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Podporovaná kódování zvuku

Tato část popisuje zvukových kodeků podporované službou řeči.

##### <a name="pcm"></a>PCM

Speech Service přijímá nekomprimované pulse zvuk modulace (PCM) kód. Zvuk se odesílají službě v [WAV](https://en.wikipedia.org/wiki/WAV) formátu, tak první zvuk bloku dat *musí* obsahovat platný [formát Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) záhlaví (RIFF). Pokud klient zahájí zapnout s zvuku bloků dat, která provádí *není* obsahovat platnou hlavičku RIFF, služba zamítne žádost a ukončí připojení soketu WebSocket.

Zvuk PCM *musí* odeberou na 16 kHz s 16 bitů na ukázky a jeden kanál (*riff – 16khz – 16 bitů-mono-pcm*). Speech Service nepodporuje stereo audiostreamy a odmítne zvukové datové proudy, které nepoužívají zadaný přenosové rychlosti, vzorkovací frekvence nebo počtu kanálů.

##### <a name="opus"></a>Díle

Díle je otevřené, nezpoplatněná a vysoce všestranný zvukový kodek. Speech Service podporuje díle konstantní přenosovou rychlostí `32000` nebo `16000`. Pouze `OGG` kontejner pro díle momentálně se podporuje, která je zadána `audio/ogg` typ mime.

Chcete-li použít díle, upravte [ukázkový JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) a změnit `RecognizerSetup` metoda vrátí.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Zjištění konce řeči

Lidé nevydá signál explicitně po jejich skončení mluvit. Každou aplikaci, která přijímá řeči, jako vstup má dvě možnosti pro zpracování konec řeči v zvukový datový proud: Služba end řeči zjišťování a zjišťování klienta end řeči. Z těchto dvou možností detekce end řeči služby obvykle poskytuje lepší uživatelské prostředí.

##### <a name="service-end-of-speech-detection"></a>Detekce end speech Service

Aplikace k vytváření prostředí ideální bezobslužné řeči, povolte službě zjistit, že uživatel dokončil mluvit. Klienti odesílají zvuk, mikrofon jako *zvuku* chunks dokud služba detekuje nečinnosti a jako odpověď vrátí zpět `speech.endDetected` zprávy.

##### <a name="client-end-of-speech-detection"></a>Zjišťování klienta end řeči

Klientské aplikace, které uživateli umožňují signalizuje, že na konec řeči nějakým způsobem také poskytnete službě, která signálu. Klientská aplikace může například mít "Stop" nebo "Ztlumit" tlačítko, které může uživatel stisknout. Signalizuje, že koncový řeči, pošlete klientské aplikace *zvuku* bloku dat zpráv s nulovou délkou text. Speech Service interpretuje tuto zprávu jako konec příchozí zvukový datový proud.

### <a name="message-telemetry"></a>Zpráva `telemetry`

Klientské aplikace *musí* potvrdit konec každého zapnout odesláním telemetrie o zapnutí Speech Service. Zapnout end potvrzení umožňuje Speech Service k zajištění, že všechny zprávy, které jsou nezbytné pro dokončení požadavku a odpovědi byly přijaty správně klientem. Zapnout end potvrzení také umožňuje Speech Service k ověření, že klientské aplikace fungují podle očekávání. Tyto informace je neocenitelný při řízení, pokud potřebujete pomoc s řešením potíží vaší aplikace s podporou řeči.

Klienty, musíte potvrdit konec zapněte odesláním `telemetry` krátce po přijetí zprávy `turn.end` zprávy. Klienti se má pokusit o potvrzení `turn.end` co nejdříve. Pokud klientská aplikace selže potvrďte end na řadě vy, Speech Service může ukončit připojení s chybou. Klienti musí odesílat pouze jeden `telemetry` zprávu pro každý požadavek a odpověď identifikován *X-RequestId* hodnotu.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `telemetry` |
| X-časové razítko | Časové razítko hodiny klienta UTC ve formátu ISO 8601 |
| Typ obsahu | `application/json` |
| Tělo | Struktura JSON obsahující klientské informace o zapnutí |

Schéma pro text `telemetry` zpráv je definována v [Telemetrie schématu](#telemetry-schema) části.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetrie pro přerušené připojení

Pokud připojení k síti z nějakého důvodu selže během důsledku a klient *není* přijímat `turn.end` zpráv ze služby, klient odešle `telemetry` zprávy. Tato zpráva popisuje neúspěšných požadavků, které se klient připojí ke službě. Klienti nemají okamžitě pokusu o připojení k odeslání `telemetry` zprávy. Zpráva může být ukládány do vyrovnávací paměti na straně klienta a odeslány prostřednictvím budoucí – uživatel si vyžádal připojení. `telemetry` Zprávu pro neúspěšné žádosti *musí* použít *X-RequestId* hodnota z požadavku se nezdařilo. Může být odeslána ke službě co nejdříve po navázání připojení, aniž byste museli čekat na odeslání nebo přijetí další zprávy.

## <a name="service-originated-messages"></a>Vytvoří se služba zprávy

Tato část popisuje zprávy, které pocházejí z Speech Service a posílají se do klienta. Speech Service udržuje registr možnosti klienta a generuje zprávy vyžaduje každý klient, tedy ne že všichni klienti obdrželi všechny zprávy, které jsou zde popsány. Jako stručný výtah zprávy je odkazováno dle hodnoty *cesta* záhlaví. Například označujeme textovou zprávu pomocí protokolu WebSocket s *cesta* hodnota `speech.hypothesis` jako speech.hypothesis zprávu.

### <a name="message-speechstartdetected"></a>Zpráva `speech.startDetected`

`speech.startDetected` Zpráva znamená, že v zvukový datový proud zjištěn řeči Speech Service.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.startDetected` |
| Typ obsahu | Application/json; charset = utf-8 |
| Tělo | Struktura JSON, který obsahuje informace o podmínkách, při spuštění řeči byla zjištěna. *Posun* pole v této struktuře určuje posun (v jednotkách 100 nanosekund) při rozpoznávání řeči zjistil v zvukový datový proud, vzhledem k začátku datového proudu. |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Zpráva `speech.hypothesis`

Při rozpoznávání řeči Speech Service pravidelně generuje hypotézy o slova služba rozpoznán. Speech Service odešle tyto hypotézy klientovi přibližně každých 300 milisekund. `speech.hypothesis` Vhodný *pouze* pro zlepšení uživatelského prostředí řeči. V těchto zprávách nesmí trvat závislost na obsah nebo přesnost textu.

 `speech.hypothesis` Zprávy se vztahuje na klienty, kteří mají určité možnosti vykreslování textu a chcete poskytnout zpětnou vazbu téměř v reálném čase rozpoznávání probíhá na ten, kdo to mluví.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.hypothesis` |
| X-RequestId | Identifikátor UUID ve formátu "no-dash" |
| Typ obsahu | application/json |
| Tělo | Předpoklad řeči strukturu JSON |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

*Posun* prvek určuje posun (v jednotkách 100 nanosekund) když bylo rozpoznáno frázi, vzhledem k začátku zvukový datový proud.

*Doba trvání* prvek určuje dobu trvání (v jednotkách 100 nanosekund) Tato fráze řeči.

Klienti nesmí jakkoli předpokládat frekvence, časování nebo součástí hypotézy řeči nebo konzistence textu v jakékoli dvě řeči hypotézy text. Tyto hypotézy jsou pouze snímky do procesu určené k transkripci ve službě. Reprezentují stabilní hromadění určené k transkripci. Například první hypotézy řeči může obsahovat slova "jemné zábavu" a druhá hypotézu může obsahovat slova "najít věc." Speech Service neprovádí žádné následné zpracování (například malá a velká písmena, interpunkční znaménka) na text v hypotézu řeči.

### <a name="message-speechphrase"></a>Zpráva `speech.phrase`

Když Speech Service určuje, že obsahuje dostatek informací k vygenerování výsledku rozpoznávání, který se nezmění, vytvoří službu `speech.phrase` zprávy. Speech Service vytvoří tyto výsledky po zjištění, že uživatel dokončil věty nebo fráze.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.phrase` |
| Typ obsahu | application/json |
| Tělo | Fráze řeči strukturu JSON |

Schéma JSON frázi řeči obsahuje následující pole: `RecognitionStatus`, `DisplayText`, `Offset`, a `Duration`. Další informace o těchto polích naleznete v tématu [určené k transkripci odpovědi](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Zpráva `speech.endDetected`

`speech.endDetected` Zprávy určuje, že klientská aplikace by se měla zastavit streamování zvuku ve službě.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.endDetected` |
| Tělo | Struktura JSON obsahující posun, když byl zjištěn konec řeči. Posun je reprezentován v jednotkách 100 nanosekund posun od začátku zvuk, který se používá k rozpoznávání. |
| Typ obsahu | Application/json; charset = utf-8 |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Posun* prvek určuje posun (v jednotkách 100 nanosekund) když bylo rozpoznáno frázi, vzhledem k začátku zvukový datový proud.

### <a name="message-turnstart"></a>Zpráva `turn.start`

`turn.start` Signály start zapnout z hlediska služby. `turn.start` Zprávy je vždy první zprávy s odpovědí se zobrazí u všech žádostí. Pokud nemáte `turn.start` zprávy, se předpokládá, že stav připojení služby je neplatný.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `turn.start` |
| Typ obsahu | Application/json; charset = utf-8 |
| Tělo | Struktura JSON |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Text `turn.start` zprávy je struktura JSON obsahující kontext pro začátek zapnout. *Kontextu* obsahuje element *serviceTag* vlastnost. Tato vlastnost určuje hodnotu značky služby přiřazené k zapnutí. Tuto hodnotu je možné od Microsoftu, pokud potřebujete pomoc, řešení potíží se selháním ve vaší aplikaci.

### <a name="message-turnend"></a>Zpráva `turn.end`

`turn.end` Signalizuje konec zapnout z hlediska služby. `turn.end` Zprávy je vždy poslední zprávy s odpovědí se zobrazí u všech žádostí. Klienti mohou používat přijetí této zprávy jako signál pro vyčištění aktivity a přechodu do stavu nečinnosti. Pokud nemáte `turn.end` zprávy, se předpokládá, že stav připojení služby je neplatný. V těchto případech se existující připojení ke službě zavřete a znovu připojit.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `turn.end` |
| Tělo | Žádný |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schéma telemetrie

Text *telemetrie* zprávy je struktura JSON, který obsahuje informace o klientovi vypněte nebo pokusu o připojení. Struktura se skládá z klienta časová razítka, které zaznamenávají, když dojde k události klienta. Každý časového razítka musí být ve formátu ISO 8601, jak je popsáno v části s názvem "Hlavička X-časové razítko." *Telemetrie* zprávy všechna povinná pole ve struktuře JSON, který není zadán, nebo nepoužívejte formát správné časového razítka může způsobit, že služba ukončit připojení ke klientovi. Klienti *musí* zadat platné hodnoty pro všechna požadovaná pole. Klienti *by měl* zadat hodnoty pro volitelná pole pokaždé, když je to vhodné. Hodnoty zobrazené v vzorků v této části slouží pouze pro ilustraci.

Telemetrická data schématu je rozdělen do následujících částí: přijatá zpráva časová razítka a metriky. Formát a využití každé části je zadán v následujících částech.

### <a name="received-message-time-stamps"></a>Přijaté zprávy Časová razítka

Klienti musí obsahovat čas přijetí hodnot pro všechny zprávy, která bude mít po úspěšném připojení ke službě. Tyto hodnoty musíte zaznamenat čas při klienta *přijatých* každou zprávu ze sítě. Hodnota by neměla zaznamenat kdykoli. Například Klient by neměl zaznamenání času při jeho *zachovali* ve zprávě. Časová razítka přijaté zprávy jsou určené v poli *názvu a hodnoty* dvojice. Určuje název páru *cesta* hodnotu zprávy. Hodnota, které odpovídá páru určuje klienta čas, kdy byla zpráva přijata. Nebo, pokud byl přijat více než jednu zprávu se zadaným názvem, které odpovídá páru licencí hodnotu pole časová razítka, která určuje, kdy byly přijaty zprávy.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienti *musí* potvrdit přijetí všech zpráv odeslaných službou včetně časová razítka těchto zpráv v textu JSON. Pokud klient nebude obdržel zprávu, může služba ukončit připojení.

### <a name="metrics"></a>Metriky

Klienti musí obsahovat informace o událostech, ke kterým došlo během životního cyklu požadavku. Jsou podporovány následující metriky: `Connection`, `Microphone`, a `ListeningTrigger`.

### <a name="metric-connection"></a>Metrika `Connection`

`Connection` Metrika určuje podrobnosti o pokusy o připojení klienta. Pokud připojení soketu WebSocket bylo spuštění a dokončení, musí zahrnovat metriku časovými razítky. `Connection` Metrika je povinné *jenom pro první zapněte připojení*. Následné zapne není nutné zahrnout tyto informace. Pokud klient zadává víc pokusy o připojení, předtím, než se vytvoří připojení, informace o *všechny* pokusy o připojení by měly být zahrnuty. Další informace najdete v tématu [telemetrie selhání připojení](#connection-failure-telemetry).

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Name | `Connection` | Požaduje se |
| ID | Hodnota identifikátoru připojení, která byla použita v *X ConnectionId* záhlaví pro tento požadavek na připojení | Požaduje se |
| Start | Čas, kdy klient odešle požadavek na připojení | Požaduje se |
| Konec | Čas při přijetí oznámení, že bylo připojení úspěšně vytvořeno klienta nebo v chybových případech, odmítnuto, odmítnuto nebo se nezdařilo | Požaduje se |
| Chyba | Popis chyby, ke které došlo k chybě, pokud existuje. Pokud připojení úspěšné, klienti měli vynechat, nechte toto pole. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případy chyb, jinak tento parametr vynechán |

Popis chyby musí být maximálně 50 znaků a v ideálním případě by měla být jedna z hodnot uvedených v následující tabulce. Pokud chybového stavu neodpovídá jednu z těchto hodnot, klientů můžete použít výstižný popis chybovou podmínku s použitím [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez mezer. Umožňuje odeslat *telemetrie* zprávy vyžaduje připojení ke službě, takže pouze přechodný nebo dočasné chybové stavy můžete oznámený v *telemetrie* zprávy. Chybové stavy, *trvale* blok klienta v navázání připojení ke službě ochranu klienta v odesílání jakékoli zprávy do služby, včetně *telemetrie* zprávy.

| Chyba | Využití |
| ----- | ----- |
| DNSfailure | Klient nemohl připojit ke službě kvůli chybě DNS v síťových protokolů. |
| NoNetwork | Klient pokus o připojení, ale síťový zásobník oznámila, že žádné fyzické síti k dispozici. |
| NoAuthorization | Při pokusu získat autorizační token pro připojení se nezdařilo připojení klienta. |
| NoResources | Klient nemá dostatek některých místních prostředků (například paměť) při pokusu o připojení. |
| Zakázáno | Klient nemohl připojit ke službě, protože služba vrátila HTTP `403 Forbidden` stavový kód na požadavek na upgrade objektu WebSocket. |
| Neautorizováno | Klient nemohl připojit ke službě, protože služba vrátila HTTP `401 Unauthorized` stavový kód na požadavek na upgrade objektu WebSocket. |
| BadRequest | Klient nemohl připojit ke službě, protože služba vrátila HTTP `400 Bad Request` stavový kód na požadavek na upgrade objektu WebSocket. |
| ServerUnavailable | Klient nemohl připojit ke službě, protože služba vrátila HTTP `503 Server Unavailable` stavový kód na požadavek na upgrade objektu WebSocket. |
| ServerError | Klient nemohl připojit ke službě, protože služba vrátila `HTTP 500` vnitřní chyba stavový kód na požadavek na upgrade objektu WebSocket. |
| Vypršení časového limitu | Požadavek na připojení klienta vypršel časový limit bez odpověď ze služby. *End* pole obsahuje čas vypršení časového limitu klienta a zastavení čeká na připojení. |
| ClientError | Klient ukončil připojení kvůli chybě vnitřní klienta. | 

### <a name="metric-microphone"></a>Metrika `Microphone`

`Microphone` Metrika je nezbytné pro všechny zapne řeči. Tato metrika měří čas na straně klienta, během které zvukového vstupu se aktivně používá pro žádost o rozpoznávání řeči.

Použít následující příklady pouze jako vodítka pro záznam *Start* časové hodnoty pro `Microphone` metriky v klientské aplikaci:

* Klientská aplikace vyžaduje, že uživatel musí stisknutím fyzického tlačítka Spustit mikrofon. Po stisknutí tlačítka klientská aplikace načte vstup mikrofon a posílá ji do služby řeči. *Start* hodnota `Microphone` metrika zaznamená čas po stisknutí tlačítka, když mikrofon je inicializována a připravena k zadávání vstupních dat. *End* hodnota `Microphone` metrika zaznamená čas, když klientská aplikace zastavena, po jakou přijala, vysílání datového proudu zvuku ve službě `speech.endDetected` zpráv ze služby.

* Klientská aplikace bude používat spotter – klíčové slovo, které naslouchá "always". Až poté, co spotter – klíčové slovo zjistí frázi mluvené slovo aktivační událost klientská aplikace shromažďovat vstup mikrofon a odeslat do Speech Service. *Start* hodnota `Microphone` metrika zaznamená čas, kdy spotter – klíčové slovo oznámení klienta, pokud chcete začít používat vstup mikrofon. *End* hodnota `Microphone` metrika zaznamená čas, když klientská aplikace zastavena, po jakou přijala, vysílání datového proudu zvuku ve službě `speech.endDetected` zpráv ze služby.

* Klientská aplikace má přístup do konstantní zvukový datový proud a provádí tento zvukový datový proud v nečinnosti/řeči detekce *modul pro zjišťování řeči*. *Start* hodnota `Microphone` metrika zaznamená čas při *modul pro zjišťování řeči* oznámení klienta, pokud chcete začít používat vstup z datového proudu zvuku. *End* hodnota `Microphone` metrika zaznamená čas, když klientská aplikace zastavena, po jakou přijala, vysílání datového proudu zvuku ve službě `speech.endDetected` zpráv ze služby.

* Klientská aplikace zpracovává druhý zapnout žádosti více zapnout a informován zprávu odpovědi služby zapnout mikrofon, shromažďovat vstup pro druhý zapnout. *Start* hodnota `Microphone` metrika zaznamená čas, kdy klientská aplikace umožňuje mikrofon a spustí pomocí vstup z tohoto zdroje zvuku. *End* hodnota `Microphone` metrika zaznamená čas, když klientská aplikace zastavena, po jakou přijala, vysílání datového proudu zvuku ve službě `speech.endDetected` zpráv ze služby.

*End* čas hodnotu `Microphone` metrika zaznamená čas, kdy klientská aplikace zastavit streamování zvukového vstupu. Ve většině situací, dojde k této události krátce po klient přijal `speech.endDetected` zpráv ze služby. Klientské aplikace může ověřit, že se už správně odpovídají protokol tím, že zajišťuje, že *koncové* čas hodnotu `Microphone` metrika dojde později než časová hodnota přijetí pro `speech.endDetected` zprávy. A vzhledem k tomu obvykle dochází ke zpoždění mezi koncem jednoho zapnutí a spuštění další na řadě vy, klienti můžou ověřit udržování souladu s protokoly tak zajistit, aby *Start* čas `Microphone` metriky pro všechny další na řadě vy správně zaznamená čas při klienta *spuštění* používat mikrofon. do datového proudu zvukového vstupu do služby.

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Name | Mikrofon | Požaduje se |
| Start | Čas, kdy klient začít používat zvukového vstupu z mikrofon nebo jiných zvukový datový proud nebo aktivační událost poslal spotter – klíčové slovo | Požaduje se |
| Konec | Čas, kdy klienta zastavena pomocí datového proudu mikrofon nebo ve zvukovém souboru | Požaduje se |
| Chyba | Popis chyby, ke které došlo k chybě, pokud existuje. Operace mikrofon byly úspěšné, klienti by měl vynechat, nechte toto pole. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případy chyb, jinak tento parametr vynechán |

### <a name="metric-listeningtrigger"></a>Metrika `ListeningTrigger`
`ListeningTrigger` Metrika měří čas, kdy uživatel provede akci, která inicializuje hlasový vstup. `ListeningTrigger` Metrika je volitelné, ale klientů, které můžete zadat tato metrika se vyskytnou Uděláte to tak.

Použít následující příklady pouze jako vodítka pro záznam *Start* a *End* časové hodnoty pro `ListeningTrigger` metriky v klientské aplikaci.

* Klientská aplikace vyžaduje, že uživatel musí stisknutím fyzického tlačítka Spustit mikrofon. *Start* hodnotu pro tuto metriku zaznamená čas stisknutí tlačítka. *End* hodnotu zaznamená čas dokončení nabízené tlačítko.

* Klientská aplikace bude používat spotter – klíčové slovo, které naslouchá "always". Po klíčovém slovu spotter zjistí fráze mluvené slovo aktivační událost, klientská aplikace načte vstup mikrofon a odesílá je do Speech Service. *Start* hodnotu pro tuto metriku zaznamená čas, kdy spotter – klíčové slovo přijal zvuk, který pak detekovala jako aktivační událost frázi. *End* hodnotu zaznamená čas, kdy byl poslední slovo nebo frázi aktivační událost používaný tímto uživatelem.

* Klientská aplikace má přístup do konstantní zvukový datový proud a provádí tento zvukový datový proud v nečinnosti/řeči detekce *modul pro zjišťování řeči*. *Start* hodnotu pro tuto metriku zaznamená čas, který *modul pro zjišťování řeči* přijatých zvuk, který pak detekovala jako řeči. *End* hodnotu zaznamená čas při *modul pro zjišťování řeči* zjistil řeči.

* Klientská aplikace zpracovává druhý zapnout žádosti více zapnout a informován zprávu odpovědi služby zapnout mikrofon, shromažďovat vstup pro druhý zapnout. Klientská aplikace by měla *není* zahrnout `ListeningTrigger` metrik pro tento na řadě vy.

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Nepovinné |
| Start | Čas zahájení aktivační událost pro naslouchání klienta | Požaduje se |
| Konec | Čas dokončení aktivační událost pro naslouchání klienta | Požaduje se |
| Chyba | Popis chyby, ke které došlo k chybě, pokud existuje. Pokud operace aktivační události byl úspěšný, klienti by měl vynechat, nechte toto pole. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případy chyb, jinak tento parametr vynechán |

#### <a name="sample-message"></a>Ukázková zpráva

Následující příklad ukazuje telemetrické zprávy s částmi ReceivedMessages a metriky:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Zpracování chyb

Tato část popisuje typy chybové zprávy a podmínky, které vaše aplikace potřebuje ke zpracování.

### <a name="http-status-codes"></a>Stavové kódy HTTP

Během požadavek na upgrade objektu WebSocket, Speech Service může vrátit všechny standardní stavové kódy HTTP, jako například `400 Bad Request`atd. Vaše aplikace musí správně zpracovat tyto chybové stavy.

#### <a name="authorization-errors"></a>Chyby autorizace

Pokud nesprávné autorizace je k dispozici během upgrade objektu websocket na straně Speech Service vrátí HTTP `403 Forbidden` stavový kód. Mezi podmínky, které můžete spustit tento kód chyby je:

* Chybí *autorizace* záhlaví

* Neplatný ověřovací token

* Vypršela platnost autorizačního tokenu

`403 Forbidden` Neukazuje, chybová zpráva problém s Speech Service. Tato chybová zpráva znamená problém s klientskou aplikací.

### <a name="protocol-violation-errors"></a>Chyby porušení protokolu

Pokud služba Speech zjistí porušení protokolu z klienta, služba ukončuje připojení soketu websocket bylo po návratu *stavový kód* a *důvod* pro ukončení. Klientské aplikace můžete použít tyto informace k řešení potíží a opravit porušení zásad.

#### <a name="incorrect-message-format"></a>Nesprávný formát

Pokud klient odešle text nebo binární zprávy do služby, který není ve správném formátu, které jsou uvedeny v téhle specifikaci kódování, služba zavře připojení *1007 Neplatná datová část Data* stavový kód. 

Tato služba vrátí tímto stavovým kódem pro celou řadu důvodů, jak je znázorněno v následujícím příkladu:

* "Formát správné zprávy. Zprávy v binární má neplatnou hlavičku velikost předpony". Klient odešle binární zprávu, která má předponu velikost neplatnou hlavičku.

* "Formát správné zprávy. Zprávy v binární má neplatnou hlavičku velikost". Klient odešle binární zpráva, že zadaná velikost neplatnou hlavičku.

* "Formát správné zprávy. Záhlaví zprávy v binární dekódování na UTF-8 se nezdařilo." Klient odešle binární zprávu, která obsahuje hlavičky, které nebyly správně kódovaný ve formátu UTF-8.

* "Formát správné zprávy. Textová zpráva neobsahuje žádná data." Klient poslali textovou zprávu, která neobsahuje žádná data subjektu.

* "Formát správné zprávy. Dekódování na UTF-8 textovou zprávu se nepodařilo." Klient poslali textovou zprávu, která nebyla správně kódovaný ve formátu UTF-8.

* "Formát správné zprávy. Žádný oddělovač záhlaví obsahuje textové zprávy." Klient poslali textovou zprávu, která neobsahovala oddělovač záhlaví nebo použít oddělovač záhlaví nesprávné.

#### <a name="missing-or-empty-headers"></a>Záhlaví chybí nebo je prázdný

Pokud klient odešle zprávu, která nemá požadované záhlaví *X-RequestId* nebo *cesta*, služba uzavře připojení s *došlo k chybě protokolu 1002* stavový kód. Zpráva je "záhlaví chybí nebo je prázdný. {Název hlavičky}."

#### <a name="requestid-values"></a>ID žádosti hodnoty

Pokud klient odešle zprávu, která určuje, *X-RequestId* záhlaví s nesprávným formátem, služba uzavře připojení a vrátí *došlo k chybě protokolu 1002* stav. Zpráva je "Neplatný požadavek. Hodnota hlavičky X-RequestId nebyla zadána ve formátu UUID dash č."

#### <a name="audio-encoding-errors"></a>Zvukový chyby kódování

Pokud klient odešle zvuku bloků dat, který iniciuje vypněte a zvukový formát nebo kódování nesplňuje požadovanou specifikaci, služba uzavře připojení a vrátí *1007 Neplatná datová část Data* stavový kód. Zpráva ve formátu kódování zdroj chyby.

#### <a name="requestid-reuse"></a>Opakované použití ID žádosti

Po dokončení zapněte, pokud klient odešle zprávu, která opakovaně používá identifikátor žádosti z tohoto zapnout, služba uzavře připojení a vrátí *došlo k chybě protokolu 1002* stavový kód. Zpráva je "Neplatný požadavek. Opakované použití identifikátorů požadavku není povoleno."

## <a name="connection-failure-telemetry"></a>Telemetrie selhání připojení

K zajištění nejlepší uživatelské prostředí, klienti musí informovat Speech Service časová razítka pro důležité kontrolní body v rámci určitého připojení s použitím *telemetrie* zprávy. Je stejně důležité, aby klienti informovat službu připojení, které byly pokus nezdařil.

Pro každý pokus o připojení, která selhala, klientů vytvořit *telemetrie* zprávu s jedinečným *X-RequestId* hodnota hlavičky. Protože klient nebyl schopen navázat připojení, *ReceivedMessages* pole v datové části JSON může vynechat. Pouze `Connection` položku *metriky* pole je součástí. Tato položka obsahuje počáteční a koncové časová razítka, jakož i chybový stav, který byl zjištěn.

### <a name="connection-retries-in-telemetry"></a>Opakování připojení v telemetrii

Klienti musí rozlišovat *opakování* z *více pokusů připojení* událost, která aktivuje pokus o připojení. Pokusy o připojení, které se provádějí prostřednictvím kódu programu žádné akce uživatele jsou opakované pokusy. Více pokusy o připojení, které jsou provedeny v reakci na vstup uživatele jsou více pokusů o připojení. Klientům poskytnout každý pokus o připojení uživatele aktivuje jedinečný *X-RequestId* a *telemetrie* zprávy. Znovu použít klienti *X-RequestId* programový opakování. Pokud více opakované pokusy byly provedeny při pokusu o jedno připojení, je jako opakovanými žádostmi `Connection` položku v *telemetrie* zprávy.

Například předpokládejme, že uživatel mluví – klíčové slovo triggeru pro spuštění připojení a první pokus o připojení selže z důvodu chyby DNS. Druhý pokus, který je prostřednictvím kódu programu k klientem však úspěšné. Protože klient opakovat připojení bez nutnosti další vstupy od uživatele, klient použije jeden *telemetrie* zprávu s několika `Connection` položky k popisu připojení.

Další příklad předpokládejme, že uživatel mluví – klíčové slovo triggeru pro spuštění připojení a tento pokus o připojení selže po třech opakovaných pokusů. Klient pak poskytuje, přestane při pokusu o připojení ke službě a informuje uživatele, že došlo k chybě. Uživatel pak mluví aktivační události – klíčové slovo znovu. Tuto chvíli Předpokládejme, že se že klient připojí ke službě. Po připojení se okamžitě klient odešle *telemetrie* zpráv ve službě, která obsahuje tři `Connection` položky, které popisují selhání připojení. Po přijetí `turn.end` zprávu, klient odešle jiného *telemetrie* zprávu s popisem úspěšné připojení.

## <a name="error-message-reference"></a>Zprávy o chybách

### <a name="http-status-codes"></a>Stavové kódy HTTP

| Stavový kód HTTP | Popis | Řešení potíží |
| - | - | - |
| 400 – Chybný požadavek | Klient odešle žádost o připojení pomocí protokolu WebSocket, který byl nesprávný. | Zkontrolujte, že jste zadali požadované parametry a hlavičky protokolu HTTP a hodnoty jsou správné. |
| 401 Neautorizováno | Klienta neobsahuje informace o vyžaduje ověření. | Zkontrolujte, jestli posíláte *autorizace* záhlaví v připojení soketu WebSocket. |
| 403 Zakázáno | Klient odešle informace o ověření, ale je neplatný. | Zkontrolujte, že jste neodesílají vypršela platnost, nebo neplatnou hodnotu *autorizace* záhlaví. |
| 404 – Nenalezeno | Klient došlo k pokusu o přístup k cestě adresy URL, která není podporována. | Zkontrolujte, že používáte správnou adresu URL pro připojení soketu WebSocket. |
| Chyba 500 | Služba došlo k interní chybě a nemůže vyhovět požadavku. | Ve většině případů se tato chyba je přechodná. Zkuste požadavek. |
| 503 – Nedostupná služba | Službu nebylo k dispozici pro zpracování požadavku. | Ve většině případů se tato chyba je přechodná. Zkuste požadavek. |

### <a name="websocket-error-codes"></a>Kódy chyb protokolu WebSocket

| WebSocketsStatus kódu | Popis | Řešení potíží |
| - | - | - |
| Normální ukončení 1000 | Služba ukončila připojení protokolu WebSocket bez chyby. | Pokud na neočekávaném ukončení protokolu WebSocket, znovu načíst v dokumentaci k zajištění, že chápete, jak a kdy službu lze ukončit připojení soketu WebSocket. |
| Chyba protokolu 1002 | Dodržovat požadavky protokolu klienta se nezdařilo. | Zajistěte pochopit dokumentace k protokolu a jsou jasně o požadavcích. Přečtěte si předchozí dokumentaci o z důvodů chyby zobrazíte, pokud jste už bychom při tom porušili požadavky protokolu. |
| 1007 Neplatná datová část dat | Klient zaslal Neplatná datová část v zprávu protokolu. | Zkontrolujte poslední zprávy, který jste odeslali do služby pro chyby. Přečtěte si předchozí dokumentaci o datovou část chyby. |
| Chyba serveru 1011 | Služba došlo k interní chybě a nemůže vyhovět požadavku. | Ve většině případů se tato chyba je přechodná. Zkuste požadavek. |

## <a name="related-topics"></a>Související témata

Najdete v článku [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) , který je implementace protokolu služeb na základě protokolu WebSocket řeči.
