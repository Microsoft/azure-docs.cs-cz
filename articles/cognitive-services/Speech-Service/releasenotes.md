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
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 12eecc4998153cbeedeb907ecad33c56141a50e6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559130"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-sdk-160-2019-june-release"></a>1\.6.0 sady Speech SDK: 2019. června – verze

**Ukázky**
*   Ukázky rychlý Start pro převod textu na řeč pro UWP a Unity
*   Ukázka rychlého startu pro SWIFT v iOS
*   Ukázky Unity pro rozpoznávání řeči & Rozpoznávání záměru a překlad
*   Aktualizované ukázky rychlý Start pro DialogServiceConnector

**Vylepšení/změny**
* Obor názvů dialogového okna:
    * SpeechBotConnector byl přejmenován na DialogServiceConnector
    * BotConfig byl přejmenován na DialogServiceConfig
    * BotConfig:: FromChannelSecret () byl přemapován na DialogServiceConfig:: FromBotSecret ()
    * Po přejmenování budou dál podporovány všechny existující klienty s přímým ovládáním řeči.
* Aktualizace adaptéru pro převod textu na řeč pro podporu proxy a trvalého připojení
* Vylepšit chybovou zprávu, když je předána neplatná oblast
* SWIFT/objektiv-C:
    * Vylepšené zasílání zpráv o chybách: Metody, které mohou mít za následek chybu, jsou nyní k dispozici ve dvou verzích: Ten, který zpřístupňuje `NSError` objekt pro zpracování chyb a druhý, který vyvolá výjimku. Bývalé jsou zpřístupněny SWIFT. Tato změna vyžaduje úpravu stávajícího kódu SWIFT.
    * Vylepšené zpracování událostí

**Opravy chyb**
*   Oprava pro TTS: kde se SpeakTextAsync budoucí vrácení bez čekání, dokud se nedokončí vykreslování zvuku
*   Oprava pro zařazovací řetězce v C# pro povolení úplné jazykové podpory
*   Oprava problému aplikace .NET Core pro načtení základní knihovny s cílovou architekturou net461 v ukázkách
*   Oprava pro příležitostné problémy s nasazováním nativních knihoven do výstupní složky v ukázkách
*   Oprava pro spolehlivou zavírání webového soketu
*   Oprava pro možnou chybu při otevírání připojení v rámci velmi velkého zatížení systému Linux
*   Oprava chybějících metadat v sadě rozhraní pro macOS
*   Oprava pro problémy s `pip install --user` Windows


## <a name="speech-sdk-151"></a>Sada Speech SDK 1.5.1

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Opravy chyb**

* Opravte FromSubscription při použití s přepisem konverzace.
* Opravte chybu v klíčovém slovu hledání pro hlasového prvního virtuálního pomocníka.


## <a name="speech-sdk-150-2019-may-release"></a>1\.5.0 sady Speech SDK: 2019 – může vydávat

**Nové funkce**

* Funkce Wake Word (klíčové slovo hledání/KWS) je teď k dispozici pro Windows a Linux. Funkce KWS můžou pracovat s jakýmkoli typem mikrofonu. oficiální podpora KWS se ale v současné době omezila na pole mikrofonu, která najdete v sadě Azure Kinect DK hardware nebo v sadě Speech SDK (Speech Devices SDK).
* Funkce pomocných parametrů fráze je dostupná prostřednictvím sady SDK. Další informace najdete [tady](how-to-phrase-lists.md).
* Funkce přepisu konverzace je k dispozici prostřednictvím sady SDK. Podívejte se [sem](conversation-transcription-service.md).
* Přidání podpory pro virtuální asistenty hlasových kanálů pomocí přímého kanálu pro rozpoznávání řádků.

**Ukázky**

* Byly přidány ukázky pro nové funkce nebo nové služby, které sada SDK podporuje.

**Vylepšení/změny**

* Přidání různých vlastností nástroje pro rozpoznávání pro úpravu chování služby nebo výsledků služby (například vulgárních výrazů a dalších).
* Nástroj pro rozpoznávání teď můžete nakonfigurovat pomocí standardních vlastností konfigurace, a to i v případě, že jste vytvořili `FromEndpoint`Nástroj pro rozpoznávání.
* Cíl-C: `OutputFormat` vlastnost byla přidána do SPXSpeechConfiguration.
* Sada SDK teď podporuje Debian 9 jako distribuci Linux.

**Opravy chyb**

* Opravili jsme problém, kdy byl prostředek mluvčíe ve fázi převodu textu na řeč příliš nestrukturované.
## <a name="speech-sdk-142"></a>Sada Speech SDK 1.4.2

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

## <a name="speech-sdk-141"></a>Sada Speech SDK 1.4.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

* Zabrání webovému balíčku načíst agenta HTTPS-proxy-.

## <a name="speech-sdk-140-2019-april-release"></a>1\.4.0 sady Speech SDK: 2019. dubna

**Nové funkce** 

* Sada SDK teď podporuje službu převodu textu na řeč jako beta verzi. Podporuje se v systémech Windows a Linux Desktop z C++ a C#. Další informace najdete v [přehledu převod textu na řeč](text-to-speech.md#get-started-with-text-to-speech).
* Sada SDK teď podporuje zvukové soubory MP3 a Opus/OGG jako vstupní soubory streamu. Tato funkce je dostupná jenom pro Linux z C++ a C# a je momentálně ve verzi beta (další podrobnosti najdete [tady](how-to-use-codec-compressed-audio-input-streams.md)).
* Sada Speech SDK pro jazyky Java, .NET Core C++ a objektivní-C získala podporu MacOS. Podpora pro macOS je v současnosti ve verzi beta.
* iOS: Sada Speech SDK pro iOS (cíl-C) je teď publikovaná taky jako CocoaPod.
* JavaScript: Podpora pro jiné než výchozí mikrofony jako vstupní zařízení.
* JavaScript: Podpora proxy serveru pro Node. js.

**Ukázky**

* Byly přidány ukázky pro použití sady Speech C++ SDK s cíli-C v MacOS.
* Byly přidány ukázky demonstrující použití služby převodu textu na řeč.

**Vylepšení/změny**

* Python: Další vlastnosti výsledků rozpoznávání jsou nyní zpřístupněny prostřednictvím `properties` vlastnosti.
* Pro další podporu vývoje a ladění můžete přesměrovat informace o protokolování a diagnostice sady SDK do souboru protokolu (další podrobnosti najdete [tady](how-to-use-logging.md)).
* JavaScript: Zvyšte výkon zpracování zvuku.

**Opravy chyb**

* Mac/iOS: Došlo k chybě, která vedla k dlouhému čekání v případě, že nebylo možné navázat spojení se službou Speech.
* Python: zlepšení zpracování chyb pro argumenty v zpětných voláních Pythonu.
* JavaScript: Opravené chybné vytváření sestav stavu pro rozpoznávání řeči skončilo v RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Sada Speech SDK 1.3.1: 2019. února – aktualizace

Toto je vydání opravy chyby a ovlivňuje pouze nativní/spravované sady SDK. Nemá vliv na verzi JavaScriptu sady SDK.

**Oprava chyby**

* Při použití vstupu mikrofonu byla vyřešena nevracení paměti. Na základě datového proudu nebo vstupu souboru není ovlivněno.

## <a name="speech-sdk-130-2019-february-release"></a>1\.3.0 sady Speech SDK: 2019. února – vydání

**Nové funkce**

* Sada Speech SDK podporuje výběr vstupního mikrofonu přes třídu AudioConfig. To umožňuje streamovat zvuková data do služeb řeči z jiného než výchozího mikrofonu. Další informace najdete v dokumentaci popisující [Výběr zvukového vstupního zařízení](how-to-select-audio-input-devices.md). Tato funkce není ještě dostupná z JavaScriptu.
* Sada Speech SDK teď ve verzi beta verze podporuje Unity. Poskytněte zpětnou vazbu v části problém v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity v systémech Windows x86 a x64 (desktopové nebo Univerzální platforma Windows aplikace) a Androidu (ARM32/64, x86). Další informace najdete v našem rychlém startu pro [Unity](quickstart-csharp-unity.md).
* Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodán v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná do základní sady SDK.


**Ukázky**

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)je k dispozici následující nový obsah:

* Další ukázky pro AudioConfig. FromMicrophoneInput.
* Další ukázky Pythonu pro rozpoznávání a překlad záměrů.
* Další ukázky pro použití objektu připojení v iOS
* Další ukázky Java pro překlad pomocí zvukového výstupu.
* Nová ukázka použití [dávkového přepisu REST API](batch-transcription.md).

**Vylepšení/změny**

* Python
  * Vylepšené ověřování parametrů a chybové zprávy v SpeechConfig.
  * Přidejte podporu pro objekt připojení.
  * Podpora 32-bitové verze Pythonu (x86) ve Windows.
  * Sada Speech SDK for Python je mimo verzi beta.
* iOS
  * Sada SDK je teď založená na sadě iOS SDK verze 12,1.
  * Sada SDK teď podporuje iOS verze 9,2 a novější.
  * Vylepšete referenční dokumentaci a opravte několik názvů vlastností.
* JavaScript
  * Přidejte podporu pro objekt připojení.
  * Přidat soubory definice typu pro seskupené JavaScripty
  * Počáteční podpora a implementace pro pomocný parametr fráze
  * Návratová kolekce vlastností s JSON služby pro rozpoznávání
* Knihovny DLL systému Windows teď obsahují prostředek verze.
* Pokud vytvoříte Nástroj pro rozpoznávání `FromEndpoint` , můžete přidat parametry přímo k adrese URL koncového bodu. Pomocí `FromEndpoint` standardních vlastností konfigurace nemůžete konfigurovat nástroj pro rozpoznávání.

**Opravy chyb**

* Prázdné uživatelské jméno proxy serveru a heslo proxy serveru nebyly zpracovány správně. Pokud v této verzi nastavíte uživatelské jméno a heslo proxy serveru na prázdný řetězec, nebudou odeslány při připojování k proxy serveru.
* Identifikátor SessionID, který sada SDK vytvořil, nebyl vždycky pro některé jazyky&nbsp;nebo prostředí vždy náhodný. K vyřešení tohoto problému se přidala inicializace náhodného generátoru.
* Vylepšete zpracování autorizačního tokenu. Pokud chcete použít autorizační token, zadejte v SpeechConfig a nechte klíč předplatného prázdný. Pak vytvořte Nástroj pro rozpoznávání obvyklým způsobem.
* V některých případech se objekt připojení neuvolnil správně. Tento problém byl vyřešen.
* Ukázka JavaScriptu byla opravena tak, aby podporovala zvukový výstup pro syntézu překladu i v Safari.

## <a name="speech-sdk-121"></a>Sada Speech SDK 1.2.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

* Požárový konec streamu. konec, ne na řeč. end.
* Oprava chyby v zvukové pumpě, která neplánovala další odeslání, pokud se aktuální odeslání nepovedlo.
* Opravte průběžné rozpoznávání pomocí ověřovacího tokenu.
* Oprava chyb pro různé funkce pro rozpoznávání nebo koncové body.
* Dokumentace k vylepšení.

## <a name="speech-sdk-120-2018-december-release"></a>1\.2.0 sady Speech SDK: verze 2018 – prosinec

**Nové funkce**

* Python
  * V této verzi je k dispozici beta verze podpory Pythonu (3,5 a novější). Další informace naleznete zde] (rychlý Start – python.md).
* JavaScript
  * Sada Speech SDK pro JavaScript je open source. Zdrojový kód je k dispozici [](https://github.com/Microsoft/cognitive-services-speech-sdk-js)na GitHubu.
  * Nyní podporujeme Node. js. Další informace najdete [tady](quickstart-js-node.md).
  * Omezení délky pro zvukové relace bylo odebráno, k automatickému připojení dojde v rámci pokrytí.
* Objekt připojení
  * Z nástroje pro rozpoznávání můžete získat přístup k objektu připojení. Tento objekt vám umožňuje explicitně iniciovat připojení služby a přihlásit se k odběru událostí a odpojovat se.
    (Tato funkce ještě není dostupná z JavaScriptu a Pythonu.)
* Podpora pro Ubuntu 18,04.
* Android
  * Povolená podpora ProGuard během generování APK

**Vylepšení**

* Vylepšení využití interního vlákna, což snižuje počet vláken, zámků a mutexů.
* Vylepšené zasílání zpráv o chybách/informace. V několika případech se chybové zprávy nerozšířily tak, jak jsou.
* Aktualizované závislosti vývoje v JavaScriptu pro použití aktuálnosti modulů.

**Opravy chyb**

* Pevná nevracení paměti kvůli neshodě typu v RecognizeAsync.
* V některých případech došlo k úniku výjimek.
* Oprava nevrácené paměti v argumentech události překladu.
* Opravili jsme problém se zámkem při opětovném připojení v dlouho spuštěných relacích.
* Opravili jsme problém, který by mohl vést k chybějícímu konečnému výsledku pro neúspěšné překlady.
* C#: Pokud v hlavním vlákně nečekala asynchronní operace, bylo by bylo možné odstranit Nástroj pro rozpoznávání před dokončením asynchronní úlohy.
* Java: Opravili jsme problém, což vedlo k chybě virtuálního počítače Java.
* Cíl-C: Pevné mapování výčtu; Místo RecognizingIntent se vrátilo RecognizedIntent.
* JavaScript: Nastavte výchozí výstupní formát na "jednoduchý" v SpeechConfig.
* JavaScript: Odebrání nekonzistence mezi vlastnostmi objektu konfigurace v jazyce JavaScript a dalších jazycích.

**Ukázky**

* Aktualizovali a opravili jsme několik ukázek (například výstupní hlasy pro překlad atd.).
* Do [ukázkového úložiště](https://aka.ms/csspeech/samples)se přidaly ukázky pro Node. js.

## <a name="speech-sdk-110"></a>Sada Speech SDK 1.1.0

**Nové funkce**

* Podpora pro Android x86/x64.
* Podpora proxy serveru: V objektu SpeechConfig nyní můžete zavolat funkci pro nastavení informací o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není k dispozici v systému iOS.
* Vylepšené chybové kód a zprávy. Pokud rozlišení vrátil chybu, to již nastaven `Reason` (v případě zrušené) nebo `CancellationDetails` (v výsledek rozpoznání) k `Error`. Zrušené událost teď obsahuje dva další členy, `ErrorCode` a `ErrorDetails`. Pokud server vrátil Další informace o chybě s oznámenou chybu, teď bude k dispozici v nové členy.

**Vylepšení**

* Přidání dalšího ověření v konfiguraci modulu pro rozpoznávání a přidal další chybové zprávy.
* Vylepšené zpracování dlouholetý nečinnosti uprostřed zvukový soubor.
* Balíček NuGet: pro projekty .NET Framework, zabraňuje sestavování s využitím konfigurace AnyCPU.

**Opravy chyb**

* Opravili jsme několik výjimek, které jsou součástí nástrojů pro rozpoznávání. Kromě toho jsou výjimky zachyceny a převedeny na zrušené události.
* Opravte nevracení paměti v vlastnost správy.
* Oprava chyby, ve kterém zvukový vstupní soubor mohlo způsobit chybu rozlišovače.
* Je opravená chyba, kde může přijaté události po události ukončení relace.
* Opravili jsme některé časování v dělení na vlákna.
* Opravili jsme potíže s kompatibilitou, které by mohlo způsobit selhání pro iOS.
* Vylepšení stability pro podporu Androidu mikrofon.
* Je opravená chyba, kdy by rozlišovač v jazyce JavaScript ignorovat rozpoznávání jazyka.
* Oprava chyby brání nastavení EndpointId (v některých případech) v jazyce JavaScript.
* Pořadí parametrů změněné v AddIntent v jazyce JavaScript a přidání chybějícího podpisu AddIntent JavaScript.

**Ukázky**

* Přidání C++ a C# samplea pull a push využití datový proud v [ukázkové úložiště](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Sada Speech SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

* Oprava potenciální závažná chyba z důvodu časování v uvolňování modulu pro rozpoznávání
* Oprava potenciální závažná chyba v případě zrušit nastavení vlastnosti.
* Přidané Další chyba a Kontrola parametrů.
* Cíl-C: Opravili jsme možnou závažnou chybu způsobenou přepsáním názvu v NSString.
* Cíl-C: Upravená viditelnost rozhraní API
* JavaScript: Opravené související události a jejich datové části.
* Dokumentace k vylepšení.

V našem [ukázkové úložiště](https://aka.ms/csspeech/samples), byl přidán nový ukázky pro jazyk JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services 1.0.0 Speech SDK: 2018. září verze

**Nové funkce**

* Podpora pro Objective-C v iOS. Podívejte se na naše [rychlý start Objective-C pro iOS](quickstart-objectivec-ios.md).
* Podpora jazyka JavaScript v prohlížeči. Podívejte se na naše [JavaScript quickstart](quickstart-js-browser.md).

**Rozbíjející změny v**

* V této verzi se zavádí určitý počet nezměněných změn.
  Podrobnosti najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services 0.6.0 Speech SDK: 2018. srpna

**Nové funkce**

* Aplikace UPW vytvořené pomocí sadou SDK pro řeč teď můžete předat Kit certifikaci aplikace Windows (souboru).
  Podívejte se [UPW quickstart](quickstart-csharp-uwp.md).
* Podpora pro .NET Standard 2.0 pro Linux (Ubuntu 16.04 x 64).
* Zkušební Podpora jazyka Java 8 v systému Windows (64 bitů) a Linux (Ubuntu 16,04 x64).
  Podívejte se [rychlý start Java Runtime Environment](quickstart-java-jre.md).

**Funkční změny**

* Zobrazit další podrobné informace o chybě na chyby připojení.

**Rozbíjející změny v**

* V jazyce Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkce už vyžaduje parametr cesty. Cesta se nyní automaticky zjistí na všech podporovaných platformách.
* Přistupující objekt get vlastnosti `EndpointUrl` byla odebrána v jazyce Java a C#.

**Opravy chyb**

* V jazyce Java zvuku syntézu výsledek pro překlad rozlišovač implementované nyní.
* Opravili jsme chybu, která by mohl způsobit neaktivní vláken a navýšení počtu otevřít a nepoužívané sokety.
* Opravili jsme problém, kde může ukončit dlouhotrvající rozpoznávání uprostřed přenosu.
* Oprava časování v modulu pro rozpoznávání vypnutí.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services 0.5.0 Speech SDK: verze 2018 – červenec

**Nové funkce**

* Podpora platformy Android (rozhraní API 23: Android 6,0 Marshmallow nebo vyšší). Podívejte se [Android rychlý Start](quickstart-java-android.md).
* Podpora .NET Standard 2.0 na Windows. Podívejte se [rychlý start pro .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Zkušební Podpora UWP ve Windows (verze 1709 nebo novější).
  * Podívejte se [UPW quickstart](quickstart-csharp-uwp.md).
  * Poznámka: Aplikace UWP vytvořené pomocí sady Speech SDK ještě nepředá certifikační sadu aplikací pro Windows (WACK).
* Podporují rozpoznávání dlouhotrvající se automatické obnovení připojení.

**Funkční změny**

* `StartContinuousRecognitionAsync()` podporuje dlouhotrvající rozpoznávání.
* Rozpoznávání výsledek obsahuje více polí. Máte posun od začátku zvuku a doba trvání (i v taktech) si rozpoznaný text a další hodnoty, které představují rozpoznávání stav, například `InitialSilenceTimeout` a `InitialBabbleTimeout`.
* Podpora AuthorizationToken vytváření instancí továrny.

**Rozbíjející změny v**

* Události rozpoznávání: Typ události neshody byl sloučen do chybové události.
* SpeechOutputFormat v jazyce C# se přejmenoval na OutputFormat zůstat zarovnané s jazykem C++.
* Návratový typ některé metody `AudioInputStream` rozhraní se mírně změní:
   * V jazyce Java `read` metody nyní vrací `long` místo `int`.
   * V jazyce C# `Read` metody nyní vrací `uint` místo `int`.
   * V jazyce C++ `Read` a `GetFormat` metody nyní návratový `size_t` místo `int`.
* C++: Instance zvukových vstupních streamů se teď dají předat jenom jako `shared_ptr`.

**Opravy chyb**

* Oprava nesprávné návratové hodnoty ve výsledku Pokud `RecognizeAsync()` vyprší časový limit.
* Závislost na knihovny media foundation ve Windows se odebrala. Sada SDK nyní používá rozhraní API Core zvuk.
* Oprava dokumentace: Přidání stránky [oblastí](regions.md) pro popis podporovaných oblastí.

**Známý problém**

* Sadou SDK pro řeč pro Android nemá sestavy o výsledcích syntézu řeči pro překlad. Tento problém bude vyřešen v příští verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services 0.4.0 Speech SDK: 2018. června – verze

**Funkční změny**

- AudioInputStream

  Rozlišovače teď můžou využívat datového proudu jako zdroje zvuku. Další informace najdete v tématu související [Příručka](how-to-use-audio-input-streams.md).

- Formát podrobný výstup

  Když vytvoříte `SpeechRecognizer`, můžete požádat o `Detailed` nebo `Simple` formát výstupu. `DetailedSpeechRecognitionResult` Obsahuje skóre spolehlivosti, textové rozpoznaných, lexikální nezpracované podobě, normalizovaná forma a normalizovaná forma se maskované vulgárních výrazů.

**Zásadní změna**

- Změnit na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` v jazyce C#.

**Opravy chyb**

- Oprava potíží možné zpětného volání ve vrstvě USP během vypínání.

- Pokud rozlišovače spotřebované zvuku vstupního souboru, to se udržuje popisovač souboru, který je delší než nezbytnou.

- Odebrat několika zablokování mezi pumpu zpráv a rozlišovače.

- Oheň `NoMatch` dojít, pokud vypršel časový limit odpověď ze služby.

- Knihovny media foundation na Windows jsou zpožděné načtení. Tato knihovna je vyžadován pro vstup pouze pro mikrofon.

- Rychlost odesílání pro zvukových dat je omezená na rychlost dvakrát původní zvuku.

- Na Windows C# .NET sestavení teď je silný název.

- Oprava dokumentace: `Region` jsou vyžadované informace k vytvoření rozpoznávání rukopisu.

Další ukázky byly přidány a jsou neustále aktualizovány. Nejnovější sadu ukázek, najdete v článku [úložišti GitHub s ukázkami sadou SDK pro řeč](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services 0.2.12733 Speech SDK: 2018 – může vydávat

Tato verze je první verze public preview služby Cognitive Services SDK řeči.
