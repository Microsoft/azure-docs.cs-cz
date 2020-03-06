---
title: Kodek Stream komprimovaný zvuk se sadou Speech SDK v iOS
titleSuffix: Azure Cognitive Services
description: Naučte se streamovat komprimovaný zvuk do služby Speech pomocí sady Speech SDK v iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 5d65ac83fc8a41008971108c5b370c1d0416113b
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331515"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Postupy: použití komprimovaného zvukového vstupu kodeku se sadou Speech SDK v iOS

Rozhraní API pro **komprimovaný zvuk vstupního streamu** sady Speech SDK poskytuje způsob, jak streamovat komprimovaný zvuk do služby pro rozpoznávání řeči pomocí datového proudu Pull nebo push.

> [!IMPORTANT]
> Pro streamování komprimovaného zvuku v iOS se vyžaduje sada Speech SDK verze 1.7.0 nebo novější. Podporuje se také pro [ C++systémy, C#a Java v systémech Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)](how-to-use-codec-compressed-audio-input-streams.md) a [Java v Androidu.](how-to-use-codec-compressed-audio-input-streams-android.md)

Informace o WAV/PCM najdete v dokumentaci k hlavní Speech. Mimo zvuk WAV/PCM jsou podporovány následující formáty komprimované vstupní hodnoty kodeku:

- MP3
- OPUS/OGG
- FLAC
- ALAW v kontejneru WAV
- MULAW v kontejneru WAV

## <a name="prerequisites"></a>Předpoklady

Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nelze tyto funkce dodávat se sadou SDK, ale Obálková knihovna obsahující tyto funkce musí být sestavena vývojáři aplikací a dodávána s aplikacemi, které používají sadu SDK.

Pro sestavení této knihovny obálek si nejdřív Stáhněte a nainstalujte [sadu GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Pak stáhněte projekt Xcode pro [knihovnu obálek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otevřete projekt v Xcode a sestavte ho pro **obecný cíl zařízení iOS** – nebude tak fungovat, aby se vytvořil pro konkrétní cíl.

Krok sestavení vygeneruje sadu dynamického rozhraní s dynamickou knihovnou pro všechny nezbytné architektury s názvem `GStreamerWrapper.framework`.

Tato architektura musí být zahrnutá ve všech aplikacích, které používají komprimované zvukové streamy pomocí sady SDK služby pro rozpoznávání řeči.

Použijte následující nastavení v projektu Xcode k tomuto účelu:

1. Zkopírujte jak vámi sestavené `GStreamerWrapper.framework`, tak i architekturu sady Cognitive Services Speech SDK, kterou si můžete stáhnout z [tohoto místa](https://aka.ms/csspeech/iosbinary), do adresáře, který obsahuje ukázkový projekt.
1. Upravte cesty k architekturám v _nastavení projektu_.
   1. Na kartě **Obecné** pod hlavičkou **vložená binární soubory** přidejte knihovnu SDK jako rozhraní: **přidejte vložené binární soubory** > **Přidat další...** > přejděte do adresáře, který jste zvolili, a vyberte obě architektury.
   1. Přejděte na kartu **Build Settings** (Nastavení sestavení) a aktivujte **všechna** nastavení.
1. Do části `$(SRCROOT)/..`Framework Search Paths _(Cesty pro hledání rozhraní) pod nadpisem_Search Paths **(Cesty pro hledání) přidejte adresář**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Příklad kódu pomocí komprimovaného zvukového vstupu kodeku

Pokud chcete streamovat v komprimovaném zvukovém formátu ke službě rozpoznávání řeči, vytvořte `SPXPullAudioInputStream` nebo `SPXPushAudioInputStream`.

Následující fragment kódu ukazuje, jak vytvořit `SPXAudioConfiguration` z instance `SPXPushAudioInputStream`a určením MP3 jako kompresního formátu datového proudu.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

Další fragment kódu ukazuje, jak je možné číst komprimovaná zvuková data ze souboru a nacházet z něj do `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Viz rozpoznávání řeči v jazyce Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
