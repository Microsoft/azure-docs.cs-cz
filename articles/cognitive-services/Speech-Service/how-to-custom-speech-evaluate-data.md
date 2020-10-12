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
ms.openlocfilehash: cadbe79bbe0af2b5cebacb3d0c7c4e910fc7dbb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85856834"
---
# <a name="evaluate-custom-speech-accuracy"></a>Vyhodnocení přesnosti služby Custom Speech

V tomto dokumentu se dozvíte, jak kvantitativní měření kvality modelu řeči od Microsoftu nebo vlastního modelu. Pro testování přesnosti se vyžadují data přepisu a přepisu, které by měly být k dispozici po dobu 30 až 5 hodin reprezentativního zvuku.

## <a name="what-is-word-error-rate-wer"></a>Co je četnost chyb ve Wordu (WER)?

Standardní hodnota pro měření přesnosti modelu je *počet chyb v aplikaci* (WER). Služba WER počítá počet nesprávných slov zjištěných během rozpoznávání a pak je vydělí celkovým počtem slov poskytnutých v rámci přepisu popisku (viz obrázek N). Nakonec se toto číslo vynásobí 100% za účelem výpočtu WER.

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

## <a name="create-a-test"></a>Vytvořit test

Pokud chcete testovat kvalitu modelu standardních hodnot řeči od Microsoftu nebo vlastního modelu, který jste si vyzkoušeli, můžete porovnat dva modely vedle sebe a vyhodnotit tak přesnost. Porovnávání zahrnuje výsledky WER a rozpoznávání. Vlastní model se obvykle porovnává se základními modely Microsoftu.

Chcete-li vyhodnotit modely vedle sebe:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. Přejít na **text > řeči Custom Speech > [název projektu] > testování**.
3. Klikněte na **Přidat test**.
4. Vyberte možnost **vyhodnotit přesnost**. Dejte testu název, popis a vyberte si zvukovou datovou sadu přepisu a s popiskem.
5. Vyberte až dva modely, které chcete testovat.
6. Klikněte na **Vytvořit**.

Po úspěšném vytvoření testu můžete porovnat výsledky vedle sebe.

## <a name="side-by-side-comparison"></a>Souběžné porovnání

Po dokončení testu, který je označen změnou stavu na *úspěch*, najdete číslo wer pro oba modely zahrnuté v testu. Kliknutím na název testu zobrazíte stránku s podrobnostmi o testování. Tato stránka podrobností obsahuje seznam všech projevy ve vaší datové sadě, které označují výsledky rozpoznávání dvou modelů společně s přepisem z odeslané datové sady. Chcete-li zkontrolovat souběžné porovnání, můžete přepínat různé typy chyb včetně vložení, odstranění a nahrazování. Díky poslechu zvuku a porovnávání výsledků rozpoznávání v každém sloupci, který ukazuje přepis a výsledky pro dva modely řeči, můžete rozhodnout, který model vyhovuje vašim potřebám a kde je potřeba další školení a vylepšení.

## <a name="next-steps"></a>Další kroky

* [Trénování modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Další zdroje

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
