---
title: Testování úlohy Azure Stream Analytics s ukázkovými daty
description: Tento článek popisuje, jak použít Azure Portal k otestování Azure Stream Analytics úlohy, ukázkového vstupu a nahrání ukázkových dat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 3fda153d4c48ced17d1a9ba5f060b435b161542e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127633"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testování úlohy Azure Stream Analytics na portálu

V Azure Stream Analytics můžete dotaz testovat bez spuštění nebo zastavení úlohy. Můžete testovat dotazy na příchozí data ze zdrojů streamování nebo nahrávat ukázková data z místního souboru na webu Azure Portal. Dotazy můžete testovat také místně z místních ukázkových dat nebo živých dat v [aplikaci Visual Studio](stream-analytics-live-data-local-testing.md) a [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Automaticky vzorkovat příchozí data ze vstupu

Azure Stream Analytics automaticky načítá události ze vstupů streamování. Můžete spustit dotazy ve výchozí ukázce nebo nastavit konkrétní časový rámec pro ukázku.

1. Přihlaste se k webu Azure Portal.

2. Vyhledejte a vyberte svou stávající úlohu Stream Analytics.

3. Na stránce Stream Analytics úlohy pod záhlavím **topologie úlohy** vyberte možnost **dotaz** . otevře se okno Editor dotazů. 

4. Pokud chcete zobrazit ukázkový seznam příchozích událostí, vyberte ikonu vstup se souborem a vzorové události se automaticky zobrazí v **náhledu vstupu** .

   a. Typ serializace pro vaše data se automaticky rozpozná, pokud je jeho JSON nebo CSV. Můžete ji ručně změnit i na formát JSON, CSV a AVRO změnou možnosti v rozevírací nabídce.
    
   b. Pomocí selektoru můžete zobrazit data v **tabulce** nebo v **nezpracovaném** formátu.
    
   c. Pokud vaše data nejsou aktuální, vyberte **aktualizovat** , aby se zobrazily nejnovější události.

   Následující tabulka je příkladem dat ve **formátu tabulky** :

   ![Azure Stream Analytics ukázkový vstup ve formátu tabulky](./media/stream-analytics-test-query/asa-sample-table.png)

   V následující tabulce je příklad dat v **nezpracovaném formátu** :

   ![Azure Stream Analytics ukázkový vstup v nezpracovaném formátu](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Chcete-li otestovat dotaz s příchozími daty, vyberte možnost **test Query** . Výsledky se zobrazí na kartě **výsledky testu** . Můžete také vybrat **Stáhnout výsledky** a stáhnout výsledky.

   ![Výsledky testovacího dotazu Azure Stream Analytics ukázka](./media/stream-analytics-test-query/asa-test-query.png)

6. Chcete-li otestovat dotaz proti určitému časovému rozsahu příchozích událostí, vyberte **možnost vybrat časový rozsah** .
   
   ![Azure Stream Analytics časový rozsah pro příchozí ukázkové události](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Nastavte časový rozsah událostí, které chcete použít k otestování dotazu, a vyberte **vzorek** . V tomto časovém rámci můžete načíst až 1000 událostí nebo 1 MB, podle toho, co nastane dřív.

   ![Azure Stream Analytics nastavit časový rozsah pro příchozí ukázkové události](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Po výběru událostí pro vybraný časový rozsah se zobrazí na kartě **Náhled vstupu** .

   ![Azure Stream Analytics zobrazení výsledků testu](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Výběrem **obnovit** zobrazíte ukázkový seznam příchozích událostí. Pokud vyberete možnost **resetovat** , váš časový rozsah bude ztracen. Vyberte **test Query** a otestujte dotaz a zkontrolujte výsledky na kartě **výsledky testu** .

10. Když provedete změny v dotazu, vyberte **Uložit dotaz** a otestujte novou logiku dotazu. To umožňuje iterativní úpravu dotazu a znovu ho otestovat, abyste viděli, jak se výstup změní.

11. Po ověření výsledků zobrazených v prohlížeči jste připraveni úlohu **Spustit** .

## <a name="upload-sample-data-from-a-local-file"></a>Nahrání ukázkových dat z místního souboru

Místo používání živých dat můžete použít ukázková data z místního souboru a otestovat svůj Azure Stream Analytics dotaz.

1. Přihlaste se k webu Azure Portal.
   
2. Vyhledejte existující úlohu Stream Analytics a vyberte ji.

3. Na stránce Stream Analytics úlohy pod záhlavím **topologie úlohy** vyberte možnost **dotaz** . otevře se okno Editor dotazů.

4. Pokud chcete dotaz otestovat pomocí místního souboru, vyberte **Odeslat vzorový vstup** na kartě **Náhled vstupu** . 

   ![Snímek obrazovky ukazuje možnost nahrání vzorku vstupu.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Nahrajte místní soubor a otestujte dotaz. Soubory lze nahrávat pouze pomocí formátu JSON, CSV nebo AVRO. Vyberte **OK** .

   ![Snímek obrazovky se zobrazí v dialogovém okně nahrát ukázková data, kde můžete vybrat soubor.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Po nahrání souboru můžete také zobrazit obsah souboru ve formuláři jako tabulku nebo v nezpracovaném formátu. Vyberete-li možnost **obnovit** , budou se ukázková data vracet do příchozích vstupních dat popsaných v předchozí části. Můžete nahrát jakýkoli jiný soubor a Testovat dotaz kdykoli.

7. Vyberte **test Query** a otestujte dotaz proti nahranému ukázkovému souboru.

8. Výsledky testů se zobrazují na základě dotazu. Můžete změnit dotaz a vybrat **Uložit dotaz** a otestovat novou logiku dotazu. To umožňuje iterativní úpravu dotazu a znovu ho otestovat, abyste viděli, jak se výstup změní.

9. Použijete-li v dotazu více výstupů, výsledky se zobrazí na základě vybraného výstupu. 

   ![Azure Stream Analytics vybraný výstup](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Po ověření výsledků zobrazených v prohlížeči můžete úlohu **Spustit** .

## <a name="limitations"></a>Omezení

1.  Časové zásady se v testování portálu nepodporují:

   * Mimo pořadí: budou objednány všechny příchozí události.
   * Pozdní doručení: nebude k dispozici pozdní událost přijetí, protože Stream Analytics může použít pouze existující data pro testování.
   
2.  Jazyk C# UDF není podporován.

3.  Všechny testy se spustí s úlohou, která má jednu jednotku streamování.

4.  Velikost časového limitu je jedna minuta. Takže jakýkoli dotaz s velikostí okna větší než 1 minuta nemůže získat žádná data.

5.  Strojové učení se nepodporuje.

## <a name="next-steps"></a>Další kroky
* [Sestavte řešení IoT pomocí Stream Analytics](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): Tento kurz vás provede vytvořením uceleného řešení s generátorem dat, který simuluje provoz v rámci bezplatného zasedacího stánku.

* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)

* [Příklady dotazů pro běžné vzorce použití Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Vysvětlení vstupů pro Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Porozumění výstupům z Azure Stream Analytics](stream-analytics-define-outputs.md)