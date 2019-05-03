---
title: Konverzace služby určené k transkripci – hlasové služby
titleSuffix: Azure Cognitive Services
description: Služby určené k transkripci konverzace je pokročilá funkce hlasových služeb, který kombinuje rozpoznávání řeči v reálném čase, identifikace mluvčího a diarization. Služby určené k transkripci konverzace je ideální pro přepisování osobně schůzky, s možností rozlišení mluvčích, upozorní vás, ale nutné dodat, co a kdy, umožňuje účastníkům zaměřit na schůzku a rychle vyřídit další kroky. Tato funkce taky zlepšuje dostupnost. S přepis může aktivně zapojit účastníky s poškozením sluchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025932"
---
# <a name="what-is-the-conversation-transcription-service"></a>Co je služba určené k transkripci konverzace?

Služby určené k transkripci konverzace je pokročilá funkce hlasových služeb, který kombinuje rozpoznávání řeči v reálném čase, identifikace mluvčího a diarization. Služby určené k transkripci konverzace je ideální pro přepisování osobně schůzky, s možností rozlišení mluvčích, upozorní vás, ale nutné dodat, co a kdy, umožňuje účastníkům zaměřit na schůzku a rychle vyřídit další kroky. Tato funkce taky zlepšuje dostupnost. S přepis může aktivně zapojit účastníky s poškozením sluchu.   

Konverzace služby určené k transkripci poskytuje přesné rozpoznávání s přizpůsobitelné modely řeči, které můžete přizpůsobit o oboru a specifické pro společnost slovník. Kromě toho můžete spárovat konverzace služby určené k transkripci se sadou SDK pro řeč zařízení k optimalizaci prostředí pro více mikrofon zařízení.

>[!NOTE]
> Konverzace služby určené k transkripci v současné době se doporučuje pro malé schůzky. Pokud chcete rozšířit služby určené k transkripci konverzace pro velké schůzky ve velkém měřítku, kontaktujte nás prosím.

Tento diagram znázorňuje hardware, software a služby, které fungují společně se službou určené k transkripci konverzace.

![Diagram služby určené k transkripci konverzace importu](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Vyžaduje se cyklické pole sedmi mikrofon s konkrétní geometrie konfigurací. Podrobnější informace o specifikaci a návrhu, naleznete v tématu [Microsoft řeči zařízení SDK mikrofon](https://aka.ms/cts/microphone). Další informace nebo zakoupit development kit, najdete v článku [získat Microsoft zařízení sadou SDK pro řeč](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Začínáme s určené k transkripci konverzace

Existují tři kroky, které potřebujete, abyste mohli začít se službou určené k transkripci konverzace.

1. Hlasové ukázek shromážděte od uživatelů.
2. Generovat profily uživatelů pomocí ukázky hlas uživatele
3. Využijte sadou SDK pro řeč k identifikaci uživatelů (přednášející) a přepisy řeči

## <a name="collect-user-voice-samples"></a>Shromáždit vzorky hlas uživatele

Prvním krokem je shromažďovat zvukové záznamy z každého uživatele. Uživatel řeči se zaznamená v tichém prostředí bez šum na pozadí. Doporučená délka každé zvukové ukázce je 30 sekund až dvě minuty. Delší zvukové vzorky způsobí zpřesnění při identifikaci mluvčích. Mono kanál s 16 KHz vzorkovací frekvence musí být zvuk.

Nad rámec výše uvedené informace se zvuk se zaznamenávají a ukládají se jenom na vás – doporučuje zabezpečené databázi. V další části si probereme, jak zvuková slouží ke generování profily uživatelů, které se používají se sadou SDK pro řeč rozpoznat mluvčí.

## <a name="generate-user-profiles"></a>Generování profilů uživatelů

V dalším kroku budete muset odeslat zvukové záznamy jste shromažďují ve službě generování podpis ověřit zvuk a vygenerovat uživatelské profily. [Služba generování podpis](https://aka.ms/cts/signaturegenservice) představuje sadu rozhraní REST API, díky kterým můžou generovat a načítat profily uživatelů.

Vytvoření uživatelského profilu, budete muset použít `GenerateVoiceSignature` rozhraní API. Specifikace podrobností a ukázky kódu jsou k dispozici:

> [!NOTE]
> Služba určené k transkripci konverzace je nyní dostupná v těchto oblastech: `centralus` a `eastasia`.

* [Specifikace REST](https://aka.ms/cts/signaturegenservice)
* [K používání služby určené k transkripci konverzace](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Přepisy a identifikaci mluvčích

Jako vstupy pro generování přepisů a identifikaci mluvčích očekává služba určené k transkripci konverzace vícekanálový audiostreamy a uživatelských profilů. Zvuk a uživatelská data profilu jsou odesílány ke službě určené k transkripci konverzace pomocí sadou SDK pro řeč zařízení. Jak už jsme zmínili cyklické pole sedmi mikrofon a sadou SDK pro řeč zařízení je potřeba pomocí služby určené k transkripci konverzace.

>[!NOTE]
> Podrobnější informace o specifikaci a návrhu, naleznete v tématu [Microsoft řeči zařízení SDK mikrofon](https://aka.ms/cts/microphone). Další informace nebo zakoupit development kit, najdete v článku [získat Microsoft zařízení sadou SDK pro řeč](https://aka.ms/cts/getsdk).

Další informace o použití služby určené k transkripci konverzace se sadou SDK pro řeč zařízení, najdete v článku [k používání služby určené k transkripci konverzace](https://aka.ms/cts/howto).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o zařízení sadou SDK pro řeč](speech-devices-sdk.md)
