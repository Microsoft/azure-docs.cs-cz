---
title: Dokumentace k sadě Speech SDK
titlesuffix: Azure Cognitive Services
description: Zpráva k vydání verze – co se změnilo v nejnovější vydané verze
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: wolfma
ms.openlocfilehash: 706f51ae1e2d81e2003f2fcd637def95c7a42f8e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567532"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-service-sdk-110"></a>Speech Service SDK 1.1.0

**Nové funkce**

* Podpora pro Android x86/x64.
* Podpora proxy: V objektu SpeechConfig můžete nyní volat funkce nastavíte informace o proxy serveru (název hostitele, port, uživatelské jméno a heslo). Tato funkce není k dispozici v systému iOS.
* Vylepšené chybové kód a zprávy. Pokud rozlišení vrátil chybu, to již nastaven `Reason` (v případě zrušené) nebo `CancellationDetails` (v výsledek rozpoznání) k `Error`. Zrušené událost teď obsahuje dva další členy, `ErrorCode` a `ErrorDetails`. Pokud server vrátil Další informace o chybě s oznámenou chybu, teď bude k dispozici v nové členy.

**Vylepšení**

* Přidání dalšího ověření v konfiguraci modulu pro rozpoznávání a přidal další chybové zprávy.
* Vylepšené zpracování dlouholetý nečinnosti uprostřed zvukový soubor.
* Balíček NuGet: pro rozhraní .NET Framework projekty zabránit sestavování s využitím konfigurace AnyCPU.

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

## <a name="speech-service-sdk-101"></a>Speech Service SDK 1.0.1

Vylepšení spolehlivosti a opravy chyb:

* Oprava potenciální závažná chyba z důvodu časování v uvolňování modulu pro rozpoznávání
* Oprava potenciální závažná chyba v případě zrušit nastavení vlastnosti.
* Přidané Další chyba a Kontrola parametrů.
* Oprava C: cíl možné závažná chyba způsobila přepsáním název v NSString.
* Upravit cíl na C: viditelnost rozhraní API
* Jazyk JavaScript: Oprava týkající se událostí a instalační soubory.
* Dokumentace k vylepšení.

V našem [ukázkové úložiště](https://aka.ms/csspeech/samples), byl přidán nový ukázky pro jazyk JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services sadou SDK pro řeč 1.0.0: 2018zářijová verze

**Nové funkce**

* Podpora pro Objective-C v iOS. Podívejte se na naše [rychlý start Objective-C pro iOS](quickstart-objectivec-ios.md).
* Podpora jazyka JavaScript v prohlížeči. Podívejte se na naše [JavaScript quickstart](quickstart-js-browser.md).

**Rozbíjející změny v**

* V této verzi představeny počet nejnovější změny.
  Zkontrolujte prosím [na této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) podrobnosti.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services sadou SDK pro řeč 0.6.0: verze srpna 2018

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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services SDK řeči si verzi 0.5.0: 2018červencové vydání

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

* Rozpoznávání události: typ události NoMatch se nesloučila do událost chyby.
* SpeechOutputFormat v jazyce C# se přejmenoval na OutputFormat zůstat zarovnané s jazykem C++.
* Návratový typ některé metody `AudioInputStream` rozhraní se mírně změní:
   * V jazyce Java `read` metody nyní vrací `long` místo `int`.
   * V jazyce C# `Read` metody nyní vrací `uint` místo `int`.
   * V jazyce C++ `Read` a `GetFormat` metody nyní návratový `size_t` místo `int`.
* Jazyk C++: Výskyty vstupních audiostreamy nyní mohou být předány pouze jako `shared_ptr`.

**Opravy chyb**

* Oprava nesprávné návratové hodnoty ve výsledku Pokud `RecognizeAsync()` vyprší časový limit.
* Závislost na knihovny media foundation ve Windows se odebrala. Sada SDK nyní používá rozhraní API Core zvuk.
* Oprava dokumentace: přidání [oblastech](regions.md) stránky k popisu podporovaných oblastí.

**Známý problém**

* Sadou SDK pro řeč pro Android nemá sestavy o výsledcích syntézu řeči pro překlad. Tento problém bude vyřešen v příští verzi.

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
