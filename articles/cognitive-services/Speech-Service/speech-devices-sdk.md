---
title: Řečová zařízení SDK - Služba řeči
titleSuffix: Azure Cognitive Services
description: Začínáme se sadou SDK pro řečová zařízení. Služba Řeč funguje s širokou škálou zařízení a zdrojů zvuku. Sada Speech Devices SDK je předem vyladěná knihovna, která je spárována s účelovými sadami pro vývoj mikrofonního pole.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370640"
---
# <a name="what-is-the-speech-devices-sdk"></a>Co je sada SDK pro řečová zařízení?

[Služba Řeč](overview.md) funguje s širokou škálou zařízení a zdrojů zvuku. Nyní můžete své řečové aplikace posuzovat na další úroveň pomocí odpovídajícího hardwaru a softwaru. Sada Speech Devices SDK je předem vyladěná knihovna, která je spárována s účelovými sadami pro vývoj mikrofonního pole.

Sada SDK pro řečová zařízení vám může pomoci:

- Rychle otestujte nové hlasové scénáře.
- Snadněji integrujte cloudovou službu Speech do vašeho zařízení.
- Vytvořte pro své zákazníky výjimečné uživatelské prostředí.

Sada SDK s potřebou řeči spotřebovává [sadu Speech SDK](speech-sdk.md). Pomocí našich pokročilých algoritmů zpracování zvuku s mikrofonním polem zařízení k odeslání zvuku do [služby Speech](overview.md). Poskytuje přesné rozpoznávání [řeči](speech-to-text.md) na dálku prostřednictvím potlačení šumu, potlačení ozvěny, tvarování paprsku a dereverberace.

Sadou SDK pro řečová zařízení můžete také použít k vytvoření okolních zařízení, která mají vlastní [vlastní klíčové slovo](speech-devices-sdk-create-kws.md). Vlastní klíčové slovo poskytuje podnět, který spustí interakci uživatele, která je jedinečná pro vaši značku.

Sada SDK pro řečová zařízení umožňuje různé scénáře s podporou hlasu, například [hlasové asistenty](https://aka.ms/bots/speech/va), systémy řazení přes jednotku, [přepis konverzace](conversation-transcription-service.md)a chytré reproduktory. Můžete reagovat na uživatele textem, mluvit jim ve výchozím nebo [vlastním hlasu](how-to-customize-voice-font.md), poskytovat výsledky hledání, [překládat](speech-translation.md) do jiných jazyků a další. Těšíme se na to, co stavíte!

## <a name="get-the-speech-devices-sdk"></a>Získání sady Speech Devices SDK

### <a name="android"></a>Android

Pro Android zařízení stáhnout nejnovější verzi [Android řeči zařízení SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Pro Windows ukázkové aplikace je k dispozici jako aplikace java pro více platforem. Stáhněte si nejnovější verzi sady [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Aplikace je vytvořena s balíčkem Speech SDK a Eclipse Java IDE (v4) na 64bitovém systému Windows. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

### <a name="linux"></a>Linux

Pro Linux je ukázková aplikace poskytována jako aplikace Java pro různé platformy. Stáhněte si nejnovější verzi sady [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Aplikace je postavena s balíčkem Speech SDK a Eclipse Java IDE (v4) na 64bitovém Linuxu (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

Další binární soubory jsou k dispozici pro podporu nadcházejících zařízení, [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)a [GGEC Speaker](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výběr řečového zařízení](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Získejte zdarma klíč předplatného služby Speech](get-started.md)
