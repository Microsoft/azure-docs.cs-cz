---
title: O sadě Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Sada Speech Software Development Kit (SDK) zveřejňuje mnoho funkcí služby pro rozpoznávání řeči, což usnadňuje vývoj aplikací podporujících řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: ad26fe0d869a2e892a419b1732727c3ff1d1e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870710"
---
# <a name="about-the-speech-sdk"></a>Informace o sadě Speech SDK

Sada Speech Software Development Kit (SDK) zveřejňuje mnoho funkcí služby pro rozpoznávání řeči, které vám umožní vyvíjet aplikace s podporou řeči. Sada Speech SDK je dostupná v řadě programovacích jazyků a na všech platformách.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Možnosti scénáře

Sada Speech SDK zpřístupňuje mnoho funkcí služby Speech, ale ne všechny. Funkce sady Speech SDK jsou často přidruženy k scénářům. Sada Speech SDK je ideální pro scénáře v reálném čase i v reálném čase, pomocí místních zařízení, souborů, úložiště objektů BLOB v Azure a dokonce i vstupních a výstupních datových proudů. Pokud scénář není pro sadu Speech SDK dosažitelný, vyhledejte REST API alternativu.

### <a name="speech-to-text"></a>Převod řeči na text

[Převod řeči na text](speech-to-text.md) (označovaný také jako *rozpoznávání řeči*) transcribes zvukové streamy na text, který můžou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Použití převodu řeči na text s [Language Understanding (Luis)](../luis/index.yml) k odvození záměrů uživatele z přepisu řeči a fungování hlasových příkazů. [Překlad řeči](speech-translation.md) použijte k překladu vstupu řeči do jiného jazyka s jedním voláním. Další informace najdete v tématu [základy převodu řeči na text](speech-to-text-basics.md).

**Rozpoznávání řeči (SR), seznam frází, záměr, překlad a místní kontejnery** jsou k dispozici na následujících platformách:

  - C++/Windows & Linux & macOS
  - C# (architektura & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE a Android)
  - JavaScript (cookies a NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Přejít (jenom SR)

### <a name="text-to-speech"></a>Převod textu na řeč

Převod [textu na řeč](text-to-speech.md) (označovaný také jako *syntéza řeči*) převede text na syntetizované rozpoznávání řeči od člověka. Vstupní text je buď řetězcové literály, nebo použití [jazyka SSML (Speech syntézy)](speech-synthesis-markup.md). Další informace o standardu nebo neuronové hlasy najdete v tématu věnovaném [jazyku pro převod textu na řeč a hlasové podpoře](language-support.md#text-to-speech).

**Převod textu na řeč** je k dispozici na následujících platformách:

  - C++/Windows & Linux
  - /Windows C# & pro UWP & Unity
  - Java (JRE a Android)
  - Python
  - Swift
  - Objective-C
  - REST API TTS lze použít v každé jiné situaci.

### <a name="voice-assistants"></a>Hlasoví asistenti

[Hlasové asistenti](voice-assistants.md) , kteří používají sadu Speech SDK, umožňují vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a asistentem. Implementace používá kanál s přímým přístupem rozhraní bot Framework nebo integrovaný vlastní příkaz (Preview) služby pro dokončení úkolu. Kromě toho můžou asistenti hlasu použít vlastní hlasy vytvořené na [vlastním hlasovém portálu](https://aka.ms/customvoice) a přidat tak jedinečné prostředí pro hlasové výstupy.

**Hlasové asistenti** jsou k dispozici na následujících platformách:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)

#### <a name="keyword-spotting"></a>Klíčové slovo hledání

Koncept [klíčového slova hledání](speech-devices-sdk-create-kws.md) je podporován v sadě Speech SDK. Klíčové slovo hledání je aktem, který identifikuje klíčové slovo v řeči a po vyslechnutí klíčového slova sleduje akci. Například "Hey Cortana" by aktivoval pomocníka Cortana.

**Klíčové slovo hledání (KWS)** je k dispozici na následujících platformách:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - Funkce klíčového slova hledání (KWS) můžou fungovat s jakýmkoli typem mikrofonu. oficiální podpora KWS se ale v tuto chvíli omezila na pole mikrofonu, která najdete v hardwaru Azure Kinect DK nebo v sadě Speech SDK pro zařízení.

### <a name="meeting-scenarios"></a>Scénáře schůzky

Sada Speech SDK je ideální pro scénáře setkání zdlouhavého přepisování, ať už z jednoho zařízení, nebo z konverzace s více zařízeními.

#### <a name="conversation-transcription"></a>Přepis konverzace

[Přepis konverzace](conversation-transcription.md) umožňuje rozpoznávání řeči v reálném čase (a asynchronní), identifikaci mluvčího a navýšení věty na jednotlivé mluvčí (označované také jako *diarization*). Je ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory.

**Přepis konverzace** je k dispozici na následujících platformách:

  - C++/Windows & Linux
  - C# (rozhraní & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)

#### <a name="multi-device-conversation"></a>Konverzace s více zařízeními

Při [konverzaci s více](multi-device-conversation.md)zařízeními můžete propojit několik zařízení nebo klientů v rámci konverzace, abyste mohli odesílat zprávy založené na řeči nebo textové zprávy s jednoduchou podporou pro přepis a překlady.

**Konverzace s více zařízeními** jsou k dispozici na následujících platformách:

  - /Windows C++
  - C# (rozhraní & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Vlastní nebo agentské scénáře

Sadu Speech SDK lze použít pro scénáře zdlouhavého přepisováního centra volání, kde jsou vygenerována data telefonního subsystému.

#### <a name="call-center-transcription"></a>Přepis nahrávek z call centra

[Přepis Center pro volání](call-center-transcription.md) je běžný scénář pro zdlouhavého přepisování velkých objemů dat telefonního subsystému, které můžou pocházet z různých systémů, jako je například interaktivní hlasová odezva (IRV). Nejnovější modely rozpoznávání řeči z Excelu služby Speech na zdlouhavého přepisování tato data telefonního subsystému, a to i v případě, že jsou data obtížná člověkem srozumitelná.

**Přepis centra volání** je k dispozici prostřednictvím služby Batch Speech Service prostřednictvím svého REST API a lze jej použít v jakékoli situaci.

### <a name="codec-compressed-audio-input"></a>Komprimovaný zvukový vstup kodeku

Několik programovacích jazyků pro sadu Speech SDK podporuje kodeky komprimované zvukové vstupní proudy. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">použití komprimovaných formátů <span class="docon docon-navigate-external x-hidden-focus"></span> zvukového vstupu </a>.

**Komprimovaný zvukový vstup kodeku** je k dispozici na následujících platformách:

  - /Linux C++
  - C#/Linux
  - Java/Linux, Android a iOS

## <a name="rest-api"></a>REST API

Sada Speech SDK obsahuje mnoho funkcí funkce služby Speech, a to v některých případech můžete chtít použít REST API.

### <a name="batch-transcription"></a>Dávkový přepis

[Batch přepis](batch-transcription.md) umožňuje provádět přepis velkých objemů dat z převodu řeči na text. Dávkový přepis je možný jenom z REST API. Kromě převodu zvukového zvuku na text, Batch Speech-to-text také umožňuje diarization a mínění-Analysis.

## <a name="customization"></a>Přizpůsobení

Služba Speech přináší skvělé funkce s výchozími modely pro převod řeči na text, převod textu na řeč a převod řeči. V některých případech můžete chtít zvýšit výkon standardních hodnot tak, aby fungoval ještě lépe díky jedinečnému případu použití. Služba Speech má řadu nástrojů pro přizpůsobení bez kódu, které usnadňují a umožňují vytvářet konkurenční výhody s vlastními modely na základě vašich vlastních dat. Tyto modely budou k dispozici pouze pro vás a vaši organizaci.

### <a name="custom-speech-to-text"></a>Custom Speech na text

Při použití převodu řeči na text pro rozpoznávání a přepis v jedinečném prostředí můžete vytvořit a vyškolit vlastní modely akustického, jazykového a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní odvětví. Vytváření a Správa modelů Custom Speechch bez kódu je k dispozici prostřednictvím [portálu Custom Speech](https://aka.ms/customspeech). Po publikování Custom Speech modelu ho může sada Speech SDK spotřebovat.

### <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní převod textu na řeč, označovaný také jako vlastní hlas, je sada online nástrojů, která umožňuje vytvořit rozpoznatelný hlas pro vaši značku. Vytváření a Správa vlastních modelů hlasu bez kódu je k dispozici prostřednictvím [vlastního hlasového portálu](https://aka.ms/customvoice). Po publikování vlastního hlasového modelu ho může sada Speech SDK spotřebovat.

## <a name="get-the-speech-sdk"></a>Získat sadu Speech SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Prohlížeč](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další kroky

* [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Přečtěte si téma rozpoznávání řeči v jazyce C. #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
