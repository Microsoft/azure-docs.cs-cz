---
title: Trénování modelu pro Custom Speech – hlasové služby
titlesuffix: Azure Cognitive Services
description: Školení řeči na text je potřeba zvýšit přesnost rozpoznávání pro základní model od Microsoftu nebo vlastního modelu, který se chystáte vytvořit. Model se trénuje pomocí přepisů lidských s názvem bez přípony a související text. Tyto datové sady společně s dříve odeslaný zvukových dat, se používají k upřesnění a trénování modelu řeči na text rozpoznat slova, frází, zkratky, názvy a další podmínky specifické pro produkt.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49195efa54c8e6eca9186a9e2fc33da84ff34413
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625387"
---
# <a name="train-a-model-for-custom-speech"></a>Trénování modelu pro Custom Speech

Školení řeči na text je potřeba zvýšit přesnost rozpoznávání pro základní model od Microsoftu nebo vlastního modelu, který se chystáte vytvořit. Model se trénuje pomocí přepisů lidských s názvem bez přípony a související text. Tyto datové sady společně s dříve odeslaný zvukových dat, se používají k upřesnění a trénování modelu řeči na text rozpoznat slova, frází, zkratky, názvy a další podmínky specifické pro produkt. Více domény datových sad, které poskytují (data, která souvisí s co budou uživatelé prohlašují a čekáte rozpoznat), přesnější bude váš model, což vede k lepší rozpoznávání. Mějte na paměti, která tak, že nesouvisející data do trénování, můžete snížit nebo nepříznivě ovlivnit přesnost modelu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Použít při řešení problémů přesnost školení

Pokud máte problémy s rozpoznávání obsahující váš model, pomocí lidských s názvem bez přípony záznamy o studiu a související data pro další školení může pomoct zlepšit přesnost. Můžete určit, které datové sadě používat k adresování vašich problémů v této tabulce:

| Případ použití | Typ dat |
|----------|-----------|
| Zlepšit přesnost rozpoznávání na slovník specifických pro dané odvětví a gramatiku, jako jsou lékařské terminologie nebo žargonu IT | Text související s (věty/projevy) |
| Definujte zapsané ve fonetické a zobrazují se formu slovo nebo termín, který má nestandardní výslovnost, jako jsou názvy produktů nebo zkratky. | Text související s (výslovnost) |
| Zlepšit přesnost rozpoznávání na mluvený styly, zvýraznění nebo dalších zvuků z konkrétní na pozadí | Zvuk + lidských s názvem bez přípony záznamy o studiu |
> [!IMPORTANT]
> Pokud jste nenahráli datové sady, získáte [přípravy a testování datových](how-to-custom-speech-test-data.md). Tento dokument obsahuje pokyny pro nahrání dat a pokyny pro vytváření vysoce kvalitních datové sady.

## <a name="train-and-evaluate-a-model"></a>Natrénování a vyhodnocení modelu

Prvním krokem k natrénování modelu je nahrát trénovací data. Použití [přípravy a testování datových](how-to-custom-speech-test-data.md) pokyny krok za krokem přípravy přepisů lidských s názvem bez přípony a související text (projevy a výslovnosti). Až nahrajete trénovacích dat, postupujte podle těchto pokynů spusťte trénování modelu:

1. Přejděte do **Speech to text > Custom Speech > školení**.
2. Klikněte na tlačítko **Train model**.
3. Dále poskytnout trénování **název** a **popis**.
4. Z **scénář a základní model** rozevírací nabídky vyberte scénář, který nejlépe vyhovuje požadavkům vaší domény. Pokud si nejste jisti scénář, který chcete vybrat, vybrat **Obecné**. Model směrného plánu je výchozím bodem pro vzdělávání. Pokud nemáte k dispozici předvolbu, můžete použít na nejnovější verzi.
5. Z **vyberte trénovacích dat** zvolte jeden nebo více přepisu zvuku + lidských s názvem bez přípony datových sad, které chcete použít k trénování.
6. Po dokončení se školení můžete provádět testování na nově trénovaného modelu přesnost. Tento krok je volitelný.
7. Vyberte **vytvořit** k vytvoření vlastního modelu.

Školení tabulce nový záznam, který odpovídá na tuto nově vytvořenou modelu. V tabulce se také zobrazí stav:  Zpracování, bylo úspěšné, neúspěšné.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Vyhodnotit jeho přesnost trénovaného modelu

Můžete kontrolovat data a vyzkoušejte přesnost modelu pomocí těchto dokumentů:

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)


Pokud jste se rozhodli testování přesnosti, je důležité vybrat akustický datové sady, která je jiná než ty, které jste použili obsahující váš model k získáte realistické představu o výkonu modelu.

## <a name="next-steps"></a>Další postup

* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další materiály

* [Připravit a otestovat vašich dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
