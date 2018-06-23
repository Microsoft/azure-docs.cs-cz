---
title: Referenční dokumentace rozhraní API pro rozpoznávání řeči překladač Microsoft | Microsoft Docs
titleSuffix: Cognitive Services
description: Referenční dokumentace rozhraní API pro rozpoznávání řeči Microsoft k překladač.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343784"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator Speech API

Tato služba nabízí streamování rozhraní API pro transcribe konverzačního řeči z jednoho jazyka do textu jiný jazyk. Rozhraní API se také integruje je funkce, které řeči přeložený text. Rozhraní API služby Microsoft překladač řeči umožňuje scénáře, jako v reálném čase překlad konverzace, jak je vidět v Skype překladač.

Klientské aplikace s rozhraním API pro aplikaci Microsoft překladač řeči stream řeči zvuk ke službě a zobrazí zpět proud textové výsledky, které zahrnují rozpoznaný text v source jazyk a jeho překlad do cílového jazyka. Textové výsledky vytváří použitím automatické rozpoznávání řeči rozpoznávání (ASR) používá technologii hluboké neuronové sítě pro příchozí datový proud zvuku. Nezpracovaná automatické obnovení systému výstup další lepší pomocí nové počítačového názvem PravdivýText, aby bylo zřejmé, přesněji záměru uživatele. Například PravdivýText odebere disfluencies (hmms a coughs) a správné interpunkce obnovení a velkých písmen. Možnost maskování nebo vyloučit profanities je rovněž obsažena. Moduly rozpoznávání a překlad probíhá Trénink speciálně pro zpracování konverzačního řeči. Služba překladu řeči používá interval detekce určit konec utterance. Po pozastavení hlasové aktivity bude služba zpět stream konečný výsledek pro dokončené utterance. Službu můžete také odeslat zpět částečné výsledky, která poskytnou zprostředkující uznání a překladů pro utterance v průběhu. Pro konečné výsledky služba poskytuje možnost syntetizovat rozpoznávání řeči (převod textu na řeč) z mluvené textu v jazycích cíl. Převod textu na řeč zvuk se vytvoří ve formátu určeném klientem. Formáty WAV a MP3 nejsou k dispozici.

Rozhraní API služby Microsoft překladač řeči využívá protokol WebSocket zajistit plně duplexní komunikační kanál mezi klientem a serverem. Aplikace, bude vyžadovat tyto kroky při použití služby:

## <a name="1-getting-started"></a>1. Začínáme
Pro přístup k rozhraní API Text Microsoft překladač budete muset [registrace pro Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Klíč předplatného se používají k ověření. Rozhraní API služby Microsoft překladač řeči podporuje dva režimy ověřování:

* **Pomocí přístupového tokenu:** ve vaší aplikaci, získat přístupový token od služby tokenů. Použijte svůj klíč předplatného Microsoft překladač řeči API můžete získat přístupový token ze služby ověřování kognitivní služby. Přístupový token je platný 10 minut. Získejte nový přístupový token každých 10 minut a udržovat stejný přístup pomocí tokenu pro opakované žádosti v rámci těchto 10 minut.

* **Přímo pomocí klíč předplatného:** ve vaší aplikaci, předat jako hodnota v svůj klíč předplatného `Ocp-Apim-Subscription-Key` záhlaví.

Považovat za svůj klíč předplatného a přístupový token tajné klíče, které by měla být v zobrazení skrytá.

## <a name="3-query-languages"></a>3. Dotaz jazyky
**Dotaz na prostředek jazyky pro aktuální sadu podporovaných jazyků.** [Jazyky prostředků](languages-reference.md) zveřejňuje sadu jazyky a k dispozici pro rozpoznávání řeči, pro překlad textu a pro převod textu na řeč hlasy. Každý jazyk nebo hlasové dostane identifikátor, který rozhraní API služby Microsoft překladač řeči používá k identifikaci jazyce nebo hlasu.

## <a name="4-stream-audio"></a>4. Datový proud zvuk
**Otevření připojení a začít streamování zvuk ke službě.** Adresa URL služby je `wss://dev.microsofttranslator.com/speech/translate`. Parametry a zvukových formáty očekávanou službu jsou popsány níže v `/speech/translate` operaci. Jeden z parametrů slouží k předávání tokenu přístupu z kroku 2 výše.

## <a name="5-process-the-results"></a>5. Zpracování výsledků
**Zpracování výsledků streamování zpět ze služby.** Formát částečné výsledky, konečných výsledků a převod textu na řeč zvuk segmenty jsou popsané v dokumentaci k systému `/speech/translate` operace níže.

Ukázka použití rozhraní API služby Microsoft překladač řeči ukázky kódu jsou k dispozici na [lokality Microsoft překladač Github](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

ZÍSKAT /speech/translate Establishes relaci pro překlad řeči

### <a name="connecting"></a>Připojování
Před připojením ke službě, projděte si seznam parametrů uvedených dále v této části. Žádost o příklad je:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Požadavek určuje, bude oznamována angličtina streamování ke službě a přeložit na italština. Každý rozpoznávání konečný výsledek bude generovat je zvuk odpověď s ženského hlasu s názvem grafické karty Elsa. Všimněte si, že žádost obsahuje přihlašovací údaje v `Ocp-Apim-Subscription-Key header`. Osvědčeným postupem požadavek také odpovídá nastavením globálně jedinečný identifikátor v záhlaví `X-ClientTraceId`. Klientská aplikace by měla protokolu ID trasování, aby se může použít k řešení potíží, když k nim dojde.

### <a name="sending-audio"></a>Odesílání zvuk
Po vytvoření připojení klienta začne streamování zvuk ke službě. Klient odešle zvuk v bloků. Každého bloku se přenáší prostřednictvím protokolu Websocket zpráva typu Binary.

Zvuk vstup je ve formátu souboru zvuk zvukového průběhu (WAVE nebo více obvykle označuje jako WAV z důvodu jeho přípona názvu souboru). Klientská aplikace by měla stream jeden kanál, zvuk PCM podepsaný 16bitové odebírána data v 16 kHz. První sadu bajtů streamování klient bude obsahovat hlavičku WAV. Záhlaví 44 bajtů pro jeden kanál podepsané 16 datového proudu PCM bit odebírána data v 16 kHz je:

|Posun|Hodnota|
|:---|:---|
|0 – 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 – 21|1|
|22 – 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 – 35|16|
|36 - 39|"data.|
|40 - 43|0|

Všimněte si, že celkové velikosti (v bajtech 4-7) a velikost "data" (v bajtech 40 43) nastaveny na nulu. To je v pořádku pro streamování scénář, kde je celková velikost nezná nutně předem.

Po odeslání hlaviček WAV (RIFF), klient odešle bloky dat zvuk. Klient bude obvykle stream pevné velikosti bloků představující Pevná doba trvání (například datový proud 100ms zvuk v čase).

### <a name="final-result"></a>Konečný výsledek
V důsledku toho rozpoznávání řeči konečné se generuje na konci utterance. V důsledku toho se přenáší z službu do klienta pomocí protokolu WebSocket zpráva typu Text. Obsah zprávy je serializace JSON objektu s následujícími vlastnostmi:

* `type`: Řetězcová konstanta k identifikaci typ výsledku. Hodnota je poslední pro konečné výsledky.
* `id`: Řetězec identifikátor přiřazený k rozpoznávání výsledek.
* `recognition`: Rozpoznaný text v jazyce zdroje. Text může být prázdný řetězec. v případě false rozpoznávání.
* `translation`: Rozpoznaný text přeložit v jazyce cíl.
* `audioTimeOffset`: Posun čas zahájení rozpoznávání v rysky (1 značek = 100 nanosekundách). Posun je relativní vzhledem k začátku streamování.
* `audioTimeSize`: Doba trvání v rysky (100 nanosekundách) rozpoznávání.
* `audioStreamPosition`: Posun bajtů spouštění rozpoznávání. Posun je relativní vzhledem k začátkem datového proudu.
* `audioSizeBytes`: Velikost v bajtech rozpoznávání.

Všimněte si, že umístění rozpoznávání v zvukový datový proud není zahrnutý ve výsledcích ve výchozím nastavení. `TimingInfo` Funkce musí být vybrán klientem (viz `features` parametr).

Ukázka konečný výsledek je následující:

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
Výsledky rozpoznávání řeči částečné nebo zprostředkující nejsou Streamovat do klienta ve výchozím nastavení. Klienta můžete parametr dotazu funkce je vyžádat.

Částečný výsledek se přenáší z službu do klienta pomocí protokolu WebSocket zpráva typu Text. Obsah zprávy je serializace JSON objektu s následujícími vlastnostmi:

* `type`: Řetězcová konstanta k identifikaci typ výsledku. Hodnota je částečně pro částečné výsledky.
* `id`: Řetězec identifikátor přiřazený k rozpoznávání výsledek.
* `recognition`: Rozpoznaný text v jazyce zdroje.
* `translation`: Rozpoznaný text přeložit v jazyce cíl.
* `audioTimeOffset`: Posun čas zahájení rozpoznávání v rysky (1 značek = 100 nanosekundách). Posun je relativní vzhledem k začátku streamování.
* `audioTimeSize`: Doba trvání v rysky (100 nanosekundách) rozpoznávání.
* `audioStreamPosition`: Posun bajtů spouštění rozpoznávání. Posun je relativní vzhledem k začátkem datového proudu.
* `audioSizeBytes`: Velikost v bajtech rozpoznávání.

Všimněte si, že umístění rozpoznávání v zvukový datový proud není zahrnutý ve výsledcích ve výchozím nastavení. Funkci TimingInfo musí být vybrán klientem (viz parametr funkce).

Ukázka konečný výsledek je následující:

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
Když je funkce zapnutá (najdete v části `features` parametr níže), konečný výsledek následuje zvuk mluvené přeložený textu. Zvuk data se blokové a odeslaných ze služby klientovi jako sekvenci zpráv protokolu Websocket typ binární. Klient může rozpoznat konec datového proudu kontrolou bit najít každé zprávy. Poslední zprávy v binární bude mít jeho najít bit nastaven na jednu udávajících konec datového proudu. Formát datového proudu závisí na hodnotu `format` parametr.

### <a name="closing-the-connection"></a>Probíhá ukončování připojení
Když klientské aplikace dokončil streamování zvuk a přijal poslední konečný výsledek, ho zavřete připojení pomocí inicializace handshake zavření protokolu WebSocket. Existují podmínky, které způsobí, že server ukončit připojení. Následující kódy protokolu WebSocket uzavřený může přijatých klientem:

* `1003 - Invalid Message Type`: Server je ukončován připojení, protože datovým typem, který obdržel nemůže přijmout. Obvykle se to stane, když příchozí zvuk nezačíná správnou hlavičku.
* `1000 - Normal closure`: Připojení ukončeno po splnil požadavek. Server připojení zavře: když žádné zvuk je přijatou od klienta po delší dobu čas; Když je ticho streamování po delší dobu čas; Když je relace dosáhne maximální délka trvání povolené (přibližně 90 minut).
* `1001 - Endpoint Unavailable`: Určuje, že server nebude k dispozici. Klientská aplikace může pokusit o nové připojení s omezený počet opakování.
* `1011 - Internal Server Error`: Připojení se serverem ukončeno z důvodu chyby na serveru.

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:---|:---|:---|:---|:---|
|verze rozhraní API.|1.0|Verze rozhraní API požadovaná klientem. Povolené hodnoty jsou: `1.0`.|query   |řetězec|
|od|(prázdné)   |Určuje jazyk příchozí řeči. Hodnota je jedním z identifikátory jazyka z `speech` oboru v odpovědi z rozhraní API jazyky.|query|řetězec|
|na|(prázdné)|Určuje jazyk přeložit přepisována text do. Hodnota je jedním z identifikátory jazyka z `text` oboru v odpovědi z rozhraní API jazyky.|query|řetězec|
|Database|(prázdné)   |Textový soubor s oddělovači sadu funkcí pro vybraný klientem. K dispozici funkce patří:<ul><li>`TextToSpeech`: Určuje, zda služba musí vrátit přeložený zvuk poslední přeložený věty.</li><li>`Partial`: Určuje, zda služba musí vrátit zprostředkující rozpoznávání výsledky při zvukovém souboru je streamování ke službě.</li><li>`TimingInfo`: Určuje, zda služba musí vrátit časování informace spojené s každou rozpoznávání.</li></ul>Jako příklad by klient zadejte `features=partial,texttospeech` přijímat částečné výsledky a převod textu na řeč, ale žádné informace časování. Všimněte si, že konečných výsledků vždy streamovaných klientovi.|query|řetězec|
|hlas|(prázdné)|Určuje, jaké hlasu pro převod textu na řeč vykreslování přeložený text. Hodnota je jeden hlas identifikátorů z oboru převod textu na řeč v odpovědi z rozhraní API jazyky. Pokud daný hlas není zadán, že bude systém automaticky vyberte jednu když je funkce zapnutá.|query|řetězec|
|Formát|(prázdné)|Určuje formát, je vrácena službou zvuk datového proudu. Jsou dostupné možnosti:<ul><li>`audio/wav`: Zvukový datový proud zvukového průběhu. Klient musí použít záhlaví WAV správně interpretovat formát zvuku. Zvuku WAV pro převod textu na řeč je 16 bitů, jeden kanál PCM s vzorkovací frekvenci 24kHz nebo 16kHz.</li><li>`audio/mp3`: Zvukový datový proud MP3.</li></ul>Výchozí hodnota je `audio/wav`.|query|řetězec|
|ProfanityAction    |(prázdné)    |Určuje, jak službu profanities rozpoznán v řeč pracovat. Platné akce jsou:<ul><li>`NoAction`: Profanities nezbývají, jako je.</li><li>`Marked`: Profanities se nahradí značku. V tématu `ProfanityMarker` parametr.</li><li>`Deleted`: Profanities se odstraní. Například pokud slovo `"jackass"` je považován za vulgárnost, fráze `"He is a jackass."` bude `"He is a .".`</li></ul>Výchozí hodnota je označen.|query|řetězec|
|ProfanityMarker|(prázdné)    |Určuje, jak zjištěné profanities jsou zpracovávány při `ProfanityAction` je nastaven na `Marked`. Platné možnosti jsou:<ul><li>`Asterisk`: Profanities jsou nahrazeny řetězec `***`. Například pokud slovo `"jackass"` je považován za vulgárnost, fráze `"He is a jackass."` bude `"He is a ***.".`</li><li>`Tag`: Vulgárnost jsou obklopená vulgárnost – značka XML. Například pokud slovo `"jackass"` je považován za vulgárnost, fráze `"He is a jackass."` bude `"He is a <profanity>jackass</profanity>."`.</li></ul>Výchozí hodnota je `Asterisk`.|query|řetězec|
|Autorizace|(prázdné)  |Určuje hodnotu tokenu nosiče klienta. Použijte předponu `Bearer` následuje hodnotu `access_token` hodnoty vrácené ověřovací token služby.|záhlaví   |řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `Authorization` hlavičky není zadán.|záhlaví|řetězec|
|access_token|(prázdné)   |Alternativní způsob, jak předat platný přístup token OAuth. Token nosiče je obvykle poskytují s hlavičkou `Authorization`. Některé knihovny protokolu websocket neumožňují kód klienta k nastavení hlavičky. V takovém případě může klient použít `access_token` parametr předat platný token dotazu. Při použití přístupový token k ověření, pokud `Authorization` není nastavena hlavička, pak `access_token` musí být nastavena. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu. Klienti měli používat jenom jednu metodu předat token.|query|řetězec|
|klíč předplatného|(prázdné)   |Alternativní způsob, jak předat klíč předplatného. Některé knihovny protokolu websocket neumožňují kód klienta k nastavení hlavičky. V takovém případě může klient použít `subscription-key` parametr předat platné předplatné klíč dotazu. Při použití předplatného klíč k ověření, pokud `Ocp-Apim-Subscription-Key` není nastavena hlavička, pak klíč předplatného musí být nastavena. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu. Klienti měli používat jenom jednu metodu předat `subscription key`.|query|řetězec|
|X ClientTraceId    |(prázdné)    |Identifikátor GUID klientem generovaná použít ke sledování žádost. Pro správné řešení potíží s problémy, musí klienti zadejte novou hodnotu s každou žádostí a jeho přihlášení.<br/>Místo použití záhlaví, lze předat tuto hodnotu s parametr dotazu `X-ClientTraceId`. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X CorrelationId|(prázdné)    |Klientem generovaná identifikátor použitý ke korelaci více typů komunikačních kanálů v konverzaci. Povolit konverzace mezi uživateli lze vytvořit více relací řeči překlad. V takovém scénáři všechny relace překlad řeči použít stejné ID korelace ke svázání kanály společně. To usnadňuje trasování a diagnostiku. Identifikátor by měl vyhovovat: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Místo použití záhlaví, lze předat tuto hodnotu s parametr dotazu `X-CorrelationId`. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X ClientVersion|(prázdné)    |Identifikuje verze klienta aplikace. Příklad: "2.1.0.123".<br/>Místo použití záhlaví, lze předat tuto hodnotu s parametr dotazu `X-ClientVersion`. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X OsPlatform|(prázdné)   |Určuje název a verzi operačního systému, který klientská aplikace běží na. Příklady: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Místo použití záhlaví, lze předat tuto hodnotu s parametr dotazu `X-OsPlatform`. Pokud jsou nastavena hlavička i parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|Model odpovědi|Záhlaví|
|:--|:--|:--|:--|
|101    |Upgrade protokolu WebSocket.|Příklad hodnota modelu <br/> objekt {}|X-RequestId<br/>Hodnota identifikaci požadavků pro účely odstraňování potíží.<br/>řetězec|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry zajistit, že jsou platné. Objekt odpovědi obsahuje podrobnější popis chyby.|||
|401    |Neautorizováno. Ujistěte se, že jsou přihlašovací údaje nastavit, platnými a že vaše předplatné Azure dat trhu je v dobré stojící s dostupné vyrovnávání.|||
|500    |Došlo k chybě. Pokud chyba přetrvává, nahlaste to identifikátorem trasování klienta (X-ClientTraceId) nebo požadavku identifikátoru (X-RequestId).|||
|503    |Server není dočasně k dispozici. Opakujte žádost. Pokud chyba přetrvává, nahlaste to identifikátorem trasování klienta (X-ClientTraceId) nebo požadavku identifikátoru (X-RequestId).|||

    


    





    
    




    




    




    

            




        









