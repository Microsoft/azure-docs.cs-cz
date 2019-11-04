---
title: O sadě Speech SDK – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Začněte se sadou Speech SDK pro zařízení. Služba Speech funguje se širokou škálou zařízení a zdrojů zvuku. Nyní můžete své aplikace pro rozpoznávání řeči přenést na další úroveň se shodným hardwarem a softwarem. Sada Speech Devices SDK je předem vyladěná knihovna, která se spáruje s účelově postavenými vývojovými sadami pro vytváření mikrofonů.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 243072477c7d249d7066a7a448061c51a0bd2f34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468715"
---
# <a name="about-the-speech-devices-sdk"></a>O sadě Speech SDK pro zařízení

[Služba Speech Services](overview.md) funguje se širokou škálou zařízení a zdrojů zvuku. Nyní můžete své aplikace pro rozpoznávání řeči přenést na další úroveň se shodným hardwarem a softwarem. Sada Speech Devices SDK je předpřipravená knihovna, která se spáruje s navrženými vývojovými sadami pro vytváření mikrofonních polí.

Sada Speech Devices SDK vám může pomáhat:

* Rychlé testování nových hlasových scénářů.
* Jednodušší integrace cloudových hlasových služeb do zařízení.
* Vytvořte výjimečné uživatelské prostředí pro vaše zákazníky.

SADA Speech SDK pro zařízení spotřebovává [sadu Speech](speech-sdk.md). Pomocí sady Speech SDK pošle zvuk, který zpracovává náš pokročilý algoritmus zvukového zpracování, z pole mikrofonu zařízení do služby pro [rozpoznávání řeči](overview.md). Používá vícekanálový zvuk k zajištění přesnější [rozpoznávání řeči](speech-to-text.md) při potlačení hluku, ozvěny zrušení, beamforming a dereverberation.

Sadu Speech Devices SDK můžete použít také k sestavení okolních zařízení s vlastním [přizpůsobeným klíčovým slovem](speech-devices-sdk-create-kws.md) , aby se upozornění, které iniciuje interakci uživatele, pro vaši značku unikátní.

Sada Speech Devices SDK usnadňuje celou řadu scénářů s podporou hlasu, jako jsou [hlasové asistenty](https://aka.ms/bots/speech/va), systémy řazení, [přepisy konverzací](conversation-transcription-service.md)a inteligentní reproduktory. Můžete reagovat na uživatele s textem, mluvit zpátky do nich ve výchozím nebo [vlastním hlasu](how-to-customize-voice-font.md), poskytovat výsledky hledání, [překládat](speech-translation.md) je na jiné jazyky a další. Těšíme se, abychom viděli, co sestavíte!

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
