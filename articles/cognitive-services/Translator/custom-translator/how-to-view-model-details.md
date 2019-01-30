---
title: Zobrazit podrobné informace o modelu – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Karta modely v rámci žádného projektu zobrazuje podrobnosti o každý model, jako je například název modelu, stav modelu, BLEU skóre, školení, ladění, testování počet věty.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: d82d41a254164c635f4488db63766b39d922f002
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219104"
---
# <a name="view-model-details"></a>Zobrazení podrobností o modelu

Na kartě modely v rámci projektu zobrazí všechny modely v daném projektu. Všechny modely školení, které projekt je uveden na této kartě.

Pro každý model v projektu se zobrazí následující podrobnosti modelu.

1.  Název modelu: Zobrazí název modelu z daného modelu.

2.  Stav: Zobrazuje stav daného modelu. Nové trénování bude mít tudíž stav Odesláno, dokud je přijat. Stav se změní na zpracování dat, zatímco služba vyhodnocuje obsah dokumentů. Po dokončení hodnocení dokumentů se stav změní na spuštěno a budete mít viz počet věty, které jsou součástí školení, včetně ladění a testování sad, které se automaticky vytvoří za vás. Níže je seznam stav modelu, který popisuje stav modelů.

    -  Odeslal: Určuje, že back-end je zpracování dokumentů pro tento model.

    -  TrainingQueued: Určuje, že se školení byla zařazena do systému MT pro tento model.

    -  Spuštěno: Určuje, zda je spuštěna na školení systému MT pro tento model.

    -  Úspěšné: Určuje, že v systému MT byla úspěšná na školení a model je k dispozici. V tomto stavu zobrazí se pro tento model BLEU skóre.

    -  Nasazení: Určuje, že úspěšná trénovaného modelu se odešle do systému MT pro nasazení.

    -  Undeploying: Určuje, že je undeploying nasazeného modelu.

    -  Nasazení: Určuje, že proces zrušení nasazení modelu byla úspěšně dokončena.

    -  Školení se nezdařilo: Určuje, že se školení se nezdařilo. Pokud dojde k selhání školení, vzdělávání úlohu opakujte. Pokud potíže potrvají, kontaktujte nás. Neodstraňovat modelu se nezdařilo.

    - DataProcessingFailed: Určuje, že zpracování dat se nezdařila pro jeden nebo více dokumentů, které patří do modelu.

    - DeploymentFailed: Určuje, že model nasazení nebylo úspěšné.

    - MigratedDraft: Určuje, že je ve stavu návrhu model po migraci z centra k Translatoru Custom.

4.  Skóre BLEU: zobrazí skóre BLEU (Understudy dvojjazyčné vyhodnocení) modelu udávající kvalitu překladu systému. Toto skóre zjistíte, jak překladů provádí překlad systému vyplývajících z této školicí odpovídalo vět referenční datové sady testů. Skóre BLEU se zobrazí v případě, že je úspěšně dokončené školení. Není-li školení kompletní / neúspěšné, neuvidíte žádné BLEU skóre.

5.  Počet věty školení: Zobrazí celkový počet použít jako trénovací sada věty.

6.  Počet věty ladění: Zobrazí celkový počet věty použít jako ladění sady.

7.  Počet věty školení: Zobrazí celkový počet věty použít jako testovací sadu.

8.  Počet mono větu: Zobrazí celkový počet věty používá jako sada mono.

9.  Nasazení tlačítko akce: Pro úspěšně trénovaného modelu se zobrazí tlačítko "Nasazení", pokud není nasazení. Pokud model nasazení, zobrazí se tlačítko "Undeploy".

10. Odstraňte: Pokud chcete odstranit modelu, můžete pomocí tohoto tlačítka. Odstraňuje se model se neodstraní žádná dokumentů použitý k vytvoření tohoto modelu.

    ![Zobrazení podrobností o modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>K porovnání po sobě jdoucích školení pro stejných systémů, je důležité udržovat o optimalizaci nastavení a testování nastavit konstantní.

## <a name="view-model-training-details"></a>Zobrazit podrobnosti o trénování modelu

Po dokončení trénování můžete zkontrolovat informace o školení na stránce Podrobnosti. Vyberte projekt, vyhledejte a vyberte kartu modely a zvolte model.

Stránce o modelu obsahuje dvě karty: Podrobnosti trénování a testování.

1.  **Školení podrobnosti:** Tato karta zobrazuje seznam dokumentů používán vzdělávání:

    -  Název dokumenty: Toto pole zobrazí název dokumentu

    -  Typ dokumentu: Toto pole zobrazí, pokud tento dokument slouží paralelní / mono.

    -  Počet větu v Zdrojový jazyk: Toto pole obsahuje počet věty jsou existuje jako součást Zdrojový jazyk.

    -  Počet větu v cílovém jazyce: Toto pole obsahuje počet věty jsou existuje jako součást cílový jazyk.

    -  Zarovnané věty: Toto pole obsahuje počet věty má byly zarovnané vlastní Translator během zarovnat procesu.

    -  Použít věty: Toto pole obsahuje počet věty se použil ve vlastní Translator během tohoto cvičení.

    ![Podrobnosti trénování modelu](media/how-to/how-to-model-training-details.png)

2.  **Test:** Tato karta zobrazuje podrobnosti testu pro úspěšné školení.

## <a name="next-steps"></a>Další postup

- Kontrola [výsledky testů](how-to-view-system-test-results.md) a analyzujte výsledky školení.
