---
title: Konverzace určené k transkripci – hlasové služby
titleSuffix: Azure Cognitive Services
description: Přepis konverzace je pokročilá funkce hlasových služeb, který kombinuje rozpoznávání řeči v reálném čase, identifikace mluvčího a diarization. Přepis konverzace je ideální pro přepisování osobně schůzky, s možností rozlišení mluvčích, upozorní vás, ale nutné dodat, co a kdy, umožňuje účastníkům zaměřit na schůzku a rychle vyřídit další kroky. Tato funkce taky zlepšuje dostupnost. S přepis může aktivně zapojit účastníky s poškozením sluchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243845"
---
# <a name="what-is-conversation-transcription"></a>Co je určené k transkripci konverzace?

Přepis konverzace je pokročilá funkce hlasových služeb, který kombinuje rozpoznávání řeči v reálném čase, identifikace mluvčího a diarization. Přepis konverzace je ideální pro přepisování osobně schůzky, s možností rozlišení mluvčích, upozorní vás, ale nutné dodat, co a kdy, umožňuje účastníkům zaměřit na schůzku a rychle vyřídit další kroky. Tato funkce taky zlepšuje dostupnost. S přepis může aktivně zapojit účastníky s poškozením sluchu.   

Konverzace určené k transkripci poskytuje přesné rozpoznávání s přizpůsobitelné modely řeči, které můžete přizpůsobit o oboru a specifické pro společnost slovník. Kromě toho můžete spárovat konverzace přepisu se sadou SDK pro řeč zařízení k optimalizaci prostředí pro více mikrofon zařízení.

>[!NOTE]
> V současné době určené k transkripci konverzace se doporučuje pro malé schůzky. Pokud chcete rozšířit konverzace přepis pro velké schůzky ve velkém měřítku, kontaktujte nás prosím.

Tento diagram znázorňuje hardwaru, softwaru a služeb, které fungují společně s určené k transkripci konverzace.

![Diagram určené k transkripci Import konverzace](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Vyžaduje se cyklické pole sedmi mikrofon s konkrétní geometrie konfigurací. Podrobnější informace o specifikaci a návrhu, naleznete v tématu [Microsoft řeči zařízení SDK mikrofon](https://aka.ms/cts/microphone). Další informace nebo zakoupit development kit, najdete v článku [získat Microsoft zařízení sadou SDK pro řeč](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Začínáme s určené k transkripci konverzace

Existují tři kroky, které potřebujete, abyste mohli začít s určené k transkripci konverzace.

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
> Přepis konverzace je aktuálně dostupné v "en US" a "zh-CN" v těchto oblastech: `centralus` a `eastasia`.

* [Specifikace REST](https://aka.ms/cts/signaturegenservice)
* [Použití přepisu konverzace](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Přepisy a identifikaci mluvčích

Konverzace určené k transkripci očekává, že vícekanálový audiostreamy a profily uživatelů jako vstupy pro generování přepisů a identifikaci mluvčích. Zvuk a uživatelská data profilu se odesílají do určené k transkripci konverzace služby pomocí sady SDK zařízení řeči. Jak už jsme zmínili cyklické pole sedmi mikrofon a sadou SDK pro řeč zařízení je potřeba použít určené k transkripci konverzace.

>[!NOTE]
> Podrobnější informace o specifikaci a návrhu, naleznete v tématu [Microsoft řeči zařízení SDK mikrofon](https://aka.ms/cts/microphone). Další informace nebo zakoupit development kit, najdete v článku [získat Microsoft zařízení sadou SDK pro řeč](https://aka.ms/cts/getsdk).

Další informace o použití přepisu konverzace se sadou SDK pro řeč zařízení, najdete v článku [použití přepisu konverzace](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Rychlý Start s ukázkovou aplikací

Microsoft řeči zařízení SDK obsahuje ukázkové aplikace rychlý start pro všechna zařízení související ukázky. Přepis konverzace je jedním z nich. Najdete ho v [sadou SDK pro řeč zařízení s androidem quickstart](https://aka.ms/sdsdk-quickstart) s ukázkovou aplikaci a jeho zdrojový kód pro vaši informaci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o zařízení sadou SDK pro řeč](speech-devices-sdk.md)
