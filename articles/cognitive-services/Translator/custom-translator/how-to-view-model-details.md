---
title: Zobrazit podrobnosti modelu – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Karta modely v rámci libovolného projektu zobrazuje podrobnosti o jednotlivých modelech, jako je název modelu, stav modelu, BLEU skóre, školení, ladění, počet vět.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595560"
---
# <a name="view-model-details"></a>Zobrazení podrobností o modelu

Karta modely v rámci projektu zobrazuje všechny modely v daném projektu. Na této kartě jsou uvedeny všechny modely školení pro daný projekt.

Pro každý model v projektu se zobrazí tyto podrobnosti.

1.  Název modelu: Zobrazuje název modelu daného modelu.

2.  Stav: Zobrazuje stav daného modelu. Vaše nové školení bude mít stav Odesláno až do přijetí. Stav se změní na zpracování dat, zatímco služba vyhodnocuje obsah vašich dokumentů. Po dokončení vyhodnocení vašich dokumentů se stav změní na spuštěno a uvidíte počet vět, které jsou součástí školení, včetně sad pro vyladění a testování, které jsou vytvořeny automaticky. Níže je uveden seznam stavů modelu, který popisuje stav modelů.

    -  Předán Určuje, že back-end zpracovává dokumenty pro tento model.

    -  TrainingQueued: Určuje, že školení je zařazené do fronty MT systému pro tento model.

    -  Spuštěno: Určuje, že školení běží v systému MT pro daný model.

    -  Úspěchu Určuje, že školení bylo úspěšné v systému MT a že je k dispozici model. V tomto stavu se pro tento model zobrazí BLEU skóre.

    -  Nainstalována Určuje, že se úspěšně proučený model posílá do MT systému pro nasazení.

    -  Odsazování se nasazuje: Určuje, že nasazený model se odsazuje.

    -  Nenasazené Určuje, že proces odsazování modelu byl úspěšně dokončen.

    -  Neúspěšné školení: Určuje, že se nezdařila analýza. Pokud dojde k selhání školení, zkuste úlohu školení zopakovat. Pokud chyba přetrvává, kontaktujte nás. Neodstraňujte model, který selhal.

    - DataProcessingFailed: Určuje, že zpracování dat pro jeden nebo více dokumentů patřících do modelu selhalo.

    - DeploymentFailed Určuje, že se nasazení modelu nezdařilo.

    - MigratedDraft: Určuje, že model je ve stavu konceptu po migraci z centra na vlastní překladatel.

4.  BLEU skore: zobrazuje skóre BLEU (dvojjazyčné vyhodnocení destudie) modelu a označuje kvalitu vašeho systému překladu. V tomto skóre se dozvíte, jak pečlivě překlady provedené systémem překladu vycházející z tohoto školení odpovídají referenčním vět v sadě testovacích dat. Po úspěšném dokončení školení se zobrazí BLEU skóre. Pokud není školení dokončené/neúspěšné, neuvidíte žádné BLEU skóre.

5.  Počet vět pro cvičení: Zobrazuje celkový počet vět použitých jako školicí sada.

6.  Počet vět pro ladění: Zobrazuje celkový počet vět použitých jako nastavení optimalizace.

7.  Počet vět pro cvičení: Zobrazuje celkový počet vět použitých jako testovací sada.

8.  Počet vět v mono: Zobrazuje celkový počet vět použitých jako sada mono.

9.  Tlačítko pro nasazení akce: V případě úspěšného školení modelu se zobrazí tlačítko "nasadit", pokud není nasazeno. Pokud je model nasazený, zobrazí se tlačítko zrušit nasazení.

10. Dstranit Toto tlačítko můžete použít, pokud chcete model odstranit. Odstraněním modelu se neodstraní žádné dokumenty použité k vytvoření tohoto modelu.

    ![Zobrazení podrobností o modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Chcete-li porovnat po sobě jdoucí školení pro tytéž systémy, je důležité udržovat sadu optimalizace a sadu testování nastavenou na konstantní.

## <a name="view-model-training-details"></a>Zobrazit podrobnosti o školeních modelu

Po dokončení školení si můžete prohlédnout podrobnosti o školení ze stránky s podrobnostmi. Vyberte projekt, najděte a vyberte kartu modely a zvolte model.

Stránka model obsahuje dvě karty: Podrobnosti a testování školení.

1.  **Podrobnosti o školení:** Tato karta zobrazuje seznam dokumentů používaných při školení:

    -  Název dokumentů: V tomto poli se zobrazuje název dokumentu.

    -  Typ dokumentu: Toto pole ukazuje, jestli je tento dokument paralelní/mono.

    -  Počet vět ve zdrojovém jazyce: V tomto poli se zobrazuje počet vět v rámci zdrojového jazyka.

    -  Počet vět v cílovém jazyce: V tomto poli se zobrazuje počet vět v rámci cílového jazyka.

    -  Zarovnané věty: Toto pole zobrazuje počet vět, které vlastní Překladatel zarovnává během procesu zarovnávání.

    -  Použité věty: Toto pole zobrazuje počet vět, které vlastní Překladatel používá během tohoto školení.

    ![Podrobnosti o školeních modelu](media/how-to/how-to-model-training-details.png)

2.  **Napaden** Tato karta zobrazuje podrobné informace o testu pro úspěšné školení.

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [výsledky testů](how-to-view-system-test-results.md) a analyzujte výsledky školení.
