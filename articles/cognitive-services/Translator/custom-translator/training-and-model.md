---
title: Co je školení a model? – Vlastní Translator
titleSuffix: Azure Cognitive Services
description: Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků. Výsledkem úspěšného školení je model. Při výuce modelu se pro školicí datovou sadu, optimalizační datovou sadu a testovací datovou sadu vyžadují tři vzájemně se vylučující datové sady.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: af3f795dc5036b23b82562e7af4582bd90b44f47
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595428"
---
# <a name="what-are-trainings-and-models"></a>Co jsou kurzy a modely?

Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků.
Výsledkem úspěšného školení je model. Při výuce modelu jsou vyžadovány tři vzájemně se vylučující datové sady: školicí datová sada, optimalizační datová sada a testovací datová sada. Je také možné zadat data slovníku.

Pokud jsou při zařazování do fronty školení k dispozici jenom školicí údaje, vlastní Překladatel automaticky sestaví ladění a testování datových sad. Vyloučí z vašich školicích dat 5 000 vět a pro sestavování sad pro vyladění a testování se použijí 2 500.

## <a name="training-dataset-for-custom-translator"></a>Datová sada školení pro vlastní překladatele

Dokumenty zahrnuté do sady školení jsou používány vlastním překladatelem jako základem pro vytvoření modelu. Během provádění školení jsou věty, které jsou k dispozici v těchto dokumentech, zarovnané (nebo spárovány). Liberties můžete využít při vytváření sady školicích dokumentů. Můžete zahrnout dokumenty, u kterých se domníváte, že jsou v jednom modelu tečná relevance. Znovu je vylučte v jiném, abyste viděli dopad ve [výsledku Bleu (dvojjazyčné vyhodnocení destudie)](what-is-bleu-score.md). Pokud zachováte nastavení optimalizace a konstantní sadu testů, můžete experimentovat se složením sady školení. Tento přístup představuje účinný způsob, jak změnit kvalitu systému překladu.

V rámci projektu můžete spustit více školení a porovnat [Bleu skóre](what-is-bleu-score.md) v rámci všech školicích běhů. Když spouštíte více školení pro porovnání, zajistěte, aby byla pokaždé zadáno stejné údaje o ladění a testování. Nezapomeňte také zkontrolovat výsledky ručně na kartě [testování](how-to-view-system-test-results.md) .

## <a name="tuning-dataset-for-custom-translator"></a>Vyladit datovou sadu pro vlastní překladatele

Paralelní dokumenty zahrnuté v této sadě používá vlastní Překladatel k ladění systému překladu pro optimální výsledky.

Sada pro vyladění se používá během školení k úpravě všech parametrů a vah systému překladu na optimální hodnoty. Pečlivě vyberte svou sadu optimalizací: sada vyladění by měla odpovídat obsahu dokumentů, které v budoucnu hodláte přeložit. Sada optimalizací má zásadní vliv na kvalitu vytvořeného překladu. Vyladění umožňuje, aby systém překladu poskytoval překlady, které jsou nejblíže ukázkám, které zadáte v sadě dat pro optimalizaci. Jako nastavení optimalizace nepotřebujete více než 2500 vět. Pro zajištění optimální kvality překladu se doporučuje vybrat sadu optimalizace ručně volbou nejvhodnějšího výběru vět.

Při vytváření sady pro vyladění vyberte věty, které jsou smysluplnou a zástupnou délkou budoucích vět, které chcete přeložit. Měli byste také zvolit věty, které mají slova a fráze, které chcete přeložit, do přibližné distribuce, kterou očekáváte v budoucích překladech. V praxi výsledkem je délka věty 8 až 18 slov nejlepší výsledky, protože tyto věty obsahují dostatek kontextu pro zobrazení inflexe a zadání délky fráze, která je významná, aniž by byla příliš složitá.

Dobrým popisem typu vět, které se mají použít v sadě pro optimalizaci, je prose: skutečnými větami Fluent. Nejedná se o buňky tabulky, nikoli POEMS, ne pouze interpunkční znaménka nebo číslice v regulárním jazyce věty.

Pokud ručně vyberete svou sadu dat optimalizace, neměla by mít žádné ze stejných vět jako data školení a testování. Sada optimalizace má významný dopad na kvalitu překladů – pečlivě vybírejte věty.

Pokud si nejste jisti, co zvolit pro sadu pro vyladění, stačí vybrat sadu školení a nechat si vlastní překladatelskou sadu optimalizace vybrat. Když necháte vlastní Překladatel zvolit automaticky sadu optimalizace, bude používat náhodnou podmnožinu vět z dvojjazyčných školicích dokumentů a vyloučit tyto věty z školicího materiálu.

## <a name="testing-dataset-for-custom-translator"></a>Testovací datová sada pro vlastní překladatele

Paralelní dokumenty zahrnuté do testovací sady se používají k výpočtu skóre BLEU (dvojjazyčné vyhodnocení destudie). Toto skóre indikuje kvalitu vašeho systému překladu. V tomto skóre se ve skutečnosti dozvíte, jak pečlivě překlady provedené systémem překladu vycházející z tohoto školení odpovídají referenčním vět v sadě testovacích dat.

BLEU skóre je měření rozdílu mezi automatickým překladem a překladem odkazu. Jeho rozsah hodnot je od 0 do 100. Skóre 0 značí, že v překladu se nezobrazuje jedno slovo odkazu. Skóre 100 znamená, že automatický překlad přesně odpovídá odkazu: stejné slovo je ve stejné pozici. Skóre, které obdržíte, je průměrem skóre BLEU pro všechny věty sady testování.

Testovací sada by měla obsahovat paralelní dokumenty, kde cílové jazykové věty jsou nejvhodnějšími překlady odpovídajících vět zdrojového jazyka ve páru. Možná budete chtít použít stejná kritéria, která jste použili k vytvoření sady optimalizace. Nicméně sada testů nemá žádný vliv na kvalitu systému překladu. Používá se výhradně k vygenerování BLEU skóre pro vás a pro nic jiného.

Jako sadu testování nepotřebujete více než 2 500 vět. Když necháte systému možnost automaticky zvolit sadu testů, použije se náhodná podmnožina vět z dvojjazyčných školicích dokumentů a tyto věty vyloučí z školicího materiálu.

Můžete zobrazit vlastní překlady sady testování a porovnat je s překlady uvedenými v sadě testování, a to tak, že přejdete na kartu test v rámci modelu.
