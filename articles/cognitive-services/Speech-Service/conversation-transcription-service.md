---
title: Přepis konverzace – služba Speech
titleSuffix: Azure Cognitive Services
description: Přepis konverzace je pokročilou funkcí služeb Speech, které kombinují rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Přepisy konverzací jsou ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. to vám umožní zjistit, kdo řekl a kdy, aby se účastníci mohli soustředit na schůzku a rychle postupovat podle dalších kroků. Tato funkce také vylepšuje přístupnost. S přepisem můžete aktivně zapojit účastníky s postižením sluchu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562888"
---
# <a name="what-is-conversation-transcription"></a>Co je přepis konverzace?

Přepis konverzace je pokročilou funkcí služeb Speech, které kombinují rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Přepisy konverzací jsou ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. to vám umožní zjistit, kdo řekl a kdy, aby se účastníci mohli soustředit na schůzku a rychle postupovat podle dalších kroků. Tato funkce také vylepšuje přístupnost. S přepisem můžete aktivně zapojit účastníky s postižením sluchu.   

Přepis konverzace nabízí přesné rozpoznávání s přizpůsobitelnými modely řeči, které můžete přizpůsobit pro pochopení odvětví a slovníku konkrétní společnosti. Kromě toho můžete pro optimalizaci prostředí pro zařízení s více mikrofony párovat přepisy konverzace pomocí sady Speech Devices SDK.

>[!NOTE]
> V současné době se pro malé schůzky doporučuje přepisovat konverzaci. Pokud chcete rozšířit přepis konverzace pro velké schůzky se škálováním, kontaktujte nás prosím.

Tento diagram znázorňuje hardware, software a služby, které spolupracují s přepisem konverzace.

![Diagram přepisu importu konverzací](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Je nutné zadat kruhové pole mikrofonu s konkrétní konfigurací geometrie. Podrobnosti specifikace a návrhu najdete v tématu věnovaném [mikrofonu sady SDK pro zařízení Microsoft Speech](https://aka.ms/cts/microphone). Další informace o tom, jak si můžete koupit vývojovou sadu, najdete v tématu věnovaném [sadě SDK pro zařízení Speech](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Začínáme s přepisem konverzace

Chcete-li začít s přepisem konverzace, je třeba provést tři kroky.

1. Shromažďování hlasových ukázek od uživatelů
2. Generování profilů uživatelů pomocí ukázek uživatelského hlasu
3. Použití sady Speech SDK k identifikaci uživatelů (mluvčích) a přepisovat řeči

## <a name="collect-user-voice-samples"></a>Shromažďování uživatelských ukázek hlasu

Prvním krokem je shromažďování zvukových záznamů od jednotlivých uživatelů. Rozpoznávání řeči uživatele by se mělo zaznamenávat v tichém prostředí bez šumu na pozadí. Doporučená délka každého zvukového vzorku je mezi 30 sekundami a dvěma minutami. Delší zvukové vzorky budou mít za následek lepší přesnost při identifikaci mluvčích. Zvuk musí být mono kanál s vzorkovací frekvencí 16 KHz.

Kromě výše uvedených pokynů je způsob, jakým se záznam zvuku nahraje a je uložený na vás – je vhodná zabezpečená databáze. V další části si ukážeme, jak se tento zvuk používá ke generování profilů uživatelů používaných se sadou Speech SDK k rozpoznávání mluvčích.

## <a name="generate-user-profiles"></a>Generování profilů uživatelů

V dalším kroku budete muset odeslat zvukové nahrávky, které jste shromáždili do služby generování podpisů, abyste ověřili zvuk a vygenerovali profily uživatelů. [Služba generování podpisů](https://aka.ms/cts/signaturegenservice) je sada rozhraní REST API, která umožňuje vygenerovat a načíst profily uživatelů.

Chcete-li vytvořit profil uživatele, bude nutné použít `GenerateVoiceSignature` rozhraní API. K dispozici jsou podrobnosti specifikace a vzorový kód:

> [!NOTE]
> Přepis konverzace je aktuálně k dispozici v části "en-US" a "zh-CN" v následujících `centralus` oblastech `eastasia`: a.

* [Specifikace REST](https://aka.ms/cts/signaturegenservice)
* [Použití přepisu konverzace](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Přepisovat a Identifikujte reproduktory

Přepis konverzace očekává vícekanálový audio streamy a profily uživatelů jako vstupy pro generování přepisů a identifikaci mluvčích. Data profilu zvuku a uživatele se odesílají do služby přepisu konverzace pomocí sady Speech Devices SDK. Jak už jsme uvedli, je k použití přepisu konverzace potřeba vytvořit kruhové pole mikrofonu a sadu Speech SDK pro zařízení.

>[!NOTE]
> Podrobnosti specifikace a návrhu najdete v tématu věnovaném [mikrofonu sady SDK pro zařízení Microsoft Speech](https://aka.ms/cts/microphone). Další informace o tom, jak si můžete koupit vývojovou sadu, najdete v tématu věnovaném [sadě SDK pro zařízení Speech](https://aka.ms/cts/getsdk).

Informace o použití přepisu konverzace v sadě Speech Devices SDK najdete v článku [Jak používat přepis konverzace](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>rychlé zprovoznění s ukázkovou aplikací

Sada Microsoft Speech zařízení SDK má rychlou úvodní ukázkovou aplikaci pro všechny ukázky týkající se zařízení. Jedním z nich je přepis konverzace. Můžete ji najít v [sadě Speech SDK pro zařízení Android rychlý Start](https://aka.ms/sdsdk-quickstart) s ukázkovou aplikací a jejím zdrojovým kódem pro váš odkaz.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o sadě Speech SDK pro zařízení](speech-devices-sdk.md)
