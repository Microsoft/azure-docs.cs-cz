---
title: Translator Speech API Reference
titleSuffix: Azure Cognitive Services
description: Referenční dokumentace pro rozhraní Translator Speech API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: 1fc48687141ea8a7e8cb30d3438d81e8f1088e4f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340439"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Tato služba nabízí rozhraní API pro streamování na přepisy konverzační řeči z jednoho jazyka do textu z jiného jazyka. Rozhraní API se také integruje možnosti převod textu na řeč k přečtení přeloženého textu. Rozhraní Translator Speech API umožňuje scénáře, jako jsou překlad v reálném čase konverzací, jak je vidět v Skype Translator.

S rozhraním Translator Speech API klientské aplikace Streamovat zvuk řeči ve službě a získávejte zpět o textové výsledky, které zahrnují rozpoznaný text v Zdrojový jazyk a překladu v cílovém jazyce. Výsledný text se vytváří tak, že se pro příchozí zvukový stream použije automatické rozpoznávání řeči (ASR) na bázi neuronových sítí. Nezpracovaného výstupu Azure Site Recovery je dál vylepšit nové techniky označované jako TrueText, aby bylo možné lépe odrážejí záměru uživatele. Například TrueText odebere disfluencies (hmms a coughs) a interpunkční znaménka správné obnovení a malá a velká písmena. K dispozici je také možnost maskování nebo vyloučení neslušných slov. Moduly pro rozpoznávání a překlad jsou speciálně natrénované tak, aby zvládly zpracování hovorové řeči. Překlad řeči služba používá nečinnosti detekce k určení konce utterance. Po pauze v hlasové aktivitě začne služba streamovat zpět konečný výsledek pro skončenou promluvu. Služba může odesílat zpět také částečné výsledky, které poskytují rozpoznávání a překlady částí probíhající promluvy. Pro konečných výsledků služba poskytuje možnost tak, aby odpovídaly řeči (převod textu na řeč) z mluveného textu v cílovém jazyce. Zvuk se při převodu textu na řeč vytváří ve formátu určeném klientem. K dispozici jsou formáty WAV a MP3.

Translator Speech API využívá protokol WebSocket poskytnout plně duplexní komunikační kanál mezi klientem a serverem. Aplikace vyžaduje tyto kroky k používání služby:

## <a name="1-getting-started"></a>1. Začínáme
Pro přístup k rozhraní Translator Text API je potřeba [zaregistrovat do Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Klíč předplatného se používají k ověření. Translator Speech API podporuje dva režimy ověřování:

* **Pomocí přístupového tokenu:** ve vaší aplikaci, získat přístupový token od služby tokenů. Pomocí svého klíče předplatné Translator Speech API k získání přístupového tokenu z Azure Cognitive Services ověřovací služby. Přístupový token je platný 10 minut. Získejte nový přístupový token každých 10 minut a mohli dál využívat stejný přístup token pro opakované požadavky v rámci těchto 10 minut.

* **Použití klíče předplatného přímo:** ve vaší aplikaci, předejte klíč předplatného. jako hodnotu v `Ocp-Apim-Subscription-Key` záhlaví.

Považovat za váš klíč předplatného a přístupovým tokenem tajné kódy, které by měl být skryta.

## <a name="3-query-languages"></a>3. Dotazovací jazyky
**Dotaz na prostředek jazyky pro aktuální sadu podporované jazyky.** [Jazyky prostředků](languages-reference.md) zveřejňuje sadu jazyků a hlasů pro rozpoznávání řeči, pro překlad textu a převod textu na řeč k dispozici. Každý jazyk nebo hlasu je přiřazena identifikátor, který rozhraní Translator Speech API používá k identifikaci stejný jazyk nebo hlasu.

## <a name="4-stream-audio"></a>4. Zvukový Stream
**Otevřete připojení a začít Streamovat zvuku ve službě.** Adresa URL služby je `wss://dev.microsofttranslator.com/speech/translate`. Parametry a formáty zvuku očekává službou jsou popsány níže v `/speech/translate` operace. Jeden z parametrů slouží k předání přístupový token z kroku 2 výše.

## <a name="5-process-the-results"></a>5. Zpracování výsledků
**Zpracování výsledků streamovaných ze služby.** Formát částečných výsledků, konečných výsledků a převod textu na řeč zvuku segmenty jsou popsány v dokumentaci `/speech/translate` operace níže.

Ukázky kódu pro demonstraci použití rozhraní Translator Speech API jsou k dispozici [webu Microsoft Translator Github](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Poznámky k implementaci

ZÍSKAT /speech/translate Establishes relace pro překlad řeči

### <a name="connecting"></a>Připojování
Před připojením ke službě, přečtěte si seznam parametrů uvedených dále v této části. Příklad žádosti je:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Žádost Určuje, že mluvené slovo angličtina bude možné streamovali do služby a přeloženy do italština. Každý rozpoznávání konečný výsledek bude generovat převod textu na řeč zvuku odpověď s Ženský hlas s názvem grafické karty Elsa. Všimněte si, že žádost obsahuje přihlašovací údaje `Ocp-Apim-Subscription-Key header`. Požadavek také řídí osvědčený postup tak, že nastavíte globálně jedinečný identifikátor v hlavičce `X-ClientTraceId`. Klientská aplikace by měla protokolovat ID trasování tak, aby ho můžete použít při řešení problémů při jejich výskytu.

### <a name="sending-audio"></a>Posílání zvuku
Po připojení se naváže, začne klient, vysílání datového proudu zvuku ve službě. Klient odešle zvuku v blocích. Každý blok se přenáší pomocí protokolu Websocket zprávy typu Binary.

Zvukový vstup je ve formátu souboru zvuk zvukového průběhu (WAVE nebo více obvykle označuje jako WAV z důvodu jeho příponu názvu souboru). Klientská aplikace by měl datový proud jeden kanál, odebírána data v 16 kHz zvuk PCM podepsaný 16 bitů. První sada bajtů streamuje klientovi bude zahrnovat hlavičku WAV. Záhlaví 44 bajtů pro jeden kanál podepsané 16 bitů PCM stream odebírána data v 16 kHz je:

|Posun|Hodnota|
|:---|:---|
|0 – 3|"RIFF"|
|4 – 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 – 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 – 35|16|
|36 - 39|"data"|
|40 - 43|0|

Všimněte si, že velikost celkový počet souborů (bajty 4-7) a velikost "data" (v bajtech 40-43) jsou nastaveny na hodnotu nula. To je v pořádku pro scénář streamování, ve kterém je celková velikost není znám nutně předem.

Po odeslání hlaviček WAV (RIFF), klient odešle bloky zvuková data. Klient bude obvykle datový proud pevnou velikost bloků dat představující pevnou platnost (například datový proud 100 MS zvuku současně).

### <a name="final-result"></a>Konečný výsledek
Na konci utterance se vygeneruje výsledek rozpoznání řeči finální. Výsledkem je ze služby předány klienta pomocí protokolu WebSocket zpráva typu Text. Obsah zprávy se serializací JSON objektu s následujícími vlastnostmi:

* `type`: Řetězec konstanty k označení typu výsledku. Hodnota je konečný konečných výsledků.
* `id`: Řetězec identifikátor přiřazený k rozpoznávání výsledku.
* `recognition`: Rozpoznaný text v Zdrojový jazyk. Text může být prázdný řetězec v případě false rozpoznávání.
* `translation`: Rozpoznaný text přeložit v cílovém jazyce.
* `audioTimeOffset`: Posun času zahájení rozpoznávání v taktech (1 značek = 100 nanosekund). Posun je relativní vzhledem k začátku streamování.
* `audioTimeSize`: Doba trvání v taktech (100 nanosekund) uznání.
* `audioStreamPosition`: Posun bajtů zahájení uznání. Posun je relativní vzhledem k začátku datového proudu.
* `audioSizeBytes`: Velikost v bajtech uznání.

Všimněte si, že umístění rozpoznávání na zvukový datový proud není zahrnut ve výsledcích ve výchozím nastavení. `TimingInfo` Funkce musí být určen klienta (viz `features` parametr).

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
Výsledky rozpoznávání řeči částečné nebo zprostředkující nejsou streamuje klientovi ve výchozím nastavení. Klienta můžete použít parametr dotazu funkcí na vyžádání.

Částečný výsledek se přenášejí ze služby do klienta pomocí protokolu WebSocket zpráva typu Text. Obsah zprávy se serializací JSON objektu s následujícími vlastnostmi:

* `type`: Řetězec konstanty k označení typu výsledku. Hodnota je částečně pro částečné výsledky.
* `id`: Řetězec identifikátor přiřazený k rozpoznávání výsledku.
* `recognition`: Rozpoznaný text v Zdrojový jazyk.
* `translation`: Rozpoznaný text přeložit v cílovém jazyce.
* `audioTimeOffset`: Posun času zahájení rozpoznávání v taktech (1 značek = 100 nanosekund). Posun je relativní vzhledem k začátku streamování.
* `audioTimeSize`: Doba trvání v taktech (100 nanosekund) uznání.
* `audioStreamPosition`: Posun bajtů zahájení uznání. Posun je relativní vzhledem k začátku datového proudu.
* `audioSizeBytes`: Velikost v bajtech uznání.

Všimněte si, že umístění rozpoznávání na zvukový datový proud není zahrnut ve výsledcích ve výchozím nastavení. Funkce TimingInfo vybrány klientem (viz parametr funkce).

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
Pokud je povoleno funkci pro převod textu na řeč (naleznete v tématu `features` parametr níže), konečný výsledek je následována zvuk mluvené slovo přeloženého textu. Zvukových dat je rozdělený do bloků dat a odeslané ze služby ke klientovi jako pořadí zpráv protokolu Websocket typu Binary. Klient dokáže detekovat konce datového proudu kontrolou bitu dokončení každé zprávy. Poslední zprávy v binární bude mít jeho dokončení bit nastaven na jednu k označení konce datového proudu. Formát datový proud závisí na hodnotě `format` parametru.

### <a name="closing-the-connection"></a>Probíhá ukončování připojení
Když klientská aplikace dokončení vysílání datového proudu zvuku, obdržela na poslední konečný výsledek ji by měl ukončit připojení pomocí inicializace uzavírací handshake protokolu WebSocket. Existují podmínky, které způsobí, že server k ukončení připojení. Následující kódy zavření protokolu WebSocket může přijmout klienta:

* `1003 - Invalid Message Type`: Server je ukončován připojení, protože datový typ, který obdržel nemůže přijmout. Běžně se to stane, když příchozí zvuk nezačíná řetězcem správné záhlaví.
* `1000 - Normal closure`: Poté, co se požadavek splnil připojení ukončeno. Server připojení zavře: při přijetí zvuk z klienta delší dobu času; Když se streamuje nečinnosti delší dobu času; dosáhne-li relaci maximální dobu trvání povolené (přibližně 90 minut).
* `1001 - Endpoint Unavailable`: Označuje, že server nebude k dispozici. Klientská aplikace může pokusí znovu připojit se limitu počtu opakování.
* `1011 - Internal Server Error`: Připojení bude ukončeno serverem kvůli chybě na serveru.

### <a name="parameters"></a>Parametry

|Parametr|Hodnota|Popis|Typ parametru|Typ dat|
|:---|:---|:---|:---|:---|
|verze API-version|1.0|Verze rozhraní API požadovaná klientem. Povolené hodnoty jsou: `1.0`.|query   |řetězec|
|od|(prázdné)   |Určuje jazyk příchozí řeči. Hodnota je jeden z identifikátorů jazyka z `speech` oboru v odpovědi z rozhraní API pro jazyky.|query|řetězec|
|na|(prázdné)|Určuje jazyk, který chcete přeložit text přepisu do. Hodnota je jeden z identifikátorů jazyka z `text` oboru v odpovědi z rozhraní API pro jazyky.|query|řetězec|
|Database|(prázdné)   |Exportovaná sadu funkcí zvolila klienta. Mezi dostupné funkce patří:<ul><li>`TextToSpeech`: Určuje, že služba musí vrátit přeložené zvuk na finální přeloženou větu.</li><li>`Partial`: Určuje, že služba musí vracet výsledky zprostředkující rozpoznávání při streamování zvuku ve službě.</li><li>`TimingInfo`: Určuje, že služba musí vracet informace o časování, které jsou spojené s každou rozpoznávání.</li></ul>Jako příklad klienta zadáte `features=partial,texttospeech` přijímat částečné výsledky a převod textu na řeč, ale žádné informace o časování. Všimněte si, že konečných výsledků se vždy streamují do klienta.|query|řetězec|
|Hlasu|(prázdné)|Určuje co hlasu pro převod textu na řeč vykreslování přeloženého textu. Hodnota je jeden z identifikátorů hlasové z oboru převod textu na řeč v odpovědi z rozhraní API pro jazyky. Pokud hlasový vstup není zadán, že systém bude automaticky zvolte jeden, pokud je povolena funkce Převod textu na řeč.|query|řetězec|
|Formát|(prázdné)|Určuje formát převod textu na řeč zvukový stream vrácený poskytovatelem služby. Dostupné možnosti jsou:<ul><li>`audio/wav`: Zvukový datový proud zvukového průběhu. Klient musí použít hlavičku WAV správně interpretovat zvukový formát. Zvuk WAV pro převod textu na řeč je 16 bitů, jeden kanál PCM s vzorkovací frekvenci 24kHz nebo 16kHz.</li><li>`audio/mp3`: Zvukový datový proud MP3.</li></ul>Výchozí hodnota je `audio/wav`.|query|řetězec|
|ProfanityAction    |(prázdné)    |Určuje způsob, jakým služba pracovat profanities rozpoznán v řeči. Jsou platné akce:<ul><li>`NoAction`: Profanities je ponechán beze změny.</li><li>`Marked`: Profanities jsou nahrazeny značku. Zobrazit `ProfanityMarker` parametru.</li><li>`Deleted`: Profanities se odstraní. Například pokud slovo `"jackass"` je považován za vulgárních výrazů frázi `"He is a jackass."` se stane `"He is a .".`</li></ul>Výchozí hodnota je označen.|query|řetězec|
|ProfanityMarker|(prázdné)    |Určuje, jak zjištěné profanities jsou zpracovány při `ProfanityAction` je nastavena na `Marked`. Platné možnosti jsou:<ul><li>`Asterisk`: Profanities se nahradí řetězcem `***`. Například pokud slovo `"jackass"` je považován za vulgárních výrazů frázi `"He is a jackass."` se stane `"He is a ***.".`</li><li>`Tag`: Vulgárních výrazů jsou ohraničeny vulgárních výrazů – značka XML. Například pokud slovo `"jackass"` je považován za vulgárních výrazů frázi `"He is a jackass."` se stanou `"He is a <profanity>jackass</profanity>."`.</li></ul>Výchozí hodnota je `Asterisk`.|query|řetězec|
|Autorizace|(prázdné)  |Určuje hodnotu klienta nosný token. Použijte předponu `Bearer` za nímž následuje hodnotu `access_token` hodnoty vrácené službou tokenu ověřování.|záhlaví   |řetězec|
|OCP-Apim-Subscription-Key|(prázdné)|Požadováno pokud `Authorization` není zadána hlavička.|záhlaví|řetězec|
|access_token|(prázdné)   |Alternativní způsob, jak předat platný přístupový token OAuth. Nosný token je obvykle poskytují s hlavičkou `Authorization`. Některé knihovny pomocí protokolu websocket neumožňují klientským kódem, aby nastavení hlaviček. V takovém případě může klient použít `access_token` parametr předat platný token dotazu. Při použití přístupového tokenu pro ověření, pokud `Authorization` není nastavena hlavička, pak `access_token` musí být nastavena. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu. Klienti měli používat jenom jedna metoda předat token.|query|řetězec|
|klíč předplatného|(prázdné)   |Alternativní způsob, jak předat klíč předplatného. Některé knihovny pomocí protokolu websocket neumožňují klientským kódem, aby nastavení hlaviček. V takovém případě může klient použít `subscription-key` parametr předejte platné předplatné klíč dotazu. Pokud používáte klíč předplatného k ověření, `Ocp-Apim-Subscription-Key` není nastavena hlavička, pak musí být nastaven klíč předplatného. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu. Klienti měli používat jenom jednu metodu k předání `subscription key`.|query|řetězec|
|X-ClientTraceId    |(prázdné)    |Identifikátor GUID klientem generovaná použít ke sledování požadavku. Pro správné řešení potíží, musí klienti zadejte novou hodnotu s každou žádostí a zaznamenejte ho.<br/>Namísto použití záhlaví, lze předat tuto hodnotu s parametrem dotazu `X-ClientTraceId`. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X-ID korelace|(prázdné)    |Klientem generovaná identifikátor použité pro sladění více kanálů v konverzaci. Konverzace mezi uživateli povolit lze vytvořit více relací překladu řeči. V takovém scénáři všechny relace překlad řeči pomocí stejné ID korelace spojovat kanály. To usnadňuje trasování a Diagnostika. Musí odpovídat identifikátoru: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Namísto použití záhlaví, lze předat tuto hodnotu s parametrem dotazu `X-CorrelationId`. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X-ClientVersion|(prázdné)    |Určuje verzi klientská aplikace. Příklad: "2.1.0.123".<br/>Namísto použití záhlaví, lze předat tuto hodnotu s parametrem dotazu `X-ClientVersion`. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|
|X-OsPlatform|(prázdné)   |Určuje název a verzi operačního systému, které klientská aplikace běží na. Příklady: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Namísto použití záhlaví, lze předat tuto hodnotu s parametrem dotazu `X-OsPlatform`. Pokud jsou nastaveny záhlaví a parametr dotazu, je ignorován parametr dotazu.|záhlaví|řetězec|

### <a name="response-messages"></a>Zprávy odpovědi

|Kód stavu HTTP|Důvod|Model odpovědi|Hlavičky|
|:--|:--|:--|:--|
|101    |Upgrade objektu WebSocket.|Příklad hodnoty modelu <br/> objekt {}|X-RequestId<br/>Hodnota identifikaci požadavků pro účely odstraňování potíží.<br/>řetězec|
|400    |Chybný požadavek. Zkontrolujte vstupní parametry k zajištění, že jsou platné. Objekt odpovědi obsahuje podrobnější popis chyby.|||
|401    |Neautorizováno. Ujistěte se, že se přihlašovací údaje nastavené, že jsou platné a že vaše předplatné Azure Tržiště dat je v pořádku s dostupné vyrovnávání.|||
|500    |Došlo k chybě. Pokud potíže potrvají, zprávu s identifikátorem trasování klienta (X-ClientTraceId) nebo identifikátor (X-RequestId) žádosti.|||
|503    |Server je dočasně nedostupný. Zkuste prosím požadavek. Pokud potíže potrvají, zprávu s identifikátorem trasování klienta (X-ClientTraceId) nebo identifikátor (X-RequestId) žádosti.|||

    


    





    
    




    




    




    

            




        









