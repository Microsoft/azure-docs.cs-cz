---
title: Rozpoznávání řeči Microsoft protokolu WebSocket | Microsoft Docs
description: Dokumentace k protokolu pro rozpoznávání řeči služby založené na objekty WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 17954536e8bdb49c09204c2e522586b79cb1bef5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342676"
---
# <a name="speech-service-websocket-protocol"></a>Protokol WebSocket služby řeči

  Rozpoznávání řeči služba je Cloudová platforma, který funkce nejpokročilejší algoritmy, které jsou k dispozici pro převod mluvené zvuk na text. Definuje protokol služby řeči [připojení instalace](#connection-establishment) mezi klientské aplikace a služby a vyměňují mezi svými protějšky zprávy rozpoznávání řeči ([klienta pochází zprávy](#client-originated-messages)a [pochází služby zprávy](#service-originated-messages)). Kromě toho [telemetrické zprávy](#telemetry-schema) a [zpracování chyb](#error-handling) jsou popsány.

## <a name="connection-establishment"></a>Navazování připojení

Protokol služby řeči dodržuje specifikaci standardní protokolu WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Požadavek HTTP, který obsahuje hlavičky protokolu HTTP, které indikují desire klienta upgradovat připojení protokolu WebSocket místo pomocí sémantiky HTTP vychází připojení protokolu WebSocket. Server určuje jeho ochoty účast v připojení protokolu WebSocket pomocí protokolu HTTP vrácení `101 Switching Protocols` odpovědi. Po exchange této metody handshake klient a služba soket nechat otevřený a začít posílat a přijímat informace o použití protokolu na základě zpráv.

Zahájíte handshake protokolu WebSocket klientská aplikace odešle požadavek HTTPS získat ke službě. Obsahuje standardní upgradu hlavičky protokolu WebSocket spolu s další hlavičky, které jsou specifické pro rozpoznávání řeči.

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

Služba odpoví:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Všechny žádosti řeči vyžadovat [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) šifrování. Použití nezašifrované řeči požadavků není podporováno. Je podporován následující TLS verze:

* TLS 1.2

### <a name="connection-identifier"></a>Identifikátor připojení

Rozpoznávání řeči služba vyžaduje, aby všichni klienti zahrnují jedinečné ID pro identifikaci připojení. Klienti *musí* zahrnují *X ConnectionId* záhlaví při spuštění metody handshake protokolu WebSocket. *X ConnectionId* záhlaví musí být [identifikátor UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) hodnotu (UUID). Protokolu WebSocket upgradu požadavků, které neobsahují *X ConnectionId*, nezadávejte hodnotu pro *X ConnectionId* záhlaví, nebo nezahrnují platná hodnota UUID odmítnula služby pomocí protokolu HTTP `400 Bad Request` odpovědi.

### <a name="authorization"></a>Autorizace

Kromě standardní metody handshake hlavičky protokolu WebSocket, požadavky na rozpoznávání řeči vyžadují *autorizace* záhlaví. Požadavky na připojení bez tuto hlavičku odmítnula služby pomocí protokolu HTTP `403 Forbidden` odpovědi.

*Autorizace* záhlaví musí obsahovat přístupový token JSON Web Token (JWT).

Informace o tom, jak se přihlásit a získat klíče rozhraní API, které slouží k načtení platný přístupových tokenů JWT, najdete v článku [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky.

Klíč rozhraní API je předán do tokenu služby. Příklad:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Následující informace hlavičky jsou nezbytné pro přístup pomocí tokenu.

| Název | Formát | Popis |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného |

Služba tokenu vrátí přístupový token JWT jako `text/plain`. Pak token JWT se předá jako `Base64 access_token` k ověření typu handshake jako *autorizace* záhlaví řetězec s předponou `Bearer`. Příklad:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Soubory cookie

Klienti *musí* podporou souborů cookie HTTP zadané v [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Přesměrování protokolu HTTP

Klienti *musí* podporu mechanismy standardní přesměrování určeného [specifikace protokolu HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Rozpoznávání řeči koncové body

Klienti *musí* použít příslušný koncový bod služby řeči. Koncový bod je založena na režim rozpoznávání a jazyka. V tabulce jsou uvedeny některé příklady.

| Mode | Cesta | Identifikátor URI služby |
| -----|-----|-----|
| Interaktivní | /Speech/Recognition/Interactive/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konverzace | /Speech/Recognition/conversation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| diktování | /Speech/Recognition/Dictation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Další informace najdete v tématu [identifikátor URI služby](../GetStarted/GetStartedREST.md#service-uri) stránky.

### <a name="report-connection-problems"></a>Potíže s připojením sestavy

Klienti měli okamžitě hlásí všechny problémy vzniklé při navazování připojení. Protokol zpráv pro generování sestav se nezdařilo připojení je popsán v [telemetrie selhání připojení](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Omezení doby trvání připojení

Ve srovnání s typické webové služby HTTP připojení, poslední připojení protokolu WebSocket *dlouho* čas. Služba rozpoznávání řeči umístí na dobu trvání připojení protokolu WebSocket k službě omezení:

 * Maximální doba trvání žádné aktivní připojení protokolu WebSocket je 10 minut. Připojení je aktivní, pokud služba nebo klient odešle zprávy protokolu WebSocket přes toto připojení. Služba ukončuje připojení bez upozornění při dosažení limitu. Klienti měli vytvořit uživatelské scénáře, které nevyžadují připojení k aktivní nebo blízko životnost maximální připojení.

 * Maximální délka trvání pro jakékoli neaktivní připojení protokolu WebSocket je 180 sekund. Připojení je neaktivní, pokud služba ani klienta neodeslal zprávu protokolu WebSocket přes připojení. Po dosažení maximální doba života neaktivní služba ukončuje neaktivní připojení protokolu WebSocket.

## <a name="message-types"></a>Typy zpráv

Po navázání připojení protokolu WebSocket mezi klientem a službu klienta a služby mohou zasílat zprávy. Tato část popisuje formát tyto zprávy protokolu WebSocket.

[Sdružení IETF RFC 6455](https://tools.ietf.org/html/rfc6455) Určuje, že zprávy protokolu WebSocket může přenášet data pomocí text nebo binárního kódování. Dvě kódování pomocí různých formátech na přenosu. Každý formát je optimalizovaná pro efektivní kódování, přenosu a dekódování datovou část zprávy.

### <a name="text-websocket-messages"></a>Textové zprávy protokolu WebSocket

Textové zprávy protokolu WebSocket provádění datové části textových informací, která se skládá z části hlaviček a text oddělených pár známé nového řádku dvojitou CR používána pro zprávy HTTP. A jako zpráv protokolu HTTP, zadejte text zprávy protokolu WebSocket hlavičky v *název: hodnota* formátu oddělených znaky CR jeden pár. Jakýkoli text, zahrnuté v textové zprávě protokolu WebSocket *musí* použít [UTF-8](https://tools.ietf.org/html/rfc3629) kódování.

Textové zprávy protokolu WebSocket musíte zadat cestu zprávy v hlavičce *cestu*. Hodnotu této hlavičky musí být jeden z typů zpráv protokolu řeči definované později v tomto dokumentu.

### <a name="binary-websocket-messages"></a>Binární zprávy protokolu WebSocket

Binární zprávy protokolu WebSocket provádění binární datové části. V protokolu služby rozpoznávání řeči je zvuk předaných a přijal od služby pomocí binární zpráv protokolu WebSocket. Všechny ostatní zprávy jsou textové zprávy protokolu WebSocket. 

Binární zprávy protokolu WebSocket jako text zprávy protokolu WebSocket, obsahovat hlavičku a část textu. Zadejte první 2 bajty binární zprávy protokolu WebSocket, [big endian](https://en.wikipedia.org/wiki/Endianness) pořadí velikost 16bitové celé číslo v záhlaví části. Velikost minimální záhlaví části je 0 bajtů. Maximální velikost je 8192 bajtů. Text v záhlaví binární zpráv protokolu WebSocket *musí* použít [US-ASCII](https://tools.ietf.org/html/rfc20) kódování.

Hlavičky v binární zprávu protokolu WebSocket zakódovaným ve stejném formátu jako textové zprávy protokolu WebSocket. *Název: hodnota* formát je oddělených znaky CR jeden pár. Binární zprávy protokolu WebSocket musíte zadat cestu zprávy v hlavičce *cestu*. Hodnotu této hlavičky musí být jeden z typů zpráv protokolu řeči definované později v tomto dokumentu.

Textové a binární zprávy protokolu WebSocket se používají v protokolu řeči služby. 

## <a name="client-originated-messages"></a>Klient pochází zprávy

Po navázání připojení klienta a služby můžete začít odesílat zprávy. Tato část popisuje formát a datovou část zprávy, které klientské aplikace poslat řeči služby. V části [pochází služby zprávy](#service-originated-messages) uvede zprávy, které pocházejí z řeči služby a odešlou do klientské aplikace.

Hlavní zprávy odeslané klientem do služby jsou `speech.config`, `audio`, a `telemetry` zprávy. Před považujeme za každou zprávu podrobně, nejběžnější požadované hlavičky zpráv pro všechny tyto zprávy jsou popsány.

### <a name="required-message-headers"></a>Hlavičky požadované zpráv

Následující hlavičky jsou požadovány pro všechny zprávy klienta pochází.

| Záhlaví | Hodnota |
|----|----|
| Cesta | Cesta zprávy uvedeného v tomto dokumentu |
| X-RequestId | UUID ve formátu "Ne dash" |
| X-časového razítka | Klient UTC hodiny časové razítko ve formátu ISO 8601. |

#### <a name="x-requestid-header"></a>Hlavička X-RequestId

Klient pochází požadavky jsou jedinečně identifikovaný *X-RequestId* záhlaví zprávy. Tuto hlavičku je vyžadována pro všechny zprávy klienta pochází. *X-RequestId* hodnota hlavičky musí mít UUID "Ne dash" tvar, například *123e4567e89b12d3a456426655440000*. Ho *nelze* v kanonický tvar *123e4567-e89b-12d3-a456-426655440000*. Požadavky bez *X-RequestId* záhlaví nebo s hodnotu hlavičky, která používá má nesprávný formát pro identifikátory UUID způsobit službu, kterou chcete ukončit připojení protokolu WebSocket.

#### <a name="x-timestamp-header"></a>Hlavička X-časového razítka

Každá zpráva odeslaná do služby řeči aplikace klienta *musí* patří *X-časové razítko* záhlaví. Hodnota této hlavičky je čas, když klient odešle zprávu. Požadavky bez *X-časové razítko* záhlaví nebo s hodnotu hlavičky, která používá má nesprávný formát způsobit službu, kterou chcete ukončit připojení protokolu WebSocket.

*X-časové razítko* hodnota hlavičky musí být ve formátu 'rrrr'-'MM'-'dd'T' HH': 'mm':'ss '. " fffffffZ, kde je 'fffffff' zlomek sekundy. Například '12,5' znamená '12 + 5/10 sekund a "12.526" znamená "12 a 526/1000 sekund'. Tento formát odpovídá [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a na rozdíl od standardní HTTP *datum* záhlaví, můžete získat milisekundu řešení. Klientské aplikace může zaokrouhlí na nejbližší milisekundu časová razítka. Klientské aplikace potřeba zajistit, že zařízení hodiny přesně sleduje čas pomocí [Protokol NTP (Network Time) serveru](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Zpráva `speech.config`

Služba rozpoznávání řeči musí znát charakteristiky aplikace poskytovat nejlepší rozpoznávání řeči možné. Požadovaných charakteristik data zahrnují informace o zařízení a operační systém, který zajišťuje vaší aplikace. Zadejte tyto informace `speech.config` zprávy.

Klienti *musí* odesílat `speech.config` zpráv okamžitě po navázání připojení k službě řeči a před jejich odesílat žádné `audio` zprávy. Budete muset odeslat `speech.config` zprávy pouze jednou za připojení.

| Pole | Popis |
|----|----|
| Kódování zpráv protokolu WebSocket | Text |
| Tělo | Datová část ve struktuře JSON |

#### <a name="required-message-headers"></a>Hlavičky požadované zpráv

| Název hlavičky | Hodnota |
|----|----|
| Cesta | `speech.config` |
| X-časového razítka | Klient UTC hodiny časové razítko ve formátu ISO 8601. |
| Typ obsahu | Application/json; charset = utf-8 |

Stejně jako u všechny zprávy klienta vytvořena v protokolu služby rozpoznávání řeči `speech.config` zpráva *musí* patří *X-časové razítko* záhlaví, který zaznamenává klienta UTC Čas odeslání zprávy ke službě. `speech.config` Zpráva *nemá* vyžadují *X-RequestId* záhlaví vzhledem k tomu, že tato zpráva není spojen s konkrétní řeči požadavek.

#### <a name="message-payload"></a>Datovou část zprávy
Datovou část `speech.config` zprávy je JSON struktura, která obsahuje informace o aplikaci. Následující příklad ukazuje, tyto informace. Informace o kontextu klienta a zařízení je součástí *kontextu* element struktury JSON. 

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

##### <a name="system-element"></a>Element systému

System.version element `speech.config` zpráva obsahuje verzi nástroje řeči SDK softwaru používaných ve klientská aplikace nebo zařízení. Hodnota je ve formátu *major.minor.build.branch*. Můžete vynechat *větve* součásti, pokud není použitelný.

##### <a name="os-element"></a>Element operačního systému

| Pole | Popis | Využití |
|-|-|-|
| OS.Platform | Operační systém platformy, který je hostitelem aplikace, například, Windows, Android, iOS nebo Linux |Požaduje se |
| OS.Name | Název produktu operačního systému, například Debian nebo Windows 10 | Požaduje se |
| OS.Version | Verzi operačního systému ve formě *major.minor.build.branch* | Požaduje se |

##### <a name="device-element"></a>Element zařízení

| Pole | Popis | Využití |
|-|-|-|
| Device.Manufacturer | Výrobce zařízení hardwaru. | Požaduje se |
| Device.model | Model zařízení | Požaduje se |
| Device.Version | Verze softwaru zařízení od výrobce zařízení. Tato hodnota určuje verzi zařízení, které lze sledovat výrobcem. | Požaduje se |

### <a name="message-audio"></a>Zpráva `audio`

Rozpoznávání řeči klientské aplikace odeslání zvuk službě řeči převedením zvukový datový proud na řadu zvuk bloků. Každého bloku zvuk představuje segment mluvené zvuk, který má být přepsána službou. Maximální velikost jednoho audio bloku je 8192 bajtů. Zvukový datový proud zpráv *binární WebSocket zprávy*.

Klienti používají `audio` zpráva k odeslání zvuk bloku ke službě. Klienti číst zvuk z mikrofon v bloky dat a tyto bloky dat odeslat službě řeči pro přepis. První `audio` zpráva musí obsahovat hlavičku ve správném formátu, který správně Určuje, že vyhovuje zvukovém souboru na jednom z formátů kódování podporována službou. Další `audio` zprávy obsahují pouze binární zvuk stream data načtená z mikrofonu.

Klienti mohou volitelně odesílají `audio` zprávu s nulovou délkou textu. Tato zpráva znamená na službu, kterou klient ví, že uživatel zastavena a mluvte, dokončení utterance a mikrofon vypnutý.

Služba rozpoznávání řeči používá první `audio` zprávu, která obsahuje identifikátor jedinečné žádosti signál spustit nový cyklus požadavků a odpovědí nebo *zapnout*. Po přijetí služby `audio` zprávu s identifikátorem nové žádosti o zahodí zařazených do fronty a neodeslaných zprávy, které jsou přidruženy žádné předchozí vypněte.

| Pole | Popis |
|-------------|----------------|
| Kódování zpráv protokolu WebSocket | Binární hodnota |
| Tělo | Binární data pro zvuk bloku. Maximální velikost je 8192 bajtů. |

#### <a name="required-message-headers"></a>Hlavičky požadované zpráv

Následující hlavičky jsou požadovány pro všechny `audio` zprávy.

| Záhlaví         |  Hodnota     |
| ------------- | ---------------- |
| Cesta | `audio` |
| X-RequestId | UUID ve formátu "Ne dash" |
| X-časového razítka | Klient UTC hodiny časové razítko ve formátu ISO 8601. |
| Typ obsahu | Zvuk typ obsahu. Typ musí být buď *zvuk/x-wav* (PCM) nebo *zvuk nebo silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Podporované zvuk kódování

Tato část popisuje zvukových kodeků podporován službou řeči.

##### <a name="pcm"></a>PCM

Rozpoznávání řeči služba přijímá zvuk Modulační (PCM) nekomprimované pulse kódu. Zvuk je odeslána do služby [WAV](https://en.wikipedia.org/wiki/WAV) formátu, takže první zvuk bloku dat *musí* obsahovat platnou [formát Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) záhlaví (RIFF). Pokud klient zahájí zapnout s zvuk bloku, která provádí *není* obsahovat platnou hlavičku RIFF, služba žádost odmítne a ukončí připojení protokolu WebSocket.

Zvuk PCM *musí* vzorkovat na 16 kHz s 16 bitů na ukázka a jeden kanál (*riff-16khz-16bitové-mono-pcm*). Služba rozpoznávání řeči nepodporuje stereo zvukové datové proudy a odmítne zvukové datové proudy, které nepoužívají zadaný propustnosti, vzorkovací frekvence a počet kanálů.

##### <a name="opus"></a>Díle

Díle je otevřený, zdarma, vysoce univerzální zvukových kodeků. Rozpoznávání řeči služba podporuje díle konstantní přenosovou rychlostí `32000` nebo `16000`. Pouze `OGG` kontejner pro díle v současné době podporuje zadanou `audio/ogg` typ mime.

Chcete-li použít díle, změňte [JavaScript ukázka](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) a změňte `RecognizerSetup` metoda vrátí.

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

#### <a name="detect-end-of-speech"></a>Zjištění konec řeči

Člověka není signalizován explicitně jejich hotovi hovořícího. Jakékoli aplikace, která přijímá řeči jako vstup má dvě možnosti pro zpracování konec řeči v zvukový datový proud: služby zjišťování end řeči a end řeči detekce klienta. Z těchto dvou možností detekce end řeči služby obvykle poskytuje lepší uživatelské prostředí.

##### <a name="service-end-of-speech-detection"></a>Služba detekce end řeči

K vytvoření prostředí ideální bezobslužná rozpoznávání řeči, aplikace povolí službu, kterou chcete zjistit, kdy uživatel dokončil hovořícího. Klienti odesílají zvuk z mikrofon jako *zvuk* bloky dat, dokud služba zjistí nečinnosti a zpět se odpoví `speech.endDetected` zprávy.

##### <a name="client-end-of-speech-detection"></a>Detekce end řeči klienta

Klientské aplikace, které uživateli signál konec řeči nějakým způsobem také můžete poskytnout službu, signál. Například klientské aplikace může mít "Stop" nebo "Ztlumení" tlačítko, které může uživatel stisknout. Signál end řeči, odeslání klientské aplikace *zvuk* bloku zpráv s nulovou délkou textu. Služba rozpoznávání řeči interpretuje tuto zprávu jako konec příchozí datový proud zvuku.

### <a name="message-telemetry"></a>Zpráva `telemetry`

Klientské aplikace *musí* odesláním telemetrie o zapnutí služby rozpoznávání řeči na vědomí na konec každé vypněte. Zapněte end potvrzení umožňuje řeči služby Ujistěte se, že všechny zprávy, které jsou nezbytné pro dokončení požadavku a odpovědi byly správně přijatých klientem. Zapněte end potvrzení také umožňuje řeči služby ověřte, že klientské aplikace fungují podle očekávání. Tyto informace je velice užitečné, pokud potřebujete pomoc, řešení potíží s podporou rozpoznávání řeči aplikace.

Klienti musí potvrdit konec zapněte odesláním `telemetry` zpráva krátce po přijetí `turn.end` zprávy. Klienti mají pokusit o potvrdit `turn.end` co nejdříve. Pokud klientské aplikace se nepodaří end zapnout na vědomí, může služba rozpoznávání řeči ukončit připojení s chybou. Klienti musí odesílat pouze jeden `telemetry` zprávu pro každý požadavek a odpověď identifikovaný *X-RequestId* hodnotu.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `telemetry` |
| X-časového razítka | Klient UTC hodiny časové razítko ve formátu ISO 8601. |
| Typ obsahu | `application/json` |
| Tělo | Struktura JSON, která obsahuje klientské informace o zapnutí |

Schéma pro text `telemetry` zprávy je definována v [Telemetrie schématu](#telemetry-schema) části.

#### <a name="telemetry-for-interrupted-connections"></a>Telemetrie dojde k přerušení připojení

Pokud síťové připojení z nějakého důvodu selže během zapnout a klient nemá *není* přijímat `turn.end` zpráv ze služby, klient odešle `telemetry` zprávy. Tato zpráva popisuje další čas, který se klient připojí ke službě chybných požadavků. Klienti nemusí okamžitě pokusu o připojení k odeslání `telemetry` zprávy. Zprávy mohou být uložená do vyrovnávací paměti na straně klienta a odešlou přes budoucí-uživatel si vyžádal připojení. `telemetry` Zprávu pro chybných požadavků *musí* použít *X-RequestId* hodnotu z chybných požadavků. Službě mohou být odeslány co nejrychleji, pokud je navázáno připojení, bez čekání na odesílat nebo přijímat další zprávy.

## <a name="service-originated-messages"></a>Služba vytvořena zprávy

Tato část popisuje zprávy, které pocházejí z řeči služby a posílají se na klientovi. Služba rozpoznávání řeči udržuje registr klienta funkcí a vygeneruje zpráv vyžaduje podle jednotlivých klientů, takže není že všichni klienti obdrželi všechny zprávy, které jsou zde popsány. Jako stručný výtah zprávy odkazují hodnotu *cesta* záhlaví. Například označujeme protokolu WebSocket textovou zprávu s *cesta* hodnotu `speech.hypothesis` jako speech.hypothesis zprávu.

### <a name="message-speechstartdetected"></a>Zpráva `speech.startDetected`

`speech.startDetected` Zpráva znamená, že služba rozpoznávání řeči zjistila řeči v zvuk.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.startDetected` |
| Typ obsahu | Application/json; charset = utf-8 |
| Tělo | Struktura JSON, který obsahuje informace o podmínkách při spuštění řeči byla zjištěna. *Posun* pole v této struktuře určuje posun (v jednotkách 100 nanosekund) při rozpoznávání řeči byla zjištěna v zvukový stream, relativně ke spuštění datového proudu. |

#### <a name="sample-message"></a>Ukázkovou zprávu

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Zpráva `speech.hypothesis`

Při rozpoznávání řeči, řeči služba pravidelně generuje hypotézy o slova službu rozpoznána. Rozpoznávání řeči služby odesílá tyto hypotézy klientovi přibližně každých 300 MS. `speech.hypothesis` Vhodný *pouze* pro zlepšení rozpoznávání řeči uživatelské prostředí. Závislost na obsah nebo přesnost text nesmí provádět v těchto zpráv.

 `speech.hypothesis` Zpráva se vztahuje na klienty, kteří mají některé funkce vykreslování textu a chcete poskytnout zpětnou vazbu téměř v reálném čase v průběhu rozpoznávání osobě, která je hovořícího.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.hypothesis` |
| X-RequestId | UUID ve formátu "Ne dash" |
| Typ obsahu | application/json |
| Tělo | Předpoklad řeči struktuře JSON |

#### <a name="sample-message"></a>Ukázkovou zprávu

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

*Posun* element určuje posun (v jednotkách 100 nanosekund) Pokud byla rozpoznána fráze, relativně k začátku zvukový datový proud.

*Doba trvání* element určuje dobu (v jednotkách 100 nanosekund), tato fráze řeči.

Klienti nesmí vytvořit žádný odhad o četnost, časování nebo obsažené v předpoklad řeči nebo konzistence textu v jakékoli dvě řeči hypotézy text. Hypotézy jsou pouze snímky do procesu přepis ve službě. Reprezentují stabilní nahromadění přepis. Například první předpoklad řeči může obsahovat slova "jemné fun" a druhý předpoklad může obsahovat slova "najít věc." Služba rozpoznávání řeči neprovede žádné po zpracování (například velkých písmen, interpunkce) na text v předpoklad řeči.

### <a name="message-speechphrase"></a>Zpráva `speech.phrase`

Při řeči služby určuje, zda má dostatek informací k vytvoření rozpoznávání výsledek, který se nezmění, vytváří služba `speech.phrase` zprávy. Rozpoznávání řeči Service poskytuje tyto výsledky po zjistí, že uživatel dokončil větu nebo frázi.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.phrase` |
| Typ obsahu | application/json |
| Tělo | Rozpoznávání řeči frázi struktuře JSON |

Schéma řeči frázi JSON obsahuje následující pole: `RecognitionStatus`, `DisplayText`, `Offset`, a `Duration`. Další informace o těchto polí najdete v tématu [přepis odpovědí](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Ukázkovou zprávu

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

`speech.endDetected` Zprávy určuje, že klientská aplikace by se měla zastavit streamování zvuk ke službě.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `speech.endDetected` |
| Tělo | Struktura JSON, která obsahuje posun po konci řeči byla zjištěna. Posun je reprezentována v jednotky 100 nanosekund posun od začátku zvuk, který slouží k rozpoznávání. |
| Typ obsahu | Application/json; charset = utf-8 |

#### <a name="sample-message"></a>Ukázkovou zprávu

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Posun* element určuje posun (v jednotkách 100 nanosekund) Pokud byla rozpoznána fráze, relativně k začátku zvukový datový proud.

### <a name="message-turnstart"></a>Zpráva `turn.start`

`turn.start` Signály spuštění zapnout z hlediska služby. `turn.start` Zpráva je vždy první zprávu odpovědi, obdržíte pro každou žádost. Pokud jste neobdrželi `turn.start` zprávy, předpokládá, že stav připojení služby je neplatný.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `turn.start` |
| Typ obsahu | Application/json; charset = utf-8 |
| Tělo | Struktura JSON |

#### <a name="sample-message"></a>Ukázkovou zprávu

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

Text `turn.start` zprávy je struktura JSON, která obsahuje kontext pro spuštění zapnout. *Kontextu* obsahuje element *serviceTag* vlastnost. Tato vlastnost určuje hodnota značky, které služby jsou přidružené zapnout. Tuto hodnotu lze použít společností Microsoft, pokud potřebujete pomoc s odstraňováním chyb v aplikaci.

### <a name="message-turnend"></a>Zpráva `turn.end`

`turn.end` Označuje konec zapnout z hlediska služby. `turn.end` Zpráva je vždy zobrazí všechny žádosti o poslední zprávu odpovědi. Klienti mohou používat přijetí této zprávy jako signál pro čištění aktivity a přechodu do stavu nečinnosti. Pokud jste neobdrželi `turn.end` zprávy, předpokládá, že stav připojení služby je neplatný. V takových případech existující připojení ke službě zavřete a znovu připojit.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| Cesta | `turn.end` |
| Tělo | Žádný |

#### <a name="sample-message"></a>Ukázkovou zprávu

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schéma telemetrie

Text *telemetrie* zprávy je struktura JSON, která obsahuje informace o klientovi o zapnout nebo pokus o připojení. Struktura se skládá z klienta časová razítka, které zaznamenávají, když dojde k události klienta. Každý časového razítka musí být ve formátu ISO 8601, jak je popsáno v části s názvem "Hlavička X-časového razítka." *Telemetrie* zprávy, nezadávejte všechna povinná pole ve struktuře JSON nebo který nepoužívejte správný razítka formát času může způsobit, že službu, kterou chcete ukončit připojení ke klientovi. Klienti *musí* zadat platné hodnoty pro všechny požadované pole. Klienti *by měl* zadat hodnoty pro nepovinné pole, kdykoli je to vhodné. Hodnoty zobrazené v ukázky v této části jsou pouze pro ilustraci.

Telemetrie schématu je rozdělené do následujících částí: přijata zpráva časová razítka a metriky. Formát a využití každé části je zadána v následujících částech.

### <a name="received-message-time-stamps"></a>Přijaté zprávy Časová razítka

Klienti musí obsahovat čas potvrzení hodnoty pro všechny zprávy, které obdrží po úspěšném připojení ke službě. Tyto hodnoty musí záznam čas při klienta *přijata* každou zprávu ze sítě. Hodnota by neměla záznam další čas. Například by nemělo klienta zaznamenat čas při jeho *reagovali* na zprávu. Časová razítka přijaté zprávy jsou určené v pole *název: hodnota* páry. Určuje název páru *cesta* hodnotu zprávy. Hodnota, které odpovídá páru určuje čas klienta, pokud zpráva byla přijata. Nebo, pokud byl přijat více než jednu zprávu zadaného názvu, dvojici hodnotu pole časová razítka, které určuje, kdy byly přijaty tyto zprávy.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienti *musí* potvrdit přijetí všech zpráv odeslaných službou zahrnutím časová razítka pro tyto zprávy v textu JSON. Pokud se klientovi nepodaří potvrdit přijetí zprávy, může služba ukončit připojení.

### <a name="metrics"></a>Metriky

Klienti musí obsahovat informace o událostech, ke kterým došlo během doby platnosti požadavku. Jsou podporovány následující metriky: `Connection`, `Microphone`, a `ListeningTrigger`.

### <a name="metric-connection"></a>Metrika `Connection`

`Connection` Metrika určuje podrobnosti o pokusy o připojení klienta. Metrika musí obsahovat časovými razítky, kdy byla spuštěna a dokončil připojení protokolu WebSocket. `Connection` Je požadována metrika *jenom pro první zapnout připojení*. Následné oplátku není nutné zahrnout tyto informace. Pokud klient zadává víc pokusy o připojení, před vytvořením připojení, informace o *všechny* pokusy o připojení by měly být zahrnuty. Další informace najdete v tématu [telemetrie selhání připojení](#connection-failure-telemetry).

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Název | `Connection` | Požaduje se |
| ID | Hodnota identifikátoru připojení, který se používal v *X ConnectionId* záhlaví pro tento požadavek na připojení | Požaduje se |
| Start | Čas, kdy klient odeslal žádost o připojení | Požaduje se |
| Konec | Čas při přijetí oznámení, že spojení bylo úspěšně navázáno klienta nebo v případech chyb, zamítnuto, odmítl nebo se nezdařilo | Požaduje se |
| Chyba | Popis chyby, k níž došlo, pokud existuje. Pokud připojení bylo úspěšné, musí klienti vynechat v tomto poli. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případech chyb, v opačném případě vynechání |

Popis chyby by měl být delší než 50 znaků a v ideálním případě by měl být jeden z hodnoty uvedené v následující tabulce. Pokud chybového stavu neodpovídá jednu z těchto hodnot, klienti mohou používat výstižný popis chybového stavu pomocí [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez mezer. Schopnost posílání *telemetrie* zpráva vyžaduje připojení ke službě, takže pouze přechodný nebo mohou být oznámeny dočasné chybové stavy v *telemetrie* zprávy. Chybové stavy, *trvale* bloku klienta v navázání připojení ke službě ochranu klienta před odesláním všechny zprávy služby, včetně *telemetrie* zprávy.

| Chyba | Využití |
| ----- | ----- |
| DNSfailure | Klient se nemůže připojit ke službě kvůli selhání DNS v síťových protokolů. |
| NoNetwork | Klient pokus o připojení, ale síťový zásobník oznámil, že žádné fyzické síti nebylo k dispozici. |
| NoAuthorization | Při pokusu o získání tokenu ověřování pro připojení se nezdařilo připojení klienta. |
| NoResources | Klient nemá dostatek některé místních prostředků (například paměť) při pokusu o vytvoření připojení. |
| Zakázáno | Klient se nemohl připojit ke službě, protože služba vrátila HTTP `403 Forbidden` stavový kód na žádost o upgrade protokolu WebSocket. |
| Neautorizováno | Klient se nemohl připojit ke službě, protože služba vrátila HTTP `401 Unauthorized` stavový kód na žádost o upgrade protokolu WebSocket. |
| BadRequest | Klient se nemohl připojit ke službě, protože služba vrátila HTTP `400 Bad Request` stavový kód na žádost o upgrade protokolu WebSocket. |
| ServerUnavailable | Klient se nemohl připojit ke službě, protože služba vrátila HTTP `503 Server Unavailable` stavový kód na žádost o upgrade protokolu WebSocket. |
| ServerError | Klient se nemohl připojit ke službě, protože služba vrátila `HTTP 500` stavový kód vnitřní chyby na žádost o upgrade protokolu WebSocket. |
| Vypršení časového limitu | Požadavek na připojení klienta vypršel časový limit bez odezvy ze služby. *End* pole obsahuje čas vypršel časový limit a byla zastavena čeká na připojení klienta. |
| ClientError | Klient ukončila připojení kvůli chybě některé interního klienta. | 

### <a name="metric-microphone"></a>Metrika `Microphone`

`Microphone` Metrika je nezbytné pro všechny oplátku řeči. Tato metrika měří čas na straně klienta, během které zvuk vstup aktivně používá pro žádost o řeči.

Následující příklady použijte jako vodítko pro záznam *spustit* čas hodnoty `Microphone` metriky v aplikaci klienta:

* Klientská aplikace vyžaduje, že uživatel musí stiskněte fyzické tlačítko Spustit mikrofonu. Po stisknutí tlačítka klientská aplikace načte vstup z mikrofon a odešle ji do řeči služby. *Spustit* hodnota `Microphone` metrika zaznamenává času po tlačítko nabízeného oznámení, pokud mikrofon je inicializována a připravena k poskytnutí vstupu. *End* hodnota `Microphone` metrika zaznamenává dobu, pokud klientská aplikace byla zastavena, streamování zvuk ke službě po jeho přijetí `speech.endDetected` zpráv ze služby.

* Klientská aplikace používá spotter – klíčové slovo, která naslouchá "vždy". Pouze po spotter – klíčové slovo zjistí frázi mluvené aktivační událost klientská aplikace shromažďovat vstup z mikrofon a odeslat do služby řeči. *Spustit* hodnota `Microphone` metrika zaznamenává dobu, kdy spotter – klíčové slovo oznámení klienta k použití vstupu z mikrofonu. *End* hodnota `Microphone` metrika zaznamenává dobu, pokud klientská aplikace byla zastavena, streamování zvuk ke službě po jeho přijetí `speech.endDetected` zpráv ze služby.

* Klientská aplikace má přístup k konstantní zvukový datový proud a provádí tohoto zvuk datového proudu v nečinnosti nebo řeči detekce *modul pro zjišťování řeči*. *Spustit* hodnota `Microphone` metrika zaznamená čas při *modul pro zjišťování řeči* oznámení klienta k použití vstup z zvuk datového proudu. *End* hodnota `Microphone` metrika zaznamenává dobu, pokud klientská aplikace byla zastavena, streamování zvuk ke službě po jeho přijetí `speech.endDetected` zpráv ze služby.

* Klientská aplikace zpracovává druhý zapnout více zapnout požadavku a o tom informováni prostřednictvím služby zprávu odpovědi aktivovat mikrofon získat vstup pro druhý zapnout. *Spustit* hodnota `Microphone` metrika zaznamenává dobu, pokud klientská aplikace umožňuje mikrofon a spustí pomocí vstup z tohoto zvuk zdroje. *End* hodnota `Microphone` metrika zaznamenává dobu, pokud klientská aplikace byla zastavena, streamování zvuk ke službě po jeho přijetí `speech.endDetected` zpráv ze služby.

*End* čas hodnotu `Microphone` metrika zaznamenává dobu, pokud klientská aplikace byla zastavena, streamování zvuk vstup. Ve většině případů krátce po klient obdržel dojde k této události `speech.endDetected` zpráv ze služby. Klientské aplikace třeba ověřit, že jejich jste správně odpovídají protokol tím, který zajistí *End* čas hodnotu `Microphone` metrika dojde později než časová hodnota přijetí pro `speech.endDetected` zprávy. A vzhledem k tomu obvykle dochází ke zpoždění mezi koncem jeden zapnout a spuštění jiný zapnout, klienti mohou ověřte protokol shoda zajistit, aby *spustit* času `Microphone` metriky pro všechny následné zapnout správně zaznamená čas při klienta *spuštění* pomocí mikrofon zvuk vstupu datového proudu ke službě.

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Název | Mikrofon | Požaduje se |
| Start | Čas zahájení klienta na použití zvuk vstup z mikrofon nebo jiné zvukový datový proud nebo přijímat aktivační událost z spotter – klíčové slovo | Požaduje se |
| Konec | Čas, kdy klient zastavit pomocí datového proudu mikrofon nebo ve zvukovém souboru | Požaduje se |
| Chyba | Popis chyby, k níž došlo, pokud existuje. Pokud mikrofon operace, které byly úspěšné, musí klienti vynechat v tomto poli. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případech chyb, v opačném případě vynechání |

### <a name="metric-listeningtrigger"></a>Metrika `ListeningTrigger`
`ListeningTrigger` Metrika měří čas, kdy uživatel provede akci, která zahájí hlasový vstup. `ListeningTrigger` Metrika je nepovinný, ale doporučujeme, aby tak se klienti, kteří nabízejí tato metrika.

Následující příklady použijte jako vodítko pro záznam *spustit* a *End* časové hodnoty pro `ListeningTrigger` metriky v klientské aplikace.

* Klientská aplikace vyžaduje, že uživatel musí stiskněte fyzické tlačítko Spustit mikrofonu. *Spustit* hodnota tato metrika zaznamená čas nabízené tlačítko. *End* hodnota zaznamenává dobu, po dokončení nabízené tlačítko.

* Klientská aplikace používá spotter – klíčové slovo, která naslouchá "vždy". Po klíčové slovo spotter zjistí fráze mluvené aktivační událost, klientská aplikace načte vstup z mikrofon a odešle ji do řeči služby. *Spustit* hodnota tato metrika zaznamenává čas, kdy spotter – klíčové slovo přijal zvuk, pak zjištěného jako frázi aktivační události. *End* hodnota zaznamenává dobu, pokud byl poslední slovo fráze aktivační událost oznamována uživatelem.

* Klientská aplikace má přístup k konstantní zvukový datový proud a provádí tohoto zvuk datového proudu v nečinnosti nebo řeči detekce *modul pro zjišťování řeči*. *Spustit* hodnota tato metrika zaznamenává dobu, *modul pro zjišťování řeči* přijatých zvuk, který pak detekovala jako řeči. *End* hodnotu zaznamená čas při *modul pro zjišťování řeči* zjistil řeči.

* Klientská aplikace zpracovává druhý zapnout více zapnout požadavku a o tom informováni prostřednictvím služby zprávu odpovědi aktivovat mikrofon získat vstup pro druhý zapnout. Klientská aplikace by měla *není* zahrnují `ListeningTrigger` metriky pro tento režim.

| Pole | Popis | Využití |
| ----- | ----------- | ----- |
| Název | ListeningTrigger | Nepovinné |
| Start | Čas zahájení naslouchání aktivační událost klienta | Požaduje se |
| Konec | Čas dokončení naslouchání aktivační událost klienta | Požaduje se |
| Chyba | Popis chyby, k níž došlo, pokud existuje. Pokud operace aktivační událost byla úspěšná, musí klienti vynechejte toto pole. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro případech chyb, v opačném případě vynechání |

#### <a name="sample-message"></a>Ukázkovou zprávu

V následujícím příkladu vidíte zpráva telemetrie s ReceivedMessages a metriky částí:

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

Tato část popisuje typy chybových zprávách a podmínky, které aplikace potřebuje zpracovat.

### <a name="http-status-codes"></a>Stavové kódy HTTP

Během upgradu žádost protokolu WebSocket, řeči služba může vrátit všechny standardní stavové kódy HTTP, jako například `400 Bad Request`atd. Aplikace musí správně zpracovávat tyto chybové stavy.

#### <a name="authorization-errors"></a>Chyby autorizace

Když nesprávný autorizace je zadané během upgradu protokolu WebSocket Služba rozpoznávání řeči vrací HTTP `403 Forbidden` stavový kód. Mezi podmínky, které můžete tento kód chyby je:

* Chybí *autorizace* záhlaví

* Neplatný autorizační token

* Vypršela platnost autorizační token

`403 Forbidden` Chybová zpráva není znamenat problém související s řeči služby. Tato chybová zpráva znamená problém s klientskou aplikaci.

### <a name="protocol-violation-errors"></a>Porušení chyb protokolu

Pokud služba rozpoznávání řeči zjistí veškerá porušení zásad protokolu z klienta, služba ukončuje připojení protokolu WebSocket po návratu *stavový kód* a *důvod* pro ukončení. Klientské aplikace můžete použít tyto informace k řešení potíží a opravit porušení zásad.

#### <a name="incorrect-message-format"></a>Formát nesprávné zprávy

Pokud klient odešle do služby, která není ve správném formátu v téhle specifikaci kódovaný text nebo zprávy v binární, služba uzavře připojení s *1007 Neplatná datová část Data* stavový kód. 

Služba vrátí tento kód stavu z různých důvodů, jak je znázorněno v následujících příkladech:

* "Formát nesprávné zprávy. Binární zpráva má neplatné záhlaví velikost předponu". Klient odešle binární zprávu, která má předponu Neplatná hlavička velikost.

* "Formát nesprávné zprávy. Zprávy v binární má velikost Neplatná hlavička". Klient odešle binární zprávu, že zadaný pro velikost Neplatná hlavička.

* "Formát nesprávné zprávy. Záhlaví zprávy v binární do UTF-8 dekódování se nezdařilo." Klient odešle binární zprávu, která obsahuje hlavičky, které nebyly správně kódovaný ve formátu UTF-8.

* "Formát nesprávné zprávy. Textová zpráva neobsahuje žádná data." Klient poslali textovou zprávu, která neobsahuje žádná data textu.

* "Formát nesprávné zprávy. Textová zpráva do UTF-8 dekódování se nezdařilo." Klient poslali textovou zprávu, která nebyla správně kódovaný ve formátu UTF-8.

* "Formát nesprávné zprávy. Textová zpráva obsahuje bez oddělovače záhlaví". Klient poslali textovou zprávu, který neobsahoval oddělovače záhlaví nebo použít oddělovače záhlaví nesprávné.

#### <a name="missing-or-empty-headers"></a>Hlavičky chybí nebo je prázdný

Pokud klient odešle zprávu, která nemá požadované hlavičky *X-RequestId* nebo *cesta*, zavře připojení ke službě *Chyba protokolu 1002* stavový kód. Zpráva je "hlavička chybí nebo je prázdný. {Záhlaví name}.

#### <a name="requestid-values"></a>ID žádosti hodnoty

Pokud klient odešle zprávu, která určuje *X-RequestId* hlavičky v nesprávném formátu, službu uzavře připojení a vrátí *Chyba protokolu 1002* stavu. Zpráva je "Neplatný požadavek. Hodnota hlavičky X-RequestId nebyla zadána ve formátu UUID ne dash."

#### <a name="audio-encoding-errors"></a>Zvuk chyby kódování

Pokud klient pošle zvuk bloků dat, který iniciuje zapnout a zvukových formát nebo kódování neodpovídají požadovanou specifikaci, služba uzavře připojení a vrátí *1007 Neplatná datová část Data* stavový kód. Zpráva udává formát kódování zdroj chyb.

#### <a name="requestid-reuse"></a>Opakované použití ID žádosti

Po dokončení zapněte, pokud klient pošle zprávu, která opětovně používá identifikátoru žádosti z tohoto zapnout, služba uzavře připojení a vrátí *Chyba protokolu 1002* stavový kód. Zpráva je "Neplatný požadavek. Opakované použití identifikátorů požadavek není povoleno."

## <a name="connection-failure-telemetry"></a>Telemetrie selhání připojení

Aby nejlepší uživatelské prostředí, musí klienti informovat službu řeči časová razítka důležité kontrolních bodů v rámci připojení pomocí *telemetrie* zprávy. Je stejně důležité, aby klienti informovat službu připojení, které byly pokus nezdařil.

Pro každý pokus o připojení, se nezdařilo, klienti vytvořit *telemetrie* zpráva s jedinečným *X-RequestId* hodnotu hlavičky. Protože klient nebylo možné navázat připojení, *ReceivedMessages* pole v textu JSON je lze vynechat. Pouze `Connection` položku *metriky* pole je součástí. Tato položka zahrnuje spuštění a end časová razítka, jakož i chybový stav, který byl zjištěn.

### <a name="connection-retries-in-telemetry"></a>Opakování připojení v telemetrie

Klienti měli rozlišit *opakování* z *více pokusů připojení* událostí, která aktivuje pokus o připojení. Pokusy o připojení, které se provádějí prostřednictvím kódu programu bez jakéhokoli uživatelského vstupu jsou opakování. Více pokusů o připojení, které se provádějí v reakci na vstup uživatele jsou více pokusů o připojení. Klienti poskytnout každý pokus o připojení uživatele aktivované jedinečný *X-RequestId* a *telemetrie* zprávy. Znovu použít klienti *X-RequestId* pro programové opakování. Pokud první pokus byly provedeny pro pokus o jednom připojení, je zahrnuta jako jednotlivé pokusy o opakování `Connection` položku v *telemetrie* zprávy.

Předpokládejme například, že uživatel mluví – klíčové slovo aktivační události na spuštění připojení a první pokus o připojení se nezdaří z důvodu chyb DNS. Druhý pokus o vytvořený prostřednictvím kódu programu klientem však úspěšné. Protože klient opakovat připojení bez nutnosti další vstup od uživatele, klient použije jeden *telemetrie* zpráva s více `Connection` položky k popisu připojení.

Například předpokládejme, že uživatel hovoří – klíčové slovo aktivační události na spuštění připojení a tento pokus o připojení neúspěšný po třech pokusech. Klient pak poskytuje, zastaví se pokouší o připojení ke službě a informuje uživatele, že se něco pokazilo. Uživatel pak mluví aktivační událost – klíčové slovo znovu. Tentokrát Předpokládejme, že se klient připojí ke službě. Po připojení, ihned odešle *telemetrie* zpráv do služby, která obsahuje tři `Connection` položky, které popisují selhání připojení. Po přijetí `turn.end` zpráva, klient odešle jiné *telemetrie* zpráva, která popisuje úspěšné připojení.

## <a name="error-message-reference"></a>Referenční příručka chybových zpráv

### <a name="http-status-codes"></a>Stavové kódy HTTP

| Stavový kód HTTP | Popis | Řešení potíží |
| - | - | - |
| 400 – Chybný požadavek | Klient odešle žádost o připojení protokolu WebSocket nesprávný. | Zkontrolujte, zda jste zadali všechny požadované parametry a hlavičky protokolu HTTP a správnost hodnoty. |
| 401 unauthorized | Klienta neobsahuje informace o vyžaduje ověření. | Zkontrolujte, zda chcete odeslat *autorizace* hlavičky v připojení protokolu WebSocket. |
| 403 Zakázáno | Klient odešle informace o ověření, ale je neplatný. | Zkontrolujte, zda nejsou odesílání vypršela platnost nebo neplatná hodnota v *autorizace* záhlaví. |
| 404 – Nenalezeno | Klient se pokusil o přístup k cestě adresy URL, která není podporována. | Zkontrolujte, že používáte správnou adresu URL pro připojení protokolu WebSocket. |
| 500 Chyba serveru | Služba zjistila vnitřní chybu a nemůže vyhovět požadavku. | Ve většině případů se tato chyba je přechodná. Opakujte žádost. |
| 503 – Nedostupná služba | Službu nebylo k dispozici pro zpracování požadavku. | Ve většině případů se tato chyba je přechodná. Opakujte žádost. |

### <a name="websocket-error-codes"></a>Kódy chyb protokolu WebSocket

| WebSocketsStatus kódu | Popis | Řešení potíží |
| - | - | - |
| 1000 normální uzavření | Služba ukončila připojení protokolu WebSocket bez chyby. | Pokud se uzavření protokolu WebSocket, znovu načíst v dokumentaci k zajištění, že chápete, jak a kdy službu můžete ukončit připojení protokolu WebSocket. |
| Chyba protokolu 1002 | Klientovi se nepodařilo splnit požadavky na protokol. | Zajistěte, aby vám pochopit dokumentace k protokolu a zřetelné o požadavcích na. Přečtěte si předchozí dokumentaci o z důvodů chyby zobrazíte, když jste narušení požadavky protokolu. |
| 1007 Neplatná datová část dat | Klient zaslal Neplatná datová část ve zprávě protokolu. | Zkontrolujte poslední zprávu, která odešle do služby pro chyby. Přečtěte si předchozí dokumentaci o datovou část chyby. |
| Chyba serveru 1011 | Služba zjistila vnitřní chybu a nemůže vyhovět požadavku. | Ve většině případů se tato chyba je přechodná. Opakujte žádost. |

## <a name="related-topics"></a>Související témata

V tématu [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) tedy implementace protokolu řeči služeb na základě protokolu WebSocket.
