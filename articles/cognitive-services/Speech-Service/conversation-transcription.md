---
title: Přepis konverzace (náhled) – služba řeči
titleSuffix: Azure Cognitive Services
description: Přepis konverzace je řešení pro schůzky, které kombinuje rozpoznávání, ID mluvčího a diarizaci, aby poskytlpřepis jakékoli konverzace.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: ba56c7fb989658195f6394c7390c4f83027c7c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479735"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Co je přepis konverzace na schůzkách (náhled)?

Přepis konverzace je řešení [převodu řeči na text,](speech-to-text.md) které kombinuje rozpoznávání řeči, identifikaci mluvčího a přiřazení věty každému řečníkovi (označované také jako _diarizace)_ a poskytuje tak v reálném čase a/nebo asynchronní přepis jakékoli konverzace. Přepis konverzace rozlišuje řečníky v konverzaci, aby zjistil, kdo co řekl co a kdy, a usnadňuje vývojářům přidávání řeči na text do svých aplikací, které provádějí diarizaci více reproduktorů.

## <a name="key-features"></a>Klíčové funkce

- **Časová razítka** - každý mluvčí utterance má časové razítko, takže můžete snadno najít, když byla řečfráze.
- **Čitelné přepisy** - přepisy mají formátování a interpunkci automaticky přidané, aby se zajistilo, že text úzce odpovídá tomu, co bylo řečeno.
- **Profily uživatelů** - uživatelské profily jsou generovány sběrem hlasových vzorků uživatelů a jejich odesláním generování podpisu.
- **Identifikace mluvčího** - reproduktory jsou identifikovány pomocí uživatelských profilů a každému z nich je přiřazen _identifikátor reproduktoru._
- **Diarizace více reproduktorů** - zjistěte, kdo co řekl syntetizovat zvukový proud s každým identifikátorem reproduktoru.
- **Přepis v reálném čase** - poskytněte živé přepisy toho, kdo říká, co a kdy se konverzace děje.
- **asynchronní přepis** – poskytují přepisy s vyšší přesností pomocí vícekanálového zvukového proudu.

> [!NOTE]
> Přestože přepis konverzace neomezuje počet reproduktorů v místnosti, je optimalizován pro 2-10 reproduktorů na relaci.

## <a name="use-cases"></a>Případy použití

### <a name="inclusive-meetings"></a>Inkluzivní schůzky

Chcete-li, aby setkání zahrnovala všechny, například pro neslyšící a nedoslýchavé účastníky, je důležité mít přepis v reálném čase. Přepis konverzace v režimu v reálném čase přebírá zvuk schůzky a určuje, kdo co říká, což umožňuje všem účastníkům schůzky sledovat přepis a účastnit se schůzky bez prodlení.

### <a name="improved-efficiency"></a>Vyšší efektivita

Účastníci schůzky se mohou soustředit na schůzku a ponechat poznámku na přepis konverzace. Účastníci se mohou aktivně zapojit do schůzky a rychle navázat na další kroky, používat přepis namísto pořizování poznámek a potenciálně něco během schůzky chybět.

## <a name="how-it-works"></a>Jak to funguje

Toto je přehled na vysoké úrovni o tom, jak funguje přepis konverzace.

![Diagram přepisu importu konverzace](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Očekávané vstupy

- **Vícekanálový zvukový proud** – podrobnosti o specifikacích a návrhu naleznete v [tématu Microsoft Speech Device SDK Microphone](https://aka.ms/cts/microphone). Další informace nebo zakoupení vývojové sady naleznete v [tématu Získání sady Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Ukázky hlasu uživatele** – Přepis konverzace potřebuje profily uživatelů před konverzací. Budete muset shromažďovat zvukové nahrávky od každého uživatele a poté je odeslat do [služby Generování podpisu,](https://aka.ms/cts/signaturegenservice) abyste ověřili zvuk a vygenerovali uživatelské profily.

## <a name="real-time-vs-asynchronous"></a>V reálném čase vs. asynchronní

Přepis konverzace nabízí tři režimy přepisu:

### <a name="real-time"></a>Reálný čas

Zvuková data jsou zpracovávána živě, aby se vrátil identifikátor reproduktoru + přepis. Tento režim vyberte, pokud je požadavkem na řešení přepisu poskytnout účastníkům konverzace živé zobrazení živého přepisu jejich probíhající konverzace. Například vytvoření aplikace, aby se schůzky přístupnější neslyšící a nedoslýchaví účastníci je ideální případ použití pro real-time přepis.

### <a name="asynchronous"></a>Asynchronní

Zvuková data jsou dávkově zpracována, aby se vrátil identifikátor mluvčího a přepis. Tento režim vyberte, pokud je požadavkem na řešení přepisu poskytnout vyšší přesnost bez zobrazení živého přepisu. Například pokud chcete vytvořit aplikaci, která umožní účastníkům schůzky snadno dohnat zmeškané schůzky, pak použijte režim asynchronního přepisu získat výsledky přepisu s vysokou přesností.

### <a name="real-time-plus-asynchronous"></a>V reálném čase plus asynchronní

Zvuková data jsou zpracována živě vrátit identifikátor reproduktoru + přepis, a navíc je vytvořen požadavek také získat vysokou přesnost přepis prostřednictvím asynchronního zpracování. Tento režim vyberte, pokud vaše aplikace potřebuje přepis v reálném čase, ale také vyžaduje přepis vyšší přesnosti pro použití po konverzaci nebo schůzce.

## <a name="language-support"></a>Podpora jazyků

V současné době, Přepis konverzace podporuje "en-US" a "zh-CN" v následujících oblastech: *centralus* a *eastasia*. Pokud požadujete další podporu národního prostředí, obraťte se na [tým funkcí přepisu konverzace](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přepiskonverzace v reálném čase](how-to-use-conversation-transcription-service.md)
