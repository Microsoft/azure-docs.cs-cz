---
title: Protokol Zpracování řeči Bingu protokolu WebSocket | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Dokumentace k protokolu pro Zpracování řeči Bingu založená na WebSockets
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966891"
---
# <a name="bing-speech-websocket-protocol"></a>Protokol Zpracování řeči Bingu protokolu WebSocket

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Zpracování řeči Bingu je cloudová platforma, která nabízí nejběžnější algoritmy, které jsou k dispozici pro převod mluveného zvuku na text. Protokol Zpracování řeči Bingu definuje [nastavení připojení](#connection-establishment) mezi klientskými aplikacemi a službou a zprávami pro rozpoznávání řeči vyměňované mezi protějšky ([zprávy na straně klienta](#client-originated-messages) a zprávy, které byly [vytvořeny podle služby](#service-originated-messages) ). ). Kromě toho jsou popsány [zprávy telemetrie](#telemetry-schema) a [zpracování chyb](#error-handling) .

## <a name="connection-establishment"></a>Vytváření připojení

Protokol služby Speech následuje Standard specifikace protokolu WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Připojení protokolu WebSocket se spouští jako požadavek HTTP, který obsahuje hlavičky HTTP, které označují přání klienta upgradovat připojení k objektu WebSocket namísto použití sémantiky protokolu HTTP. Server označuje, že se jeho ochota zapojit do připojení protokolu WebSocket, a `101 Switching Protocols` to vrácením odpovědi HTTP. Po výměně této metody handshake ponechá klient i služba soket otevřený a začne používat protokol založený na zprávách k posílání a přijímání informací.

Pokud chcete spustit metodu handshake protokolu WebSocket, klientská aplikace odešle službě požadavek HTTPS GET. Obsahuje standardní hlavičky pro upgrade WebSocket spolu s jinými hlavičkami, které jsou specifické pro řeč.

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

Služba reaguje na:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Všechny požadavky na řeč vyžadují šifrování [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) . Použití nešifrovaných požadavků na řeč není podporováno. Podporuje se následující verze TLS:

* TLS 1.2

### <a name="connection-identifier"></a>Identifikátor připojení

Služba Speech vyžaduje, aby všichni klienti měli k identifikaci připojení jedinečný identifikátor. Při spuštění metody handshake pro WebSocket *musí* klienti zahrnovat hlavičku *X-ConnectionID* . Hlavičkou *X-ConnectionID* musí být hodnota UUID ( [Universal Unique Identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier) ). Požadavky na upgrade protokolu WebSocket, které neobsahují *x-ConnectionID*, nezadávejte hodnotu pro hlavičku *x-ConnectionID* , nebo nezahrnují platnou hodnotu UUID, kterou služba odmítla s odpovědí HTTP `400 Bad Request` .

### <a name="authorization"></a>Authorization

Kromě standardních hlaviček ověřování pomocí protokolu handshake vyžadují požadavky na zadání řeči hlavičku *autorizace* . Žádosti o připojení bez této hlavičky jsou službou odmítnuty pomocí odpovědi HTTP `403 Forbidden` .

*Autorizační* hlavička musí obsahovat přístupový token JSON web token (Jwt).

Informace o tom, jak se přihlásit k odběru a získat klíče rozhraní API, které se používají k načtení platných přístupových tokenů JWT, najdete na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) .

Klíč rozhraní API se předává službě tokenů. Příklad:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Pro přístup k tokenu jsou vyžadovány následující informace hlavičky.

| Name | Formát | Popis |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Váš klíč předplatného |

Tokenová služba vrátí přístupový token JWT jako `text/plain`. Pak se token JWT předává jako `Base64 access_token` k metodě handshake jako *autorizační* hlavička s předponou s řetězcem `Bearer`. Příklad:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Soubory cookie

Klienti *musí* podporovat soubory cookie protokolu HTTP, jak jsou uvedeny v [dokumentu RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Přesměrování protokolu HTTP

Klienti *musí* podporovat standardní mechanismy přesměrování určené [specifikací protokolu HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Koncové body řeči

Klienti *musí* používat příslušný koncový bod služby Speech. Koncový bod je založený na režimu rozpoznávání a jazyku. V tabulce jsou uvedeny některé příklady.

| Režim | `Path` | Identifikátor URI služby |
| -----|-----|-----|
| Interaktivní | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konverzace | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Diktování | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Další informace najdete na stránce s [identifikátorem URI služby](../GetStarted/GetStartedREST.md#service-uri) .

### <a name="report-connection-problems"></a>Nahlásit problémy s připojením

Klienti by měli hned ohlásit všechny problémy, ke kterým došlo při navazování připojení. Protokol zpráv pro oznamování neúspěšných připojení je popsán v tématu [telemetrie selhání připojení](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Omezení doby trvání připojení

Ve srovnání s typickými připojeními HTTP webové služby se připojení protokolu WebSocket po *dlouhou* dobu. Služba rozpoznávání řeči umístí omezení doby trvání připojení protokolu WebSocket ke službě:

 * Maximální doba trvání aktivního připojení protokolu WebSocket je 10 minut. Připojení je aktivní, pokud služba nebo klient odesílá zprávy protokolu WebSocket prostřednictvím tohoto připojení. Služba ukončí připojení bez upozornění, když je dosaženo limitu. Klienti by měli vyvíjet uživatelské scénáře, které nevyžadují, aby připojení zůstalo aktivní při maximálním trvání připojení nebo téměř.

 * Maximální doba trvání neaktivního připojení protokolu WebSocket je 180 sekund. Připojení je neaktivní, pokud ani služba ani klient neodeslali v rámci připojení zprávu protokolu WebSocket. Po dosažení maximální aktivní životnosti ukončí služba neaktivní připojení protokolu WebSocket.

## <a name="message-types"></a>Typy zpráv

Po navázání připojení protokolu WebSocket mezi klientem a službou může klient i služba odesílat zprávy. Tato část popisuje formát těchto zpráv protokolu WebSocket.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) určuje, zda zprávy protokolu WebSocket mohou přenášet data buď pomocí textu, nebo binárního kódování. Tato dvě kódování používají jiné formáty přenosů. Každý formát je optimalizován pro efektivní kódování, přenos a dekódování datové části zprávy.

### <a name="text-websocket-messages"></a>Textové zprávy protokolu WebSocket

Textové zprávy protokolu WebSocket přenášejí datovou část textových informací, které se skládají z části záhlaví a textu odděleného dvojici řádků s dvojitým návratem na konci používané pro zprávy HTTP. A podobně jako zprávy protokolu HTTP, textové zprávy WebSocket určují záhlaví v *názvu: formát hodnoty* oddělený dvojicí s návratovou hodnotou s jedním koncem řádku. Libovolný text obsažený v textové zprávě WebSocket *musí* používat kódování [UTF-8](https://tools.ietf.org/html/rfc3629) .

Textové zprávy protokolu WebSocket musí v *cestě*k záhlaví určovat cestu k této zprávě. Hodnota této hlavičky musí být jedním z typů zpráv protokolu řeči definovaných dále v tomto dokumentu.

### <a name="binary-websocket-messages"></a>Binární zprávy protokolu WebSocket

Binární datová část zprávy protokolu WebSocket je binární. V protokolu služby Speech se ke službě přenáší a přijímá zvuk pomocí binárních zpráv protokolu WebSocket. Všechny ostatní zprávy jsou textové zprávy protokolu WebSocket.

Podobně jako textové zprávy protokolu WebSocket se zprávy binárního protokolu WebSocket skládají z záhlaví a části těla. První 2 bajty binární zprávy protokolu WebSocket určují v pořadí [big-endian](https://en.wikipedia.org/wiki/Endianness) 16bitové celočíselnou velikost oddílu záhlaví. Minimální velikost oddílu hlavičky je 0 bajtů. Maximální velikost je 8 192 bajtů. Text v hlavičkách binárních zpráv protokolu WebSocket *musí* používat kódování [US-ASCII](https://tools.ietf.org/html/rfc20) .

Hlavičky v binární zprávě protokolu WebSocket jsou kódované ve stejném formátu jako v textových zprávách protokolu WebSocket. Formát *Název: hodnota* se oddělí dvojicí nového řádku s návratovou hodnotou s jedním znakem. Binární zprávy protokolu WebSocket musí v *cestě*záhlaví určovat cestu k této zprávě. Hodnota této hlavičky musí být jedním z typů zpráv protokolu řeči definovaných dále v tomto dokumentu.

V protokolu služby Speech se používají textové i binární zprávy protokolu WebSocket.

## <a name="client-originated-messages"></a>Zprávy pocházející od klienta

Po navázání připojení může klient i služba spustit odesílání zpráv. Tato část popisuje formát a datovou část zpráv, které klientské aplikace odesílají službě Speech. V části [Služba – zprávy](#service-originated-messages) , které pocházejí z hlasové služby a jsou odesílány klientským aplikacím.

Hlavní zprávy odesílané klientem do služeb jsou `speech.config`zprávy, `audio`a `telemetry` . Předtím, než se podíváme na každou zprávu, jsou popsána společná požadovaná záhlaví zpráv pro všechny tyto zprávy.

### <a name="required-message-headers"></a>Požadovaná záhlaví zpráv

Pro všechny zprávy z klienta jsou vyžadovány následující hlavičky.

| Záhlaví | Value |
|----|----|
| `Path` | Cesta ke zprávě, jak je uvedeno v tomto dokumentu |
| X-RequestId | UUID ve formátu "bez čárek" |
| X-Timestamp | Časové razítko času UTC klienta ve formátu ISO 8601 |

#### <a name="x-requestid-header"></a>Hlavička X-RequestId

Požadavky na klienta jsou jedinečně identifikovány hlavičkou zprávy *X-RequestId* . Tato hlavička je vyžadována pro všechny zprávy z klienta. Hodnota hlavičky *X-RequestId* musí být identifikátor UUID ve tvaru "No-spojovník", například *123e4567e89b12d3a456426655440000*. *Nemůže* být ve formě kanonického tvaru *123e4567-e89b-12d3-A456-426655440000*. Požadavky bez hlavičky *X-RequestId* nebo s hodnotou hlavičky, která používá nesprávný formát pro identifikátory UUID způsobí, že služba ukončí připojení protokolu WebSocket.

#### <a name="x-timestamp-header"></a>Záhlaví X – časové razítko

Každá zpráva odeslaná službě Speech pomocí klientské aplikace *musí* obsahovat hlavičku *X-timestamp* . Hodnota tohoto záhlaví je čas, kdy klient zprávu pošle. Požadavky bez záhlaví *X-timestamp* nebo s hodnotou hlavičky, která používá nesprávný formát, způsobí ukončení připojení protokolu WebSocket službou.

Hodnota záhlaví *X-timestamp* musí být ve formátu ' yyyy-yyyy '-DD 't ': ' mm ': ' ss '. ' fffffffZ "je-li" fffffff "zlomkem sekundy. Například ' 12,5 ' znamená ' 12 + 5/10 sekund ' a ' 12,526 ' znamená ' 12 Plus 526/1000 sekund '. Tento formát vyhovuje normě [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a na rozdíl od standardního záhlaví *data* protokolu HTTP může poskytovat řešení milisekund. Klientské aplikace mohou zaokrouhlit časová razítka na nejbližší milisekundu. Klientské aplikace musí zajistit, aby zařízení přesně sleduje čas pomocí [serveru NTP (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Zpráva`speech.config`

Služba Speech potřebuje znát charakteristiky vaší aplikace, aby poskytovala nejlepší možné rozpoznávání řeči. Údaje o požadovaných vlastnostech obsahují informace o zařízení a operačním systému, které vaši aplikaci využívají. Tyto informace zadáte do `speech.config` zprávy.

Klienti *musí* poslat `speech.config` zprávu hned po navázání připojení ke službě Speech Service a před odesláním jakýchkoli `audio` zpráv. Pro každé připojení je potřeba `speech.config` poslat zprávu jenom jednou.

| Pole | Popis |
|----|----|
| Kódování zpráv protokolu WebSocket | Text |
| Tělo | Datová část jako struktura JSON |

#### <a name="required-message-headers"></a>Požadovaná záhlaví zpráv

| Název hlavičky | Value |
|----|----|
| `Path` | `speech.config` |
| X-Timestamp | Časové razítko času UTC klienta ve formátu ISO 8601 |
| Typ obsahu | aplikace/JSON; charset = UTF-8 |

Stejně jako u všech zpráv pocházejících od klienta v protokolu `speech.config` služby Speech *musí* zpráva obsahovat hlavičku *X-timestamp* , která zaznamenává časovou dobu času UTC klienta při odeslání zprávy do služby. Zpráva nevyžaduje záhlaví *X-RequestId* , protože tato zpráva není přidružena k konkrétnímu požadavku na řeč. `speech.config`

#### <a name="message-payload"></a>Datová část zprávy
Datová část `speech.config` zprávy je struktura JSON, která obsahuje informace o aplikaci. Následující příklad ukazuje tyto informace. Informace o kontextu klienta a zařízení jsou obsaženy v elementu *kontextu* struktury JSON.

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

##### <a name="system-element"></a>Systémový element

Element System. Version `speech.config` zprávy obsahuje verzi softwaru sady Speech SDK, kterou používá klientská aplikace nebo zařízení. Hodnota je ve formátu *hlavní. podverze. sestavení. větev*. Komponentu *větve* můžete vynechat, pokud není k dispozici.

##### <a name="os-element"></a>Element OS

| Pole | Popis | Použití |
|-|-|-|
| OS. Platform | Platforma operačního systému, která hostuje aplikaci, například Windows, Android, iOS nebo Linux |Požadováno |
| os.name | Název produktu operačního systému, například Debian nebo Windows 10 | Požadováno |
| OS. Version | Verze operačního systému ve formátu *hlavní. podverze. sestavení. větev* | Požadováno |

##### <a name="device-element"></a>Prvek zařízení

| Pole | Popis | Použití |
|-|-|-|
| zařízení. Manufacturer | Výrobce hardwaru zařízení | Požadováno |
| device.model | Model zařízení | Požadováno |
| verze zařízení. verze | Verze softwaru zařízení poskytovaná výrobcem zařízení. Tato hodnota určuje verzi zařízení, kterou může výrobce sledovat. | Požadováno |

### <a name="message-audio"></a>Zpráva`audio`

Klientské aplikace s podporou řeči odesílají zvuk do služby Speech převodem zvukového datového proudu na řadu zvukových bloků dat. Každý blok zvuku přináší segment mluveného zvuku, který je přepisu službou. Maximální velikost jednoho zvukového bloku je 8 192 bajtů. Zprávy o zvukovém streamu jsou *binární zprávy protokolu WebSocket*.

Klienti používají `audio` zprávu k odeslání zvukového bloku do služby. Klienti čtou zvuk z mikrofonu v blocích a odesílají tyto bloky do služby Speech pro přepis. První `audio` zpráva musí obsahovat správné záhlaví, které správně určuje, že zvuk vyhovuje jednomu z formátů kódování, které služba podporuje. Další `audio` zprávy obsahují jenom data binárního zvukového proudu načtená z mikrofonu.

Klienti mohou volitelně odeslat `audio` zprávu s textem s nulovou délkou. Tato zpráva oznamuje službě, že klient ví, že uživatel přestal mluvit, že utterance je dokončený a mikrofon je vypnutý.

Služba Speech používá první `audio` zprávu, která obsahuje jedinečný identifikátor požadavku k signalizaci začátku nového cyklu žádosti nebo odpovědi nebo *vypnutí*. Jakmile služba obdrží `audio` zprávu s novým identifikátorem žádosti, zahodí všechny zprávy ve frontě nebo neodeslaných zprávách, které jsou přidruženy k jakékoli předchozí zapínání.

| Pole | Popis |
|-------------|----------------|
| Kódování zpráv protokolu WebSocket | Binary |
| Tělo | Binární data zvukového bloku Maximální velikost je 8 192 bajtů. |

#### <a name="required-message-headers"></a>Požadovaná záhlaví zpráv

Pro všechny `audio` zprávy jsou vyžadovány následující hlavičky.

| Záhlaví         |  Value     |
| ------------- | ---------------- |
| `Path` | `audio` |
| X-RequestId | UUID ve formátu "bez čárek" |
| X-Timestamp | Časové razítko času UTC klienta ve formátu ISO 8601 |
| Typ obsahu | Typ zvukového obsahu. Typ musí být *audio/x-WAV* (PCM) nebo *audio/hedvábí* (hedvábí). |

#### <a name="supported-audio-encodings"></a>Podporovaná kódování zvuku

Tato část popisuje zvukové kodeky podporované službou Speech.

##### <a name="pcm"></a>PCM

Služba Speech přijímá nekomprimovaný zvuk PCM (Pulse Code modulace). Zvuk se pošle do služby ve formátu [WAV](https://en.wikipedia.org/wiki/WAV) , takže první zvukový blok *musí* obsahovat platný nadpis formátu RIFF ( [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) ). Pokud klient zahájí zapínání zvukového bloku, *který neobsahuje platnou* hlavičku riff, služba žádost odmítne a ukončí připojení protokolu WebSocket.

Zvuk PCM se *musí* nacházet ve 16 kHz a 16 bitech na vzorek a jeden kanál (*RIFF-16khz-16bitový-mono-PCM*). Služba Speech nepodporuje streamy stereofonních zvuků a odmítá zvukové streamy, které nepoužívají zadanou přenosovou rychlost, vzorkovací frekvenci nebo počet kanálů.

##### <a name="opus"></a>Opus

Opus je otevřený, vysoce všestranný zvukový kodek bez licenčních práv. Služba Speech podporuje Opus konstantní přenosovou rychlostí `32000` nebo. `16000` V současné době je podporován pouze `audio/ogg` kontejnerproOpus,kterýjeurčentypemMIME.`OGG`

Chcete-li použít Opus, upravte [ukázku JavaScriptu](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) a změňte `RecognizerSetup` metodu, která se má vrátit.

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

Lidé po dokončení mluvení nesignalizují explicitně. Všechny aplikace, které přijímají rozpoznávání řeči jako vstup, mají dvě možnosti pro zpracování konce řeči ve zvukovém streamu: rozpoznávání koncových znaků a zjišťování konce řeči klienta. Z těchto dvou možností rozpoznávání koncových řeči služby obvykle nabízí lepší uživatelské prostředí.

##### <a name="service-end-of-speech-detection"></a>Zjišťování konce řeči služby

Aby bylo možné vytvořit ideální prostředí pro hlasový vstup bez obsluhy, aplikace umožní, aby služba zjišťoval, kdy uživatel dokončí domluvu. Klienti odesílají zvuk z mikrofonu jako *zvukového* bloku, dokud služba nedetekuje tichou `speech.endDetected` zprávu a odpoví zpátky zprávou.

##### <a name="client-end-of-speech-detection"></a>Zjišťování konce řeči klienta

Služba, která signalizuje, může také klientským aplikacím umožnit uživateli signál ke konci řeči. Například klientská aplikace může mít tlačítko Zastavit nebo ztlumit, které může uživatel stisknout. K signalizaci ukončení řeči klientské aplikace odesílají zprávu *zvukového* bloku s textem s nulovou délkou. Služba Speech tuto zprávu interpretuje jako konec příchozího zvukového streamu.

### <a name="message-telemetry"></a>Zpráva`telemetry`

Klientské aplikace *musí* na konci každé z nich potvrdit odeslání telemetrie o službě rozpoznávání řeči. Změna koncového oznámení umožňuje službě Speech Service zajistit, aby byly všechny zprávy potřebné k dokončení požadavku a jeho odpověď správně přijaty klientem. Potvrzení na konci taky umožňuje službě Speech Service ověřit, jestli jsou klientské aplikace prováděné podle očekávání. Tyto informace jsou nevýznamné, pokud potřebujete pomoc při řešení potíží s aplikací podporujícími rozpoznávání řeči.

Klienti musí na konci zaslat `telemetry` zprávu odesláním zprávy hned po `turn.end` přijetí zprávy. Klienti by se měli pokusit `turn.end` co nejdříve o potvrzení. Pokud klientská aplikace nemůže potvrdit ukončení, služba řeči může ukončit připojení s chybou. Klienti musí poslat jenom jednu `telemetry` zprávu pro každý požadavek a odpověď identifikovanou hodnotou *X-RequestId* .

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `telemetry` |
| X-Timestamp | Časové razítko času UTC klienta ve formátu ISO 8601 |
| Typ obsahu | `application/json` |
| Tělo | Struktura JSON, která obsahuje informace o klientech o zapnutí |

Schéma pro tělo `telemetry` zprávy je definováno v části [schéma telemetrie](#telemetry-schema) .

#### <a name="telemetry-for-interrupted-connections"></a>Telemetrie pro přerušená připojení

Pokud dojde k chybě síťového připojení z jakéhokoli důvodu a klient `turn.end` *neobdrží zprávu* od služby, klient pošle `telemetry` zprávu. Tato zpráva popisuje neúspěšný požadavek při dalším připojení klienta k této službě. Klienti se nemusí hned pokusit o připojení k odeslání `telemetry` zprávy. Zpráva může být v klientovi ukládána do vyrovnávací paměti a odeslána v budoucím připojení požadovaného uživatelem. Zpráva pro požadavek, který selhal, *musí* používat hodnotu *X-RequestId* z neúspěšné žádosti. `telemetry` Může se odeslat službě, jakmile se naváže připojení, a to bez čekání na odeslání nebo přijetí dalších zpráv.

## <a name="service-originated-messages"></a>Zprávy, které pocházejí ze služby

Tato část popisuje zprávy, které pocházejí z hlasové služby a jsou odesílány klientovi. Služba Speech Service udržuje registr funkcí klienta a generuje zprávy vyžadované jednotlivými klienty, takže ne všichni klienti obdrží všechny zprávy, které jsou zde popsány. V případě zkrácení jsou zprávy odkazovány hodnotou záhlaví *path* . Například odkazujeme na textovou zprávu WebSocket s hodnotou `speech.hypothesis` *cesty* jako na řeč. hypotéza.

### <a name="message-speechstartdetected"></a>Zpráva`speech.startDetected`

`speech.startDetected` Zpráva označuje, že služba rozpoznávání řeči zjistila rozpoznávání řeči ve zvukovém streamu.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `speech.startDetected` |
| Typ obsahu | aplikace/JSON; charset = UTF-8 |
| Tělo | Struktura JSON obsahující informace o podmínkách, kdy byl zjištěn začátek rozpoznávání řeči. Pole *posun* v této struktuře určuje posun (v jednotkách 100 – nanosekund), kdy byl ve zvukovém streamu zjištěn řeč vzhledem k začátku streamu. |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Zpráva`speech.hypothesis`

Při rozpoznávání řeči služba Speech Service pravidelně generuje hypotézu o slovech, které služba rozpoznala. Služba Speech odesílá tyto hypotézy klientovi přibližně každých 300 milisekund. Je vhodný jenom pro vylepšení uživatelského prostředí pro rozpoznávání řeči. `speech.hypothesis` Nemusíte mít žádnou závislost na obsahu ani přesnosti textu v těchto zprávách.

 Tato `speech.hypothesis` zpráva se vztahuje na klienty, kteří mají některé možnosti vykreslování textu, a chtějí poskytnout zpětnou vazbu k převádění v reálném čase na osobu, která je mluví.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `speech.hypothesis` |
| X-RequestId | UUID ve formátu "bez čárek" |
| Typ obsahu | application/json |
| Tělo | Struktura rozpoznávání JSON pro řeč |

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

Element *offset* určuje posun (v jednotkách 100 – nanosekund), kdy byla fráze rozpoznána vzhledem k začátku zvukového datového proudu.

Element *Duration* určuje dobu trvání (v jednotkách 100 – nanosekund) této fráze řeči.

Klienti nesmí dělat žádné předpoklady o četnosti, časování ani textu, který je obsažený v řeči hypotéza nebo konzistenci textu v jakékoli dvou příslovnících hypotéza. Hypotéza je pouze snímky do procesu přepisu ve službě. Nepředstavují stabilní akumulaci přepisu. Například první funkce řeči hypotéza může obsahovat slova "jemné zábavné" a druhá hypotéza může obsahovat slova "Find Funny". Služba Speech Service neprovádí žádné následné zpracování (například velká a interpunkční znaménka) textu ve Speech hypotéze.

### <a name="message-speechphrase"></a>Zpráva`speech.phrase`

Pokud služba rozpoznávání řeči zjistí, že má dostatek informací pro vytvoření výsledku rozpoznávání, který se nezmění, služba vytvoří `speech.phrase` zprávu. Služba Speech vytvoří tyto výsledky poté, co zjistí, že uživatel dokončil větu nebo frázi.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `speech.phrase` |
| Typ obsahu | application/json |
| Tělo | Struktura JSON fráze pro rozpoznávání řeči |

Schéma JSON fráze pro rozpoznávání řeči obsahuje následující pole: `RecognitionStatus`, `DisplayText`, `Offset` `Duration`a. Další informace o těchto polích naleznete v tématu [přepisovat odpovědi](../concepts.md#transcription-responses).

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

### <a name="message-speechenddetected"></a>Zpráva`speech.endDetected`

`speech.endDetected` Zpráva určuje, že klientská aplikace by měla zastavit streamování zvuku do služby.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `speech.endDetected` |
| Tělo | Struktura JSON, která obsahuje posun po zjištění konce řeči. Posun je reprezentován v 100 jednotek nanosekund posun od začátku zvuku, který se používá pro rozpoznávání. |
| Typ obsahu | aplikace/JSON; charset = UTF-8 |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

Element *offset* určuje posun (v jednotkách 100 – nanosekund), kdy byla fráze rozpoznána vzhledem k začátku zvukového datového proudu.

### <a name="message-turnstart"></a>Zpráva`turn.start`

`turn.start` Signalizuje začátek z perspektivy služby. `turn.start` Zpráva je vždy první zpráva odpovědi, kterou dostanete pro libovolný požadavek. Pokud neobdržíte `turn.start` zprávu, Předpokládejme, že stav připojení služby není platný.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `turn.start` |
| Typ obsahu | aplikace/JSON; charset = UTF-8 |
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

Tělo `turn.start` zprávy je struktura JSON, která obsahuje kontext pro začátek odbočení. *Kontextový* prvek obsahuje vlastnost *serviceTag* . Tato vlastnost určuje hodnotu značky, kterou služba přidruží k funkci zapnout. Tuto hodnotu může společnost Microsoft využít, pokud potřebujete pomoc při řešení potíží s chybami ve vaší aplikaci.

### <a name="message-turnend"></a>Zpráva`turn.end`

`turn.end` Signalizuje konec tahu od perspektivy služby. `turn.end` Zpráva je vždy poslední zprávou odpovědi, kterou dostanete pro všechny žádosti. Klienti mohou používat příjem této zprávy jako signál pro aktivity vyčištění a přechod do stavu nečinnosti. Pokud neobdržíte `turn.end` zprávu, Předpokládejme, že stav připojení služby není platný. V těchto případech zavřete existující připojení ke službě a znovu se připojte.

| Pole | Popis |
| ------------- | ---------------- |
| Kódování zpráv protokolu WebSocket | Text |
| `Path` | `turn.end` |
| Tělo | Žádné |

#### <a name="sample-message"></a>Ukázková zpráva

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schéma telemetrie

Tělo zprávy *telemetrie* je struktura JSON, která obsahuje informace o klientovi o zapnutí nebo pokusu o připojení. Struktura se skládá z časových razítek klienta, která se zaznamenávají, když dojde k událostem klienta. Každé časové razítko musí být ve formátu ISO 8601, jak je popsáno v části s názvem "X-timestamp header". Zprávy *telemetrie* , které neurčují všechna povinná pole ve struktuře JSON nebo které nepoužívají správný formát časového razítka, mohou způsobit ukončení připojení ke klientovi. Klienti *musí* zadat platné hodnoty pro všechna povinná pole. Klienti *musí* zadat hodnoty pro volitelná pole, kdykoli to bude vhodné. Hodnoty uvedené v ukázkách v této části jsou pouze pro ilustraci.

Schéma telemetrie je rozdělené do následujících částí: přijatá časová razítka zpráv a metriky. Formát a použití jednotlivých částí jsou uvedeny v následujících částech.

### <a name="received-message-time-stamps"></a>Přijatá časová razítka zpráv

Klienti musí zahrnovat hodnoty času příjmu pro všechny zprávy, které obdrží po úspěšném připojení ke službě. Tyto hodnoty musí zaznamenávat čas, kdy klient *přijal* každou zprávu ze sítě. Hodnota by neměla zaznamenávat jiné časy. Klient by například neměl zaznamenávat čas, kdy se zpráva *jednala* o zprávu. Časová razítka přijaté zprávy jsou uvedena v poli *Název: hodnoty* . Název páru Určuje hodnotu *cesty* zprávy. Hodnota páru určuje čas klienta, kdy byla zpráva přijata. Nebo, pokud byla přijata více než jedna zpráva zadaného názvu, hodnota páru je pole časových razítek, která určují, kdy byly tyto zprávy přijaty.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienti *musí* potvrdit přijetí všech zpráv odesílaných službou zahrnutím časových razítek pro tyto zprávy v těle JSON. Pokud klient nepotvrdí přijetí zprávy, služba může připojení ukončit.

### <a name="metrics"></a>Metriky

Klienti musí obsahovat informace o událostech, ke kterým došlo během životnosti žádosti. Podporují se tyto metriky: `Connection`, `Microphone` `ListeningTrigger`a.

### <a name="metric-connection"></a>Metriky`Connection`

`Connection` Metrika určuje podrobnosti o pokusůch o připojení klienta. Metrika musí zahrnovat časová razítka při spuštění a dokončení připojení protokolu WebSocket. Metrika se vyžaduje *jenom při prvním zapnutí připojení.* `Connection` Následující povýšení není nutné k zahrnutí těchto informací. Pokud klient provede více pokusů o připojení před navázáním připojení, musí být zahrnuty informace o *všech* pokusůch o připojení. Další informace najdete v tématu [telemetrie selhání připojení](#connection-failure-telemetry).

| Pole | Popis | Použití |
| ----- | ----------- | ----- |
| Name | `Connection` | Požadováno |
| Id | Hodnota identifikátoru připojení, která se použila v hlavičce *X-ConnectionID* pro tento požadavek na připojení | Požadováno |
| Spustit | Čas, kdy klient odeslal požadavek na připojení | Požadováno |
| End | Čas, kdy klient obdržel oznámení, že připojení bylo úspěšně navázáno, nebo v případě chyb, zamítnutí, zamítnutí nebo selhání | Požadováno |
| Chyba | Popis chyby, ke které došlo, pokud existuje. Pokud bylo připojení úspěšné, klienti by měli toto pole vynechat. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro chybové případy, vynechává se jinak. |

Popis chyby by měl být nejvýše 50 znaků a v ideálním případě by měl být jednou z hodnot uvedených v následující tabulce. Pokud chybový stav neodpovídá jedné z těchto hodnot, klienti mohou použít výstižný popis chybové podmínky pomocí [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez mezer. Schopnost odeslat zprávu *telemetrie* vyžaduje připojení ke službě, takže ve zprávě *telemetrie* můžou být nahlášené jenom přechodné nebo dočasné chybové stavy. Chybové stavy, které *trvale* blokují klienta od navázání připojení ke službě, brání klientovi v posílání jakékoli zprávy službě, včetně zpráv *telemetrie* .

| Chyba | Použití |
| ----- | ----- |
| DNSfailure | Klient se nemohl připojit ke službě z důvodu selhání DNS v zásobníku sítě. |
| Síť v síti | Klient se pokusil o připojení, ale síťový zásobník ohlásil, že nebyla k dispozici žádná fyzická síť. |
| Nevytvářet autory | Při pokusu o získání autorizačního tokenu pro připojení došlo k chybě připojení klienta. |
| Neprostředky | Při pokusu o připojení došlo k chybě klienta z nějakého místního prostředku (například paměti). |
| Zakázáno | Klient se nemohl připojit ke službě, protože služba vrátila stavový kód HTTP `403 Forbidden` v žádosti o upgrade protokolu WebSocket. |
| Neautorizováno | Klient se nemohl připojit ke službě, protože služba vrátila stavový kód HTTP `401 Unauthorized` v žádosti o upgrade protokolu WebSocket. |
| BadRequest | Klient se nemohl připojit ke službě, protože služba vrátila stavový kód HTTP `400 Bad Request` v žádosti o upgrade protokolu WebSocket. |
| ServerUnavailable | Klient se nemohl připojit ke službě, protože služba vrátila stavový kód HTTP `503 Server Unavailable` v žádosti o upgrade protokolu WebSocket. |
| ServerError | Klient se nemohl připojit ke službě, protože služba vrátila `HTTP 500` stavový kód vnitřní chyby v žádosti o upgrade protokolu WebSocket. |
| časový limit | Časový limit požadavku na připojení klienta vypršel bez odpovědi služby. Pole *konec* obsahuje čas, kdy vypršel časový limit pro vypršení platnosti klienta, a přestal čekat na připojení. |
| ClientError | Klient ukončil připojení z důvodu některé interní chyby klienta. |

### <a name="metric-microphone"></a>Metriky`Microphone`

Tato `Microphone` metrika se vyžaduje pro zapnutí rozpoznávání řeči. Tato metrika měří čas v klientovi, během kterého se pro požadavek na řeč aktivně používá zvukový vstup.

Následující příklady použijte jako vodítko pro nahrávání hodnot *počátečního* času pro `Microphone` metriku v klientské aplikaci:

* Klientská aplikace vyžaduje, aby uživatel musel stisknout fyzické tlačítko ke spuštění mikrofonu. Po stisknutí tlačítka klientská aplikace přečte vstup z mikrofonu a odešle ho do služby Speech. *Počáteční* hodnota `Microphone` metriky zaznamenává čas po vysdílení tlačítka při inicializaci mikrofonu a připraven k zadání vstupu. *Koncová* hodnota `Microphone` metriky zaznamenává čas, kdy klientská aplikace zastavila streamování zvuku do služby `speech.endDetected` po přijetí zprávy ze služby.

* Klientská aplikace používá klíčové slovo spotter, které je "vždy" naslouchá. Jenom za klíčovým slovem spotter detekuje hlasovou frázi, kterou klientská aplikace vyfiltruje a odešle ji do služby Speech. *Počáteční* hodnota `Microphone` metriky zaznamenává čas, kdy se klíčové slovo spotter upozorní klientovi na zahájení používání vstupu z mikrofonu. *Koncová* hodnota `Microphone` metriky zaznamenává čas, kdy klientská aplikace zastavila streamování zvuku do služby `speech.endDetected` po přijetí zprávy ze služby.

* Klientská aplikace má přístup ke konstantnímu zvukovému streamu a v tomto zvukovém streamu v modulu rozpoznávání *řeči*provádí detekci tichého nebo hlasového zvuku. *Počáteční* hodnota `Microphone` metriky zaznamenává čas, kdy se modul rozpoznávání *řeči* upozorní klienta na zahájení používání vstupu ze zvukového datového proudu. *Koncová* hodnota `Microphone` metriky zaznamenává čas, kdy klientská aplikace zastavila streamování zvuku do služby `speech.endDetected` po přijetí zprávy ze služby.

* Klientská aplikace zpracovává druhou funkci vícenásobného vypínání žádosti a je informována zprávou s odpovědí na službu, aby zapnula vstup na mikrofon za účelem získání vstupu pro druhý tah. *Počáteční* hodnota `Microphone` metriky zaznamenává čas, kdy klientská aplikace povolí mikrofon a začne používat vstup z tohoto zdroje zvukového záznamu. *Koncová* hodnota `Microphone` metriky zaznamenává čas, kdy klientská aplikace zastavila streamování zvuku do služby `speech.endDetected` po přijetí zprávy ze služby.

Hodnota *koncového* času pro `Microphone` metriku zaznamenává čas, kdy klientská aplikace zastavila zvukový vstup streamování. Ve většině případů k této události dochází krátce poté, co klient obdrží `speech.endDetected` zprávu od služby. Klientské aplikace mohou ověřit, že správně vyhovují protokolu tím, že zajišťují, že hodnota *koncového* času pro `Microphone` metrika nastane později než hodnota `speech.endDetected` času příjmu pro zprávu. A vzhledem k tomu, že je obvykle prodleva mezi koncem jednoho a začátkem jiné zapínání, mohou klienti ověřit shodu protokolu tím, že zajistí, že *počáteční* čas `Microphone` metriky pro jakékoli následné správně zaznamenává čas, kdy se klient *začal* používat mikrofon ke streamování zvukového vstupu do služby.

| Pole | Popis | Použití |
| ----- | ----------- | ----- |
| Name | Odpovídající | Požadováno |
| Spustit | Čas, kdy klient začal používat zvukový vstup z mikrofonu nebo jiného zvukového datového proudu nebo obdržel Trigger z klíčového slova spotter | Požadováno |
| End | Čas, kdy se klient zastavil pomocí mikrofonu nebo zvukového streamu | Požadováno |
| Chyba | Popis chyby, ke které došlo, pokud existuje. Pokud byly operace mikrofonu úspěšné, klienti by měli toto pole vynechat. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro chybové případy, vynechává se jinak. |

### <a name="metric-listeningtrigger"></a>Metriky`ListeningTrigger`
`ListeningTrigger` Metrika měří čas, kdy uživatel provede akci, která spustí vstup řeči. `ListeningTrigger` Metrika je volitelná, ale je doporučována pro klienty, kteří můžou tuto metriku poskytnout.

Následující příklady použijte jako vodítko pro nahrávání `ListeningTrigger` hodnot *počátečního* a *koncového* času pro metriku v klientské aplikaci.

* Klientská aplikace vyžaduje, aby uživatel musel stisknout fyzické tlačítko ke spuštění mikrofonu. *Počáteční* hodnota této metriky zaznamenává čas vložení tlačítka. *Koncová* hodnota zaznamenává čas, kdy se dokončí Vložení tlačítka.

* Klientská aplikace používá klíčové slovo spotter, které je "vždy" naslouchá. Jakmile klíčové slovo spotter detekuje hlasovou frázi, přečte ji vstup z mikrofonu a odešle ji do služby Speech. *Počáteční* hodnota této metriky zaznamenává čas, kdy klíčové slovo spotter přijalo zvuk, který byl následně zjištěn jako spouštěcí fráze. *Koncová* hodnota zaznamenává čas, kdy uživatel použil poslední slovo spouštěcí fráze.

* Klientská aplikace má přístup ke konstantnímu zvukovému streamu a v tomto zvukovém streamu v modulu rozpoznávání *řeči*provádí detekci tichého nebo hlasového zvuku. *Počáteční* hodnota této metriky zaznamenává čas, po který *modul rozpoznávání řeči* přijal zvuk, který byl zjištěn jako řeč. *Koncová* hodnota zaznamenává čas, kdy *modul rozpoznávání řeči* zjistil rozpoznávání řeči.

* Klientská aplikace zpracovává druhou funkci vícenásobného vypínání žádosti a je informována zprávou s odpovědí na službu, aby zapnula vstup na mikrofon za účelem získání vstupu pro druhý tah. Klientská *aplikace by neměla* obsahovat `ListeningTrigger` metriku pro tuto funkci.

| Pole | Popis | Použití |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | volitelná, |
| Spustit | Čas spuštění triggeru naslouchání klienta | Požadováno |
| End | Čas, kdy klient naslouchá triggeru dokončen | Požadováno |
| Chyba | Popis chyby, ke které došlo, pokud existuje. Pokud byla operace triggeru úspěšná, klienti by měli toto pole vynechat. Maximální délka tohoto pole je 50 znaků. | Vyžaduje se pro chybové případy, vynechává se jinak. |

#### <a name="sample-message"></a>Ukázková zpráva

Následující ukázka ukazuje zprávu telemetrie s částmi ReceivedMessages a metrikami:

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

Tato část popisuje druhy chybových zpráv a podmínek, které vaše aplikace potřebuje zpracovat.

### <a name="http-status-codes"></a>Stavové kódy HTTP

V rámci žádosti o upgrade protokolu WebSocket může služba Speech vracet všechny standardní stavové kódy HTTP, například `400 Bad Request`atd. Vaše aplikace musí správně zpracovat tyto chybové stavy.

#### <a name="authorization-errors"></a>Chyby autorizace

Pokud během upgradu protokolu WebSocket dojde k nesprávnému ověření, služba Speech vrátí `403 Forbidden` stavový kód HTTP. Mezi podmínky, které mohou aktivovat tento kód chyby, patří:

* Chybí *autorizační* hlavička.

* Neplatný autorizační token

* Vypršela platnost autorizačního tokenu

`403 Forbidden` Chybová zpráva neindikuje problém se službou Speech. Tato chybová zpráva indikuje problém s klientskou aplikací.

### <a name="protocol-violation-errors"></a>Chyby narušení protokolu

Pokud služba rozpoznávání řeči detekuje jakékoli porušení protokolu z klienta, služba ukončí připojení protokolem WebSocket poté, co vrátí *stavový kód* a *důvod* ukončení. Klientské aplikace mohou tyto informace použít k řešení potíží a k nápravě porušení.

#### <a name="incorrect-message-format"></a>Nesprávný formát zprávy

Pokud klient pošle do služby text nebo binární zprávu, která není kódovaná ve správném formátu uvedeném v této specifikaci, služba ukončí připojení s *1007 neplatným stavovým kódem dat datové části* .

Služba vrátí tento stavový kód z nejrůznějších důvodů, jak je znázorněno v následujících příkladech:

* "Nesprávný formát zprávy. Binární zpráva má neplatnou předponu velikosti hlavičky. Klient odeslal binární zprávu s neplatnou předponou velikosti hlavičky.

* "Nesprávný formát zprávy. Binární zpráva má neplatnou velikost hlavičky. Klient odeslal binární zprávu, která zadala neplatnou velikost hlavičky.

* "Nesprávný formát zprávy. Při dekódování binárních hlaviček zpráv do UTF-8 došlo k chybě. Klient odeslal binární zprávu, která obsahuje hlavičky, které nejsou správně kódované v kódování UTF-8.

* "Nesprávný formát zprávy. Textová zpráva neobsahuje žádná data. " Klient odeslal textovou zprávu, která neobsahuje žádná data těla.

* "Nesprávný formát zprávy. Textová zpráva dekódování do kódování UTF-8 se nezdařila. " Klient odeslal textovou zprávu, která nebyla správně kódována v kódování UTF-8.

* "Nesprávný formát zprávy. Textová zpráva neobsahuje žádné oddělovače záhlaví. Klient odeslal textovou zprávu, která neobsahovala oddělovač záhlaví, nebo použila nesprávný oddělovač záhlaví.

#### <a name="missing-or-empty-headers"></a>Chybějící nebo prázdné hlavičky

Pokud klient pošle zprávu, která nemá požadovaná záhlaví *X-RequestId* nebo *cesta*, služba ukončí připojení pomocí kódu stavu *chyby protokolu 1002* . Zpráva je "chybějící/prázdné záhlaví". {Header Name}. "

#### <a name="requestid-values"></a>Hodnoty RequestId

Pokud klient pošle zprávu, která určuje hlavičku *X-RequestId* s nesprávným formátem, služba ukončí připojení a vrátí chybový stav *protokolu 1002* . Zpráva je "neplatný požadavek. Hodnota hlavičky X-RequestId nebyla zadána ve formátu UUID bez čárek.

#### <a name="audio-encoding-errors"></a>Chyby kódování zvuku

Pokud klient pošle zvukovou stopu, která zahájí zapínání a formát zvuku nebo kódování nevyhovuje požadované specifikaci, služba ukončí připojení a vrátí *1007 neplatný* stavový kód datové dávky. Zpráva indikuje zdroj chyby kódování formátu.

#### <a name="requestid-reuse"></a>Použít RequestId

Pokud klient odešle zprávu, která znovu používá identifikátor požadavku, po dokončení zapínání služby ukončí připojení a vrátí stavový kód *chyby protokolu 1002* . Zpráva je "neplatný požadavek. Opakované použití identifikátorů požadavků není povoleno. "

## <a name="connection-failure-telemetry"></a>Telemetrie selhání připojení

Aby bylo zajištěno nejlepší možné uživatelské prostředí, musí klienti informovat službu Speech pro časová razítka pro důležité kontrolní body v rámci připojení pomocí zprávy *telemetrie* . Je stejně důležité, aby klienti informovali o službě, u které došlo k pokusu o připojení, ale nedošlo k chybě.

U každého pokusu o připojení, který selhal, vytvoří klienti zprávu *telemetrie* s jedinečnou hodnotou hlavičky *X-RequestId* . Vzhledem k tomu, že klient nemohl navázat připojení, pole *ReceivedMessages* v těle JSON může být vynecháno. Je zahrnutá jenom položkavpolimetriky`Connection` . Tato položka zahrnuje časová razítka začátku a konce a také chybový stav, ke kterému došlo.

### <a name="connection-retries-in-telemetry"></a>Opakované pokusy o připojení v telemetrii

Klienti by měli rozlišovat *opakování* z *několika pokusů o připojení* událostmi, které aktivují pokus o připojení. Pokusy o připojení, které se provedou programově bez zásahu uživatele, se zopakují. Více pokusů o připojení, které jsou prováděny v reakci na vstup uživatele, je více pokusů o připojení. Klienti přidávají každé uživatelem aktivované připojení pokus o jedinečnou zprávu *X-RequestId* a *telemetrie* . Klienti znovu znovu *narequestid* pro programové pokusy. Pokud bylo provedeno více opakovaných pokusů pro jeden pokus o připojení, bude každý pokus o opakování zahrnut `Connection` jako položka ve zprávě *telemetrie* .

Předpokládejme například, že uživatel představí aktivační událost s klíčovým slovem ke spuštění připojení a první pokus o připojení se nezdaří z důvodu chyb služby DNS. Druhý pokus provedený programově klientem je však úspěšný. Vzhledem k tomu, že klient znovu vyzkoušel připojení bez nutnosti dalšího vstupu od uživatele, klient použije jednu zprávu *telemetrie* s několika `Connection` záznamy pro popis připojení.

Další příklad předpokládá, že uživatel mluví s triggerem klíčová slova ke spuštění připojení a pokus o připojení se nezdaří po třech opakovaných pokusech. Klient pak zaznamená, ukončí se pokus o připojení ke službě a informuje uživatele o tom, že se něco pokazilo. Uživatel pak znovu vysloví aktivační proceduru klíčového slova. Tentokrát Předpokládejme, že se klient připojí ke službě. Po připojení klient okamžitě pošle zprávu *telemetrie* službě, která obsahuje tři `Connection` položky popisující selhání připojení. Po přijetí `turn.end` zprávy pošle klient další zprávu *telemetrie* , která popisuje úspěšné připojení.

## <a name="error-message-reference"></a>Odkaz na chybovou zprávu

### <a name="http-status-codes"></a>Stavové kódy HTTP

| Stavový kód HTTP | Popis | Řešení potíží |
| - | - | - |
| 400 Chybný požadavek | Klient odeslal požadavek na připojení soketu WebSocket, který nebyl správný. | Ověřte, zda jste zadali všechny požadované parametry a hlavičky HTTP a zda jsou hodnoty správné. |
| 401 Neautorizováno | Klient neobsahoval požadované autorizační údaje. | Ověřte, že posíláte *autorizační* hlavičku v připojení protokolu WebSocket. |
| 403 zakázané | Klient odeslal informace o autorizaci, ale neplatný. | Ověřte, že v *autorizační* hlavičce neposíláte neplatnou nebo neplatnou hodnotu. |
| 404 Nenalezeno | Klient se pokusil o přístup k cestě URL, která není podporována. | Ověřte, že používáte správnou adresu URL pro připojení protokolu WebSocket. |
| Chyba serveru 500 | Služba zjistila vnitřní chybu a nemohla požadavek splnit. | Ve většině případů je tato chyba přechodný. Opakujte požadavek. |
| 503 – Nedostupná služba | Služba nebyla pro zpracování požadavku k dispozici. | Ve většině případů je tato chyba přechodný. Opakujte požadavek. |

### <a name="websocket-error-codes"></a>Kódy chyb protokolu WebSocket

| WebSocketsStatus kód | Popis | Řešení potíží |
| - | - | - |
| Normální ukončení 1000 | Služba ukončila připojení protokolu WebSocket bez chyby. | Pokud se ukončení protokolu WebSocket neočekávalo, přečtěte si dokumentaci a ujistěte se, že rozumíte tomu, jak a kdy může služba ukončit připojení protokolu WebSocket. |
| Chyba protokolu 1002 | Klientovi se nepovedlo dodržovat požadavky protokolu. | Ujistěte se, že rozumíte dokumentaci k protokolu a že jsou tyto požadavky jasné. Přečtěte si předchozí dokumentaci k chybám, abyste viděli, jestli jste porušili požadavky na protokoly. |
| 1007 neplatná data datové části | Klient odeslal do zprávy protokolu neplatnou datovou část. | Ověřte poslední zprávu, kterou jste službě odeslali, kvůli chybám. Přečtěte si předchozí dokumentaci o chybách datových částí. |
| Chyba serveru 1011 | Služba zjistila vnitřní chybu a nemohla požadavek splnit. | Ve většině případů je tato chyba přechodný. Opakujte požadavek. |

## <a name="related-topics"></a>Související témata

Viz [sada JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) , která je implementací protokolu hlasových služeb založených na protokolu WebSocket.
