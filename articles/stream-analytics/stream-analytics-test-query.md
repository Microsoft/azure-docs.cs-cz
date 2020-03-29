---
title: Testování úlohy Azure Stream Analytics pomocí ukázkových dat
description: Tento článek popisuje, jak pomocí portálu Azure k testování úlohy Azure Stream Analytics, ukázkovému vstupu a nahrání ukázkových dat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898391"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testování úlohy Azure Stream Analytics na portálu

Ve službě Azure Stream Analytics můžete dotaz otestovat bez spuštění nebo zastavení úlohy. Můžete otestovat dotazy na příchozí data z vašich zdrojů streamování nebo nahrát ukázková data z místního souboru na Webu Azure Portal. Dotazy můžete také testovat místně z místních ukázkových dat nebo živých dat v [sadě Visual Studio](stream-analytics-live-data-local-testing.md) a Visual Studio [Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Automatické vzorkování příchozích dat ze vstupu

Azure Stream Analytics automaticky načte události z vašich streamovacích vstupů. Můžete spustit dotazy na výchozí ukázku nebo nastavit konkrétní časový rámec pro ukázku.

1. Přihlaste se k portálu Azure.

2. Vyhledejte a vyberte svou stávající úlohu Stream Analytics.

3. Na stránce úlohy Stream Analytics vyberte v části **Topologie úlohy** **dotaz,** abyste otevřeli okno editoru dotazů. 

4. Chcete-li zobrazit ukázkový seznam příchozích událostí, vyberte vstup s ikonou souboru a ukázkové události se automaticky zobrazí v **náhledu vstupu**.

   a. Typ serializace pro vaše data je automaticky rozpoznán, pokud jeho JSON nebo CSV. Můžete ručně změnit také JSON, CSV, AVRO změnou možnosti v rozbalovací nabídce.
    
   b. Pomocí voliče můžete zobrazit data ve formátu **Tabulka** nebo **Nezpracovaný.**
    
   c. Pokud zobrazená data nejsou aktuální, kliknutím na **Aktualizovat** zobrazte nejnovější události.

   Následující tabulka je příkladem dat ve **formátu tabulka**:

   ![Ukázkový vstup Azure Stream Analytics ve formátu tabulky](./media/stream-analytics-test-query/asa-sample-table.png)

   Následující tabulka je příkladem dat ve **formátu Raw**:

   ![Ukázkový vstup Azure Stream Analytics v nezpracovaném formátu](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Chcete-li dotaz otestovat s příchozími daty, vyberte **možnost Testovat dotaz**. Výsledky se zobrazí na kartě **Výsledky testu.** Můžete také vybrat **stáhnout výsledky** ke stažení výsledky.

   ![Výsledky ukázkového testovacího dotazu Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Chcete-li dotaz otestovat podle určitého časového rozsahu příchozích událostí, vyberte **možnost Vybrat časový rozsah**.
   
   ![Časový rozsah Azure Stream Analytics pro příchozí ukázkové události](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Nastavte časový rozsah událostí, které chcete použít k testování dotazu, a vyberte **ukázku**. V tomto časovém rámci můžete načíst až 1000 událostí nebo 1 MB, podle toho, co nastane dříve.

   ![Azure Stream Analytics nastavit časový rozsah pro příchozí ukázkové události](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Jakmile jsou události vzorkovány pro vybraný časový rozsah, zobrazí se na kartě **Vstupní náhled.**

   ![Výsledky testů zobrazení Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Výběrem **možnosti Obnovit** zobrazíte ukázkový seznam příchozích událostí. Pokud vyberete **Obnovit**, bude výběr časového rozsahu ztracen. Vyberte **Testovat dotaz,** chcete-li dotaz otestovat a zkontrolovat výsledky na kartě **Výsledky testu.**

10. Když v dotazu provedete změny, vyberte **Uložit dotaz** a otestujte novou logiku dotazu. To umožňuje iterativně upravit dotaz a znovu jej otestovat, abyste viděli, jak se změní výstup.

11. Po ověření výsledků zobrazených v prohlížeči jste připraveni **úlohu spustit.**

## <a name="upload-sample-data-from-a-local-file"></a>Nahrání ukázkových dat z místního souboru

Místo použití živých dat můžete k testování dotazu Azure Stream Analytics použít ukázková data z místního souboru.

1. Přihlaste se k portálu Azure.
   
2. Vyhledejte stávající úlohu Stream Analytics a vyberte ji.

3. Na stránce úlohy Stream Analytics vyberte v části **Topologie úlohy** **dotaz,** abyste otevřeli okno editoru dotazů.

4. Chcete-li dotaz otestovat pomocí místního souboru, vyberte na kartě **Náhled vstupu** možnost **Odeslat ukázkový vstup.** 

   ![Ukázkový soubor pro nahrávání služby Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Nahrajte místní soubor a otestujte dotaz. Soubory lze nahrávat pouze ve formátech JSON, CSV nebo AVRO. Vyberte **OK**.

   ![Ukázkový soubor pro nahrávání služby Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Jakmile soubor nahrajete, můžete také zobrazit obsah souboru ve formuláři jako tabulku nebo v jeho nezpracovaném formátu. Pokud vyberete **obnovit**, ukázková data se vrátí k příchozím vstupním datům vysvětleným v předchozí části. Dotaz můžete kdykoli nahrát libovolný jiný soubor.

7. Vyberte **Testovat dotaz,** chcete-li dotaz otestovat proti nahranému ukázkovému souboru.

8. Výsledky testů jsou zobrazeny na základě dotazu. Můžete změnit dotaz a vybrat **uložit dotaz** a otestovat novou logiku dotazu. To umožňuje iterativně upravit dotaz a znovu jej otestovat, abyste viděli, jak se změní výstup.

9. Při použití více výstupů v dotazu jsou výsledky zobrazeny na základě vybraného výstupu. 

   ![Vybraný výstup Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Po ověření výsledků zobrazených v prohlížeči můžete úlohu **spustit.**

## <a name="next-steps"></a>Další kroky
* [Vytvořte řešení IoT pomocí Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics): tento kurz vás provede vytvořením komplexního řešení s generátorem dat, který bude simulovat provoz na mýtném stánku.

* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Příklady dotazů pro běžné vzorce využití služby Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Principy vstupů pro Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Principy výstupů z Azure Stream Analytics](stream-analytics-define-outputs.md)
