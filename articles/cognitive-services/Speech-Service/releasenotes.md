---
title: Poznámky k verzi – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Podívejte se na běžící protokol vydaných verzí, vylepšení, opravy chyb a známé problémy služby Speech.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: f38e23f0648d802ba7b09391d6f6bbd0c093d989
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888172"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-sdk-180-2019-november-release"></a>Sada Speech SDK 1.8.0:2019 – listopadová verze

**Nové funkce**

- Přidali jsme rozhraní API FromHost (), které vám umožní snadnou práci s Prem kontejnery a cloudy v svrchovaném provozu.
- Přidání automatického zdrojového Rozpoznávání jazyka pro rozpoznávání řeči (v jazyce Java C++a)
- Přidání objektu SourceLanguageConfig pro rozpoznávání řeči, pomocí kterého se určí očekávané zdrojové jazyky (v jazycích C++Java a)
- Přidání podpory KeywordRecognizer do Windows (UWP), Androidu a iOS prostřednictvím balíčků NuGet a Unity
- Bylo přidáno rozhraní API vzdálené konverzace Java, které umožní přepis konverzace v asynchronních dávkách.

**Průlomové změny**

- Funkce konverzace Transcriber přesunuté do oboru názvů Microsoft. Cognitiveservices Account. Speech. přepis.
- Součást metod konverzace Transcriber je přesunuta do nové třídy konverzace.
- Vyřazena podpora pro 32-bit (ARMv7 a x86) iOS

**Opravy chyb**

- Oprava pro chybu, pokud se používá místní KeywordRecognizer bez platného klíče předplatného služby Speech Service

**Ukázky**

- Ukázka Xamarin pro KeywordRecognizer
- Ukázka Unity pro KeywordRecognizer
- C++a ukázky Java pro automatické zdrojové Rozpoznávání jazyka.

## <a name="speech-sdk-170-2019-september-release"></a>Sada Speech SDK 1.7.0:2019 – září verze

**Nové funkce**

- Přidání podpory beta verze pro Xamarin on Univerzální platforma Windows (UWP), Android a iOS
- Přidala se podpora iOS pro Unity.
- Přidání podpory komprimovaných vstupů pro ALaw, mulaw, FLAC v Androidu, iOS a Linux
- Přidání SendMessageAsync do třídy Connection pro odeslání zprávy službě
- Přidání SetMessageProperty do třídy Connection pro nastavení vlastnosti zprávy
- TTS – přidané vazby pro Java (JRE a Android), Python, SWIFT a cíl-C
- TTS přidal podporu přehrávání pro macOS, iOS a Android.
- Přidání informací o hranici slova pro TTS

**Opravy chyb**

- Problém s opravenou IL2CPP sestavení v Unity 2019 pro Android
- Opravený problém se nesprávně zpracovávanými nepoškozenými záhlavími ve vstupním souboru WAV
- Opravený problém s identifikátory UUID není jedinečný v některých vlastnostech připojení.
- Opravili jsme několik upozornění na specifikátory hodnoty null v vazbách SWIFT (může vyžadovat malé změny kódu).
- Opravili jsme chybu, která způsobila, že připojení protokolu WebSocket se v zatížení sítě nekorektně zavřou.
- Opravili jsme problém v Androidu, který někdy má za následek duplicitní ID dojmu, které používá DialogServiceConnector.
- Vylepšení stability připojení v rámci vícenásobných interakcí a hlášení selhání (prostřednictvím zrušených událostí), když k nim dojde v DialogServiceConnector
- DialogServiceConnector relace začne nyní správně poskytovat události, včetně při volání ListenOnceAsync () během aktivního StartKeywordRecognitionAsync ().
- Vyřešená havárie přidružená k přijatým aktivitám DialogServiceConnector

**Ukázky**

- Rychlý Start pro Xamarin
- Aktualizovaný rychlý Start pro CPP s ARM64 informacemi pro Linux
- Aktualizovaný rychlý Start Unity s informacemi pro iOS

## <a name="speech-sdk-160-2019-june-release"></a>Sada Speech SDK 1.6.0:2019 – červenou verzi

**Ukázky**

- Ukázky rychlý Start pro převod textu na řeč pro UWP a Unity
- Ukázka rychlého startu pro SWIFT v iOS
- Ukázky Unity pro rozpoznávání řeči & Rozpoznávání záměru a překlad
- Aktualizované ukázky rychlý Start pro DialogServiceConnector

**Vylepšení/změny**

- Obor názvů dialogového okna:
  - SpeechBotConnector byl přejmenován na DialogServiceConnector
  - BotConfig byl přejmenován na DialogServiceConfig
  - BotConfig:: FromChannelSecret () byl přemapován na DialogServiceConfig:: FromBotSecret ()
  - Po přejmenování budou dál podporovány všechny existující klienty s přímým ovládáním řeči.
- Aktualizace adaptéru pro převod textu na řeč pro podporu proxy a trvalého připojení
- Vylepšit chybovou zprávu, když je předána neplatná oblast
- SWIFT/objektiv-C:
  - Vylepšené zasílání zpráv o chybách: metody, které mohou mít za následek chybu, jsou nyní k dispozici ve dvou verzích: jeden, který zpřístupňuje `NSError` objekt pro zpracování chyb a druhý, který vyvolává výjimku. Bývalé jsou zpřístupněny SWIFT. Tato změna vyžaduje úpravu stávajícího kódu SWIFT.
  - Vylepšené zpracování událostí

**Opravy chyb**

- Oprava pro TTS: kde se SpeakTextAsync budoucí vrácení bez čekání, dokud se nedokončí vykreslování zvuku
- Oprava pro zařazovací řetězce v C# pro povolení úplné jazykové podpory
- Oprava problému aplikace .NET Core pro načtení základní knihovny s cílovou architekturou net461 v ukázkách
- Oprava pro příležitostné problémy s nasazováním nativních knihoven do výstupní složky v ukázkách
- Oprava pro spolehlivou zavírání webového soketu
- Oprava pro možnou chybu při otevírání připojení v rámci velmi velkého zatížení systému Linux
- Oprava chybějících metadat v sadě rozhraní pro macOS
- Oprava problémů s `pip install --user` ve Windows

## <a name="speech-sdk-151"></a>Sada Speech SDK 1.5.1

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Opravy chyb**

- Opravte FromSubscription při použití s přepisem konverzace.
- Opravte chybu v klíčovém slovu hledání pro hlasové asistenty.

## <a name="speech-sdk-150-2019-may-release"></a>Sada Speech SDK 1.5.0:2019 – může vydávat

**Nové funkce**

- Klíčové slovo hledání (KWS) je teď k dispozici pro Windows a Linux. Funkce KWS můžou pracovat s jakýmkoli typem mikrofonu. oficiální podpora KWS se ale v současné době omezila na pole mikrofonu, která najdete v sadě Azure Kinect DK hardware nebo v sadě Speech SDK (Speech Devices SDK).
- Funkce pomocných parametrů fráze je dostupná prostřednictvím sady SDK. Další informace najdete [tady](how-to-phrase-lists.md).
- Funkce přepisu konverzace je k dispozici prostřednictvím sady SDK. Podívejte se [sem](conversation-transcription-service.md).
- Přidejte podporu pro hlasové asistenty pomocí kanálu Direct line Speech.

**Ukázky**

- Byly přidány ukázky pro nové funkce nebo nové služby, které sada SDK podporuje.

**Vylepšení/změny**

- Přidání různých vlastností nástroje pro rozpoznávání pro úpravu chování služby nebo výsledků služby (například vulgárních výrazů a dalších).
- Nástroj pro rozpoznávání teď můžete nakonfigurovat pomocí standardních vlastností konfigurace, a to i v případě, že jste vytvořili Nástroj pro rozpoznávání `FromEndpoint`.
- Cíl-C: `OutputFormat` vlastnost byla přidána do SPXSpeechConfiguration.
- Sada SDK teď podporuje Debian 9 jako distribuci Linux.

**Opravy chyb**

- Opravili jsme problém, kdy byl prostředek mluvčíe ve fázi převodu textu na řeč příliš nestrukturované.

## <a name="speech-sdk-142"></a>Sada Speech SDK 1.4.2

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

## <a name="speech-sdk-141"></a>Sada Speech SDK 1.4.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

- Zabrání webovému balíčku načíst agenta HTTPS-proxy-.

## <a name="speech-sdk-140-2019-april-release"></a>Sada Speech SDK 1.4.0:2019 – duben

**Nové funkce**

- Sada SDK teď podporuje službu převodu textu na řeč jako beta verzi. Podporuje se v systémech Windows a Linux Desktop z C++ a C#. Další informace najdete v [přehledu převod textu na řeč](text-to-speech.md#get-started-with-text-to-speech).
- Sada SDK teď podporuje zvukové soubory MP3 a Opus/OGG jako vstupní soubory streamu. Tato funkce je dostupná jenom pro Linux z C++ a C# a je momentálně ve verzi beta (další podrobnosti najdete [tady](how-to-use-codec-compressed-audio-input-streams.md)).
- Sada Speech SDK pro jazyky Java, .NET Core C++ a objektivní-C získala podporu MacOS. Podpora pro macOS je v současnosti ve verzi beta.
- iOS: sada Speech SDK pro iOS (cíl-C) je teď publikovaná taky jako CocoaPod.
- JavaScript: podpora pro jiný než výchozí mikrofon jako vstupní zařízení.
- JavaScript: proxy podpora pro Node. js.

**Ukázky**

- Byly přidány ukázky pro použití sady Speech C++ SDK s cíli-C v MacOS.
- Byly přidány ukázky demonstrující použití služby převodu textu na řeč.

**Vylepšení/změny**

- Python: další vlastnosti výsledků rozpoznávání jsou nyní zpřístupněny prostřednictvím vlastnosti `properties`.
- Pro další podporu vývoje a ladění můžete přesměrovat informace o protokolování a diagnostice sady SDK do souboru protokolu (další podrobnosti najdete [tady](how-to-use-logging.md)).
- JavaScript: zlepšení výkonu zpracování zvuku.

**Opravy chyb**

- Mac/iOS: Chyba, která vedla k dlouhému čekání v případě, že nebylo možné navázat spojení se službou Speech.
- Python: zlepšení zpracování chyb pro argumenty v zpětných voláních Pythonu.
- JavaScript: Opraveno chybné vytváření sestav stavu pro rozpoznávání řeči skončilo v RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Sada Speech SDK 1.3.1:2019 – aktualizace z února

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Oprava chyby**

- Při použití vstupu mikrofonu byla vyřešena nevracení paměti. Na základě datového proudu nebo vstupu souboru není ovlivněno.

## <a name="speech-sdk-130-2019-february-release"></a>Sada Speech SDK 1.3.0:2019 – Únorová verze

**Nové funkce**

- Sada Speech SDK podporuje výběr vstupního mikrofonu přes třídu AudioConfig. To umožňuje streamovat zvuková data do služeb řeči z jiného než výchozího mikrofonu. Další informace najdete v dokumentaci popisující [Výběr zvukového vstupního zařízení](how-to-select-audio-input-devices.md). Tato funkce není ještě dostupná z JavaScriptu.
- Sada Speech SDK teď ve verzi beta verze podporuje Unity. Poskytněte zpětnou vazbu v části problém v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity v systémech Windows x86 a x64 (desktopové nebo Univerzální platforma Windows aplikace) a Androidu (ARM32/64, x86). Další informace najdete v našem [rychlém](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)startu pro Unity.
- Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodán v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná do základní sady SDK.

**Ukázky**

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)je k dispozici následující nový obsah:

- Další ukázky pro AudioConfig. FromMicrophoneInput.
- Další ukázky Pythonu pro rozpoznávání a překlad záměrů.
- Další ukázky pro použití objektu připojení v iOS
- Další ukázky Java pro překlad pomocí zvukového výstupu.
- Nová ukázka použití [dávkového přepisu REST API](batch-transcription.md).

**Vylepšení/změny**

- Python
  - Vylepšené ověřování parametrů a chybové zprávy v SpeechConfig.
  - Přidejte podporu pro objekt připojení.
  - Podpora 32-bitové verze Pythonu (x86) ve Windows.
  - Sada Speech SDK for Python je mimo verzi beta.
- iOS
  - Sada SDK je teď založená na sadě iOS SDK verze 12,1.
  - Sada SDK teď podporuje iOS verze 9,2 a novější.
  - Vylepšete referenční dokumentaci a opravte několik názvů vlastností.
- JavaScript
  - Přidejte podporu pro objekt připojení.
  - Přidat soubory definice typu pro seskupené JavaScripty
  - Počáteční podpora a implementace pro pomocný parametr fráze
  - Návratová kolekce vlastností s JSON služby pro rozpoznávání
- Knihovny DLL systému Windows teď obsahují prostředek verze.
- Pokud vytvoříte Nástroj pro rozpoznávání `FromEndpoint` můžete přidat parametry přímo do adresy URL koncového bodu. Pomocí `FromEndpoint` nemůžete konfigurovat nástroj pro rozpoznávání pomocí standardních vlastností konfigurace.

**Opravy chyb**

- Prázdné uživatelské jméno proxy serveru a heslo proxy serveru nebyly zpracovány správně. Pokud v této verzi nastavíte uživatelské jméno a heslo proxy serveru na prázdný řetězec, nebudou odeslány při připojování k proxy serveru.
- Identifikátor SessionId vytvořený sadou SDK pro některé jazyky&nbsp;nebo prostředí vždy nenáhodný. K vyřešení tohoto problému se přidala inicializace náhodného generátoru.
- Vylepšete zpracování autorizačního tokenu. Pokud chcete použít autorizační token, zadejte v SpeechConfig a nechte klíč předplatného prázdný. Pak vytvořte Nástroj pro rozpoznávání obvyklým způsobem.
- V některých případech se objekt připojení neuvolnil správně. Tento problém byl opraven.
- Ukázka JavaScriptu byla opravena tak, aby podporovala zvukový výstup pro syntézu překladu i v Safari.

## <a name="speech-sdk-121"></a>Sada Speech SDK 1.2.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

- Požárový konec streamu. konec, ne na řeč. end.
- Oprava chyby v zvukové pumpě, která neplánovala další odeslání, pokud se aktuální odeslání nepovedlo.
- Opravte průběžné rozpoznávání pomocí ověřovacího tokenu.
- Oprava chyb pro různé funkce pro rozpoznávání nebo koncové body.
- Vylepšení dokumentace.

## <a name="speech-sdk-120-2018-december-release"></a>Sada Speech SDK 1.2.0:2018 – prosinec verze

**Nové funkce**

- Python
  - V této verzi je k dispozici beta verze podpory Pythonu (3,5 a novější). Další informace naleznete zde] (rychlý Start – python.md).
- JavaScript
  - Sada Speech SDK pro JavaScript je open source. Zdrojový kód je k dispozici na [GitHubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Nyní podporujeme Node. js. Další informace najdete [tady](quickstart-js-node.md).
  - Omezení délky pro zvukové relace bylo odebráno, k automatickému připojení dojde v rámci pokrytí.
- Objekt připojení
  - Z nástroje pro rozpoznávání můžete získat přístup k objektu připojení. Tento objekt vám umožňuje explicitně iniciovat připojení služby a přihlásit se k odběru událostí a odpojovat se.
    (Tato funkce ještě není dostupná z JavaScriptu a Pythonu.)
- Podpora pro Ubuntu 18,04.
- Android
  - Povolená podpora ProGuard během generování APK

**Vylepšen**

- Vylepšení využití interního vlákna, což snižuje počet vláken, zámků a mutexů.
- Vylepšené zasílání zpráv o chybách/informace. V několika případech se chybové zprávy nerozšířily tak, jak jsou.
- Aktualizované závislosti vývoje v JavaScriptu pro použití aktuálnosti modulů.

**Opravy chyb**

- Pevná nevracení paměti kvůli neshodě typu v RecognizeAsync.
- V některých případech došlo k úniku výjimek.
- Oprava nevrácené paměti v argumentech události překladu.
- Opravili jsme problém se zámkem při opětovném připojení v dlouho spuštěných relacích.
- Opravili jsme problém, který by mohl vést k chybějícímu konečnému výsledku pro neúspěšné překlady.
- C#: Pokud v hlavním vlákně nebylo očekávána asynchronní operace, bylo možné vyřadit Nástroj pro rozpoznávání před dokončením asynchronní úlohy.
- Java: Opravili jsme problém, což vedlo k chybě virtuálního počítače Java.
- Cíl-C: pevné mapování výčtu; Místo RecognizingIntent se vrátilo RecognizedIntent.
- JavaScript: v SpeechConfig nastavte výchozí výstupní formát na ' jednoduchý '.
- JavaScript: odebrání nekonzistence mezi vlastnostmi objektu konfigurace v jazyce JavaScript a dalších jazycích.

**Ukázky**

- Aktualizovali a opravili jsme několik ukázek (například výstupní hlasy pro překlad atd.).
- Do [ukázkového úložiště](https://aka.ms/csspeech/samples)se přidaly ukázky pro Node. js.

## <a name="speech-sdk-110"></a>Sada Speech SDK 1.1.0

**Nové funkce**

- Podpora pro Android x86/x64.
- Podpora proxy serveru: v objektu SpeechConfig nyní můžete zavolat funkci pro nastavení informací o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není ještě k dispozici v iOS.
- Vylepšený kód chyby a zprávy. Pokud rozpoznávání vrátilo chybu, nastavilo se již `Reason` (ve zrušené události) nebo `CancellationDetails` (ve výsledku rozpoznávání) na `Error`. Zrušená událost teď obsahuje dva další členy `ErrorCode` a `ErrorDetails`. Pokud server vrátil Další informace o chybě s nahlášenou chybou, bude nyní k dispozici v nových členech.

**Vylepšen**

- Do konfigurace nástroje pro rozpoznávání se přidalo další ověření a přidala se další chybová zpráva.
- Vylepšené zpracování dlouhodobě tichého podoby uprostřed zvukového souboru.
- Balíček NuGet: u .NET Frameworkch projektů brání sestavování s konfigurací AnyCPU.

**Opravy chyb**

- V nástroji pro rozpoznávání se našla několik výjimek. Kromě toho jsou výjimky zachyceny a převedeny na zrušené události.
- Opravte nevrácení paměti ve správě vlastností.
- Opravili jsme chybu, ve které by zvukový vstupní soubor mohl způsobit selhání nástroje pro rozpoznávání.
- Opravili jsme chybu, kdy se události dostaly po události ukončení relace.
- Opravili jsme některé konflikty časování ve vláknování.
- Opravili jsme problém s kompatibilitou iOS, který by mohl způsobit chybu.
- Vylepšení stability pro podporu mikrofonu Androidu.
- Opravili jsme chybu, kdy by nástroj pro rozpoznávání v JavaScriptu ignoroval jazyk rozpoznávání.
- Opravili jsme chybu, která znemožňuje nastavení EndpointId (v některých případech) v JavaScriptu.
- Změnili jsme pořadí parametrů v AddIntent v JavaScriptu a Přidali jste chybějící podpis JavaScriptu AddIntent.

**Ukázky**

- Přidání C++ a C# vzorkování pro použití pro vyžádání obsahu a nabízených oznámení v [ukázkovém úložišti](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Sada Speech SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

- Opravená potenciální závažná chyba kvůli konfliktu časování v nástroji pro rozpoznávání disposing
- Opravená potenciální závažná chyba pro případ zrušení vlastností
- Přidala se další chyba a kontrola parametrů.
- Cíl-C: Opravili jsme možnou závažnou chybu způsobenou přepsáním názvu v NSString.
- Cíl-C: upravená viditelnost rozhraní API
- JavaScript: opravené související události a jejich datové části.
- Vylepšení dokumentace.

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)se přidala Nová ukázka pro JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – září verze

**Nové funkce**

- Podpora pro cíl-C v iOS. Podívejte se na náš rychlý úvodní úkol [v jazyce C pro iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Podpora JavaScriptu v prohlížeči Podívejte se na náš [rychlý úvodní JavaScript](quickstart-js-browser.md).

**Průlomové změny**

- V této verzi se zavádí určitý počet nezměněných změn.
  Podrobnosti najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – srpen Release

**Nové funkce**

- Aplikace pro UWP vytvořené pomocí sady Speech SDK teď můžou předat sadu Windows App Certification Kit (WACK).
  Podívejte se na [rychlé zprovoznění UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Podpora pro .NET Standard 2,0 na platformě Linux (Ubuntu 16,04 x64).
- Experimentální: podporují Java 8 v systému Windows (64 bitů) a Linux (Ubuntu 16,04 x64).
  Podívejte se na [rychlý úvodní Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Změna funkčnosti**

- Vystavení dalších podrobností o chybách při chybě připojení

**Průlomové změny**

- V jazyce Java (Android) již funkce `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` nevyžaduje parametr cesty. Tato cesta se teď automaticky detekuje na všech podporovaných platformách.
- Přístupový objekt get vlastnosti `EndpointUrl` v jazyce Java a C# byl odebrán.

**Opravy chyb**

- V jazyce Java je nyní implementována výsledná syntéza zvuku na nástroji pro rozpoznávání překladu.
- Opravili jsme chybu, která by mohla způsobit neaktivní vlákna a zvýšený počet otevřených a nevyužitých soketů.
- Opravili jsme problém, kdy dlouhodobě běžící rozpoznávání může končit uprostřed přenosu.
- Opravili jste konflikt časování při vypnutí nástroje pro rozpoznávání.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – červenec verze

**Nové funkce**

- Podpora platformy Android (rozhraní API 23: Android 6,0 Marshmallow nebo novější). Podívejte se na [rychlý Start pro Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Podpora .NET Standard 2,0 ve Windows. Podívejte se na [rychlé zprovoznění .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentální: podporují UWP ve Windows (verze 1709 nebo novější).
  - Podívejte se na [rychlé zprovoznění UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Poznámka: aplikace pro UWP vytvořené pomocí sady Speech SDK ještě nepředá certifikační sadu aplikací pro Windows (WACK).
- Podpora dlouhotrvajícího rozpoznávání při automatickém opětovném připojení.

**Funkční změny**

- `StartContinuousRecognitionAsync()` podporuje dlouhodobě běžící rozpoznávání.
- Výsledek rozpoznávání obsahuje více polí. Jsou posunuty od zahájení zvuku a doby trvání (v tiků) rozpoznaného textu a dalších hodnot, které představují stav rozpoznávání, například `InitialSilenceTimeout` a `InitialBabbleTimeout`.
- Podporuje AuthorizationToken pro vytváření instancí továrny.

**Průlomové změny**

- Události rozpoznávání: typ události neodpovídá typu se sloučil do chybové události.
- SpeechOutputFormat v C# byla přejmenována na OutputFormat, aby C++zůstala v souladu s.
- Návratový typ některých metod `AudioInputStream` rozhraní se mírně změnil:
  - V jazyce Java nyní metoda `read` vrátí `long` namísto `int`.
  - V C#rozhraní `Read` metoda nyní vrací `uint` namísto `int`.
  - V C++nástroji `Read` a `GetFormat` metody nyní vrací `size_t` místo `int`.
- C++: Instance zvukových vstupních streamů se teď dají předávat jenom jako `shared_ptr`.

**Opravy chyb**

- Opravily se nesprávné návratové hodnoty v důsledku `RecognizeAsync()` vypršení časového limitu.
- Došlo k odebrání závislosti v knihovnách Media Foundation v systému Windows. SDK teď používá základní zvuková rozhraní API.
- Oprava dokumentace: Přidání stránky [oblastí](regions.md) pro popis podporovaných oblastí.

**Známý problém**

- Sada Speech SDK pro Android nehlásí výsledky Shrnutí řeči pro překlad. Tento problém bude vyřešen v příští verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – červená verze

**Funkční změny**

- AudioInputStream

  Nástroj pro rozpoznávání teď může jako zdroj zvuku spotřebovat datový proud. Další informace najdete v části související [Průvodce](how-to-use-audio-input-streams.md).

- Podrobný výstupní formát

  Když vytváříte `SpeechRecognizer`, můžete požádat o výstupní formát `Detailed` nebo `Simple`. `DetailedSpeechRecognitionResult` obsahuje hodnocení spolehlivosti, rozpoznaný text, nezpracovaný lexikální tvar, normalizovanou formu a normalizovanou formu s maskovánými vulgárními výrazy.

**Zásadní změna**

- Změna na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` v C#.

**Opravy chyb**

- Opravili jsme možný problém zpětného volání ve vrstvě USP během vypínání.

- Pokud nástroj pro rozpoznávání použil zvukový vstupní soubor, byl uložený na popisovači souboru déle, než je nutné.

- Bylo odebráno několik zablokování mezi čerpadlem zpráv a modulem pro rozpoznávání.

- Spustí `NoMatch` výsledek, když vypršel časový limit odpovědi na službu.

- Knihovny Media Foundation v systému Windows jsou načteny opožděně. Tato knihovna se vyžaduje jenom pro vstup přes mikrofon.

- Rychlost nahrávání zvukových dat je omezená na přibližně dvojnásobek původní rychlosti zvuku.

- Ve Windows jsou C# nyní sestavení .NET silně pojmenována.

- Oprava dokumentace: `Region` jsou požadovány informace k vytvoření nástroje pro rozpoznávání.

Přidali jsme další ukázky a neustále se aktualizují. Nejnovější sadu ukázek najdete v části [sada Speech SDK Samples v úložišti GitHub](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018-může vydávat

Tato verze je první verze Public Preview sady Cognitive Services Speech SDK.
