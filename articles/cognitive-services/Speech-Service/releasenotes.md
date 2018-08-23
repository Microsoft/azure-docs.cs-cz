---
title: Dokumentace k sadě SDK pro řeč služeb cognitive Services | Dokumentace Microsoftu
description: Zpráva k vydání verze – co se změnilo v nejnovější vydané verze
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: wolfma
ms.openlocfilehash: 0900588f818855d72e415678338c96fb6505318d
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "41994382"
---
# <a name="release-notes"></a>Poznámky k verzi

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services sadou SDK pro řeč 0.6.0: verze srpna 2018

**Nové funkce**

* Aplikace UPW vytvořené pomocí sadou SDK pro řeč teď můžete předat Kit certifikaci aplikace Windows (souboru).
  Podívejte se na naše [UPW quickstart](quickstart-csharp-uwp.md).
* Podpora pro .NET Standard 2.0 pro Linux (Ubuntu 16.04 x 64).
* Experimentální: Podpora jazyka Java 8 na Windows (64 bitů) a Linux (Ubuntu 16.04 x 64).
  Podívejte se [Java Runtime Environment rychlý start](quickstart-java-jre.md)

**Funkční změny**

* Vystavení další podrobné informace o chybě na chyby připojení.

**Rozbíjející změny v**

* V jazyce Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funkce už vyžaduje parametr cesty. Cesta je nyní zjištěn automaticky na všech podporovaných platformách.
* Přistupující objekt get vlastnosti `EndpointUrl` byla odebrána v jazyce Java a C#.

**Opravy chyb**

* V jazyce Java se teď implementuje zvuku syntézu výsledek pro překlad rozlišovače.
* Oprava chyby, který by mohl způsobit neaktivní vláken a navýšení počtu otevřít a nepoužívané sokety.
* Opravili jsme problém, kdy dlouho běžící rozpoznávání může ukončit uprostřed přenosu.
* Oprava časování v modulu pro rozpoznávání vypnutí.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services SDK řeči si verzi 0.5.0: 2018červencové vydání

**Nové funkce**

* Podpora platformy Android (API 23: Android 6.0 Marshmallow nebo vyšší).
  Podívejte se [Android rychlý Start](quickstart-java-android.md).
* Podpora .NET Standard 2.0 na Windows.
  Podívejte se [rychlý start pro .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimentální: Podpora UWP na Windows (verze 1709 nebo novější)
  * Podívejte se na naše [UPW quickstart](quickstart-csharp-uwp.md).
  * Poznámka: Aplikace UPW vytvořené pomocí sadou SDK pro řeč ještě nepředávejte Kit certifikaci aplikace Windows (souboru).
* Podpora dlouho trvající rozpoznávání se automatické obnovení připojení.

**Funkční změny**

* `StartContinuousRecognitionAsync()` podporuje dlouho trvající rozpoznávání
* Rozpoznávání výsledek obsahuje více polí: posun ze začátku zvuku a doba trvání (i v taktech) rozpoznaný text další hodnoty představující rozpoznávání stav, například `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Podpora AuthorizationToken vytváření instancí továrny.

**Rozbíjející změny v**

* Rozpoznávání události: typ události NoMatch je sloučen do událost chyby.
* SpeechOutputFormat v jazyce C# je přejmenován na OutputFormat zachovat zarovnané s jazykem C++.
* Návratový typ některé metody `AudioInputStream` rozhraní nepatrná změna:
   * V jazyce Java `read` metody nyní vrací `long` místo `int`.
   * V jazyce C# `Read` metody nyní vrací `uint` místo `int`.
   * V jazyce C++ `Read` a `GetFormat` metody nyní návratový `size_t` místo `int`.
* Jazyk C++: instance zvukové streamy ve vstupní lze nyní pouze předat jako `shared_ptr`.

**Opravy chyb**

* Oprava nesprávné návratové hodnoty ve výsledku Pokud `RecognizeAsync()` vyprší časový limit.
* Závislost na knihovny media foundation na Windows Odebereme. Sady SDK je teď používá rozhraní API Core zvuk.
* Oprava dokumentace: přidání oblasti stránky popisující, jaké jsou podporované oblasti.

**Známé problémy**

* Sadou SDK pro řeč pro Android nevytváří sestavu výsledků syntézu řeči pro překlad.
  Tato chyba bude opravena v další vydané verzi.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Services cognitive řeči vydání sady SDK 0.4.0: červen 2018

**Funkční změny**

- AudioInputStream

  Rozlišovače teď můžou využívat datového proudu jako zdroje zvuku. Podrobné informace najdete v tématu související [Příručka](how-to-use-audio-input-streams.md).

- Formát podrobný výstup

  Při vytváření `SpeechRecognizer`, můžete požádat o `Detailed` nebo `Simple` formát výstupu. `DetailedSpeechRecognitionResult` Obsahuje skóre spolehlivosti, textové rozpoznaných, lexikální nezpracované podobě, normalizovaná forma a normalizovaná forma se maskované vulgárních výrazů.

**Zásadní změna**

- Změnit na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` v jazyce C#.

**Opravy chyb**

- Opravte problém možná zpětného volání ve vrstvě USP během vypínání.

- Pokud rozlišovače spotřebované zvuku vstupního souboru, to se udržuje popisovač souboru, který je delší než nezbytnou.

- Odebrat několika zablokování mezi pumpu zpráv a rozpoznávání rukopisu.

- Oheň `NoMatch` dojít, pokud vypršel časový limit odpověď ze služby.

- Knihovny media foundation na Windows jsou načtené se zpožděním. Tato knihovna je pouze požadované pro vstup mikrofonu.

- Rychlost odesílání pro zvukových dat je omezená na rychlost dvakrát původní zvuku.

- Na Windows sestavení C# .NET jsou nyní silným názvem.

- Oprava dokumentace: `Region` jsou vyžadované informace k vytvoření rozpoznávání rukopisu.

Další ukázky byly přidány a jsou neustále aktualizovány. Nejnovější sadu ukázek, najdete v článku [úložiště Githubu ukázky řeči SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Services cognitive řeči vydání sady SDK 0.2.12733: květen 2018

První verze public preview služby Cognitive Services SDK řeči.
