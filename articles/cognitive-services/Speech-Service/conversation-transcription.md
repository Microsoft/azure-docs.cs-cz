---
title: Přepis konverzace (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Přepis konverzace je řešení pro schůzky, které kombinuje rozpoznávání, ID mluvčího a diarization, aby poskytovala přepis jakékoli konverzace.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81402519"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Co je přepis konverzace na schůzkách (Preview)?

Přepis konverzace je řešení převodu [řeči na text](speech-to-text.md) , které kombinuje rozpoznávání řeči, identifikaci mluvčího a přidaných vět ke každému mluvčímu (označuje se také jako _diarization_), aby poskytovala v reálném čase a/nebo asynchronní přepis jakékoli konverzace. Přepis konverzace rozlišuje mluvčí v konverzacích a určí, kdo, co a kdy a usnadňuje vývojářům přidávání řeči k textu do aplikací, které provádějí diarization více mluvčích.

## <a name="key-features"></a>Klíčové funkce

- **Časová razítka** – každé mluvčí utterance má časové razítko, abyste mohli snadno najít, kdy se fráze říká.
- **Čitelná přepisy** – přepisy mají automaticky přidané formátování a interpunkci, aby text přesně odpovídal tomu, co bylo zmíněno.
- **Uživatelské profily** – profily uživatelů jsou generovány shromažďováním ukázek uživatelského hlasu a jejich odesláním do generování podpisu.
- **Identifikace mluvčího** – mluvčí se identifikují pomocí uživatelských profilů a každý z nich se přiřadí _identifikátor mluvčího_ .
- **Diarization pro více mluvčích** – určení toho, kdo říká, co vysyntetizuje zvukový stream s každým identifikátorem mluvčího.
- **Přepis v reálném čase** – poskytněte živý přepis, který říká, co a kdy se konverzace děje.
- **asynchronní přepis** – poskytují přepisy s vyšší přesností pomocí zvukového streamu ve vícekanálovém formátu.

> [!NOTE]
> I když přepis konverzace neomezuje počet mluvčích v místnosti, je optimalizován pro 2-10 mluvčích na relaci.

## <a name="use-cases"></a>Případy použití

### <a name="inclusive-meetings"></a>Uzavřené schůzky

Chcete-li vytvořit schůzky, které jsou pro všechny, jako jsou neslyšící a sluchové slyšení, je důležité mít v reálném čase přepis. Přepis konverzace v režimu v reálném čase přijímá zvuk a určuje, kdo říká, co říkají, aby všichni účastníci schůzky mohli postupovat podle přepisu a zúčastnit se schůzky bez prodlení.

### <a name="improved-efficiency"></a>Vyšší efektivita

Účastníci schůzky se můžou soustředit na schůzku a nechat si pořizovat poznámky k přepisu konverzace. Účastníci se můžou aktivně zapojit do schůzky a rychle se řídit podle dalších kroků, a to pomocí přepisu namísto pořizování poznámek a potenciálně chybějících něco během schůzky.

## <a name="how-it-works"></a>Jak to funguje

Toto je podrobný přehled toho, jak funguje přepis konverzace.

![Diagram přepisu importu konverzací](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Očekávané vstupy

- **Audio stream s více kanály** – pro specifikace a podrobné informace o návrhu najdete informace v tématu věnovaném [mikrofonu sady SDK pro zařízení Microsoft Speech](https://aka.ms/cts/microphone). Další informace o tom, jak si můžete koupit vývojovou sadu, najdete v tématu věnovaném [sadě SDK pro zařízení Speech](https://aka.ms/cts/getsdk).
- **Ukázky uživatelských hlasu** – přepis konverzace potřebuje profily uživatelů před konverzací. Budete potřebovat shromažďovat zvukové nahrávky od každého uživatele a pak je odeslat do [služby pro generování podpisů](https://aka.ms/cts/signaturegenservice) , abyste ověřili zvuk a vygenerovali profily uživatelů.

## <a name="real-time-vs-asynchronous"></a>Vs. asynchronní v reálném čase

Přepis konverzace nabízí tři režimy přepisu:

### <a name="real-time"></a>Reálný čas

Zvuková data se zpracovávají živě, aby se vracel identifikátor mluvčího a přepis. Tento režim vyberte, pokud vaše požadavek na přepis řešení vyžaduje, aby účastníci konverzace měli k dispozici přehled o probíhajících konverzacích za živý přepis. Například sestavíte aplikaci, aby se schůzky lépe přizpůsobily neslyšící a byli postižení účastníci sluchu, což je ideální případ použití pro přepis v reálném čase.

### <a name="asynchronous"></a>Asynchronní

Zvuková data jsou zpracována dávkou, která vrací identifikátor mluvčího a přepis. Tento režim vyberte, pokud váš požadavek na přepis řešení vyžaduje zajištění vyšší přesnosti bez zobrazení živého přepisu. Například pokud chcete vytvořit aplikaci, která účastníkům schůzky umožní snadno zachytit zmeškané schůzky, použijte režim asynchronního přepisu, abyste získali přesnější výsledky v přepisu.

### <a name="real-time-plus-asynchronous"></a>Asynchronní plus v reálném čase

Zvuková data se zpracovávají živě, aby se vracel identifikátor mluvčího a přepisu, a navíc se vytvoří požadavek, který také získá přepis s vysokou přesností prostřednictvím asynchronního zpracování. Tento režim vyberte, pokud vaše aplikace potřebuje přepis v reálném čase, ale také vyžaduje přesnější přepis pro použití po konverzaci nebo schůzi.

## <a name="language-support"></a>Podpora jazyků

Přepis konverzace v současné době podporuje "en-US" a "zh-CN" v následujících oblastech: *centralus* a *eastasia*. Pokud požadujete další podporu národního prostředí, obraťte se na pracovníka funkce v rámci [konverzace](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přepisovat konverzací v reálném čase](how-to-use-conversation-transcription-service.md)
