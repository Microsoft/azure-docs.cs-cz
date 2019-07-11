---
title: Informace o zařízeních řeči SDK – hlasové služby
titleSuffix: Azure Cognitive Services
description: Začínáme se sadou SDK pro řeč zařízení. Hlasové služby pracovat s širokou škálu zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. Sada SDK zařízení řeči je předem připravené knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718539"
---
# <a name="about-the-speech-devices-sdk"></a>Informace o zařízeních řeči SDK

[Hlasové služby](overview.md) fungují s širokou škálu zařízení a zdrojů zvuku. Teď může trvat vašich aplikací rozpoznávání řeči na další úroveň díky odpovídající hardware a software. Sada SDK zařízení řeči je pretuned knihovny, které je spárováno pomocí účelových, mikrofon pole development Kit.

Vám můžou pomoct sadou SDK pro řeč zařízení:

* Rychle otestujte nové hlasové scénáře.
* Mnohem snazší integrate hlasové služby založené na cloudu do zařízení.
* Vytvoření mimořádné uživatelské prostředí pro vaše zákazníky.

Využívá sadou SDK pro řeč zařízení [sadou SDK pro řeč](speech-sdk.md). Používá sadou SDK pro řeč pro posílat zvuk, který je zpracován našeho algoritmu pokročilé zpracování zvuku z pole mikrofon zařízení, které chcete [hlasové služby](overview.md). Používá vícekanálový zvuku můžete poskytnout přesnější úplně pole [rozpoznávání řeči](speech-to-text.md) prostřednictvím šumu potlačení, zrušení zpětné vazby, beamforming a dereverberation.

Sadou SDK pro řeč zařízení můžete použít také k vytváření okolí zařízení, které mají vlastní [přizpůsobit probuzení slovo](speech-devices-sdk-create-kws.md) startovací, který iniciuje interakce s uživatelem je jedinečné pro vlastní značky.

Sadou SDK pro řeč zařízení usnadňuje širokou škálu scénářů hlasové služby, jako například [vlastní hlasové první virtuálních asistentů](https://aka.ms/bots/speech/va), jednotky – do řazení systémy, [konverzace určené k transkripci](conversation-transcription-service.md)a inteligentní přednášející. Může reagovat na uživatele s textem, mluvit zpět na ně ve výchozí nebo [vlastní hlasové](how-to-customize-voice-font.md), poskytovat výsledky vyhledávání [přeložit](speech-translation.md) do jiných jazyků a další. Těšíme se zobrazuje, co je vytvořit!

## <a name="get-the-speech-devices-sdk"></a>Získání sady Speech Devices SDK

### <a name="android"></a>Android

Pro Android zařízení, stáhněte si nejnovější verzi [sadou SDK pro zařízení s Androidem Řeč](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Ukázková aplikace je pro Windows k dispozici jako multiplatformní aplikace v Javě. Stáhněte si nejnovější verzi [prostředí JRE řeči Devices SDK](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená s balíček sadou SDK pro řeč a Java IDE Eclipse (v4) na Windows 64-bit. Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

### <a name="linux"></a>Linux

Ukázková aplikace je pro Linux k dispozici jako multiplatformní aplikace v Javě. Stáhněte si nejnovější verzi [prostředí JRE řeči Devices SDK](https://aka.ms/sdsdk-download-JRE).
Aplikace je sestavená s balíček sadou SDK pro řeč a Java IDE Eclipse (v4) na 64-bit Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Běží na 64bitovém prostředí Java 8 Runtime Environment (JRE).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zvolte si zařízení řeči](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
