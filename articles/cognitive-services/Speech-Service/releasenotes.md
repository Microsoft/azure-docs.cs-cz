---
title: Poznámky k verzi – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Běžící protokol vydaných verzí funkcí hlasové služby, vylepšení, oprav chyb a známé problémy.
services: cognitive-services
author: oscholz
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 1cf9ce6d57c1e106472caeef6c1f2a4b008a09bd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277872"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-sdk-190-2020-january-release"></a>Sada Speech SDK 1.9.0:2020 – leden Release

**Nové funkce**

- Konverzace s více zařízeními: umožňuje propojit více zařízení se stejnou hlasovou nebo textovou konverzací a volitelně překládat zprávy odesílané mezi nimi. Další informace najdete v [tomto článku](multi-device-conversation.md). 
- Podpora rozpoznávání klíčových slov se přidala pro balíček Android. AAR a přidala se podpora pro typy x86 a x64. 
- Cíl-C: metody `SendMessage` a `SetMessageProperty` přidány do objektu `Connection`. Další informace [najdete v dokumentaci.](https://docs.microsoft.com/objectivec/cognitive-services/speech/)
- Rozhraní C++ API pro TTS teď podporuje `std::wstring` jako Shrnutí textu a odstraňuje nutnost převést wstring na řetězec před předáním do sady SDK. [Tady](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)najdete podrobnosti. 
- C#: [ID jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) a [konfigurace zdrojového jazyka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) jsou nyní k dispozici.
- JavaScript: Přidání funkce pro `Connection` objektu pro předání vlastní zprávy ze služby Speech jako zpětného volání `receivedServiceMessage`.
- JavaScript: Přidání podpory pro `FromHost API`, která usnadňuje použití s kontejnery Prem a svrchovanými cloudy. Další informace [najdete v dokumentaci.](speech-container-howto.md)
- JavaScript: nyní `NODE_TLS_REJECT_UNAUTHORIZED`me, protože přispíváme k příspěvku z [orgads](https://github.com/orgads). [Tady](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)najdete podrobnosti.


**Rozbíjející změny v**

- `OpenSSL` byla aktualizována na verzi 1.1.1 b a staticky propojená se základní knihovnou sady Speech SDK pro Linux. To může způsobit přerušení, pokud `OpenSSL` doručených zpráv není nainstalován do `/usr/lib/ssl` adresáře v systému. Pokud chcete tento problém obejít, přečtěte si [naši dokumentaci](how-to-configure-openssl-linux.md) v části dokumentace k sadě Speech SDK.
- Změnili jsme datový typ vrácený pro C# `WordLevelTimingResult.Offset` z `int` na `long`, aby bylo možné získat přístup k `WordLevelTimingResults`ům, když jsou data řeči delší než 2 minuty.
- `PushAudioInputStream` a `PullAudioInputStream` nyní odesílají informace o hlavičce WAV do služby pro rozpoznávání řeči na základě `AudioStreamFormat`volitelně zadané při jejich vytvoření. Zákazníci teď musí používat [podporovaný formát zvukového vstupu](how-to-use-audio-input-streams.md). Všechny ostatní formáty získají dílčí optimální výsledky rozpoznávání nebo můžou způsobit jiné problémy. 


**Opravy chyb**

- V části nejnovější změny výše se podívejte na aktualizaci `OpenSSL`. V systému Linux a Java jsme opravili občasné chyby a problémy s výkonem (při vysokém zatížení zamknete spory). 
- Java: vylepšení uzavření objektu ve scénářích s vysokou mírou souběžnosti.
- Znovu se strukturuje náš balíček NuGet. Odebrali jsme tři kopie `Microsoft.CognitiveServices.Speech.core.dll` a `Microsoft.CognitiveServices.Speech.extension.kws.dll` ve složkách lib, čímž se balíček NuGet zmenší a rychleji stáhne a přidali jsme hlavičky potřebné k zkompilování některých C++ nativních aplikací.
- Pevné ukázky pro rychlý Start [najdete tady](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Ty se ukončily bez zobrazení výjimky "mikrofon nebyl nalezen" na Linux, MacOS a Windows.
- Opravili jsme chybu sady SDK s dlouhým výsledkem rozpoznávání řeči na určitých cestách kódu, jako je [Tato ukázka](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Opravená chyba nasazení sady SDK ve službě Azure Web App Environment pro vyřešení [tohoto problému se zákazníky](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Opravili jsme chybu TTS při použití `<voice>` značky nebo značky `<audio>` k vyřešení [tohoto problému zákazníka](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Opravená chyba TTS 401 při obnovení sady SDK z pozastavené.
- JavaScript: Děkujeme, že se vám jako příspěvek z [Euirim](https://github.com/euirim)opravil cyklický import zvukových dat. 
- JavaScript: Přidání podpory pro nastavení vlastností služby, jak je přidáno v 1,7.
- JavaScript: Opravili jsme problém, kdy by chyba připojení mohla způsobit průběžné pokusy o opětovné připojení k soketu WebSocket.


**Ukázky**

- [Sem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)přidejte ukázku rozpoznávání klíčových slov pro Android.
- [Sem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)přidejte ukázku TTS pro scénář serveru.
- Přidali jsme rychlé starty konverzace s více C# zařízeními C++ pro a [zde](quickstarts/multi-device-conversation.md).


**Další změny**

- Optimalizovaná velikost knihovny SDK jádra v Androidu
- Sada SDK v 1.9.0 a vyšší podporuje typy `int` a `string` v poli verze hlasového podpisu pro Transcriber konverzace.

## <a name="speech-sdk-180-2019-november-release"></a>Sada Speech SDK 1.8.0:2019 – listopadová verze

**Nové funkce**

- Přidali jsme rozhraní `FromHost()` API, abyste se mohli snadno používat s kontejnery Prem a svrchovanými cloudy.
- Přidání automatického zdrojového Rozpoznávání jazyka pro rozpoznávání řeči (v jazyce Java C++a)
- Přidání objektu `SourceLanguageConfig` pro rozpoznávání řeči, pomocí kterého se určí očekávané zdrojové jazyky (v jazycích C++Java a)
- Přidání podpory `KeywordRecognizer` do Windows (UWP), Androidu a iOS prostřednictvím balíčků NuGet a Unity
- Bylo přidáno rozhraní API vzdálené konverzace Java, které umožní přepis konverzace v asynchronních dávkách.

**Rozbíjející změny v**

- Funkce konverzace Transcriber přesunuté do oboru názvů `Microsoft.CognitiveServices.Speech.Transcription`.
- Součást metod konverzace Transcriber je přesunuta do nové třídy `Conversation`.
- Vyřazena podpora pro 32-bit (ARMv7 a x86) iOS

**Opravy chyb**

- Oprava pro chybu, pokud se místní `KeywordRecognizer` používá bez platného klíče předplatného služby Speech

**Ukázky**

- Ukázka Xamarin pro `KeywordRecognizer`
- Ukázka Unity pro `KeywordRecognizer`
- C++a ukázky Java pro automatické zdrojové Rozpoznávání jazyka.

## <a name="speech-sdk-170-2019-september-release"></a>Sada Speech SDK 1.7.0:2019 – září verze

**Nové funkce**

- Přidání podpory beta verze pro Xamarin on Univerzální platforma Windows (UWP), Android a iOS
- Přidala se podpora iOS pro Unity.
- Přidání podpory vstupu `Compressed` pro ALaw, mulaw, FLAC v Androidu, iOS a Linux
- Přidáno `SendMessageAsync` do `Connection` třídy pro odeslání zprávy službě
- Přidáno `SetMessageProperty` do `Connection` třídy pro nastavení vlastnosti zprávy
- TTS – přidané vazby pro Java (JRE a Android), Python, SWIFT a cíl-C
- TTS přidal podporu přehrávání pro macOS, iOS a Android.
- Přidání informací o hranici slova pro TTS

**Opravy chyb**

- Problém s opravenou IL2CPP sestavení v Unity 2019 pro Android
- Opravený problém se nesprávně zpracovávanými nepoškozenými záhlavími ve vstupním souboru WAV
- Opravený problém s identifikátory UUID není jedinečný v některých vlastnostech připojení.
- Opravili jsme několik upozornění na specifikátory hodnoty null v vazbách SWIFT (může vyžadovat malé změny kódu).
- Opravili jsme chybu, která způsobila, že připojení protokolu WebSocket se v zatížení sítě nekorektně zavřou.
- Opravili jsme problém v Androidu, který někdy má za následek duplicitní ID dojmu, které používá `DialogServiceConnector`
- Vylepšení stability připojení v rámci vícenásobných interakcí a hlášení selhání (prostřednictvím `Canceled`ch událostí), když k nim dojde v `DialogServiceConnector`
- `DialogServiceConnector` relace začne nyní správně poskytovat události, včetně při volání `ListenOnceAsync()` během aktivní `StartKeywordRecognitionAsync()`
- Vyřešená havárie přidružená k přijatým aktivitám `DialogServiceConnector`

**Ukázky**

- Rychlý Start pro Xamarin
- Aktualizovaný rychlý Start pro CPP s ARM64 informacemi pro Linux
- Aktualizovaný rychlý Start Unity s informacemi pro iOS

## <a name="speech-sdk-160-2019-june-release"></a>Sada Speech SDK 1.6.0:2019 – červenou verzi

**Ukázky**

- Ukázky rychlý Start pro převod textu na řeč pro UWP a Unity
- Ukázka rychlého startu pro SWIFT v iOS
- Ukázky Unity pro rozpoznávání řeči & Rozpoznávání záměru a překlad
- Aktualizované ukázky pro rychlý Start pro `DialogServiceConnector`

**Vylepšení/změny**

- Obor názvů dialogového okna:
  - Přejmenování `SpeechBotConnector` na `DialogServiceConnector`
  - Přejmenování `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` byla přemapována na `DialogServiceConfig::FromBotSecret()`
  - Po přejmenování budou dál podporovány všechny existující klienty s přímým ovládáním řeči.
- Aktualizace adaptéru pro převod textu na řeč pro podporu proxy a trvalého připojení
- Vylepšit chybovou zprávu, když je předána neplatná oblast
- SWIFT/objektiv-C:
  - Vylepšené zasílání zpráv o chybách: metody, které mohou mít za následek chybu, jsou nyní k dispozici ve dvou verzích: jeden, který zpřístupňuje `NSError` objekt pro zpracování chyb a druhý, který vyvolává výjimku. Bývalé jsou zpřístupněny SWIFT. Tato změna vyžaduje úpravu stávajícího kódu SWIFT.
  - Vylepšené zpracování událostí

**Opravy chyb**

- Oprava pro TTS: kde `SpeakTextAsync` budoucí vrácení bez čekání, dokud se nedokončí vykreslování zvuku
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
- Cíl-C: `OutputFormat` vlastnost byla přidána do `SPXSpeechConfiguration`.
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

- Sada SDK teď podporuje službu převodu textu na řeč jako beta verzi. Podporuje se v systémech Windows a Linux Desktop z C++ a C#. Další informace najdete v [přehledu převod textu na řeč](text-to-speech.md#get-started).
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

- Sada Speech SDK podporuje výběr vstupního mikrofonu prostřednictvím třídy `AudioConfig`. To vám umožní streamovat zvuková data do služby pro rozpoznávání řeči z jiného než výchozího mikrofonu. Další informace najdete v dokumentaci popisující [Výběr zvukového vstupního zařízení](how-to-select-audio-input-devices.md). Tato funkce není ještě dostupná z JavaScriptu.
- Sada Speech SDK teď ve verzi beta verze podporuje Unity. Poskytněte zpětnou vazbu v části problém v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity v systémech Windows x86 a x64 (desktopové nebo Univerzální platforma Windows aplikace) a Androidu (ARM32/64, x86). Další informace najdete v našem [rychlém](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)startu pro Unity.
- Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dodán v předchozích verzích) už není potřeba. Tato funkce je teď integrovaná do základní sady SDK.

**Ukázky**

V našem [ukázkovém úložišti](https://aka.ms/csspeech/samples)je k dispozici následující nový obsah:

- Další ukázky pro `AudioConfig.FromMicrophoneInput`.
- Další ukázky Pythonu pro rozpoznávání a překlad záměrů.
- Další ukázky pro použití objektu `Connection` v iOS
- Další ukázky Java pro překlad pomocí zvukového výstupu.
- Nová ukázka použití [dávkového přepisu REST API](batch-transcription.md).

**Vylepšení/změny**

- Python
  - Vylepšené ověřování parametrů a chybové zprávy v `SpeechConfig`.
  - Přidejte podporu pro objekt `Connection`.
  - Podpora 32-bitové verze Pythonu (x86) ve Windows.
  - Sada Speech SDK for Python je mimo verzi beta.
- iOS
  - Sada SDK je teď založená na sadě iOS SDK verze 12,1.
  - Sada SDK teď podporuje iOS verze 9,2 a novější.
  - Vylepšete referenční dokumentaci a opravte několik názvů vlastností.
- JavaScript
  - Přidejte podporu pro objekt `Connection`.
  - Přidat soubory definice typu pro seskupené JavaScripty
  - Počáteční podpora a implementace pro pomocný parametr fráze
  - Návratová kolekce vlastností s JSON služby pro rozpoznávání
- Knihovny DLL systému Windows teď obsahují prostředek verze.
- Pokud vytvoříte Nástroj pro rozpoznávání `FromEndpoint` můžete přidat parametry přímo do adresy URL koncového bodu. Pomocí `FromEndpoint` nemůžete konfigurovat nástroj pro rozpoznávání pomocí standardních vlastností konfigurace.

**Opravy chyb**

- Prázdné uživatelské jméno proxy serveru a heslo proxy serveru nebyly zpracovány správně. Pokud v této verzi nastavíte uživatelské jméno a heslo proxy serveru na prázdný řetězec, nebudou odeslány při připojování k proxy serveru.
- Identifikátor SessionId vytvořený sadou SDK pro některé jazyky&nbsp;nebo prostředí vždy nenáhodný. K vyřešení tohoto problému se přidala inicializace náhodného generátoru.
- Vylepšete zpracování autorizačního tokenu. Pokud chcete použít autorizační token, zadejte v `SpeechConfig` a nechte klíč předplatného prázdný. Pak vytvořte Nástroj pro rozpoznávání obvyklým způsobem.
- V některých případech nebyl objekt `Connection` správně uvolněn. Tento problém byl vyřešen.
- Ukázka JavaScriptu byla opravena tak, aby podporovala zvukový výstup pro syntézu překladu i v Safari.

## <a name="speech-sdk-121"></a>Sada Speech SDK 1.2.1

Toto je verze jenom pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

- Požárový konec streamu. konec, ne na řeč. end.
- Oprava chyby v zvukové pumpě, která neplánovala další odeslání, pokud se aktuální odeslání nepovedlo.
- Opravte průběžné rozpoznávání pomocí ověřovacího tokenu.
- Oprava chyb pro různé funkce pro rozpoznávání nebo koncové body.
- Dokumentace k vylepšení.

## <a name="speech-sdk-120-2018-december-release"></a>Sada Speech SDK 1.2.0:2018 – prosinec verze

**Nové funkce**

- Python
  - V této verzi je k dispozici beta verze podpory Pythonu (3,5 a novější). Další informace naleznete zde] (rychlý Start – python.md).
- JavaScript
  - Sada Speech SDK pro JavaScript je open source. Zdrojový kód je k dispozici na [GitHubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Nyní podporujeme Node. js. Další informace najdete [tady](quickstart-js-node.md).
  - Omezení délky pro zvukové relace bylo odebráno, k automatickému připojení dojde v rámci pokrytí.
- objekt `Connection`
  - Z `Recognizer`můžete získat přístup k objektu `Connection`. Tento objekt vám umožňuje explicitně iniciovat připojení služby a přihlásit se k odběru událostí a odpojovat se.
    (Tato funkce ještě není dostupná z JavaScriptu a Pythonu.)
- Podpora pro Ubuntu 18,04.
- Android
  - Povolená podpora ProGuard během generování APK

**Vylepšení**

- Vylepšení využití interního vlákna, což snižuje počet vláken, zámků a mutexů.
- Vylepšené zasílání zpráv o chybách/informace. V několika případech se chybové zprávy nerozšířily tak, jak jsou.
- Aktualizované závislosti vývoje v JavaScriptu pro použití aktuálnosti modulů.

**Opravy chyb**

- Pevná nevracení paměti kvůli neshodě typu v `RecognizeAsync`.
- V některých případech došlo k úniku výjimek.
- Oprava nevrácené paměti v argumentech události překladu.
- Opravili jsme problém se zámkem při opětovném připojení v dlouho spuštěných relacích.
- Opravili jsme problém, který by mohl vést k chybějícímu konečnému výsledku pro neúspěšné překlady.
- C#: Pokud v hlavním vlákně nečekala operace `async`, je možné, že by se modul pro rozpoznávání odstranil před dokončením asynchronní úlohy.
- Java: Opravili jsme problém, což vedlo k chybě virtuálního počítače Java.
- Cíl-C: pevné mapování výčtu; Místo `RecognizingIntent`byl vrácen RecognizedIntent.
- JavaScript: v `SpeechConfig`nastavte výchozí výstupní formát na ' jednoduchý '.
- JavaScript: odebrání nekonzistence mezi vlastnostmi objektu konfigurace v jazyce JavaScript a dalších jazycích.

**Ukázky**

- Aktualizovali a opravili jsme několik ukázek (například výstupní hlasy pro překlad atd.).
- Do [ukázkového úložiště](https://aka.ms/csspeech/samples)se přidaly ukázky pro Node. js.

## <a name="speech-sdk-110"></a>Sada Speech SDK 1.1.0

**Nové funkce**

- Podpora pro Android x86/x64.
- Podpora proxy serveru: v objektu `SpeechConfig` nyní můžete zavolat funkci pro nastavení informací o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není k dispozici v systému iOS.
- Vylepšené chybové kód a zprávy. Pokud rozlišení vrátil chybu, to již nastaven `Reason` (v případě zrušené) nebo `CancellationDetails` (v výsledek rozpoznání) k `Error`. Zrušené událost teď obsahuje dva další členy, `ErrorCode` a `ErrorDetails`. Pokud server vrátil Další informace o chybě s oznámenou chybu, teď bude k dispozici v nové členy.

**Vylepšení**

- Přidání dalšího ověření v konfiguraci modulu pro rozpoznávání a přidal další chybové zprávy.
- Vylepšené zpracování dlouholetý nečinnosti uprostřed zvukový soubor.
- Balíček NuGet: pro projekty .NET Framework, zabraňuje sestavování s využitím konfigurace AnyCPU.

**Opravy chyb**

- Opravili jsme několik výjimek, které jsou součástí nástrojů pro rozpoznávání. Kromě toho jsou výjimky zachyceny a převedeny na událost `Canceled`.
- Opravte nevracení paměti v vlastnost správy.
- Oprava chyby, ve kterém zvukový vstupní soubor mohlo způsobit chybu rozlišovače.
- Je opravená chyba, kde může přijaté události po události ukončení relace.
- Opravili jsme některé časování v dělení na vlákna.
- Opravili jsme potíže s kompatibilitou, které by mohlo způsobit selhání pro iOS.
- Vylepšení stability pro podporu Androidu mikrofon.
- Je opravená chyba, kdy by rozlišovač v jazyce JavaScript ignorovat rozpoznávání jazyka.
- Opravili jsme chybu, která znemožňuje nastavování `EndpointId` (v některých případech) v JavaScriptu.
- Změna pořadí parametrů v AddIntent v JavaScriptu a přidání chybějícího `AddIntent` signatury JavaScript.

**Ukázky**

- Přidány C++ a C# ukázky pro použití pro vyžádání obsahu a nabízených oznámení v [ukázkovém úložišti](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Sada Speech SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

- Oprava potenciální závažná chyba z důvodu časování v uvolňování modulu pro rozpoznávání
- Oprava potenciální závažná chyba v případě zrušit nastavení vlastnosti.
- Přidané Další chyba a Kontrola parametrů.
- Oprava C: cíl možné závažná chyba způsobila přepsáním název v NSString.
- Upravit cíl na C: viditelnost rozhraní API
- Jazyk JavaScript: Oprava týkající se událostí a instalační soubory.
- Dokumentace k vylepšení.

V našem [ukázkové úložiště](https://aka.ms/csspeech/samples), byl přidán nový ukázky pro jazyk JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services sadou SDK pro řeč 1.0.0: 2018zářijová verze

**Nové funkce**

- Podpora pro Objective-C v iOS. Podívejte se na naše [rychlý start Objective-C pro iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Podpora jazyka JavaScript v prohlížeči. Podívejte se na naše [JavaScript quickstart](quickstart-js-browser.md).

**Rozbíjející změny v**

- V této verzi se zavádí určitý počet nezměněných změn.
  Podrobnosti najdete na [této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services sadou SDK pro řeč 0.6.0: verze srpna 2018

**Nové funkce**

- Aplikace UPW vytvořené pomocí sadou SDK pro řeč teď můžete předat Kit certifikaci aplikace Windows (souboru).
  Podívejte se [UPW quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Podpora pro .NET Standard 2.0 pro Linux (Ubuntu 16.04 x 64).
- Experimentální: Podpora jazyka Java 8 na Windows (64 bitů) a Linux (Ubuntu 16.04 x 64).
  Podívejte se [rychlý start Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Funkční změny**

- Zobrazit další podrobné informace o chybě na chyby připojení.

**Rozbíjející změny v**

- V jazyce Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkce už vyžaduje parametr cesty. Cesta se nyní automaticky zjistí na všech podporovaných platformách.
- Přistupující objekt get vlastnosti `EndpointUrl` byla odebrána v jazyce Java a C#.

**Opravy chyb**

- V jazyce Java zvuku syntézu výsledek pro překlad rozlišovač implementované nyní.
- Opravili jsme chybu, která by mohl způsobit neaktivní vláken a navýšení počtu otevřít a nepoužívané sokety.
- Opravili jsme problém, kde může ukončit dlouhotrvající rozpoznávání uprostřed přenosu.
- Oprava časování v modulu pro rozpoznávání vypnutí.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services SDK řeči si verzi 0.5.0: 2018červencové vydání

**Nové funkce**

- Podpora platformy Android (API 23: Android 6.0 Marshmallow nebo vyšší). Podívejte se [Android rychlý Start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Podpora .NET Standard 2.0 na Windows. Podívejte se [rychlý start pro .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentální: Podpora UWP na Windows (verze 1709 nebo novější).
  - Podívejte se [UPW quickstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Poznámka: Aplikace UPW vytvořené pomocí sadou SDK pro řeč ještě nepředávejte Kit certifikaci aplikace Windows (souboru).
- Podporují rozpoznávání dlouhotrvající se automatické obnovení připojení.

**Funkční změny**

- `StartContinuousRecognitionAsync()` podporuje dlouhotrvající rozpoznávání.
- Rozpoznávání výsledek obsahuje více polí. Máte posun od začátku zvuku a doba trvání (i v taktech) si rozpoznaný text a další hodnoty, které představují rozpoznávání stav, například `InitialSilenceTimeout` a `InitialBabbleTimeout`.
- Podpora AuthorizationToken vytváření instancí továrny.

**Rozbíjející změny v**

- Události rozpoznávání: typ události `NoMatch` byl sloučen do události `Error`.
- SpeechOutputFormat v C# byla přejmenována na `OutputFormat`, aby C++zůstala zarovnána s.
- Návratový typ některé metody `AudioInputStream` rozhraní se mírně změní:
  - V jazyce Java `read` metody nyní vrací `long` místo `int`.
  - V jazyce C# `Read` metody nyní vrací `uint` místo `int`.
  - V jazyce C++ `Read` a `GetFormat` metody nyní návratový `size_t` místo `int`.
- Jazyk C++: Výskyty vstupních audiostreamy nyní mohou být předány pouze jako `shared_ptr`.

**Opravy chyb**

- Oprava nesprávné návratové hodnoty ve výsledku Pokud `RecognizeAsync()` vyprší časový limit.
- Závislost na knihovny media foundation ve Windows se odebrala. Sada SDK nyní používá rozhraní API Core zvuk.
- Oprava dokumentace: přidání [oblastech](regions.md) stránky k popisu podporovaných oblastí.

**Známý problém**

- Sadou SDK pro řeč pro Android nemá sestavy o výsledcích syntézu řeči pro překlad. Tento problém bude vyřešen v příští verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Services cognitive řeči vydání sady SDK 0.4.0: červen 2018

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Services cognitive řeči vydání sady SDK 0.2.12733: květen 2018

Tato verze je první verze public preview služby Cognitive Services SDK řeči.
