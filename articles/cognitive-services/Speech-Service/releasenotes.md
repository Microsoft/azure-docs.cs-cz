---
title: Zpráva k vydání verze – hlasové služby
titlesuffix: Azure Cognitive Services
description: Zobrazit spuštěné protokol vydání funkcí, vylepšení, oprav chyb a známé problémy pro hlasové služby Azure.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 80cf0d9970eb7052eeb19fb297176453864202cf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205691"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: Verze prosince 2018

**Nové funkce**

* Python
  * Je k dispozici v této vydané verzi Beta verze podpory Pythonu (3.5 a vyšší). Další podrobnosti [tady uvidíte](quickstart-python.md).
* JavaScript
  * Open source se sadou SDK pro řeč pro jazyk JavaScript. Zdrojový kód je k dispozici na [Githubu](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Nyní podporujeme Node.js, najdete další informace o [tady](quickstart-js-node.md).
  * Omezení délky pro zvuk relace byla odebrána, opětovné připojení v rámci obálek provede automaticky.
* Objekt připojení
  * Z Rozlišovač můžete získat objekt připojení. Tento objekt umožňuje explicitně iniciovat připojení k službě a přihlaste se k připojení a odpojení události odběru.
    (Toto není zatím k dispozici z jazyka JavaScript a Python.)
* Podpora pro Ubuntu 18.04.
* Android
  * Povolené ProGuard podporují během generování APK.

**Vylepšení**

* Vylepšení ve využití interní vlákna, snížení počtu vláken, zámky, vzájemně vyloučené přístupy.
* Vylepšené hlášení chyb a informace. V několika případech byl nepropagovala jak chybové zprávy.
* Aktualizovat závislosti vývoj v jazyce JavaScript použití aktuální modulů.

**Opravy chyb**

* Z důvodu neshody typu v RecognizeAsync nevracení pevné paměti.
* V některých případech se za následek únik výjimek.
* Oprava nevracení paměti v argumentech události překladu.
* Oprava zamykání problém na znovu připojit v dlouho spuštěna relace.
* Opravili jsme chybu, která by mohla vést k chybějící konečný výsledek pro překlady se nezdařilo.
* C#: Pokud asynchronní operace nebyla očekávána v hlavním vlákně, bylo možné že nástroj pro rozpoznávání může být uvolněn předtím, než se asynchronní úloha byla dokončena.
* Java: Opravili jsme problém, což vede k chybovému ukončení virtuálního počítače Java.
* Objective-C: Oprava výčtu mapování; RecognizedIntent vrátila místo RecognizingIntent.
* JavaScript: Nastavení výchozího formátu výstupu na jednoduché v SpeechConfig.
* JavaScript: Odebírá se nekonzistence mezi vlastnosti objektu konfigurace v jazyce JavaScript a další jazyky.

**Ukázky**

* Aktualizace a opravy několik vzorových (například výstup hlasy pro překlad atd).
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
  Zkontrolujte prosím [na této stránce](https://aka.ms/csspeech/breakingchanges_1_0_0) podrobnosti.

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
