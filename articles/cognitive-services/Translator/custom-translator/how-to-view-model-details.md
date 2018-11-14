---
title: Zobrazit podrobné informace o modelu – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Karta modely v rámci žádného projektu zobrazuje podrobnosti o každý model, jako je například název modelu, stav modelu, BLEU skóre, školení, ladění, testování počet věty.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 80ee99fc4490f1fdc591093f357c8db55a97abb9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627324"
---
# <a name="view-model-details"></a>Zobrazit podrobnosti o modelu

Na kartě modely v rámci projektu zobrazí všechny modely v daném projektu. Všechny modely školení, které projekt je uveden na této kartě.

Pro každý model v projektu se zobrazí následující podrobnosti modelu.

1.  Název modelu: Zobrazí název modelu z daného modelu.

2.  Stav: Zobrazí se stav daného modelu. Nové trénování bude mít tudíž stav Odesláno, dokud je přijat. Stav se změní na zpracování dat, zatímco služba vyhodnocuje obsah dokumentů. Po dokončení hodnocení dokumentů se stav změní na spuštěno a budete mít viz počet věty, které jsou součástí školení, včetně ladění a testování sad, které se automaticky vytvoří za vás. Níže je seznam stav modelu, který popisuje stav modelů.

    -  Odeslal: Určuje, že back-end je zpracování dokumentů pro tento model.

    -  TrainingQueued: Určuje, že se školení byla zařazena do systému MT pro tento model.

    -  Spuštění: Určuje, zda školení je spuštěna v systému MT pro tento model.

    -  Úspěch: Určuje, že v systému MT byla úspěšná na školení a model je k dispozici. V tomto stavu zobrazí se pro tento model BLEU skóre.

    -  Nasazeno: Určuje, že úspěšná trénovaného modelu se odešle do systému MT pro nasazení.

    -  Undeploying: Určuje, že je undeploying nasazeného modelu.

    -  Nenasazené: Určuje, že proces zrušení nasazení modelu byla úspěšně dokončena.

    -  Školení se nezdařilo: Určuje, že se školení se nezdařilo. Pokud dojde k selhání školení, vzdělávání úlohu opakujte. Pokud potíže potrvají, kontaktujte nás. Neodstraňovat modelu se nezdařilo.

    - DataProcessingFailed: Určuje, že zpracování dat se nezdařila pro jeden nebo více dokumentů, které patří do modelu.

    - DeploymentFailed: Určuje, že model nasazení nebylo úspěšné.

    - MigratedDraft: Určuje, že je ve stavu návrhu model po migraci z centra k Translatoru Custom.

4.  Skóre BLEU: zobrazí skóre BLEU (Understudy dvojjazyčné vyhodnocení) modelu udávající kvalitu překladu systému. Toto skóre zjistíte, jak překladů provádí překlad systému vyplývajících z této školicí odpovídalo vět referenční datové sady testů. Skóre BLEU se zobrazí v případě, že je úspěšně dokončené školení. Není-li školení kompletní / neúspěšné, neuvidíte žádné BLEU skóre.

5.  Školení počet větu: Zobrazí celkový počet použít jako trénovací sada věty.

6.  Ladění počet větu: Zobrazí celkový počet věty použít jako ladění sady.

7.  Školení počet větu: Zobrazí celkový počet věty použít jako testování sady.

8.  Počet mono větu: Zobrazí celkový počet věty použít jako mono sady.

9.  Tlačítko akce nasazení: úspěšně trénovaného modelu, ukazuje "Nasazení" tlačítko není-li nasadit. Pokud model nasazení, zobrazí se tlačítko "Undeploy".

10. Odstranit: Toto tlačítko můžete použít, pokud chcete odstranit model. Odstraňuje se model se neodstraní žádná dokumentů použitý k vytvoření tohoto modelu.

    ![Zobrazit podrobnosti o modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>K porovnání po sobě jdoucích školení pro stejných systémů, je důležité udržovat o optimalizaci nastavení a testování nastavit konstantní.

## <a name="view-model-training-details"></a>Zobrazit podrobnosti o trénování modelu

Po dokončení trénování můžete zkontrolovat informace o školení na stránce Podrobnosti. Vyberte projekt, vyhledejte a vyberte kartu modely a zvolte model.

Stránce o modelu obsahuje dvě karty: Podrobnosti trénování a testování.

1.  **Školení o:** Tato karta zobrazuje seznam dokumentů používán vzdělávání:

    -  Název dokumentů: Toto pole zobrazí název dokumentu

    -  Typ dokumentu: Toto pole zobrazí, pokud tento dokument slouží paralelní / mono.

    -  Věta počet v Zdrojový jazyk: Toto pole obsahuje počet věty jsou existuje jako součást Zdrojový jazyk.

    -  Věta počet v cílovém jazyce: Toto pole obsahuje počet věty jsou existuje jako součást cílový jazyk.

    -  Zarovnané věty: Toto pole obsahuje počet věty má byly zarovnané vlastní Translator během zarovnat procesu.

    -  Využité věty: Toto obsahuje počet věty se použil ve vlastní Translator během tohoto cvičení pole.

    ![Podrobnosti trénování modelu](media/how-to/how-to-model-training-details.png)

2.  **Test:** Tato karta zobrazuje podrobnosti testu pro úspěšné školení.

## <a name="next-steps"></a>Další postup

- Kontrola [výsledky testů](how-to-view-system-test-results.md) a analyzujte výsledky školení.
