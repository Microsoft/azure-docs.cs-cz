---
title: Zobrazit podrobnosti o modelu - Vlastní překladač
titleSuffix: Azure Cognitive Services
description: Karta Modely v rámci libovolného projektu zobrazuje podrobnosti o každém modelu, jako je název modelu, stav modelu, skóre BLEU, školení, ladění, testování počtu vět.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595560"
---
# <a name="view-model-details"></a>Zobrazení podrobností o modelu

Karta Modely v rámci projektu zobrazuje všechny modely v tomto projektu. Všechny modely trénované pro tento projekt jsou uvedeny na této kartě.

Pro každý model v projektu jsou zobrazeny tyto podrobnosti.

1.  Název modelu: Zobrazuje název modelu daného modelu.

2.  Stav: Zobrazuje stav daného modelu. Váš nový trénink bude mít stav Odesláno, dokud nebude přijat. Stav se změní na Zpracování dat, zatímco služba vyhodnotí obsah vašich dokumentů. Po dokončení vyhodnocení dokumentů se stav změní na Spuštěno a budete moci zobrazit počet vět, které jsou součástí školení, včetně ladění a testovacích sad, které jsou vytvořeny automaticky. Níže je uveden seznam stavu modelu, který popisuje stav modelů.

    -  Odesláno: Určuje, že back-end zpracovává dokumenty pro daný model.

    -  TrainingQueued: Určuje, že školení je ve frontě do systému MT pro tento model.

    -  Běh: Určuje, že školení je spuštěno v systému MT pro tento model.

    -  Úspěšné: Určuje, že školení proběhlo úspěšně v systému MT a model je k dispozici. V tomto stavu se pro tento model zobrazí skóre BLEU.

    -  Nasazeno: Určuje, že úspěšný trénovaný model je odeslán do systému MT pro nasazení.

    -  Zrušení nasazení: Určuje, že nasazený model se nenasazuje.

    -  Undeployed: Určuje, že proces zrušení nasazení modelu byl úspěšně dokončen.

    -  Školení se nezdařilo: Určuje, že školení se nezdařilo. Pokud dojde k selhání školení, opakujte úlohu školení. Pokud chyba přetrvává, kontaktujte nás. Neodstraňujte neúspěšný model.

    - DataProcessingFailed: Určuje, že zpracování dat se nezdařilo pro jeden nebo více dokumentů, které patří do modelu.

    - DeploymentFailed: Určuje, že nasazení modelu se nezdařilo.

    - MigratedDraft: Určuje, že model je ve stavu konceptu po migraci z centra do vlastního překladače.

4.  BLEU Skóre: ukazuje BLEU (Dvojjazyčné hodnocení Understudy) skóre modelu, označující kvalitu vašeho překladatelského systému. Toto skóre vám řekne, jak úzce překlady provedené překladového systému vyplývající z tohoto školení odpovídají referenčním větám v sadě testovacích dat. Skóre BLEU se zobrazí, pokud je školení úspěšně dokončeno. Pokud trénink není kompletní / selhal, neuvidíte žádné skóre BLEU.

5.  Počet trénovacích vět: Zobrazuje celkový počet vět použitých jako trénovací sada.

6.  Ladění počtu vět: Zobrazuje celkový počet vět použitých jako sada ladění.

7.  Počet trénovacích vět: Zobrazuje celkový počet vět použitých jako testovací sada.

8.  Počet mono vět: Zobrazuje celkový počet vět použitých jako mono množina.

9.  Tlačítko akce nasazení: Pro úspěšně trénovaný model se zobrazí tlačítko "Nasadit", pokud není nasazeno. Pokud je nasazen model, zobrazí se tlačítko "Undeploy".

10. Odstranit: Toto tlačítko můžete použít, pokud chcete model odstranit. Odstraněním modelu neodstraníte žádný z dokumentů použitých k vytvoření tohoto modelu.

    ![Zobrazení podrobností o modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Chcete-li porovnat po sobě jdoucí školení pro stejné systémy, je důležité zachovat sadu ladění a testování nastavit konstantní.

## <a name="view-model-training-details"></a>Zobrazit podrobnosti školení modelu

Po dokončení školení si můžete přečíst podrobnosti o školení na stránce podrobností. Vyberte projekt, vyhledejte a vyberte kartu Modely a zvolte model.

Stránka modelu má dvě karty: Podrobnosti školení a Test.

1.  **Podrobnosti o tréninku:** Na této kartě je uveden seznam dokumentů použitých při školení:

    -  Název dokumentů: Toto pole zobrazuje název dokladu.

    -  Typ dokumentu: Toto pole ukazuje, zda je tento dokument paralelní/ mono.

    -  Počet vět ve zdrojovém jazyce: Toto pole zobrazuje počet vět, které jsou součástí zdrojového jazyka.

    -  Počet vět v cílovém jazyce: Toto pole zobrazuje počet vět, které jsou součástí cílového jazyka.

    -  Zarovnané věty: Toto pole zobrazuje počet vět, které byly zarovnány vlastním překladačem během procesu zarovnání.

    -  Použité věty: Toto pole zobrazuje počet vět, které byl použit vlastní překladač během tohoto školení.

    ![Podrobnosti o tréninku modelu](media/how-to/how-to-model-training-details.png)

2.  **Test:** Na této kartě jsou uvedeny podrobnosti testu pro úspěšné školení.

## <a name="next-steps"></a>Další kroky

- Prohlédněte si [výsledky testů](how-to-view-system-test-results.md) a analyzujte výsledky školení.
