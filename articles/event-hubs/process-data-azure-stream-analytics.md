---
title: Zpracování dat z Event Hubs Azure pomocí Stream Analytics | Microsoft Docs
description: V tomto článku se dozvíte, jak zpracovávat data z centra událostí Azure pomocí úlohy Azure Stream Analytics.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85312900"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Zpracování dat z centra událostí pomocí Azure Stream Analytics 
Služba Azure Stream Analytics usnadňuje ingestování, zpracování a analýzu streamování dat z Azure Event Hubs a umožňuje efektivní přehledy, které umožňují řídit akce v reálném čase. Tato integrace vám umožní rychle vytvořit kanál analýzy v Hot-Path. Azure Portal můžete použít k vizualizaci příchozích dat a napsání dotazu Stream Analytics. Jakmile je dotaz připravený, můžete ho přesunout do produkčního prostředí jenom několika kliknutími. 

## <a name="key-benefits"></a>Klíčové výhody
Tady jsou klíčové výhody služby Azure Event Hubs a Integrace Azure Stream Analytics: 
- **Náhled dat** – můžete zobrazit náhled příchozích dat z centra událostí v Azure Portal.
- **Otestujte dotaz** – Připravte dotaz na transformaci a otestujte ho přímo v Azure Portal. Syntaxi jazyka dotazů naleznete v tématu [Stream Analytics dokumentaci jazyka dotazů](/stream-analytics-query/built-in-functions-azure-stream-analytics) .
- **Nasazení dotazu do produkčního** prostředí – dotaz můžete nasadit do produkčního prostředí vytvořením a spuštěním Azure Stream Analytics úlohy.

## <a name="end-to-end-flow"></a>Koncový tok

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
1. Přejděte do **oboru názvů Event Hubs** a potom přejděte do **centra událostí**, které obsahuje příchozí data. 
1. Na stránce centrum událostí vyberte **zpracovat data** .  

    ![Dlaždice zpracovat data](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Na dlaždici **Povolit přehledy událostí v reálném čase** vyberte **prozkoumat** . 

    ![Vyberte Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Zobrazí se stránka s dotazem s hodnotami, které jsou už nastavené pro následující pole:
    1. **Centrum událostí** jako vstup pro dotaz.
    1. Ukázkový **dotaz SQL** s příkazem SELECT. 
    1. Alias **výstupu** , který odkazuje na výsledky testu dotazu. 

        ![Editor dotazů](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Při prvním použití této funkce bude tato stránka požádána o vaše oprávnění k vytvoření skupiny uživatelů a zásad pro vaše centrum událostí pro zobrazení náhledu příchozích dat.
1. V podokně **Náhled vstupu** vyberte **vytvořit** , jak je znázorněno na předchozím obrázku. 
1. Na této kartě se okamžitě zobrazí snímek nejnovějších příchozích dat.
    - Typ serializace v datech se automaticky detekuje (JSON/CSV). Můžete ji ručně změnit i na JSON/CSV/AVRO.
    - Můžete zobrazit náhled příchozích dat ve formátu tabulky nebo v nezpracovaném formátu. 
    - Pokud vaše data nejsou aktuální, vyberte **aktualizovat** , aby se zobrazily nejnovější události. 

        Tady je příklad dat ve **formátu tabulky**:   ![ výsledky ve formátu tabulky.](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Tady je příklad dat v **nezpracovaném formátu**: 

        ![Výsledky v nezpracovaném formátu](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Vyberte možnost **test Query** a zobrazte snímek výsledků testu dotazu na kartě **výsledky testu** . Můžete si také stáhnout výsledky.

    ![Test výsledků dotazu](./media/process-data-azure-stream-analytics/test-results.png)
1. Napište vlastní dotaz pro transformaci dat. Viz [referenční informace k jazyku Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference).
1. Až dotaz otestujete a chcete ho přesunout do produkčního prostředí, vyberte **nasadit dotaz**. Chcete-li nasadit dotaz, vytvořte úlohu Azure Stream Analytics, kde můžete nastavit výstup pro svou úlohu a spustit úlohu. Chcete-li vytvořit úlohu Stream Analytics, zadejte název úlohy a vyberte **vytvořit**.

      ![Vytvoření úlohy Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Doporučujeme vytvořit skupinu příjemců a zásadu pro každou novou Azure Stream Analytics úlohu, kterou vytvoříte ze stránky Event Hubs. Skupiny uživatelů umožňují jenom pět současných čtecích zařízení, takže pokud pro každou úlohu zadáte vyhrazenou skupinu uživatelů, vyhnete se případným chybám, které by mohly vzniknout nad rámec tohoto omezení. Vyhrazená zásada vám umožní otočit klíč nebo odvolat oprávnění, aniž by to mělo vliv na jiné prostředky. 
1. Vaše úloha Stream Analytics se teď vytvoří tam, kde je váš dotaz stejný, který jste otestovali, a vstupem je vaše centrum událostí. 

9.  Pro dokončení kanálu nastavte **výstup** dotazu a výběrem **Spustit** spusťte úlohu.

    > [!NOTE]
    > Před zahájením úlohy Nezapomeňte nahradit outputalias názvem výstupu, který jste vytvořili v Azure Stream Analytics.

      ![Nastavte výstup a spusťte úlohu.](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Známá omezení
Při testování dotazu trvá načtení výsledků testů přibližně 6 sekund. Pracujeme na vylepšení výkonu testování. Pokud však dojde k nasazení v produkčním prostředí, Azure Stream Analytics bude k dispozici i sekundová latence.

## <a name="streaming-units"></a>Jednotky streamování
Vaše úloha Azure Stream Analytics ve výchozím nastavení má tři jednotky streamování (SUs). Chcete-li upravit toto nastavení, vyberte možnost **škálovat** v levé nabídce na stránce **Stream Analytics úlohy** v Azure Portal. Další informace o jednotkách streamování najdete v tématu [pochopení a úprava jednotek streamování](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Škálování jednotek streamování](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Další kroky
Další informace o Stream Analyticsch dotazech najdete v tématu [Stream Analytics dotazovací jazyk](/stream-analytics-query/built-in-functions-azure-stream-analytics) .
