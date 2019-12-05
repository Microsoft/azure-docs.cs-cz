---
title: Vyhodnotit přesnost pro službu Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak kvantitativní měření kvality našeho modelu řeči (Speech-to-text) nebo vlastního modelu. Pro testování přesnosti se vyžadují data přepisu a přepisu, které by měly být k dispozici po dobu 30 až 5 hodin reprezentativního zvuku.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806092"
---
# <a name="evaluate-custom-speech-accuracy"></a>Vyhodnotit přesnost Custom Speech

V tomto dokumentu se dozvíte, jak kvantitativní měření kvality modelu řeči od Microsoftu nebo vlastního modelu. Pro testování přesnosti se vyžadují data přepisu a přepisu, které by měly být k dispozici po dobu 30 až 5 hodin reprezentativního zvuku.

## <a name="what-is-word-error-rate-wer"></a>Co je četnost chyb ve Wordu (WER)?

Standardní hodnota pro měření přesnosti modelu je *počet chyb v aplikaci* (WER). Služba WER počítá počet nesprávných slov identifikovaných během rozpoznávání a pak je vydělí celkovým počtem slov poskytnutých v přepisu popisku. Nakonec se toto číslo vynásobí 100% za účelem výpočtu WER.

![Vzorec WER](./media/custom-speech/custom-speech-wer-formula.png)

Nesprávně identifikovaná slova spadají do tří kategorií:

* Vložení (I): slova, která jsou nesprávně přidána do přepisu hypotéz
* Odstranění (D): slova, která jsou v přepisu hypotéza nerozpoznána
* Nahrazování: slova, která byla nahrazena mezi odkazem a hypotézou

Tady je příklad:

![Příklad nesprávně identifikovaných slov](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Řešení chyb a vylepšení WER

Pomocí služby WER z výsledků rozpoznávání počítačů můžete vyhodnotit kvalitu modelu, který používáte, pro aplikaci, nástroj nebo produkt. WER 5% až 10% se považuje za dobrou kvalitu a je připravená k použití. Je přijatelné akceptovat 20%, ale možná budete chtít zvážit další školení. WER na více než 30% signalizuje špatné kvality a vyžaduje přizpůsobení a školení.

Důležité je, jak jsou distribuované chyby. V případě, že dojde k mnoha chybám při odstraňování, většinou se jedná o slabý signál zvukové signalizace. Pokud chcete tento problém vyřešit, budete muset shromáždit zvuková data blíž ke zdroji. Chyby vkládání znamenají, že zvuk byl zaznamenán v prostředí s vysokou úrovní šumu a Crosstalk může být k dispozici, což způsobuje problémy s rozpoznáváním. K chybám nahrazení dochází často v případě, že se nedostatečná ukázka podmínek specifických pro doménu poskytla jako přepisy nebo související text.

Analýzou jednotlivých souborů můžete určit, jaký typ chyb existuje a které chyby jsou jedinečné pro určitý soubor. Porozumění problémům na úrovni souborů vám pomůže zaměřit se na vylepšení.

## <a name="create-a-test"></a>Vytvoření testu

Pokud chcete testovat kvalitu modelu standardních hodnot řeči od Microsoftu nebo vlastního modelu, který jste si vyzkoušeli, můžete porovnat dva modely vedle sebe a vyhodnotit tak přesnost. Porovnávání zahrnuje výsledky WER a rozpoznávání. Vlastní model se obvykle porovnává se základními modely Microsoftu.

Chcete-li vyhodnotit modely vedle sebe:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. **> Custom Speech > testování**, přejděte na text na řeč.
3. Klikněte na **Přidat test**.
4. Vyberte možnost **vyhodnotit přesnost**. Dejte testu název, popis a vyberte si zvukovou datovou sadu přepisu a s popiskem.
5. Vyberte až dva modely, které chcete testovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat výsledky vedle sebe.

## <a name="side-by-side-comparison"></a>Souběžné porovnání

Po dokončení testu, který je označen změnou stavu na *úspěch*, najdete číslo wer pro oba modely zahrnuté v testu. Kliknutím na název testu zobrazíte stránku s podrobnostmi o testování. Tato stránka podrobností obsahuje seznam všech projevy ve vaší datové sadě, které označují výsledky rozpoznávání dvou modelů společně s přepisem z odeslané datové sady. Chcete-li zkontrolovat souběžné porovnání, můžete přepínat různé typy chyb včetně vložení, odstranění a nahrazování. Díky poslechu zvuku a porovnání výsledků rozpoznávání v každém sloupci, který ukazuje přepis a výsledky pro dva modely řeči a textu, můžete rozhodnout, který model vyhovuje vašim potřebám, a kde jsou další školení a vylepšení. požadovanou.

## <a name="next-steps"></a>Další kroky

* [Výuka modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje informací:

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
