---
title: Poznámky k verzi – služba Speech
titleSuffix: Azure Cognitive Services
description: Spuštěný protokol funkcí služby Speech Service, vylepšení, opravy chyb a známé problémy.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219640"
---
# <a name="release-notes"></a>Poznámky k verzi
## <a name="speech-sdk-1100-2020-february-release"></a>Řeč SDK 1.10.0: 2020-únor vydání

**Nové funkce**
 - Přidány balíčky Pythonu pro podporu nové verze Pythonu 3.8.
 - Podpora Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Zákazníci musí nakonfigurovat OpenSSL podle [těchto pokynů](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Podpora Linuxu ARM32 pro Debian a Ubuntu.
 - DialogServiceConnector nyní podporuje volitelný parametr "bot ID" na BotFrameworkConfig. Tento parametr umožňuje použití více direct line řeči roboty s jedním prostředkem řeči Azure. Bez zadaného parametru bude použit výchozí robot (určený konfigurační stránkou kanálu Direct Line Speech).
 - DialogServiceConnector má nyní vlastnost SpeechActivityTemplate. Obsah tohoto řetězce JSON bude použit direct line řeč předem vyplnit širokou škálu podporovaných polí ve všech aktivit, které dosáhnou Direct Line Řeč bot, včetně aktivit automaticky generovány v reakci na události, jako je rozpoznávání řeči.
 - TTS nyní používá klíč předplatného pro ověřování, snížení latence první bajt první výsledek syntézy po vytvoření syntezátoru.
 - Aktualizované modely rozpoznávání řeči pro 19 národních prostředí pro průměrné snížení chybovosti slov o 18,6 % (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, TR-TR). Nové modely přinášejí významná vylepšení ve více doménách, včetně scénářů diktování, přepisu call centra a indexování videa.

**Opravy chyb**
 - Opravena chyba, kdy přepiskonverzace nenačítala správně v JAVA API 
 - Oprava emulátoru Android x86 pro problém Samarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Přidat chybějící (Získat| Set)Metody vlastností pro AudioConfig
 - Oprava chyby tts, kde nelze zastavit audioDataStream, když se připojení nezdaří
 - Použití koncového bodu bez oblasti by způsobilo selhání USP pro překladač konverzace
 - Generování ID v univerzálních aplikacích systému Windows nyní používá vhodně jedinečný algoritmus GUID; dříve a neúmyslně selhání stubbed implementace, která často vyrábí kolize přes velké sady interakcí.
 
 **ukázky**
 - Ukázka unity pro použití sady Speech SDK s [mikrofonem Unity a streamováním v režimu push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Další změny**
 - [Konfigurační dokumentace OpenSSL aktualizovaná pro Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Řeč SDK 1.9.0: 2020-leden vydání

**Nové funkce**

- Konverzace na více zařízeních: Připojte více zařízení ke stejné konverzaci pomocí řeči nebo textu a volitelně překládejte zprávy odeslané mezi nimi. Další informace naleznete v [tomto článku](multi-device-conversation.md). 
- Podpora rozpoznávání klíčových slov přidána pro balíček Android .aar a přidána podpora pro varianty x86 a x64. 
- Cíl-C: `SendMessage` `SetMessageProperty` a metody `Connection` přidané k objektu. Naleznete v dokumentaci [zde](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- Rozhraní API tts c++ nyní podporuje `std::wstring` jako vstup syntetického textu, čímž odstraňuje potřebu převést wstring na řetězec před předáním do sady SDK. Podrobnosti naleznete [zde](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [ID jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) a [konfigurace zdrojového jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) jsou nyní k dispozici.
- JavaScript: Přidána `Connection` funkce objektu pro průchod vlastních zpráv `receivedServiceMessage`ze služby řeči jako zpětné volání .
- JavaScript: Přidána `FromHost API` podpora pro snadné použití s on-prem kontejnery a suverénní mraky. Naleznete v dokumentaci [zde](speech-container-howto.md).
- JavaScript: Nyní `NODE_TLS_REJECT_UNAUTHORIZED` ctíme díky příspěvku z [orgads](https://github.com/orgads). Podrobnosti naleznete [zde](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Změny způsobující chyby**

- `OpenSSL`byla aktualizována na verzi 1.1.1b a je staticky propojena s základní knihovnou sady Speech SDK pro Linux. To může způsobit přerušení, `OpenSSL` pokud vaše doručená `/usr/lib/ssl` pošta nebyla nainstalována do adresáře v systému. Chcete-li problém vyřešit, podívejte se do [naší dokumentace](how-to-configure-openssl-linux.md) v části Dokumenty sady Speech SDK.
- Změnili jsme datový typ vrácený `int` `long` pro C# `WordLevelTimingResult.Offset` `WordLevelTimingResults` z do umožnit přístup k řeči data delší než 2 minuty.
- `PushAudioInputStream`a `PullAudioInputStream` nyní odesílat informace záhlaví wav `AudioStreamFormat`do služby řeči založené na , volitelně určené při jejich vytvoření. Zákazníci nyní musí používat [podporovaný formát zvukového vstupu](how-to-use-audio-input-streams.md). Jakékoli jiné formáty získají neoptimální výsledky rozpoznávání nebo mohou způsobit další problémy. 


**Opravy chyb**

- Viz `OpenSSL` aktualizace v části Nejnovější změny výše. Opravili jsme jak občasné selhání, tak problém s výkonem (konflikt zámků při vysokém zatížení) v Linuxu a Javě. 
- Java: Provedla vylepšení uzavření objektu ve scénářích vysoké souběžnosti.
- Restrukturalizovalnáš balíček NuGet. Odebrali jsme tři `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` kopie a pod lib složky, takže balíček NuGet menší a rychlejší ke stažení a přidali jsme záhlaví potřebné ke kompilaci některých c++ nativní aplikace.
- Opraveny ukázky rychlého startu [zde](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Ty byly ukončení bez zobrazení "mikrofon nebyl nalezen" výjimku na Linux, MacOS, Windows.
- Opravenpád sady SDK s dlouhými výsledky rozpoznávání řeči na určitých cestách kódu, jako [je tato ukázka](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Opravena chyba nasazení sady SDK v prostředí Azure Web Appu, aby se tento [problém se zákazníky](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)vyřešil .
- Opravena chyba tts `<voice>` při `<audio>` použití víceznaček nebo značky k řešení [tohoto problému se zákazníkem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Opravena chyba TTS 401, když je sada SDK obnovena z pozastavena.
- JavaScript: Opraven kruhový import zvukových dat díky příspěvku [euirim](https://github.com/euirim). 
- JavaScript: přidána podpora pro nastavení vlastností služby, jak je přidáno v 1.7.
- JavaScript: opraven problém, kdy chyba připojení mohla vést k nepřetržitým neúspěšným pokusům o opětovné připojení websocketu.


**ukázky**

- Zde byla přidána ukázka rozpoznávání klíčových slov pro [Android](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Zde byla přidána ukázka serveru pro [serverový](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)scénář .
- Zde byly přidány rychlé starty konverzace s více zařízeními pro c# a [c++](quickstarts/multi-device-conversation.md).


**Další změny**

- Optimalizovaná velikost základní knihovny Sady SDK v systému Android.
- Sada SDK v 1.9.0 a `int` `string` dále podporuje oba typy a typy v poli verze hlasového podpisu pro přepis konverzace.

## <a name="speech-sdk-180-2019-november-release"></a>Řeč SDK 1.8.0: 2019-listopad vydání

**Nové funkce**

- Přidáno `FromHost()` rozhraní API pro usnadnění používání s kontejnery on-prem a suverénními cloudy.
- Přidána automatická detekce jazyka zdroje pro rozpoznávání řeči (v jazyce Java a C++)
- Přidán `SourceLanguageConfig` objekt pro rozpoznávání řeči, který se používá k určení očekávaných zdrojových jazyků (v jazyce Java a C++)
- Přidána `KeywordRecognizer` podpora v systémech Windows (UPW), Android a iOS prostřednictvím balíčků NuGet a Unity
- Přidáno rozhraní Java API vzdálené konverzace pro přepis konverzace v asynchronních dávkách.

**Změny způsobující chyby**

- Funkce přepisu konverzace přesunuté `Microsoft.CognitiveServices.Speech.Transcription`pod obornázvů .
- Část metody přepisování konverzace jsou přesunuty do nové `Conversation` třídy.
- Zrušená podpora pro 32bitový (ARMv7 a x86) iOS

**Opravy chyb**

- Oprava selhání, `KeywordRecognizer` pokud se používá místní bez platného klíče předplatného služby Speech

**ukázky**

- Vzorek Xamarinu pro`KeywordRecognizer`
- Ukázka jednoty pro`KeywordRecognizer`
- Ukázky jazyka C++ a Jazyka Java pro automatickou detekci jazyka zdroje.

## <a name="speech-sdk-170-2019-september-release"></a>Řeč SDK 1.7.0: vydání 2019-září

**Nové funkce**

- Přidána beta podpora pro Xamarin na univerzální platformě Windows (UPW), Android a iOS
- Přidána podpora iOS pro Unity
- Přidána `Compressed` vstupní podpora pro ALaw, Mulaw, FLAC na Android, iOS a Linux
- `SendMessageAsync` Přidáno `Connection` do třídy pro odeslání zprávy do služby
- `SetMessageProperty` Přidáno `Connection` do třídy pro nastavení vlastnosti zprávy
- TTS přidali vazby pro Javu (Jre a Android), Python, Swift a Objective-C
- TTS přidal podporu přehrávání pro macOS, iOS a Android.
- Přidány informace o hranici slova pro tts.

**Opravy chyb**

- Opraven problém se stavbou IL2CPP na Unity 2019 pro Android
- Opraven problém s nesprávně vytvořenými hlavičkami ve vstupu souboru WAV, které byly zpracovány nesprávně
- Opraven problém s identifikátory UUID, které nebyly v některých vlastnostech připojení jedinečné
- Opraveno několik upozornění na specifikátory nullability ve vazbách Swift (může vyžadovat malé změny kódu)
- Opravena chyba, která způsobovala neřádné uzavření připojení websocketu při zatížení sítě
- Opraven problém s Androidem, který někdy vede k duplicitním ID zobrazení, která`DialogServiceConnector`
- Zlepšení stability spojení napříč vícefázovými interakcemi a hlášení `Canceled` poruch (prostřednictvím událostí), pokud k nim dojde`DialogServiceConnector`
- `DialogServiceConnector`spuštění relace bude nyní správně poskytovat `ListenOnceAsync()` události, a to i při volání během aktivního`StartKeywordRecognitionAsync()`
- Řešení selhání spojeného `DialogServiceConnector` s přijatými aktivitami

**ukázky**

- Rychlý start pro Xamarin
- Aktualizovaný CPP Quickstart s Linuxem ARM64 informace
- Aktualizovaný rychlý start Unity s informacemi o iOS

## <a name="speech-sdk-160-2019-june-release"></a>Řeč SDK 1.6.0: vydání 2019-june

**ukázky**

- Ukázky rychlého startu pro převod textu na řeč na UPW a Unity
- Ukázka rychlého startu pro Swift v systému iOS
- Ukázky jednoty pro rozpoznávání záměru řeči & záměru a překlad
- Aktualizované ukázky rychlého startu pro`DialogServiceConnector`

**Vylepšení / Změny**

- Obor názvů dialogového okna:
  - Přejmenování `SpeechBotConnector` na `DialogServiceConnector`
  - Přejmenování `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`byla přemapována na`DialogServiceConfig::FromBotSecret()`
  - Všichni existující klienti Direct Line Speech jsou i nadále podporováni i po přejmenování
- Aktualizace adaptéru TTS REST pro podporu proxy, trvalého připojení
- Zlepšit chybovou zprávu při předání neplatné oblasti
- Swift/Cíl-C:
  - Vylepšené zasílání zpráv o chybách: Metody, které mohou vést k `NSError` chybě, jsou nyní k dispozici ve dvou verzích: Jedna, která zpřístupňuje objekt pro zpracování chyb, a jedna, která vyvolá výjimku. První z nich jsou vystaveny Swift. Tato změna vyžaduje úpravy existujícího kódu Swift.
  - Vylepšené zpracování událostí

**Opravy chyb**

- Oprava pro TTS: kde `SpeakTextAsync` se budoucí vrátí bez čekání na dokončení vykreslování zvuku
- Oprava zařazovacích řetězců v jazyce C# pro povolení plné jazykové podpory
- Oprava problému s základní aplikací .NET pro načtení základní knihovny s cílovým rámcem net461 ve vzorcích
- Oprava příležitostných problémů při nasazování nativních knihoven do výstupní složky v ukázkách
- Oprava spolehlivého zavírání webových soketů
- Oprava možného pádu při otevírání připojení při velmi velkém zatížení na Linuxu
- Oprava chybějících metadat v sadě framework pro macOS
- Oprava problémů `pip install --user` v systému Windows

## <a name="speech-sdk-151"></a>Řeč SDK 1.5.1

Toto je vydání opravy chyb a týká se pouze nativní/spravované sady SDK. Nemá vliv na javascriptovou verzi sady SDK.

**Opravy chyb**

- Oprava FromSubscription při použití s přepiskonverzace.
- Oprava chyby v pozorování klíčových slov pro hlasové asistenty.

## <a name="speech-sdk-150-2019-may-release"></a>Řeč SDK 1.5.0: 2019-Květen vydání

**Nové funkce**

- Klíčové slovo špinění (KWS) je nyní k dispozici pro Windows a Linux. Funkce KWS může fungovat s libovolným typem mikrofonu, oficiální podpora KWS je však v současné době omezena na pole mikrofonu, která se nacházejí v hardwaru Azure Kinect DK nebo v sadě SDK řečových zařízení.
- Funkce nápovědy frází je k dispozici prostřednictvím sady SDK. Další informace naleznete [zde](how-to-phrase-lists.md).
- Funkce přepisu konverzace je k dispozici prostřednictvím sady SDK. Viz [zde](conversation-transcription-service.md).
- Přidejte podporu pro hlasové asistenty pomocí kanálu Direct Line Speech.

**ukázky**

- Přidány ukázky pro nové funkce nebo nové služby podporované sadou SDK.

**Vylepšení / Změny**

- Přidány různé vlastnosti nástroje pro rozpoznávání pro úpravu chování služby nebo výsledků služby (například maskování vulgárních výrazů a dalších).
- Nyní můžete nakonfigurovat nástroj pro rozpoznávání prostřednictvím standardních vlastností konfigurace, a to i v případě, že jste nástroj pro rozpoznávání vytvořili `FromEndpoint`.
- Cíl-C: `OutputFormat` vlastnost byla `SPXSpeechConfiguration`přidána do .
- Sada SDK nyní podporuje Debian 9 jako linuxovou distribuci.

**Opravy chyb**

- Opraven problém, kdy byl prostředek mluvčího zničen příliš brzy v převodu textu na řeč.

## <a name="speech-sdk-142"></a>Řeč SDK 1.4.2

Toto je vydání opravy chyb a týká se pouze nativní/spravované sady SDK. Nemá vliv na javascriptovou verzi sady SDK.

## <a name="speech-sdk-141"></a>Řeč SDK 1.4.1

Toto je verze pouze pro JavaScript. Nebyly přidány žádné funkce. Byly provedeny následující opravy:

- Zabránit webový balíček z načítání https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Řeč SDK 1.4.0: vydání 2019-duben

**Nové funkce**

- Sada SDK nyní podporuje službu převodu textu na řeč jako beta verzi. Je podporován na Windows a Linux Desktop z C++ a C#. Další informace naleznete v [přehledu převodu textu na řeč](text-to-speech.md#get-started).
- SDK nyní podporuje MP3 a Opus / OGG zvukové soubory jako stream vstupní soubory. Tato funkce je k dispozici pouze na Linuxu z C++ a C# a je v současné době v beta verzi (více informací [zde](how-to-use-codec-compressed-audio-input-streams.md)).
- Sada Speech SDK pro jazyk Java, jádro .NET, C++ a Objective-C získala podporu macOS. Podpora Objective-C pro macOS je momentálně v beta verzi.
- iOS: Sada Speech SDK pro iOS (Objective-C) je nyní také publikována jako Kakaový pod.
- JavaScript: Podpora pro non-výchozí mikrofon jako vstupní zařízení.
- JavaScript: Podpora proxy pro Node.js.

**ukázky**

- Byly přidány ukázky pro použití sady Speech SDK s C++ a objective-C v systému macOS.
- Byly přidány ukázky demonstrující použití služby převodu textu na řeč.

**Vylepšení / Změny**

- Python: Další vlastnosti výsledků rozpoznávání `properties` jsou nyní vystaveny prostřednictvím vlastnosti.
- Pro další podporu vývoje a ladění můžete přesměrovat informace o protokolování a diagnostice sady SDK do souboru protokolu (další podrobnosti [zde).](how-to-use-logging.md)
- JavaScript: Zlepšete výkon zpracování zvuku.

**Opravy chyb**

- Mac/iOS: Byla opravena chyba, která vedla k dlouhému čekání, když nebylo možné navázat připojení ke službě Speech.
- Python: zlepšit zpracování chyb pro argumenty v Pythonu zpětná volání.
- JavaScript: Opraveno nesprávné hlášení stavu pro řeč ukončenou na RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Řeč SDK 1.3.1: 2019-únor obnovit

Toto je vydání opravy chyb a týká se pouze nativní/spravované sady SDK. Nemá vliv na javascriptovou verzi sady SDK.

**Oprava chyby**

- Opraven únik paměti při použití vstupu do mikrofonu. Vstup založený na datovém proudu nebo vstup souboru není ovlivněn.

## <a name="speech-sdk-130-2019-february-release"></a>Řeč SDK 1.3.0: 2019-únor vydání

**Nové funkce**

- Sada Speech SDK podporuje výběr vstupního mikrofonu prostřednictvím třídy. `AudioConfig` To umožňuje streamovat zvuková data do služby Řeč z jiného než výchozího mikrofonu. Další informace naleznete v dokumentaci popisující [výběr vstupního zvukového zařízení](how-to-select-audio-input-devices.md). Tato funkce ještě není k dispozici z JavaScriptu.
- Sada Speech SDK nyní podporuje unity v beta verzi. Poskytněte zpětnou vazbu prostřednictvím části problému v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity v systému Windows x86 a x64 (desktop nebo univerzální platformy Windows aplikace) a Android (ARM32/64, x86). Více informací je k dispozici v našem [unity quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodaný v předchozích verzích) již není potřeba. Funkce je nyní integrována do základní sady SDK.

**ukázky**

Následující nový obsah je k dispozici v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples):

- Další vzorky pro `AudioConfig.FromMicrophoneInput`.
- Další ukázky Pythonu pro rozpoznávání záměru a překlad.
- Další ukázky `Connection` pro použití objektu v iOS.
- Další java ukázky pro překlad se zvukovým výstupem.
- Nový vzorek pro použití [rozhraní REST API pro přepis dávky](batch-transcription.md).

**Vylepšení / Změny**

- Python
  - Vylepšené ověřování parametrů `SpeechConfig`a chybové zprávy v rozhraní .
  - Přidejte podporu `Connection` pro objekt.
  - Podpora 32bitového Pythonu (x86) v systému Windows.
  - Sada Speech SDK pro Python je mimo beta verzi.
- iOS
  - Sada SDK je nyní postavena proti iOS SDK verze 12.1.
  - Sada SDK nyní podporuje iOS verze 9.2 a novější.
  - Zlepšete referenční dokumentaci a opravte několik názvů vlastností.
- JavaScript
  - Přidejte podporu `Connection` pro objekt.
  - Přidání souborů definice typu pro přibalený JavaScript
  - Počáteční podpora a implementace pro tipy na frázi.
  - Vrácení kolekce vlastností se službou JSON pro rozpoznávání
- Knihovny DLL systému Windows nyní obsahují prostředek verze.
- Pokud vytvoříte nástroj `FromEndpoint` pro rozpoznávání, můžete přidat parametry přímo do adresy URL koncového bodu. Pomocí `FromEndpoint` nástroje pro rozpoznávání nelze nakonfigurovat pomocí standardních vlastností konfigurace.

**Opravy chyb**

- Prázdné uživatelské jméno proxy a heslo proxy nebyly zpracovány správně. V této verzi, pokud nastavíte proxy uživatelské jméno a proxy heslo na prázdný řetězec, nebudou odeslány při připojení k proxy serveru.
- SessionId je vytvořen SDK nebyly vždy opravdu náhodné&nbsp;pro některé jazyky / prostředí. Přidána inicializace náhodného generátoru k vyřešení tohoto problému.
- Zlepšete zpracování autorizačního tokenu. Pokud chcete použít autorizační token, `SpeechConfig` zadejte v a ponechte klíč předplatného prázdný. Pak vytvořte nástroj pro rozpoznávání jako obvykle.
- V některých `Connection` případech objekt nebyl správně uvolněn. Tento problém byl opraven.
- JavaScript ukázka byla stanovena na podporu audio výstup pro syntézu překladu i na Safari.

## <a name="speech-sdk-121"></a>Řeč SDK 1.2.1

Toto je verze pouze pro JavaScript. Nebyly přidány žádné funkce. Byly provedeny následující opravy:

- Fire konec proudu na turn.end, ne na speech.end.
- Oprava chyby v audio čerpadle, které nenaplánovalo další odeslání, pokud se aktuální odeslání nezdařilo.
- Opravte průběžné rozpoznávání pomocí tokenu auth.
- Oprava chyb pro různé rozpoznávače / koncové body.
- Vylepšení dokumentace.

## <a name="speech-sdk-120-2018-december-release"></a>Řeč SDK 1.2.0: 2018-prosinec vydání

**Nové funkce**

- Python
  - Beta verze podpory Pythonu (3.5 a vyšší) je k dispozici s touto verzí. Další informace naleznete zde](quickstart-python.md).
- JavaScript
  - Sada Speech SDK pro JavaScript byla otevřena. Zdrojový kód je k dispozici na [GitHubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Nyní podporujeme Node.js, více informací naleznete [zde](quickstart-js-node.md).
  - Omezení délky zvukových relací bylo odstraněno, opětovné připojení proběhne automaticky pod krytem.
- `Connection`Objekt
  - Z `Recognizer`, můžete přistupovat k objektu. `Connection` Tento objekt umožňuje explicitně zahájit připojení služby a přihlásit se k připojení a odpojení událostí.
    (Tato funkce ještě není k dispozici z JavaScriptu a Pythonu.)
- Podpora pro Ubuntu 18.04.
- Android
  - Povolená podpora ProGuard během generování APK.

**Zlepšení**

- Zlepšení využití vnitřního vlákna, snížení počtu podprocesů, zámky, objekty mutex.
- Vylepšené hlášení chyb / informace. V několika případech chybové zprávy nebyly šířeny celou cestu ven.
- Byly aktualizovány vývojové závislosti v jazyce JavaScript, aby používaly aktuální moduly.

**Opravy chyb**

- Opraveny nevracení paměti z důvodu `RecognizeAsync`neshody typů v .
- V některých případech byly výjimky unikly.
- Oprava nevracení paměti v argumentech události překladu.
- Opraven problém se zamykáním při opětovném připojení v dlouhotrvajících relacích.
- Byl opraven problém, který mohl vést k chybějícímu konečnému výsledku neúspěšných překladů.
- C#: Pokud `async` operace nebyla v hlavním vlákně očekávána, bylo možné, že nástroj pro rozpoznávání mohl být uvolněn před dokončením asynchronní úlohy.
- Java: Opraven problém, který vedl k chybě virtuálního virtuálního soudu java.
- Cíl-C: Mapování pevných výčtů; RecognizedIntent byl vrácen `RecognizingIntent`místo .
- JavaScript: Nastavte výchozí výstupní formát `SpeechConfig`na "jednoduchý" v .
- JavaScript: Odstranění nekonzistence mezi vlastnostmi objektu config v Jazyce JavaScript a dalších jazycích.

**ukázky**

- Bylo aktualizováno a opraveno několik vzorků (například výstupní hlasy pro překlad atd.).
- Byly přidány ukázky souboru Node.js v [ukázkovém úložišti](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Řeč SDK 1.1.0

**Nové funkce**

- Podpora pro Android x86/x64.
- Podpora proxy: `SpeechConfig` V objektu můžete nyní volat funkci pro nastavení informací proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce ještě není v iOS dostupná.
- Vylepšený kód chyby a zprávy. Pokud rozpoznávání vrátilo chybu, toto již bylo nastaveno `Reason` (v zrušené události) nebo `CancellationDetails` (ve výsledku rozpoznávání) na `Error`. Zrušená událost nyní obsahuje dva `ErrorCode` `ErrorDetails`další členy a . Pokud server vrátil další informace o chybě s hlášenou chybou, bude nyní k dispozici v nových členech.

**Zlepšení**

- Přidáno další ověření v konfiguraci nástroje pro rozpoznávání a další chybová zpráva.
- Vylepšené zpracování dlouhodobého ticha uprostřed zvukového souboru.
- Balíček NuGet: pro projekty rozhraní .NET Framework zabraňuje vytváření s konfigurací AnyCPU.

**Opravy chyb**

- Opraveno několik výjimek nalezených v nástrojích pro rozpoznávání. Kromě toho jsou výjimky zachyceny `Canceled` a převedeny na událost.
- Opravte nevracení paměti ve správě vlastností.
- Opravena chyba, při které mohl zvukový vstupní soubor zřítit nástroj pro rozpoznávání.
- Opravena chyba, kdy mohly být události přijaty po události zastavení relace.
- Opraveny některé časovací podmínky v závitování.
- Opraven problém s kompatibilitou iOS, který mohl způsobit selhání.
- Vylepšení stability pro podporu mikrofonu Android.
- Opravena chyba, kdy nástroj pro rozpoznávání v JavaScriptu ignoroval jazyk rozpoznávání.
- Opravena chyba, `EndpointId` která bránila nastavení (v některých případech) v JavaScriptu.
- Změnilo pořadí parametrů v AddIntent v `AddIntent` Jazyce JavaScript a přidalo chybějící podpis JavaScriptu.

**ukázky**

- Přidány ukázky C++ a C# pro použití vytahování a nabízený datový proud v [ukázkovém úložišti](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Řeč SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

- Opravena potenciální závažná chyba v důsledku sporu v likvrzování
- Opravena potenciální závažná chyba v případě odsazení vlastností.
- Přidána další kontrola chyb a parametrů.
- Cíl-C: Opravena možná závažná chyba způsobená přepsáním názvu v NSString.
- Cíl-C: Upravená viditelnost rozhraní API
- JavaScript: Opraveno, pokud jde o události a jejich datové části.
- Vylepšení dokumentace.

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)byla přidána nová ukázka javascriptu.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Kognitivní služby řeči SDK 1.0.0: 2018-září vydání

**Nové funkce**

- Podpora pro Objective-C na iOS. Podívejte se na náš [rychlý start Objective-C pro iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Podpora JavaScriptu v prohlížeči. Podívejte se na náš [javascriptový rychlý start](quickstart-js-browser.md).

**Změny způsobující chyby**

- V této verzi je zavedena řada nejnovějších změn.
  Podrobnosti na jdete na [této stránce.](https://aka.ms/csspeech/breakingchanges_1_0_0)

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Kognitivní služby řeči SDK 0.6.0: 2018-srpen vydání

**Nové funkce**

- Aplikace UPW vytvořené pomocí sady Speech SDK teď můžou projít certifikační sadou aplikací pro Windows (WACK).
  Podívejte se na [rychlý start UPW](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Podpora pro .NET Standard 2.0 na Linuxu (Ubuntu 16.04 x64).
- Experimentální: Podpora Java 8 na Windows (64-bit) a Linux (Ubuntu 16.04 x64).
  Podívejte se na [java runtime prostředí quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Funkční změna**

- Vystavit další informace o podrobnostech chyb y o chybách připojení.

**Změny způsobující chyby**

- Na Javě `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` (Android) funkce již nevyžaduje parametr cesty. Nyní je cesta automaticky rozpoznána na všech podporovaných platformách.
- Byl odebrán přístupový objekt `EndpointUrl` vlastnosti v jazyce Java a C#.

**Opravy chyb**

- V javě je nyní implementován výsledek syntézy zvuku na rozpoznávání překladů.
- Opravena chyba, která mohla způsobit neaktivní vlákna a zvýšený počet otevřených a nepoužívaných soketů.
- Opraven problém, kdy dlouhodobé rozpoznávání mohlo skončit uprostřed přenosu.
- Opravena sporová podmínka při vypnutí nástroje pro rozpoznávání.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Kognitivní služby řeči SDK 0.5.0: 2018-červenec vydání

**Nové funkce**

- Podpora platformy Android (API 23: Android 6.0 Marshmallow nebo vyšší). Podívejte se na [android rychlý start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Podpora rozhraní .NET Standard 2.0 v systému Windows. Podívejte se na [rychlý start jádra .NET](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentální: Podpora UPW ve Windows (verze 1709 nebo novější).
  - Podívejte se na [rychlý start UPW](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Poznámka: Aplikace UPW vytvořené pomocí sady Speech SDK ještě neprojdou certifikační sadou aplikací pro Windows (WACK).
- Podpora dlouhotrvajícího rozpoznávání pomocí automatického opětovného připojení.

**Funkční změny**

- `StartContinuousRecognitionAsync()`podporuje dlouhodobé rozpoznávání.
- Výsledek rozpoznávání obsahuje více polí. Jsou posunuty od začátku zvuku a doby trvání (v lístcích) rozpoznaného `InitialSilenceTimeout` `InitialBabbleTimeout`textu a dalších hodnot, které představují například stav rozpoznávání, a .
- Podpora AuthorizationToken pro vytváření instancí továrny.

**Změny způsobující chyby**

- Události rozpoznávání: `NoMatch` Typ události `Error` byl sloučen do události.
- SpeechOutputFormat v jazyce C# byl přejmenován tak, `OutputFormat` aby zůstal v souladu s jazykem C++.
- Návratový typ některých `AudioInputStream` metod rozhraní se mírně změnil:
  - V jazyce `read` Java `long` metoda `int`nyní vrátí místo .
  - V c# `Read` metoda nyní `uint` vrátí `int`místo .
  - V jazyce C++ a `size_t` metody `int` `Read` a `GetFormat` nyní vrátí místo .
- C++: Instance zvukových vstupních datových proudů `shared_ptr`lze nyní předat pouze jako .

**Opravy chyb**

- Opraveny nesprávné vrácené `RecognizeAsync()` hodnoty ve výsledku při výpadku.
- Závislost na knihovnách základů médií v systému Windows byla odebrána. Sada SDK nyní používá základní zvuková api.
- Oprava dokumentace: Byla přidána stránka [oblastí](regions.md) popisující podporované oblasti.

**Známý problém**

- Sada Speech SDK pro Android nehlásí výsledky syntézy řeči pro překlad. Tento problém bude vyřešen v další verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kognitivní služby řeči SDK 0.4.0: 2018-červnové vydání

**Funkční změny**

- AudioInputStream

  Nástroj pro rozpoznávání nyní může spotřebovat datový proud jako zdroj zvuku. Další informace naleznete v souvisejícím [návodu .](how-to-use-audio-input-streams.md)

- Podrobný výstupní formát

  Při vytváření `SpeechRecognizer`můžete požádat `Detailed` `Simple` nebo výstup formátu. Obsahuje `DetailedSpeechRecognitionResult` skóre spolehlivosti, rozpoznaný text, nezpracovaný lexikální formulář, normalizovanou formu a normalizovanou formu s maskovanými vulgárními výrazy.

**Narušující změny**

- Změněno `SpeechRecognitionResult.Text` `SpeechRecognitionResult.RecognizedText` z v c#.

**Opravy chyb**

- Opraven možný problém zpětného volání ve vrstvě USP během vypnutí.
- Pokud nástroj pro rozpoznávání spotřebovává vstupní zvukový soubor, držel se popisovače souboru déle, než bylo nutné.
- Byla odstraněna několik zablokování mezi čerpadlo zprávy a rozpoznávání.
- Vypalte `NoMatch` výsledek, když je časový čas odezvy ze služby.
- Knihovny nadace médií v systému Windows jsou načteny zpoždění. Tato knihovna je vyžadována pouze pro vstup mikrofonu.
- Rychlost nahrávání zvukových dat je omezena na přibližně dvojnásobek původní rychlosti zvuku.
- V systému Windows sestavení Jazyka C# .NET jsou nyní silné pojmenované.
- Oprava dokumentace: `Region` je nutné informace k vytvoření nástroje pro rozpoznávání.

Byly přidány další vzorky, které jsou neustále aktualizovány. Nejnovější sadu ukázek najdete v [tématu řeči SDK ukázky úložiště GitHub](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kognitivní služby řeči SDK 0.2.12733: 2018-Květen vydání

This release is the first public preview release of the Cognitive Services Speech SDK.
