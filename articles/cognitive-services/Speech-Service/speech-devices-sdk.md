---
title: O sadě Speech SDK – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Začínáme se sadou SDK pro řeč zařízení. Služba Speech funguje se širokou škálou zařízení a zdrojů zvuku. Sada SDK zařízení řeči je předem připravené knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 40179c7d421a26fd9a77b435668f75ec6fde8ab1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136969"
---
# <a name="about-the-speech-devices-sdk"></a>Sada Speech Devices SDK

[Služba Speech](overview.md) funguje se širokou škálou zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. Sada SDK zařízení řeči je pretuned knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.

Vám můžou pomoct sadou SDK pro řeč zařízení:

- Rychle otestujte nové hlasové scénáře.
- Speech service založené na cloudu mnohem snazší integrate do zařízení.
- Vytvoření mimořádné uživatelské prostředí pro vaše zákazníky.

SADA Speech SDK pro zařízení spotřebovává [sadu Speech](speech-sdk.md). Použití našich pokročilých algoritmů pro zpracování zvuku s polem mikrofonu zařízení k odeslání zvuku do [služby pro rozpoznávání řeči](overview.md). Poskytuje přesné [rozpoznávání řeči](speech-to-text.md) se špičkovým potlačením hluku, ozvěnou zrušení, beamforming a dereverberation.

Můžete také použít sadu Speech SDK pro zařízení k vytváření okolních zařízení, která mají vlastní [přizpůsobené klíčové slovo](speech-devices-sdk-create-kws.md). Vlastní klíčové slovo poskytuje hromádku, která spouští interakci uživatele, která je pro vaši značku jedinečná.

Sada Speech Devices SDK umožňuje celou řadu scénářů s podporou hlasu, jako jsou například [hlasové asistenty](https://aka.ms/bots/speech/va), systémy řazení, [přepisy konverzací](conversation-transcription-service.md)a inteligentní reproduktory. Můžete reagovat na uživatele s textem, mluvit zpátky do nich ve výchozím nebo [vlastním hlasu](how-to-customize-voice-font.md), poskytovat výsledky hledání, [překládat](speech-translation.md) je na jiné jazyky a další. Těšíme se zobrazuje, co je vytvořit!

## <a name="get-the-speech-devices-sdk"></a>Získání sady Speech Devices SDK

### <a name="android"></a>Android

V případě Androidu si zařízení stáhnou nejnovější verzi [sady SDK pro zařízení s Androidem pro rozpoznávání řeči](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

V případě systému Windows je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE v 64 (v4) na Windows. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

### <a name="linux"></a>Linux

Pro Linux je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE na 64 (v4) na. Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Další binární soubory jsou k dispozici pro podporu nadcházejících zařízení, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)a [mluvčího GGEC](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba zařízení pro rozpoznávání řeči](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
