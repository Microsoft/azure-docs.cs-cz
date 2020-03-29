---
title: Vyhodnoťte přesnost vlastní řeči – služba řeč
titleSuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak kvantitativně měřit kvalitu našeho modelu převodu řeči na text nebo vlastního modelu. Audio + člověkem označené transkripční údaje jsou vyžadovány pro přesnost testu a 30 minut až 5 hodin reprezentativního zvuku by mělo být poskytnuto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806092"
---
# <a name="evaluate-custom-speech-accuracy"></a>Vyhodnocení vlastní přesnosti řeči

V tomto dokumentu se dozvíte, jak kvantitativně měřit kvalitu modelu převodu řeči na text společnosti Microsoft nebo vlastního modelu. Audio + člověkem označené transkripční údaje jsou vyžadovány pro přesnost testu a 30 minut až 5 hodin reprezentativního zvuku by mělo být poskytnuto.

## <a name="what-is-word-error-rate-wer"></a>Co je chybová frekvence aplikace Word (WER)?

Oborový standard pro měření přesnosti modelu je *chybová rychlost aplikace Word* (WER). WER spočítá počet nesprávných slov identifikovaných během rozpoznávání, pak se vydělí celkovým počtem slov uvedených v přepisu označeném člověkem. Nakonec se toto číslo vynásobí 100 % pro výpočet WER.

![Vzorec WER](./media/custom-speech/custom-speech-wer-formula.png)

Nesprávně identifikovaná slova spadají do tří kategorií:

* Vložení (I): Slova, která jsou nesprávně přidána do přepisu hypotézy
* Odstranění (D): Slova, která nejsou v přepisu hypotézy odhalena
* Substituce (S): Slova, která byla nahrazena odkazem a hypotézou

Tady je příklad:

![Příklad nesprávně identifikovaných slov](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Řešení chyb a zlepšení WER

Wer z výsledků rozpoznávání stroje můžete použít k vyhodnocení kvality modelu, který používáte s aplikací, nástrojem nebo produktem. WER 5%-10% je považován za kvalitní a je připraven k použití. Wer 20% je přijatelné, ale možná budete chtít, aby zvážila další školení. Wer 30% nebo více signály špatnou kvalitu a vyžaduje přizpůsobení a školení.

Jak jsou chyby distribuovány, je důležité. Pokud dojde k mnoha chybám při odstraňování, je to obvykle kvůli slabé síle zvukového signálu. Chcete-li tento problém vyřešit, budete muset shromažďovat zvuková data blíže ke zdroji. Chyby vložení znamenají, že zvuk byl zaznamenán v hlučném prostředí a může být přítomen přeslech, což způsobuje problémy s rozpoznáváním. Chyby nahrazení se často vyskytují, pokud byl poskytnut nedostatečný vzorek termínů specifických pro doménu jako přepisy s lidským označením nebo související text.

Analýzou jednotlivých souborů můžete určit, jaký typ chyb existuje a které chyby jsou jedinečné pro určitý soubor. Principy problémů na úrovni souboru vám pomohou zaměřit se na vylepšení.

## <a name="create-a-test"></a>Vytvoření testu

Pokud chcete otestovat kvalitu základního modelu microsoftu pro převod řeči na text nebo vlastního modelu, který jste vycvičili, můžete porovnat dva modely vedle sebe a vyhodnotit přesnost. Porovnání zahrnuje WER a výsledky rozpoznávání. Vlastní model je obvykle porovnán se základním modelem společnosti Microsoft.

Vyhodnocení modelů vedle sebe:

1. Přihlaste se k [portálu Vlastní řeč](https://speech.microsoft.com/customspeech).
2. Přejděte **na možnost Převod řeči na text > vlastní testování > řeči**.
3. Klepněte na **tlačítko Přidat test**.
4. Vyberte **hodnotit přesnost**. Pojmenujte test, popis a vyberte audio + human-labeled transkripce datové sady.
5. Vyberte až dva modely, které chcete otestovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat výsledky vedle sebe.

## <a name="side-by-side-comparison"></a>Porovnání vedle sebe

Po dokončení testu, označené změnou stavu na *Úspěšné*, najdete číslo WER pro oba modely zahrnuté v testu. Kliknutím na název testu zobrazíte stránku podrobností o testování. Tato stránka podrobností obsahuje seznam všech projevy v datové sadě, označující výsledky rozpoznávání dvou modelů vedle přepisu z odeslané datové sady. Chcete-li zkontrolovat porovnání vedle sebe, můžete přepnout různé typy chyb, včetně vkládání, odstraňování a nahrazení. Poslechem zvuku a porovnáním výsledků rozpoznávání v každém sloupci, který zobrazuje přepis označený člověkem a výsledky dvou modelů převodu řeči na text, můžete se rozhodnout, který model vyhovuje vašim potřebám a kde jsou další školení a vylepšení Požadované.

## <a name="next-steps"></a>Další kroky

* [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
