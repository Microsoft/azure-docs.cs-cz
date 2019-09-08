---
title: Výukový model pro službu Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Školení řeči na text je nezbytné ke zlepšení přesnosti rozpoznávání pro základní model společnosti Microsoft nebo pro vlastní model, který plánujete vytvořit. Model je vyškolený pomocí přepisů a souvisejících textů v popisku. Tyto datové sady spolu s dříve nahranými zvukovými daty se používají k upřesnění a výukovém modelu převodu řeči na text pro rozpoznávání slov, frází, zkratek, názvů a dalších podmínek pro konkrétní produkt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 21a0cd5e257f57a9371a30c6fe57afb88e174b53
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801455"
---
# <a name="train-a-model-for-custom-speech"></a>Výuka modelu pro Custom Speech

Školení řeči na text je nezbytné ke zlepšení přesnosti rozpoznávání pro základní model společnosti Microsoft nebo pro vlastní model, který plánujete vytvořit. Model je vyškolený pomocí přepisů a souvisejících textů v popisku. Tyto datové sady spolu s dříve nahranými zvukovými daty se používají k upřesnění a výukovém modelu převodu řeči na text pro rozpoznávání slov, frází, zkratek, názvů a dalších podmínek pro konkrétní produkt. Čím více doménových datových sad zadáte (data související s tím, co uživatelé říkají a co očekáváte), tím přesnější bude váš model, což vede k lepšímu rozpoznávání. Mějte na paměti, že díky krmení nesouvisejících dat s vaším školením můžete snížit nebo snížit přesnost modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Řešení problémů s přesností pomocí školení

Pokud narazíte na problémy s rozpoznáváním pro váš model, může vám použití přepisů a souvisejících dat pro další školení pomoci zlepšit přesnost. Pomocí této tabulky můžete určit, kterou datovou sadu použít k vyřešení těchto problémů:

| Případ použití | Datový typ |
|----------|-----------|
| Vylepšete přesnost rozpoznávání v oboru a gramatikě specifické pro konkrétní odvětví, jako je lékařské terminologie nebo IT žargonu | Související text (věty/projevy) |
| Definujte fonetickou a zobrazovanou formu slova nebo termínu, který má nestandardní výslovnost, například názvy produktů nebo akronymy. | Související text (výslovnost) |
| Zlepšení přesnosti rozpoznávání pro mluvené styly, zvýraznění nebo konkrétní šum na pozadí | Audio + přepisy s popiskem |
> [!IMPORTANT]
> Pokud jste neodeslali datovou sadu, přečtěte si téma [Příprava a testování dat](how-to-custom-speech-test-data.md). Tento dokument poskytuje pokyny pro nahrávání dat a pokyny pro vytváření vysoce kvalitních datových sad.

## <a name="train-and-evaluate-a-model"></a>Výuka a vyhodnocení modelu

Prvním krokem při výuce modelu je odeslání školicích dat. Použijte [přípravu a testování dat](how-to-custom-speech-test-data.md) pro podrobné pokyny k přípravě přepisů a souvisejících textu (projevy a výslovnosti) s lidským označením. Po nahrání školicích dat postupujte podle těchto pokynů a spusťte školení modelu:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. Přejít na **> převodu řeči na text Custom Speech > školení**.
3. Klikněte na **výuka model**.
4. V dalším kroku poskytněte školení **název** a **Popis**.
5. V rozevírací nabídce **scénář a model standardních hodnot** vyberte scénář, který nejlépe odpovídá vaší doméně. Pokud si nejste jisti, který scénář zvolíte, vyberte možnost **Obecné**. Základní model je výchozím bodem pro školení. Pokud nemáte předvolbu, můžete použít nejnovější.
6. Na stránce **Vybrat školicí data** vyberte jednu nebo více zvukových a uživatelem označených datových sad, které chcete použít pro školení.
7. Po dokončení školení se můžete rozhodnout pro provádění testování přesnosti u nově vyučeného modelu. Tento krok je volitelný.
8. Vyberte **vytvořit** a sestavte vlastní model.

V tabulce školení se zobrazí nová položka, která odpovídá nově vytvořenému modelu. V tabulce se zobrazí také stav:  Zpracování, úspěšné, neúspěšné.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Vyhodnotit přesnost vyučeného modelu

Můžete kontrolovat data a vyhodnocovat přesnost modelu pomocí těchto dokumentů:

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)


Pokud se rozhodnete vyzkoušet přesnost testování, je důležité vybrat akustickou datovou sadu, která se liší od ta, kterou jste použili u svého modelu, abyste získali realistickou představu o výkonu modelu.

## <a name="next-steps"></a>Další kroky

* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Výuka modelu](how-to-custom-speech-train-model.md)
