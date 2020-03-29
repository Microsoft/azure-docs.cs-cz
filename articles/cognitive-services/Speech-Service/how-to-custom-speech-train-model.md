---
title: Trénování modelu pro vlastní řeč - služba řeči
titleSuffix: Azure Cognitive Services
description: Školení modelu převodu řeči na text může zlepšit přesnost rozpoznávání pro základní model společnosti Microsoft nebo vlastní model. Model je trénován pomocí přepisů s lidským označením a souvisejícího textu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137775"
---
# <a name="train-a-model-for-custom-speech"></a>Trénování modelu pro vlastní řeč

Školení modelu převodu řeči na text může zlepšit přesnost rozpoznávání pro základní model společnosti Microsoft. Model je trénován pomocí přepisů s lidským označením a souvisejícího textu. Tyto datové sady spolu s dříve nahranými zvukovými daty se používají k upřesnění a trénování modelu převodu řeči na text tak, aby rozpoznal slova, fráze, zkratky, názvy a další termíny specifické pro daný produkt. Čím více datových sad v doméně zadáte (data související s tím, co uživatelé řeknou a co očekáváte, že poznáte), tím přesnější bude váš model, což vede k lepšímu rozpoznávání. Mějte na paměti, že tím, že do tréninku nanesete nesouvisející data, můžete snížit nebo poškodit přesnost vašeho modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Řešení problémů s přesností pomocí školení

Pokud se setkáte s problémy s rozpoznáváním modelu, může použití přepisů s lidským označením a souvisejících dat pro další školení pomoci zlepšit přesnost. Tato tabulka slouží k určení, kterou datovou sadu použít k řešení problémů:

| Případ použití | Datový typ |
| -------- | --------- |
| Zlepšete přesnost rozpoznávání slovní zásoby a gramatiky specifické pro dané odvětví, jako je lékařská terminologie nebo žargon IT. | Související text (věty/projevy) |
| Definujte fonetický a zobrazený formulář slova nebo termínu, který má nestandardní výslovnost, například názvy produktů nebo zkratky. | Související text (výslovnost) |
| Zlepšete přesnost rozpoznávání stylů mluvení, přízvuků nebo specifických zvuků na pozadí. | Audio + přepisy označené člověkem |

> [!IMPORTANT]
> Pokud jste nenahráli sadu dat, přečtěte si informace [o přípravě a testování dat](how-to-custom-speech-test-data.md). Tento dokument obsahuje pokyny pro nahrávání dat a pokyny pro vytváření vysoce kvalitních datových sad.

## <a name="train-and-evaluate-a-model"></a>Trénování a vyhodnocení modelu

Prvním krokem k trénování modelu je nahrání trénovacích dat. Pomocí [funkce Příprava a testování dat](how-to-custom-speech-test-data.md) pro podrobné pokyny k přípravě přepisů s lidským označením a souvisejícího textu (projevy a výslovnosti). Po nahrání trénovacích dat začněte trénování modelu podle těchto pokynů:

1. Přihlaste se k [portálu Vlastní řeč](https://speech.microsoft.com/customspeech).
2. Přejděte **na > vlastní výuku řeči > řeči**.
3. Klepněte na **položku Model vlaku**.
4. Dále uveďte svůj trénink **název** a **popis**.
5. V rozevírací nabídce **Scénář a Model směrného plánu** vyberte scénář, který nejlépe vyhovuje vaší doméně. Pokud si nejste jisti, který scénář zvolit, vyberte **obecné**. Základní model je výchozím bodem pro školení. Pokud nemáte předvolbu, můžete použít nejnovější.
6. Na stránce **Vybrat trénovací data** zvolte jednu nebo více datových sad přepisů s označením člověk, které chcete použít pro trénování.
7. Po dokončení školení můžete provést testování přesnosti na nově trénovaném modelu. Tento krok je volitelný.
8. Chcete-li vytvořit vlastní model, vyberte **vytvořit.**

Tabulka Školení zobrazí novou položku, která odpovídá tomuto nově vytvořenému modelu. V tabulce se také zobrazí stav: Zpracování, Úspěšné, Neúspěšné.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Vyhodnocení přesnosti trénovaného modelu

Data můžete zkontrolovat a vyhodnotit přesnost modelu pomocí těchto dokumentů:

- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)

Pokud jste se rozhodli otestovat přesnost, je důležité vybrat akustickou datovou sadu, která se liší od té, kterou jste použili s modelem, abyste získali realistický pocit výkonu modelu.

## <a name="next-steps"></a>Další kroky

- [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

- [Příprava a testování dat](how-to-custom-speech-test-data.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
- [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
