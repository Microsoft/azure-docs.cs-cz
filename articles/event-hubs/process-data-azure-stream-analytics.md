---
title: Zpracování dat z Centra událostí Azure pomocí Stream Analytics | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak zpracovat data z centra událostí Azure pomocí úlohy Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991948"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Zpracování dat z centra událostí pomocí Azure Stream Analytics 
Služba Azure Stream Analytics usnadňuje ingestování, zpracování a analýzu streamovaných dat z Centra událostí Azure, což umožňuje výkonné přehledy pro akce v reálném čase. Tato integrace umožňuje rychle vytvořit kanál analýzy horké cesty. Na webu Azure Portal můžete použít k vizualizaci příchozích dat a k napsání dotazu Stream Analytics. Jakmile je dotaz připraven, můžete jej přesunout do produkčního prostředí pouhými několika kliknutími. 

## <a name="key-benefits"></a>Klíčové výhody
Tady jsou hlavní výhody Azure Event Hubs a integrace Azure Stream Analytics: 
- **Náhled dat** – můžete zobrazit náhled příchozích dat z centra událostí na webu Azure Portal.
- **Otestujte dotaz** – připravte dotaz transformace a otestujte ho přímo na webu Azure Portal. Syntaxe dotazovacího jazyka najdete v tématu Dokumentace [k dotazovacímu jazyku Služby Stream Analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Nasaďte dotaz do produkčního prostředí** – dotaz můžete nasadit do produkčního prostředí vytvořením a spuštěním úlohy Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Tok od konce na konec

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 
1. Přejděte do **oboru názvů Centra událostí** a potom přejděte do centra **událostí**, které má příchozí data. 
1. Na stránce centra událostí vyberte **Zpracovat data.**  

    ![Dlaždice procesních dat](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Na dlaždici **Povolit přehledy z událostí vreálném čase vyberte** **Prozkoumat.** 

    ![Vybrat analýzu datového proudu](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Zobrazí se stránka dotazu s hodnotami, které jsou již nastaveny pro následující pole:
    1. Centrum **událostí** jako vstup pro dotaz.
    1. Ukázkový **dotaz SQL** s příkazem SELECT. 
    1. **Výstupní** alias, který odkazuje na výsledky testu dotazu. 

        ![Editor dotazů](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Při prvním použití této funkce vás tato stránka požádá o oprávnění k vytvoření skupiny spotřebitelů a zásad y pro centrum událostí pro zobrazení náhledu příchozích dat.
1. V podokně **náhledu vstupu** vyberte **Vytvořit,** jak je znázorněno na předchozím obrázku. 
1. Na této kartě se okamžitě zobrazí snímek nejnovějších příchozích dat.
    - Typ serializace v datech je automaticky rozpoznán (JSON/CSV). Můžete ručně změnit také JSON / CSV / AVRO.
    - Můžete zobrazit náhled příchozích dat ve formátu tabulky nebo nezpracovaném formátu. 
    - Pokud zobrazená data nejsou aktuální, kliknutím na **Aktualizovat** zobrazte nejnovější události. 

        Zde je příklad dat ve formátu ![ **tabulky**: Výsledky ve formátu tabulky](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Zde je příklad dat v **nezpracovaném formátu**: 

        ![Výsledky v nezpracovaném formátu](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Vyberte **Testovat dotaz,** chcete-li zobrazit snímek výsledků testu dotazu na kartě **Výsledky testu.** Můžete si také stáhnout výsledky.

    ![Výsledky testovacího dotazu](./media/process-data-azure-stream-analytics/test-results.png)
1. Napište vlastní dotaz pro transformaci dat. Viz [Reference dotazovacího jazyka Analýzy datových proudů](/stream-analytics-query/stream-analytics-query-language-reference).
1. Po otestování dotazu a jeho přesunutí do produkčního prostředí vyberte **Nasadit dotaz**. Chcete-li nasadit dotaz, vytvořte úlohu Azure Stream Analytics, kde můžete nastavit výstup pro svou úlohu a spustit úlohu. Chcete-li vytvořit úlohu Stream Analytics, zadejte název úlohy a vyberte **Vytvořit**.

      ![Vytvoření úlohy Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Doporučujeme vytvořit skupinu spotřebitelů a zásady pro každou novou úlohu Azure Stream Analytics, kterou vytvoříte na stránce Centra událostí. Skupiny spotřebitelů povolují pouze pět souběžných čtecích programů, takže poskytnutí vyhrazené skupiny spotřebitelů pro každou úlohu zabrání chybám, které by mohly vzniknout při překročení tohoto limitu. Vyhrazená zásada umožňuje otočit klíč nebo odvolat oprávnění bez ovlivnění jiných prostředků. 
1. Vaše úloha Stream Analytics je teď vytvořena tam, kde je váš dotaz stejný jako vytestovaný, a vstup je vaše centrum událostí. 

9.  Chcete-li dokončit kanál, nastavte **výstup** dotazu a vyberte **Spustit** pro spuštění úlohy.

    > [!NOTE]
    > Před zahájením úlohy nezapomeňte nahradit outputalias výstupním názvem, který jste vytvořili ve službě Azure Stream Analytics.

      ![Nastavení výstupu a spuštění úlohy](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Známá omezení
Při testování dotazu, výsledky testů trvat přibližně 6 sekund načíst. Pracujeme na zlepšení výkonu testování. Ale při nasazení v produkčním prostředí Azure Stream Analytics bude mít latenci podsekund.

## <a name="streaming-units"></a>Jednotky streamování
Vaše úloha Azure Stream Analytics je ve výchozím nastavení nastavena na tři jednotky streamování (SU). Pokud chcete toto nastavení upravit, vyberte **Škálovat** v levé nabídce na stránce **úlohy Stream Analytics** na webu Azure Portal. Další informace o jednotkách streamování najdete v [tématu Principy a úpravy jednotek streamování](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Jednotky streamování ve velkém měřítku](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Další kroky
Další informace o dotazech Stream Analytics najdete v [tématu Stream Analytics Query Language](/stream-analytics-query/built-in-functions-azure-stream-analytics)
