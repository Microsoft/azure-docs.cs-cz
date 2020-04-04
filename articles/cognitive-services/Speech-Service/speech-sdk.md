---
title: O řeči SDK - řeč služby
titleSuffix: Azure Cognitive Services
description: Sada SDK (Speech software Development Kit) poskytuje mnoho funkcí služby Speech, což usnadňuje vývoj aplikací s podporou řeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656525"
---
# <a name="about-the-speech-sdk"></a>Informace o sadě Speech SDK

Sada SDK (Speech software Development Kit) poskytuje mnoho funkcí služby Speech, které vám umožňují vyvíjet aplikace s podporou řeči. Sada Speech SDK je k dispozici v mnoha programovacích jazycích a na všech platformách.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Možnosti scénáře

Sada Speech SDK zveřejňuje mnoho funkcí ze služby řeči, ale ne všechny z nich. Možnosti sady Speech SDK jsou často spojeny se scénáři. Sada Speech SDK je ideální pro scénáře v reálném čase i bez vreálném-time, pomocí místních zařízení, souborů, úložiště objektů blob Azure a dokonce i vstupních a výstupních datových proudů. Pokud scénář není dosažitelné s sadou Speech SDK, vyhledejte alternativu rozhraní REST API.

### <a name="speech-to-text"></a>Převod řeči na text

[Převod řeči na text](speech-to-text.md) (označovaný také jako *rozpoznávání řeči)* přepisuje zvukové proudy na text, který mohou aplikace, nástroje nebo zařízení využívat nebo zobrazovat. Pomocí funkce převod řeči na text s [jazykovou úpravou (LUIS)](../luis/index.yml) můžete odvodit záměry uživatelů z přepisované řeči a jednat s hlasovými příkazy. Pomocí [překladu řeči](speech-translation.md) přeložte vstup řeči do jiného jazyka pomocí jediného volání. Další informace naleznete v [tématu Základy převodu řeči na text](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Převod textu na řeč

[Převod textu na řeč](text-to-speech.md) (označovaný také jako *syntéza řeči*) převede text na syntetizovanou řeč podobné člověku. Vstupní text je buď řetězcové literály nebo pomocí [jazyka značek syntézy řeči (SSML).](speech-synthesis-markup.md) Další informace o standardních nebo neurálních hlasech naleznete v [tématu Jazyk převodu textu na řeč a hlasová podpora](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Hlasoví asistenti

Hlasoví asistenti používající sadu Speech SDK umožňují vývojářům vytvářet přirozené konverzační rozhraní podobné člověku pro své aplikace a prostředí. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a asistentem. Implementace používá kanál přímé řeči rozhraní Bot Framework nebo integrovanou službu vlastních příkazů (Preview) pro dokončení úlohy. Kromě toho hlasové asistenty lze vytvořit pomocí [vlastního hlasového portálu](https://aka.ms/customvoice) k vytvoření jedinečného hlasového prostředí.

#### <a name="keyword-spotting"></a>Pozorování klíčových slov

Koncept [zmetenní klíčových slov](speech-devices-sdk-create-kws.md) je podporován v sadě Speech SDK. Pozorování klíčových slov je akt identifikace klíčového slova v řeči, následovaný akcí při poslechu klíčového slova. Například "Hey Cortana" by aktivovat asistenta Cortana.

### <a name="meeting-scenarios"></a>Scénáře schůzky

Sada Speech SDK je ideální pro přepis scénářů schůzek, ať už z jednoho zařízení nebo konverzace s více zařízeními.

#### <a name="conversation-transcription"></a>Přepis konverzace

[Přepis konverzace](conversation-transcription.md) umožňuje rozpoznávání řeči v reálném čase (a aasynchronní), identifikaci mluvčího a přiřazení věty každému řečníkovi (označované také jako *diarizace).* Je ideální pro přepis osobních schůzek se schopností rozlišovat řečníky.

#### <a name="multi-device-conversation"></a>Konverzace na více zařízeních

Díky [konverzaci na více zařízeních](multi-device-conversation.md)připojte do konverzace více zařízení nebo klientů a odesílejte hlasové nebo textové zprávy se snadnou podporou přepisu a překladu.

### <a name="custom--agent-scenarios"></a>Vlastní scénáře / scénáře agenta

Sadu Speech SDK lze použít pro přepis scénářů centra volání, kde jsou generována telefonní data.

#### <a name="call-center-transcription"></a>Přepis nahrávek z call centra

[Přepis call centra](call-center-transcription.md) je běžný scénář pro převod řeči na text pro přepis velkých objemů telefonních dat, které mohou pocházet z různých systémů, jako je interaktivní hlasová odezva (IVR). Nejnovější modely rozpoznávání řeči ze služby Rozpoznávání řeči vynikají v přepisu těchto telefonních dat, a to i v případech, kdy je pro člověka obtížné porozumět.

### <a name="codec-compressed-audio-input"></a>Kodek komprimovaný zvukový vstup

Několik programovacích jazyků sady Speech SDK podporuje kodekkomizované vstupní datové proudy zvuku. Další informace naleznete v <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">tématu použití <span class="docon docon-navigate-external x-hidden-focus"> </span>formátů komprimovaných zvukových vstupů </a>.

## <a name="rest-api"></a>REST API

Zatímco sada Speech SDK pokrývá mnoho funkcí služby Řeči, pro některé scénáře můžete chtít použít rozhraní REST API. Jako příklad je správa koncového bodu vystavena pouze prostřednictvím rozhraní REST API.

> [!TIP]
> Při spoléhání se na rozhraní REST API, použijte Swagger Editor automaticky generovat klientské knihovny. Chcete-li například generovat knihovnu klienta batch transkripce.
> 1. Zkopírujte následující ukázkovou adresu URL:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. Přejděte do <a href="https://editor.swagger.io/" target="_blank">editoru <span class="docon docon-navigate-external x-hidden-focus"></span> Swagger</a>
> 1. Vyberte**URL importu** **souboru** > a vložte ji.
> 1. Vyberte **Generovat klienta** a zvolte požadovaný programovací jazyk

### <a name="batch-transcription"></a>Dávkový přepis

[Dávkový přepis](batch-transcription.md) umožňuje asynchronní přepis řeči na text velkých objemů dat. Dávkový přepis je možný pouze z rozhraní REST API.

## <a name="customization"></a>Přizpůsobení

Služba speech service poskytuje skvělé funkce s výchozími modely napříč převodem řeči na text, převod textu na řeč a překladem řeči. Někdy můžete chtít zvýšit výkon směrného plánu pracovat ještě lépe s jedinečným případem použití. Služba Speech Service má řadu nástrojů pro přizpůsobení bez kódu, které usnadňují a umožňují vytvořit konkurenční výhodu s vlastními modely založenými na vlastních datech. Tyto modely budou k dispozici pouze vám a vaší organizaci.

### <a name="custom-speech-to-text"></a>Vlastní převod řeči na text

Při použití funkce převodu řeči na text pro rozpoznávání a přepis v jedinečném prostředí můžete vytvářet a trénovat vlastní modely akustické, jazykové a výslovnosti, které řeší okolní hluk nebo slovní zásobu specifickou pro dané odvětví. Vytvoření a správa modelů vlastní řeči bez kódu je k dispozici prostřednictvím [portálu vlastní řeči](https://aka.ms/customspeech). Po publikování modelu vlastní řeči, může být spotřebována řeči SDK.

### <a name="custom-text-to-speech"></a>Vlastní převod textu na řeč

Vlastní převod textu na řeč, známý také jako Vlastní hlas, je sada online nástrojů, které vám umožní vytvořit rozpoznatelný, jedinečný hlas pro vaši značku. Vytvoření a správa modelů vlastních hlasových programů bez kódu je k dispozici prostřednictvím [vlastního hlasového portálu](https://aka.ms/customvoice). Po publikování modelu vlastní hlas, může být spotřebována řeči SDK.

## <a name="get-the-speech-sdk"></a>Získání sady SDK pro rozpoznávání řeči

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[Macos](#tab/macos)

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

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Podívejte se, jak rozpoznat řeč v C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
