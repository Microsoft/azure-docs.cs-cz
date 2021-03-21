---
title: Co je školení a model? – Vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků. Výsledkem úspěšného školení je model. Při výuce modelu se pro školicí datovou sadu, optimalizační datovou sadu a testovací datovou sadu vyžadují tři vzájemně se vylučující datové sady.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897930"
---
# <a name="what-are-trainings-and-models"></a>Co jsou kurzy a modely?

Model je systém, který poskytuje překlad pro konkrétní dvojici jazyků.
Výsledkem úspěšného školení je model. Při výuce modelu jsou vyžadovány tři vzájemně exkluzivní typy dokumentů: školení, optimalizace a testování. Je možné zadat také typ dokumentu slovníku. Přečtěte si prosím [Zarovnání vět](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Pokud jsou při zařazování školení k dispozici jenom školicí údaje, vlastní Překladatel automaticky sestaví data optimalizace a testování. Použije náhodnou podmnožinu vět z školicích dokumentů a vyloučí tyto věty z vašich školicích dat.

## <a name="training-document-type-for-custom-translator"></a>Typ školicího dokumentu pro vlastní překladatele

Dokumenty zahrnuté do sady školení jsou používány vlastním překladatelem jako základem pro vytvoření modelu. Během provádění školení jsou věty, které jsou k dispozici v těchto dokumentech, zarovnané (nebo spárovány). Liberties můžete využít při vytváření sady školicích dokumentů. Můžete zahrnout dokumenty, u kterých se domníváte, že jsou v jednom modelu tečná relevance. Znovu je vylučte v jiném, abyste viděli dopad ve [výsledku Bleu (dvojjazyčné vyhodnocení destudie)](what-is-bleu-score.md). Pokud zachováte nastavení optimalizace a konstantní sadu testů, můžete experimentovat se složením sady školení. Tento přístup představuje účinný způsob, jak změnit kvalitu systému překladu.

V rámci projektu můžete spustit více školení a porovnat [Bleu skóre](what-is-bleu-score.md) v rámci všech školicích běhů. Když spouštíte více školení pro porovnání, zajistěte, aby byla pokaždé zadáno stejné údaje o ladění a testování. Nezapomeňte také zkontrolovat výsledky ručně na kartě [testování](how-to-view-system-test-results.md) .

## <a name="tuning-document-type-for-custom-translator"></a>Typ dokumentu optimalizace pro vlastní Překladatel

Paralelní dokumenty zahrnuté v této sadě používá vlastní Překladatel k ladění systému překladu pro optimální výsledky.

Data optimalizace se používají během školení k úpravě všech parametrů a vah systému překladu na optimální hodnoty. Pečlivě vybírejte data pro ladění: data optimalizace by měla být reprezentativní pro obsah dokumentů, které chcete v budoucnu přeložit. Data optimalizace mají zásadní vliv na kvalitu vytvořeného překladu. Vyladění umožňuje, aby systém překladu poskytoval překlady, které jsou nejblíže ukázkám, které zadáte v datech optimalizace. V datech ladění nepotřebujete více než 2500 vět. Pro zajištění optimální kvality překladu se doporučuje vybrat sadu optimalizace ručně volbou nejvhodnějšího výběru vět.

Při vytváření sady pro vyladění vyberte věty, které jsou smysluplnou a zástupnou délkou budoucích vět, které chcete přeložit. Měli byste také zvolit věty, které mají slova a fráze, které chcete přeložit, do přibližné distribuce, kterou očekáváte v budoucích překladech. V praxi bude výsledkem délky 7 až 10 slov nejlepší výsledky, protože tyto věty obsahují dostatek kontextu k zobrazení inflexe a zadání délky fráze, která je významná, aniž by byla příliš složitá.

Dobrým popisem typu vět, které se mají použít v sadě pro optimalizaci, je prose: skutečnými větami Fluent. Nejedná se o buňky tabulky, nikoli POEMS, ne pouze interpunkční znaménka nebo číslice v regulárním jazyce věty.

Pokud ručně vyberete data optimalizace, neměla by mít žádné ze stejných vět jako data školení a testování. Data optimalizace mají významný dopad na kvalitu překladu – pečlivě vybírejte věty.

Pokud si nejste jistí, co zvolit pro data optimalizace, stačí vybrat školicí data a nechat si vlastní překladatele vybrat data optimalizace za vás. Když necháte vlastní Překladatel vybrat automaticky data optimalizace, bude používat náhodnou podmnožinu vět z dvojjazyčných školicích dokumentů a vyloučit tyto věty z školicího materiálu.

## <a name="testing-dataset-for-custom-translator"></a>Testovací datová sada pro vlastní překladatele

Paralelní dokumenty zahrnuté do testovací sady se používají k výpočtu skóre BLEU (dvojjazyčné vyhodnocení destudie). Toto skóre indikuje kvalitu vašeho systému překladu. V tomto skóre se ve skutečnosti dozvíte, jak pečlivě překlady provedené systémem překladu vycházející z tohoto školení odpovídají referenčním vět v sadě testovacích dat.

BLEU skóre je měření rozdílu mezi automatickým překladem a překladem odkazu. Jeho rozsah hodnot je od 0 do 100. Skóre 0 značí, že v překladu se nezobrazuje jedno slovo odkazu. Skóre 100 znamená, že automatický překlad přesně odpovídá odkazu: stejné slovo je ve stejné pozici. Skóre, které obdržíte, je průměrem skóre BLEU pro všechny věty testovacích dat.

Testovací data by měla obsahovat paralelní dokumenty, kde jsou cílové jazykové věty nejrelevantnější překlady odpovídajících vět zdrojového jazyka v páru zdroj-cíl. Možná budete chtít použít stejná kritéria, jako jste použili k vytvoření dat optimalizace. Testovací data však nemají žádný vliv na kvalitu systému překladu. Používá se výhradně k vygenerování BLEU skóre.

Jako testovací data nepotřebujete více než 2 500 vět. Když necháte systému možnost automaticky zvolit sadu testů, použije se náhodná podmnožina vět z dvojjazyčných školicích dokumentů a tyto věty vyloučí z školicího materiálu.

Můžete zobrazit vlastní překlady sady testování a porovnat je s překlady uvedenými v sadě testování, a to tak, že přejdete na kartu test v rámci modelu.