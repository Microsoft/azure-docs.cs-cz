---
title: O sadě Speech SDK – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Začínáme se sadou SDK pro řeč zařízení. Speech Service funguje se širokou škálu zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. Sada SDK zařízení řeči je předem připravené knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558927"
---
# <a name="about-the-speech-devices-sdk"></a>O sadě Speech SDK pro zařízení

[Služba Speech Services](overview.md) funguje se širokou škálou zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. Sada SDK zařízení řeči je pretuned knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.

Vám můžou pomoct sadou SDK pro řeč zařízení:

* Rychle otestujte nové hlasové scénáře.
* Jednodušší integrace cloudových hlasových služeb do zařízení.
* Vytvoření mimořádné uživatelské prostředí pro vaše zákazníky.

Využívá sadou SDK pro řeč zařízení [sadou SDK pro řeč](speech-sdk.md). Pomocí sady Speech SDK pošle zvuk, který zpracovává náš pokročilý algoritmus zvukového zpracování, z pole mikrofonu zařízení do služby pro [rozpoznávání řeči](overview.md). Používá vícekanálový zvuku můžete poskytnout přesnější úplně pole [rozpoznávání řeči](speech-to-text.md) prostřednictvím šumu potlačení, zrušení zpětné vazby, beamforming a dereverberation.

Sadu Speech Devices SDK můžete použít také k vytváření okolních zařízení, která mají vlastní přizpůsobená [slova probuzení](speech-devices-sdk-create-kws.md) , takže upozornění, které iniciuje interakci uživatele, je pro vaši značku jedinečné.

Sada Speech Devices SDK usnadňuje celou řadu scénářů s podporou hlasu, jako jsou [vlastní virtuální asistenti hlasu](https://aka.ms/bots/speech/va), jednotky, přepisy a [](conversation-transcription-service.md)inteligentní reproduktory. Může reagovat na uživatele s textem, mluvit zpět na ně ve výchozí nebo [vlastní hlasové](how-to-customize-voice-font.md), poskytovat výsledky vyhledávání [přeložit](speech-translation.md) do jiných jazyků a další. Těšíme se zobrazuje, co je vytvořit!

## <a name="get-the-speech-devices-sdk"></a>Získání sady Speech Devices SDK

### <a name="android"></a>Android

Pro zařízení s Androidem si stáhněte nejnovější verzi [sady SDK pro zařízení s Androidem pro rozpoznávání řeči](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

V případě systému Windows je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE v 64 (v4) na Windows. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

### <a name="linux"></a>Linux

Pro Linux je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavena pomocí balíčku sady Speech SDK a Java IDE v 64 (v4) na-bit Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba zařízení pro rozpoznávání řeči](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Získat klíč předplatného služby Speech Services zdarma](get-started.md)
