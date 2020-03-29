---
title: Co je školení a model? - Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků. Výsledkem úspěšného školení je model. Při trénování modelu jsou vyžadovány tři vzájemně se vylučující datové sady, data optimalizace a testování datové sady.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219453"
---
# <a name="what-are-trainings-and-models"></a>Co jsou školení a modely?

Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků.
Výsledkem úspěšného školení je model. Při trénování modelu jsou vyžadovány tři vzájemně se vylučující typy dokumentů: školení, ladění a testování. Lze také zadat typ dokumentu slovníku. Prosím, odkázat na [zarovnání věty](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

Pokud jsou k dispozici pouze trénovací data při řazení do fronty, vlastní překladač automaticky sestaví data optimalizace a testování. Použije náhodnou podmnožinu vět z vašich trénovacích dokumentů a vyloučí tyto věty ze samotných trénovacích dat.

## <a name="training-document-type-for-custom-translator"></a>Typ školicího dokumentu pro vlastní překladač

Dokumenty obsažené v trénovací sadě jsou používány vlastní překladač jako základ pro vytváření modelu. Během provádění školení věty, které jsou přítomny v těchto dokumentech jsou zarovnány (nebo spárovány). Můžete si vzít svobody při sestavování sady školicích dokumentů. Do jednoho modelu můžete zahrnout dokumenty, o kterých se domníváte, že mají tangenciální význam. Opět vyloučit v jiném vidět dopad v [BLEU (Dvojjazyčné hodnocení Understudy) skóre](what-is-bleu-score.md). Tak dlouho, jak budete mít tuning set a testovací sada konstantní, neváhejte experimentovat se složením tréninkové sady. Tento přístup je účinný způsob, jak změnit kvalitu vašeho překladatelského systému.

Můžete spustit více školení v rámci projektu a porovnat [skóre BLEU](what-is-bleu-score.md) ve všech spuštěníškolení. Pokud používáte více trénování pro porovnání, ujistěte se, že jsou pokaždé zadána stejná data ladění/ testu. Také se ujistěte, že výsledky také zkontrolujte ručně v záložce ["Testování".](how-to-view-system-test-results.md)

## <a name="tuning-document-type-for-custom-translator"></a>Ladění typu dokumentu pro vlastní překladač

Paralelní dokumenty obsažené v této sadě jsou používány vlastní překladač vyladit překladsystém pro dosažení optimálních výsledků.

Data ladění se používají při trénování k nastavení všech parametrů a hmotností překladového systému na optimální hodnoty. Pečlivě si vybírejte data ladění: data ladění by měla být reprezentativní pro obsah dokumentů, které chcete v budoucnu přeložit. Data ladění mají zásadní vliv na kvalitu produkovaných překladů. Optimalizace umožňuje překladu systému poskytovat překlady, které jsou nejblíže k ukázky, které zadáte v data optimalizace. V datech ladění nepotřebujete více než 2500 vět. Pro optimální kvalitu překladu se doporučuje zvolit nejreprezentativnější výběr vět.

Při vytváření sady ladění zvolte věty, které jsou smysluplnou a reprezentativní délkou budoucích vět, které očekáváte přeložit. Měli byste také zvolit věty, které mají slova a fráze, které chcete přeložit v přibližné distribuci, kterou očekáváte v budoucích překladech. V praxi délka věty 7 až 10 slov bude produkovat nejlepší výsledky, protože tyto věty obsahují dostatek kontextu ukázat skloňování a poskytují délku fráze, která je významná, aniž by byla příliš složitá.

Dobrým popisem typu vět, které mají být v sadě ladění používány, je próza: skutečné plynulé věty. Ne tabulkové buňky, ne básně, ne seznamy věcí, nejen interpunkce, nebo čísla ve větě - běžný jazyk.

Pokud ručně vyberete data optimalizace, neměly by mít žádné stejné věty jako data školení a testování. Data ladění mají významný vliv na kvalitu překladů - pečlivě vybírejte věty.

Pokud si nejste jisti, co si vybrat pro vaše data ladění, stačí vybrat trénovací data a nechat vlastní překladač vybrat data ladění za vás. Když necháte vlastní překladač zvolit data ladění automaticky, použije náhodnou podmnožinu vět z dvojjazyčných trénovacích dokumentů a vyloučí tyto věty ze samotného školicího materiálu.

## <a name="testing-dataset-for-custom-translator"></a>Testování datové sady pro vlastní překladač

Paralelní dokumenty obsažené v testovací sadě se používají k výpočtu skóre BLEU (Dvojjazyčné hodnocení understudy). Toto skóre označuje kvalitu vašeho překladatelského systému. Toto skóre ve skutečnosti vám řekne, jak úzce překlady provedené překladového systému vyplývající z tohoto školení odpovídají referenčnívěty v sadě testovacích dat.

Skóre BLEU je měření rozdílu mezi automatickým překladem a referenčním překladem. Jeho hodnota se pohybuje od 0 do 100. Skóre 0 označuje, že ani jedno slovo odkazu se zobrazí v překladu. Skóre 100 označuje, že automatický překlad přesně odpovídá odkazu: stejné slovo je v přesně stejné pozici. Skóre, které obdržíte, je průměr skóre BLEU pro všechny věty testovacích dat.

Testovací data by měla zahrnovat paralelní dokumenty, kde cílové jazykové věty jsou nejžádanější překlady odpovídajících vět zdrojového jazyka v páru zdroj-cíl. Můžete použít stejná kritéria, která jste použili k vypokládání dat optimalizace. Testovací údaje však nemají žádný vliv na kvalitu překladového systému. Používá se výhradně ke generování skóre BLEU pro vás.

Jako testovací data nepotřebujete více než 2 500 vět. Když necháte systém zvolit testovací sadu automaticky, použije náhodnou podmnožinu vět z dvojjazyčných školicích dokumentů a vyloučí tyto věty ze samotného školicího materiálu.

Můžete zobrazit vlastní překlady testovací sady a porovnat je s překlady uvedené v testovací sadě, přechodem na kartu test v rámci modelu.
