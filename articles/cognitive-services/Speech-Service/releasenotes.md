---
title: Zpráva k vydání verze – hlasové služby
titlesuffix: Azure Cognitive Services
description: Zobrazit spuštěné protokol vydání funkcí, vylepšení, oprav chyb a známé problémy pro hlasové služby Azure.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 609443a4926fabd991846faee4a0a7dffe3a696b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490178"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019 – červen vydání

**Ukázky**
*   Ukázky kódu pro rychlý start pro převod textu na řeč na UPW a Unity
*   Ukázky rychlý start pro Swift v Iosu
*   Ukázky v Unity pro zpracování řeči a rozpoznávání záměru a překladu
*   Aktualizace úvodní ukázky pro DialogServiceConnector

**Vylepšení / změny**
* Dialogové okno obor názvů:
    * SpeechBotConnector byl přejmenován na DialogServiceConnector
    * BotConfig byl přejmenován na DialogServiceConfig
    * BotConfig::FromChannelSecret() má byla přeřazena na DialogServiceConfig::FromBotSecret()
    * Všechny stávající klienty s přímým přístupem řeči řádku nadále podporovány po přejmenování
* Aktualizace adaptéru REST převod textu na ŘEČ pro podporu proxy, trvalé připojení
* Zlepšení chybovou zprávu, když je předána neplatná oblast
* Kód SWIFT a Objective-C:
    * Vylepšené hlášení chyb: Metody, které může dojít k chybě, jsou teď k dispozici ve dvou verzích: Ten, který poskytuje `NSError` objekt pro zpracování chyb a ten, který vyvolává výjimku. Předchozí jsou vystaveny Swift. Tato změna vyžaduje úpravy existující kód Swift.
    * Zpracování událostí vylepšené

**Opravy chyb**
*   Oprava pro převod textu na ŘEČ: kde SpeakTextAsync budoucnost vrátila bez čekání, dokud se nedokončí zvuk vykreslování
*   Oprava pro zařazování řetězců v C# povolit úplné jazyková podpora
*   Oprava problému aplikace .NET core se načíst knihovnu core s cílovou architekturu net461 ve vzorcích
*   Oprava pro občasné problémy, které je nasazování nativních knihoven do výstupní složky ve vzorcích
*   Oprava pro zavření spolehlivě webové sokety
*   Oprava pro možných chyb při otevírání připojení velmi při velkém zatížení v Linuxu
*   Oprava pro chybějí metadata v rámci sadě pro macOS
*   Oprava problémů s `pip install --user` na Windows


## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Toto je vydání opravy chyb a pouze by to ovlivnilo sady SDK nativního/spravovaného. Není to by to ovlivnilo verze jazyka JavaScript sady SDK.

**Opravy chyb**

* Opravte FromSubscription při použití s určené k transkripci konverzace.
* Oprava chyby v hledání pro hlasové první virtuálních asistentů – klíčové slovo.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: Května 2019 vydání

**Nové funkce**

* Funkce probuzení Wordu (klíčové slovo zjišťování/KWS) je teď dostupná pro Windows a Linux. Funkce KWS může fungovat s jakýmkoli typem mikrofon, oficiální KWS podporu, ale aktuálně je omezená na mikrofon pole najdete v Azure Kinect DK hardwaru nebo sadou SDK pro řeč zařízení.
* Fráze pomocný parametr funkce je dostupná prostřednictvím sady SDK. Další informace najdete v tématu [tady](how-to-phrase-lists.md).
* Funkce určené k transkripci konverzace je k dispozici prostřednictvím sady SDK. Zobrazit [tady](conversation-transcription-service.md).
* Přidání podpory pro hlasové první virtuální Asistenti pomocí kanálu s přímým přístupem řádku řeči.

**Ukázky**

* Přidání ukázky pro nové funkce nebo nové služeb podporovaných službou SDK.

**Vylepšení / změny**

* Přidá různé vlastnosti rozlišovač k úpravě chování služby nebo výsledků služby (např. maskování vulgárních výrazů a další).
* Teď můžete nakonfigurovat nástroj pro rozpoznávání prostřednictvím vlastnosti standardní konfigurace i v případě, že jste vytvořili nástroj pro rozpoznávání `FromEndpoint`.
* C: cíl `OutputFormat` SPXSpeechConfiguration byla přidána vlastnost.
* Sada SDK teď podporuje Debian 9 jako Linuxovou distribuci.

**Opravy chyb**

* Opravili jsme problém, kde byl prostředek mluvčího zničené příliš brzy v převodu textu na řeč.
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Toto je vydání opravy chyb a pouze by to ovlivnilo sady SDK nativního/spravovaného. Není to by to ovlivnilo verze jazyka JavaScript sady SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Jedná se o verzi jen pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

* Zabránit web balíček načtení agenta proxy https.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019 dubna vydání

**Nové funkce** 

* Sada SDK teď podporuje převod textu na řeč služby jako beta verze. Podporováno od Windows a Linuxem plochy z C++ a C#. Další informace najdete [převod textu na řeč přehled](text-to-speech.md#get-started-with-text-to-speech).
* Sada SDK teď podporuje zvukové soubory MP3 a díle/OGG jako datový proud vstupních souborů. Tato funkce je dostupná jenom v Linuxu z jazyka C++ a C# a je aktuálně ve verzi beta (Další podrobnosti [tady](how-to-use-codec-compressed-audio-input-streams.md)).
* Sadou SDK pro řeč pro Javu, .NET core, C++ a Objective-C získali podporu pro macOS. Podpora jazyka Objective-C pro macOS je aktuálně ve verzi beta.
* iOS: Sadou SDK pro řeč pro iOS (Objective-C) nyní jsme publikovali také CocoaPod.
* JavaScript: Podpora pro jiné než výchozí mikrofon jako vstupní zařízení.
* JavaScript: Podpora proxy pro Node.js.

**Ukázky**

* Přidali jsme ukázky pro použití v jazyce C++ a Objective-c. sadou SDK pro řeč v systému macOS.
* Ukázky demonstrace využití převod textu na řeč služby byly přidány.

**Vylepšení / změny**

* Python: Další vlastnosti výsledky rozpoznávání jsou teď dostupné prostřednictvím `properties` vlastnost.
* Pro další rozvoj a podporu ladění, můžete přesměrovat SDK protokolování a diagnostické informace do souboru protokolu (Další podrobnosti [tady](how-to-use-logging.md)).
* JavaScript: Zlepšení výkonu zpracování zvuku.

**Opravy chyb**

* Mac/iOS: Byl opraven chybu, která vedla k dlouhého čekání, když nelze navázat připojení ke službě řeči.
* Python: zlepšení zpracování chyb pro argumenty v Pythonu zpětná volání.
* JavaScript: Oprava nesprávného stavu vytváření sestav pro zpracování řeči, skončila RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: Aktualizace února 2019

Toto je vydání opravy chyb a pouze by to ovlivnilo sady SDK nativního/spravovaného. Není to by to ovlivnilo verze jazyka JavaScript sady SDK.

**Oprava chyby**

* Oprava nevracení paměti při použití mikrofonu vstup. Na základě Stream nebo vstupní soubor nemá vliv.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019 Únorová verze

**Nové funkce**

* Sadou SDK pro řeč podporuje výběr vstupní mikrofonu prostřednictvím AudioConfig třídy. Umožňuje zvukových dat datový proud do hlasové služby z jiné než výchozí mikrofonu. Další informace najdete v dokumentaci popisující [vstupní zvuková zařízení výběr](how-to-select-audio-input-devices.md). Tato funkce není k dispozici z jazyka JavaScript.
* Sadou SDK pro řeč teď podporuje Unity v beta verzi. Poskytnout zpětnou vazbu prostřednictvím v části problém [ukázkového úložiště Githubu](https://aka.ms/csspeech/samples). Tato verze podporuje Unity na Windows x86 a x64 (desktop nebo aplikací univerzální platformy Windows) a Android (ARM32/64, x86). Další informace najdete v našich [rychlý start Unity](quickstart-csharp-unity.md).
* Soubor `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (k dispozici v předchozích verzích) už není potřeba. Funkce je teď integrovaná v core SDK.


**Ukázky**

Následující nový obsah je k dispozici v našich [ukázkové úložiště](https://aka.ms/csspeech/samples):

* Další ukázky pro AudioConfig.FromMicrophoneInput.
* Další ukázky Pythonu pro rozpoznání záměru a převodu.
* Další ukázky pro IOS pomocí objekt připojení.
* Další ukázky v Javě pro překlad textů pomocí zvukového výstupu.
* Nové ukázky pro použití [rozhraní REST API služby Batch určené k transkripci](batch-transcription.md).

**Vylepšení / změny**

* Python
  * Vylepšené parametr ověření a chybové zprávy v SpeechConfig.
  * Přidání podpory pro objekt připojení.
  * Podpora Pythonu 32-bit (x86) na Windows.
  * Z beta verze je sadou SDK pro řeč pro Python.
* iOS
  * Sada SDK je teď integrovaný proti sady iOS SDK verze 12.1.
  * Sada SDK nyní podporuje iOS verze 9.2 a vyšší.
  * Zlepšení referenční dokumentaci a opravte několik názvů vlastností.
* JavaScript
  * Přidání podpory pro objekt připojení.
  * Přidání souborů definic typů pro připojené jazyka JavaScript
  * Počáteční podpora a implementaci pro pomocné parametry frázi.
  * Vrátí kolekci properties službou JSON pro rozpoznávání
* Knihovny DLL Windows teď obsahují verzi prostředku.
* Pokud jste vytvořili rozpoznávání `FromEndpoint` můžete přidat parametry přímo na adresu URL koncového bodu. Pomocí `FromEndpoint` nejde nakonfigurovat nástroj pro rozpoznávání prostřednictvím vlastnosti standardní konfigurace.

**Opravy chyb**

* Prázdný proxy uživatelské jméno a heslo pro proxy server nebyly zpracovány správně. V této vydané verzi pokud ji nastavíte proxy uživatelské jméno a heslo pro proxy server na prázdný řetězec, se nebude odeslat při připojování k proxy serveru.
* ID relace je vytvořena pomocí sady SDK nebyly vždy skutečně náhodného pro některé jazyky&nbsp;/ prostředí. Přidat inicializační generátor náhodných odstranění tohoto problému.
* Zlepšení zpracování autorizační token. Pokud chcete použít autorizační token, zadejte v SpeechConfig a nechte prázdné, pokud klíč předplatného. Vytvořte nástroj pro rozpoznávání jako obvykle.
* V některých případech připojení nebylo správně uvolnění objektu. Tento problém byl vyřešen.
* Ukázková JavaScript byl nastaven na podporují zvukového výstupu pro překlad syntézu také v Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Jedná se o verzi jen pro JavaScript. Nepřidaly se žádné funkce. Byly provedeny následující opravy:

* Konec datového proudu na turn.end, ne na speech.end vyvolat.
* Oprava chyby v zvuku čerpadla, že nebyla plánovat nebude dále odeslat, pokud aktuální odeslání se nezdařilo.
* Oprava průběžné rozpoznávání s ověřovací token.
* Oprava chyby pro různé rozlišovač nebo koncové body.
* Dokumentace k vylepšení.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: Verze prosince 2018

**Nové funkce**

* Python
  * Je k dispozici v této vydané verzi Beta verze podpory Pythonu (3.5 a vyšší). Další informace najdete v tématu here](quickstart-python.md).
* JavaScript
  * Open source se sadou SDK pro řeč pro jazyk JavaScript. Zdrojový kód je k dispozici na [Githubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Nyní podporujeme Node.js, najdete další informace o [tady](quickstart-js-node.md).
  * Omezení délky pro zvuk relace byla odebrána, opětovné připojení v rámci obálek provede automaticky.
* Objekt připojení
  * Z rozpoznávání rukopisu můžete použít objekt připojení. Tento objekt umožňuje explicitně iniciovat připojení k službě a přihlaste se k připojení a odpojení události odběru.
    (Tato funkce není dosud k dispozici z jazyka JavaScript a Python.)
* Podpora pro Ubuntu 18.04.
* Android
  * Povolené ProGuard podporují během generování APK.

**Vylepšení**

* Vylepšení ve využití interní vlákna, snížení počtu vláken, zámky, vzájemně vyloučené přístupy.
* Vylepšené hlášení chyb a informace. V několika případech ještě nebyly rozšířeny chybové zprávy si jak.
* Aktualizovat závislosti vývoj v jazyce JavaScript použití aktuální modulů.

**Opravy chyb**

* Z důvodu neshody typu v RecognizeAsync nevracení pevné paměti.
* V některých případech se za následek únik výjimek.
* Oprava nevracení paměti v argumentech události překladu.
* Oprava zamykání problém na znovu připojit v dlouho spuštěna relace.
* Opravili jsme problém, který může vést k chybějící konečný výsledek pro překlady se nezdařilo.
* C#: Pokud asynchronní operace nebyla očekávána v hlavním vlákně, bylo možné že nástroj pro rozpoznávání může být uvolněn předtím, než se asynchronní úloha byla dokončena.
* Java: Opravili jsme problém, což vede k chybovému ukončení virtuálního počítače Java.
* Objective-C: Oprava výčtu mapování; RecognizedIntent vrátila místo RecognizingIntent.
* JavaScript: Nastavení výchozího formátu výstupu na jednoduché v SpeechConfig.
* JavaScript: Odebírá se nekonzistence mezi vlastnosti objektu konfigurace v jazyce JavaScript a další jazyky.

**Ukázky**

* Aktualizace a opravy několik vzorových (například výstup hlasy pro překlad atd.).
* Přidání ukázky Node.js v [ukázkové úložiště](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nové funkce**

* Podpora pro Android x86/x64.
* Podpora proxy serveru: V objektu SpeechConfig můžete nyní volání funkce nastavíte informace o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není k dispozici v systému iOS.
* Vylepšené chybové kód a zprávy. Pokud rozlišení vrátil chybu, to již nastaven `Reason` (v případě zrušené) nebo `CancellationDetails` (v výsledek rozpoznání) k `Error`. Zrušené událost teď obsahuje dva další členy, `ErrorCode` a `ErrorDetails`. Pokud server vrátil Další informace o chybě s oznámenou chybu, teď bude k dispozici v nové členy.

**Vylepšení**

* Přidání dalšího ověření v konfiguraci modulu pro rozpoznávání a přidal další chybové zprávy.
* Vylepšené zpracování dlouholetý nečinnosti uprostřed zvukový soubor.
* Balíček NuGet: pro projekty .NET Framework, zabraňuje sestavování s využitím konfigurace AnyCPU.

**Opravy chyb**

* Opravili jsme několik výjimek, které jsou součástí nástrojů pro rozpoznávání. Kromě toho jsou výjimky zachycena a převeden na událost zrušení.
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

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

* Oprava potenciální závažná chyba z důvodu časování v uvolňování modulu pro rozpoznávání
* Oprava potenciální závažná chyba v případě zrušit nastavení vlastnosti.
* Přidané Další chyba a Kontrola parametrů.
* Objective-C: Oprava možné závažná chyba způsobila přepsáním název v NSString.
* Objective-C: Upravené viditelnost rozhraní API
* JavaScript: Opravili jsme týkající se událostí a instalační soubory.
* Dokumentace k vylepšení.

V našem [ukázkové úložiště](https://aka.ms/csspeech/samples), byl přidán nový ukázky pro jazyk JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Služby cognitive Services řeči SDK 1.0.0: Verze září 2018

**Nové funkce**

* Podpora pro Objective-C v iOS. Podívejte se na naše [rychlý start Objective-C pro iOS](quickstart-objectivec-ios.md).
* Podpora jazyka JavaScript v prohlížeči. Podívejte se na naše [JavaScript quickstart](quickstart-js-browser.md).

**Rozbíjející změny v**

* V této verzi představeny počet nejnovější změny.
  Zkontrolujte [na této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) podrobnosti.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Sada SDK 0.6.0 řeči služeb cognitive Services: Verze srpna 2018

**Nové funkce**

* Aplikace UPW vytvořené pomocí sadou SDK pro řeč teď můžete předat Kit certifikaci aplikace Windows (souboru).
  Podívejte se [UPW quickstart](quickstart-csharp-uwp.md).
* Podpora pro .NET Standard 2.0 pro Linux (Ubuntu 16.04 x 64).
* Experimentální: Podpora jazyka Java 8 na Windows (64 bitů) a Linux (Ubuntu 16.04 x 64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Sada SDK si verzi 0.5.0 řeči služeb cognitive Services: Července 2018

**Nové funkce**

* Podpora platformy Android (API 23: Android 6.0 Marshmallow nebo vyšší). Podívejte se [Android rychlý Start](quickstart-java-android.md).
* Podpora .NET Standard 2.0 na Windows. Podívejte se [rychlý start pro .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimentální: Podpora UWP na Windows (verze 1709 nebo novější).
  * Podívejte se [UPW quickstart](quickstart-csharp-uwp.md).
  * Poznámka: Aplikace UPW vytvořené pomocí sadou SDK pro řeč ještě nepředávejte Kit certifikaci aplikace Windows (souboru).
* Podporují rozpoznávání dlouhotrvající se automatické obnovení připojení.

**Funkční změny**

* `StartContinuousRecognitionAsync()` podporuje dlouhotrvající rozpoznávání.
* Rozpoznávání výsledek obsahuje více polí. Máte posun od začátku zvuku a doba trvání (i v taktech) si rozpoznaný text a další hodnoty, které představují rozpoznávání stav, například `InitialSilenceTimeout` a `InitialBabbleTimeout`.
* Podpora AuthorizationToken vytváření instancí továrny.

**Rozbíjející změny v**

* Rozpoznávání události: Typ události NoMatch se nesloučila do událost chyby.
* SpeechOutputFormat v jazyce C# se přejmenoval na OutputFormat zůstat zarovnané s jazykem C++.
* Návratový typ některé metody `AudioInputStream` rozhraní se mírně změní:
   * V jazyce Java `read` metody nyní vrací `long` místo `int`.
   * V jazyce C# `Read` metody nyní vrací `uint` místo `int`.
   * V jazyce C++ `Read` a `GetFormat` metody nyní návratový `size_t` místo `int`.
* C++: Instance vstupního audiostreamy nyní mohou být předány pouze jako `shared_ptr`.

**Opravy chyb**

* Oprava nesprávné návratové hodnoty ve výsledku Pokud `RecognizeAsync()` vyprší časový limit.
* Závislost na knihovny media foundation ve Windows se odebrala. Sada SDK nyní používá rozhraní API Core zvuk.
* Oprava dokumentace: Přidá [oblastech](regions.md) stránky k popisu podporovaných oblastí.

**Známý problém**

* Sadou SDK pro řeč pro Android nemá sestavy o výsledcích syntézu řeči pro překlad. Tento problém bude vyřešen v příští verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Sada SDK 0.4.0 řeči služeb cognitive Services: Verze června 2018

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Sada SDK 0.2.12733 řeči služeb cognitive Services: Verze v květnu 2018

Tato verze je první verze public preview služby Cognitive Services SDK řeči.
