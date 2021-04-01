---
title: Sada Speech zařízení SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Začněte se sadou Speech SDK pro zařízení. Služba Speech funguje se širokou škálou zařízení a zdrojů zvuku. Sada Speech Devices SDK je předem vyladěná knihovna, která se spáruje s účelově postavenými vývojovými sadami pro vytváření mikrofonů.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015269"
---
# <a name="what-is-the-speech-devices-sdk"></a>Co je sada Speech Devices SDK?

[Služba Speech](overview.md) funguje se širokou škálou zařízení a zdrojů zvuku. Nyní můžete své aplikace pro rozpoznávání řeči přenést na další úroveň se shodným hardwarem a softwarem. Sada Speech Devices SDK je předem vyladěná knihovna, která se spáruje s účelově postavenými vývojovými sadami pro vytváření mikrofonů.

Sada Speech Devices SDK vám může pomáhat:

- Rychlé testování nových hlasových scénářů.
- Snadněji Integrujte cloudovou službu hlasové řeči do svého zařízení.
- Vytvořte výjimečné uživatelské prostředí pro vaše zákazníky.

SADA Speech SDK pro zařízení spotřebovává [sadu Speech](speech-sdk.md). Použití našich pokročilých algoritmů pro zpracování zvuku s polem mikrofonu zařízení k odeslání zvuku do [služby pro rozpoznávání řeči](overview.md). Poskytuje přesné [rozpoznávání řeči](speech-to-text.md) se špičkovým potlačením hluku, ozvěnou zrušení, beamforming a dereverberation.

Můžete také použít sadu Speech SDK pro zařízení k vytváření okolních zařízení, která mají vlastní [přizpůsobené klíčové slovo](./custom-keyword-basics.md). Vlastní klíčové slovo poskytuje hromádku, která spouští interakci uživatele, která je pro vaši značku jedinečná.

Sada Speech Devices SDK umožňuje celou řadu scénářů s podporou hlasu, jako jsou například [hlasové asistenty](./voice-assistants.md), systémy řazení, [přepisy konverzací](./conversation-transcription.md)a inteligentní reproduktory. Můžete reagovat na uživatele s textem, mluvit zpátky do nich ve výchozím nebo [vlastním hlasu](./how-to-custom-voice-create-voice.md), poskytovat výsledky hledání, [překládat](speech-translation.md) je na jiné jazyky a další. Těšíme se, abychom viděli, co sestavíte!

## <a name="get-the-speech-devices-sdk"></a>Získání sady Speech Devices SDK

### <a name="android"></a>Android

Sada Speech Devices SDK pro Android podporuje [Roobo v1](speech-devices-sdk-roobo-v1.md) a ekvivalentní zařízení. pro tyto účely si stáhněte nejnovější verzi [sady SDK pro zařízení se systémem Android Speech](https://aka.ms/sdsdk-download-android).


Pokud máte jiné zařízení s Androidem, jako je telefon nebo mobilní zařízení, začněte sadou [Speech SDK pro Android](speech-sdk.md)


### <a name="windows"></a>Windows

V případě systému Windows je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE v 64 (v4) na Windows. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

### <a name="linux"></a>Linux

Pro Linux je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi [sady JRE Speech pro zařízení](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená pomocí balíčku sady Speech SDK a Java IDE na 64 (v4) na. Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

K dispozici jsou další binární soubory, které podporují nadcházející zařízení, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC mluvčí](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)a [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volba zařízení pro rozpoznávání řeči](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)