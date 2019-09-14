---
title: Odkaz na Translator Speech API
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro Translator Speech API.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965379"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Tato služba nabízí rozhraní API pro streamování, které přepisovat konverzaci hlasu z jednoho jazyka do textu jiného jazyka. Rozhraní API se také integruje možnosti převod textu na řeč k přečtení přeloženého textu. Translator Speech API umožňuje scénáře jako překlady konverzací v reálném čase, jak je vidět ve službě Skype Translator.

V Translator Speech API klientské aplikace streamují zvuk řeči do služby a přijímají zpět proud textových výsledků, které obsahují rozpoznaný text ve zdrojovém jazyce, a jeho překlad v cílovém jazyce. Výsledný text se vytváří tak, že se pro příchozí zvukový stream použije automatické rozpoznávání řeči (ASR) na bázi neuronových sítí. Nezpracovaný výstup ASR je dále vylepšený novou technikou nazvanou TrueText, aby lépe odrážela záměr uživatele. Například TrueText odebere disfluencies (HMMs and coughs) a obnoví správnou interpunkci a kapitalizace. K dispozici je také možnost maskování nebo vyloučení neslušných slov. Moduly pro rozpoznávání a překlad jsou speciálně natrénované tak, aby zvládly zpracování hovorové řeči. Služba překladu řeči používá zjišťování tichosti k určení konce utterance. Po pauze v hlasové aktivitě začne služba streamovat zpět konečný výsledek pro skončenou promluvu. Služba může odesílat zpět také částečné výsledky, které poskytují rozpoznávání a překlady částí probíhající promluvy. Pro konečné výsledky služba poskytuje možnost syntetizovat řeč (převod textu na řeč) z mluveného textu v cílových jazycích. Zvuk se při převodu textu na řeč vytváří ve formátu určeném klientem. K dispozici jsou formáty WAV a MP3.

Translator Speech API využívá protokol WebSocket k zajištění plně duplexního komunikačního kanálu mezi klientem a serverem. Aplikace bude vyžadovat, aby služba používala tyto kroky:

## <a name="1-getting-started"></a>1. Začínáme
Pokud chcete získat přístup k Translator Text API, budete se muset [zaregistrovat Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Ověřování

K ověření použijte klíč předplatného. Translator Speech API podporuje dva režimy ověřování:

* **Pomocí přístupového tokenu:** Ve své aplikaci Získejte přístupový token ze služby tokenu. K získání přístupového tokenu ze služby Azure Cognitive Services Authentication použijte svůj klíč předplatného Translator Speech API. Přístupový token je platný po dobu 10 minut. Získejte nový přístupový token každých 10 minut a používejte stejný přístupový token pro opakované žádosti v těchto 10 minutách.

* **Přímý použití klíče předplatného:** V aplikaci předejte klíč předplatného jako hodnotu v `Ocp-Apim-Subscription-Key` hlavičce.

Považovat klíč předplatného a přístupový token jako tajné klíče, které by měly být skryté ze zobrazení.

## <a name="3-query-languages"></a>3. Jazyky dotazů
**Dotaz na prostředek jazyky pro aktuální sadu podporovaných jazyků.** [Prostředek jazyky](languages-reference.md) zveřejňuje sadu jazyků a hlasů, které jsou k dispozici pro rozpoznávání řeči, pro překlad textu a pro převod textu na řeč. Každému jazyku nebo hlasu je uveden identifikátor, který Translator Speech API používá k identifikaci stejného jazyka nebo hlasu.

## <a name="4-stream-audio"></a>4. Streamovat zvuk
**Otevřete připojení a začněte streamovat zvuk do služby.** Adresa URL služby je `wss://dev.microsofttranslator.com/speech/translate`. Parametry a zvukové formáty očekávané službou jsou popsány níže v rámci `/speech/translate` operace. Jeden z parametrů se používá k předání přístupového tokenu z kroku 2 výše.

## <a name="5-process-the-results"></a>5. Zpracování výsledků
**Zpracujte výsledky streamování zpátky ze služby.** Formát částečných výsledků, konečné výsledky a zvukové segmenty pro převod textu na řeč jsou popsány v dokumentaci `/speech/translate` k následující operaci.

Ukázky kódu, které demonstrují použití Translator Speech API, jsou k dispozici na [webu Microsoft Translator GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

ZÍSKÁNÍ/Speech/Translate vytváří relaci pro překlad řeči

### <a name="connecting"></a>Připojování
Než se připojíte ke službě, Projděte si seznam parametrů uvedených dále v této části. Příkladem požadavku je:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Požadavek určuje, že mluvený hlas bude ve službě streamované a přeložený do italštiny. Každý konečný výsledek rozpoznávání vygeneruje zvukovou reakci textu na řeč s použitím ženského hlasu s názvem Elsa. Všimněte si, že požadavek obsahuje přihlašovací údaje `Ocp-Apim-Subscription-Key header`v. Požadavek také dodržuje osvědčené postupy nastavením globálně jedinečného identifikátoru v hlavičce `X-ClientTraceId`. Klientská aplikace by měla protokolovat ID trasování, aby ji bylo možné použít k řešení problémů, když k nim dojde.

### <a name="sending-audio"></a>Odesílání zvuku
Po navázání připojení zahájí klient streamování zvuku do služby. Klient odesílá zvuk do bloků dat. Každý blok se přenáší pomocí zprávy protokolu WebSocket typu Binary.

Zvukový vstup je ve formátu zvukového souboru Wave (WAVE nebo častěji označovaný jako WAV), protože jeho přípona názvu souboru je. Klientská aplikace by měla streamovat Single Channel, podepsaná 16bitový PCM audio Sample ve 16 kHz. První sada bajtů streamovaná klientem bude obsahovat hlavičku WAV. Hlavička 44-Byte pro jeden kanál se znaménkem 16bitového streamu PCM ve 16 kHz je:

|Offset|Value|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|WAVE|
|12 - 15|Formát|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 – 43|0|

Všimněte si, že celková velikost souboru (bajty 4-7) a velikost dat (bajty 40-43) jsou nastaveny na hodnotu nula. To je v pořádku pro scénář streamování, kde celková velikost není nutně známá dopředu.

Po odeslání hlavičky WAV (RIFF) odešle klient bloky dat zvuku. Klient obvykle bude streamovat pevné velikosti datových bloků, které představují pevnou dobu trvání (např. Stream 100 MS audio v čase).

### <a name="signal-the-end-of-the-utterance"></a>Signalizuje konec utterance
Translator Speech API vrátí přepis a překlad zvukového datového proudu při posílání zvuku. Konečný přepis, konečný překlad a přeložený zvuk se vám vrátí až po skončení utterance. V některých případech možná budete chtít vynutit konec utterance. Počkejte prosím, než 2,5 sekund ticha pro vynucení konce utterance.

### <a name="final-result"></a>Konečný výsledek
Konečný výsledek rozpoznávání řeči se vygeneruje na konci utterance. Z důvodu přenáší služba klientovi do klienta pomocí zprávy protokolu WebSocket typu text. Obsah zprávy je serializace JSON objektu s následujícími vlastnostmi:

* `type`: Řetězcová konstanta určující typ výsledku. Hodnota je finální pro konečné výsledky.
* `id`: Identifikátor řetězce přiřazený výsledku rozpoznávání.
* `recognition`: Rozpoznaný text ve zdrojovém jazyce. Text může být prázdný řetězec v případě nepravdivého rozpoznávání.
* `translation`: Rozpoznaný text přeložený v cílovém jazyce
* `audioTimeOffset`: Časový posun začátku rozpoznávání v impulzech (1 Tick = 100 nanosekund). Posun je relativní vzhledem ke začátku streamování.
* `audioTimeSize`: Doba trvání rozpoznávání v taktech (100 nanosekund).
* `audioStreamPosition`: Posun bajtů začátku rozpoznávání. Posun je relativní vzhledem k začátku datového proudu.
* `audioSizeBytes`: Velikost v bajtech pro rozpoznávání

Všimněte si, že umístění rozpoznávání ve zvukovém streamu není ve výchozím nastavení součástí výsledků. Tato `TimingInfo` funkce musí být vybrána klientem (viz `features` parametr).

Konečný výsledek ukázky je následující:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Částečný výsledek
Částečné nebo mezilehlé výsledky rozpoznávání řeči nejsou ve výchozím nastavení přenášeny do klienta. Klient může k vyžádání požádat použít parametr dotazu funkce.

Částečný výsledek se z této služby přenáší klientovi pomocí zprávy protokolu WebSocket typu text. Obsah zprávy je serializace JSON objektu s následujícími vlastnostmi:

* `type`: Řetězcová konstanta určující typ výsledku. Hodnota je částečně pro částečné výsledky.
* `id`: Identifikátor řetězce přiřazený výsledku rozpoznávání.
* `recognition`: Rozpoznaný text ve zdrojovém jazyce.
* `translation`: Rozpoznaný text přeložený v cílovém jazyce
* `audioTimeOffset`: Časový posun začátku rozpoznávání v impulzech (1 Tick = 100 nanosekund). Posun je relativní vzhledem ke začátku streamování.
* `audioTimeSize`: Doba trvání rozpoznávání v taktech (100 nanosekund).
* `audioStreamPosition`: Posun bajtů začátku rozpoznávání. Posun je relativní vzhledem k začátku datového proudu.
* `audioSizeBytes`: Velikost v bajtech pro rozpoznávání

Všimněte si, že umístění rozpoznávání ve zvukovém streamu není ve výchozím nastavení součástí výsledků. Funkce TimingInfo musí být vybrána klientem (viz parametr funkcí).

Konečný výsledek ukázky je následující:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Převod textu na řeč
Když je funkce převodu textu na řeč povolená (viz `features` parametr níže), za konečným výsledkem se domluví zvuk mluveného přeloženého textu. Zvuková data jsou ve formě bloků dat a posílána ze služby do klienta jako sekvence zpráv protokolu WebSocket typu Binary. Klient může detekovat konec datového proudu kontrolou modulu pro každou zprávu. Poslední binární zpráva bude mít svůj bit v typu FIN nastaven na hodnotu 1, aby označovala konec datového proudu. Formát datového proudu závisí na hodnotě `format` parametru.

### <a name="closing-the-connection"></a>Uzavírání připojení
Když klientská aplikace dokončí streamování zvuku a obdržela poslední konečný výsledek, měl by připojení ukončit tím, že inicializuje WebSocket, který ukončuje metodu handshake. Existují podmínky, které způsobí, že server ukončí připojení. Klient může přijmout tyto uzavřené kódy protokolu WebSocket:

* `1003 - Invalid Message Type`: Server ukončuje připojení, protože nemůže přijmout datový typ, který přijal. K tomu obvykle dochází, když příchozí zvuk nezačíná správnou hlavičkou.
* `1000 - Normal closure`: Po splnění žádosti bylo připojení ukončeno. Server ukončí připojení: Pokud klient po delší dobu neobdrží žádný zvuk z klienta. Když je netiché streamování po delší dobu; Když relace dosáhne maximální povolené doby trvání (přibližně 90 minut).
* `1001 - Endpoint Unavailable`: Indikuje, že server přestane být k dispozici. Klientská aplikace se může pokusit znovu připojit s omezením počtu opakovaných pokusů.
* `1011 - Internal Server Error`: Připojení bude serverem zavřeno, protože na serveru došlo k chybě.

### <a name="parameters"></a>Parametry

|Parametr|Value|Popis|Typ parametru|Typ dat|
|:---|:---|:---|:---|:---|
|api-version|1.0|Verze rozhraní API, kterou klient požaduje. Povolené hodnoty jsou: `1.0`.|query   |řetězec|
|from|obsahovat   |Určuje jazyk příchozího rozpoznávání řeči. Hodnota je jeden z identifikátorů jazyka z `speech` oboru v odpovědi z rozhraní API jazyků.|query|řetězec|
|na|obsahovat|Určuje jazyk, do kterého se má překládat přepisu text. Hodnota je jeden z identifikátorů jazyka z `text` oboru v odpovědi z rozhraní API jazyků.|query|řetězec|
|Database|obsahovat   |Sada funkcí, které jsou vybrány klientem, oddělenou čárkou. K dispozici jsou tyto funkce:<ul><li>`TextToSpeech`: Určuje, zda musí služba vracet přeložený zvuk finální přeložené věty.</li><li>`Partial`: Určuje, že služba musí vracet mezilehlé výsledky rozpoznávání, zatímco zvuk streamuje služby.</li><li>`TimingInfo`: Určuje, zda musí služba vracet informace o časování přidružené k jednotlivým rozlišením.</li></ul>Klient například určí `features=partial,texttospeech` , že má přijímat částečné výsledky a převod textu na řeč, ale neobsahuje informace o časování. Všimněte si, že konečné výsledky jsou vždycky streamované klientovi.|query|řetězec|
|Telefonní|obsahovat|Určuje, který hlas se má použít pro vykreslování textu na řeč pro přeložený text. Hodnota je jeden z identifikátorů hlasu z oboru TTS v odpovědi z rozhraní API jazyků. Pokud není zadán hlasový vstup, systém jej automaticky zvolí, pokud je funkce převodu textu na řeč povolena.|query|řetězec|
|format|obsahovat|Určuje formát datového proudu zvukového převodu textu na řeč vrácený službou. Dostupné možnosti jsou:<ul><li>`audio/wav`: Zvukový stream Wave. Klient by měl použít hlavičku WAV ke správné interpretaci formátu zvuku. Zvuk WAV pro převod textu na řeč je 16 bitů, s jedním kanálem se vzorkovací frekvencí 24kHz nebo 16kHz.</li><li>`audio/mp3`: Zvukový stream MP3.</li></ul>Výchozí hodnota je `audio/wav`.|query|řetězec|
|ProfanityAction    |obsahovat    |Určuje, jak má služba zpracovávat vulgární výrazy rozpoznané ve Speech. Platné akce:<ul><li>`NoAction`: Vulgární výrazy jsou ponechány tak, jak jsou.</li><li>`Marked`: Vulgární výrazy jsou nahrazeny značkou. Viz `ProfanityMarker` parametr.</li><li>`Deleted`: Vulgární výrazy jsou odstraněny. Pokud se například slovo `"jackass"` považuje za vulgární výrazy, bude se tato fráze `"He is a jackass."``"He is a .".`</li></ul>Výchozí hodnota je označena.|query|řetězec|
|ProfanityMarker|obsahovat    |Určuje způsob zpracování vulgárních výrazů, pokud `ProfanityAction` je nastavena na `Marked`. Platné možnosti jsou:<ul><li>`Asterisk`: Vulgární výrazy jsou nahrazeny řetězcem `***`. Pokud se například slovo `"jackass"` považuje za vulgární výrazy, bude se tato fráze `"He is a jackass."``"He is a ***.".`</li><li>`Tag`: Vulgární výrazy jsou obklopeny značkou XML vulgárních výrazů. Pokud se například slovo `"jackass"` považuje za vulgární výrazy, stane `"He is a <profanity>jackass</profanity>."`se tato fráze `"He is a jackass."` .</li></ul>Výchozí hodnota je `Asterisk`.|query|řetězec|
|Authorization|obsahovat  |Určuje hodnotu tokenu nosiče klienta. Použijte předponu `Bearer` následovaný hodnotou `access_token` hodnoty vrácenou službou tokenu ověřování.|záhlaví   |řetězec|
|OCP-Apim-Subscription-Key|obsahovat|Vyžaduje se, `Authorization` Pokud není Zadaná hlavička.|záhlaví|řetězec|
|access_token|obsahovat   |Alternativní způsob předání platného přístupového tokenu OAuth Token nosiče se obvykle poskytuje s hlavičkou `Authorization`. Některé knihovny WebSocket neumožňují, aby kód klienta nastavil hlavičky. V takovém případě může klient použít `access_token` parametr dotazu k předání platného tokenu. Pokud použijete přístupový token k ověření, pokud `Authorization` není nastavená hlavička `access_token` , musí být nastavená. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje. Klienti by měli k předání tokenu použít jenom jednu metodu.|query|řetězec|
|předplatné – klíč|obsahovat   |Alternativní způsob, jak předat klíč předplatného Některé knihovny WebSocket neumožňují, aby kód klienta nastavil hlavičky. V takovém případě může klient použít `subscription-key` parametr dotazu k předání platného klíče předplatného. Pokud použijete klíč předplatného k ověření `Ocp-Apim-Subscription-Key` , pokud není nastavená hlavička, musí se nastavit klíč předplatného. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje. Klienti by měli použít jenom jednu metodu k předání `subscription key`.|query|řetězec|
|X-ClientTraceId    |obsahovat    |Identifikátor GUID generovaný klientem, který se používá pro trasování požadavku. Pro řádné řešení potíží by klienti měli pro každou žádost zadat novou hodnotu a zaznamenat ji.<br/>Namísto použití záhlaví lze tuto hodnotu předat parametru `X-ClientTraceId`dotazu. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje.|záhlaví|řetězec|
|X-CorrelationId|obsahovat    |Identifikátor generovaný klientem, který slouží ke korelaci více kanálů v konverzaci. Pro umožnění konverzace mezi uživateli je možné vytvořit více relací překladu řeči. V takovém scénáři používají všechny relace překladu řeči stejné ID korelace, aby se kanály vzájemně propojují. To usnadňuje trasování a diagnostiku. Identifikátor by měl splňovat tyto požadavky:`^[a-zA-Z0-9-_.]{1,64}$`<br/>Namísto použití záhlaví lze tuto hodnotu předat parametru `X-CorrelationId`dotazu. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje.|záhlaví|řetězec|
|X-ClientVersion|obsahovat    |Určuje verzi klientské aplikace. Příklad: "2.1.0.123".<br/>Namísto použití záhlaví lze tuto hodnotu předat parametru `X-ClientVersion`dotazu. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje.|záhlaví|řetězec|
|X-OsPlatform|obsahovat   |Určuje název a verzi operačního systému, ve kterém je spuštěná klientská aplikace. Příklady: "Android 5,0", "iOs 8.1.3", "Windows 8.1".<br/>Namísto použití záhlaví lze tuto hodnotu předat parametru `X-OsPlatform`dotazu. Pokud je nastavená hlavička i parametr dotazu, parametr dotazu se ignoruje.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Stavový kód HTTP|Reason|Model odpovědi|Záhlaví|
|:--|:--|:--|:--|
|101    |Upgrade protokolu WebSocket.|Ukázková hodnota modelu <br/> Předmětů{}|X-RequestId<br/>Hodnota identifikující požadavek pro účely řešení potíží.<br/>řetězec|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry a ujistěte se, že jsou platné. Objekt Response obsahuje podrobnější popis chyby.|||
|401    |Přístupu. Ujistěte se, že jsou nastavené přihlašovací údaje, že jsou platné a že vaše předplatné Azure Data Marketo je v dobrém postavení s dostupným zůstatkem.|||
|500    |Došlo k chybě. Pokud chyba přetrvává, ohlaste ji pomocí identifikátoru trasování klienta (X-ClientTraceId) nebo identifikátoru žádosti (X-RequestId).|||
|503    |Server je dočasně nedostupný. Opakujte prosím požadavek. Pokud chyba přetrvává, ohlaste ji pomocí identifikátoru trasování klienta (X-ClientTraceId) nebo identifikátoru žádosti (X-RequestId).|||
