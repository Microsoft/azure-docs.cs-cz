---
title: Zpracování dat z Azure Event Hubs pomocí Stream Analytics | Dokumentace Microsoftu
description: Tento článek ukazuje, jak zpracovat data ze služby Azure event hub pomocí úlohy Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723412"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Zpracování dat z vašeho centra událostí pomocí Azure Stream Analytics
Služba Azure Stream Analytics umožňuje snadno ingestovat procesu, a analyzujte data streamovaná ze služby Azure Event Hubs výkonné přehledy jednotky akcí v reálném čase. Tato integrace umožňuje rychle vytvořit kanál horká cesta analytics. Na webu Azure portal můžete použít k vizualizaci příchozích dat a psaní dotazu Stream Analytics. Když váš dotaz je připravená, přesuňte ho do produkčního prostředí pouze pomocí několika kliknutí. 

## <a name="key-benefits"></a>Klíčové výhody
Tady jsou klíčové výhody integrace služby Azure Event Hubs a Azure Stream Analytics: 
- **Zobrazit náhled dat** – můžete zobrazit náhled příchozích dat z centra událostí na webu Azure Portal.
- **Otestování dotazu** – Příprava transformační dotaz a otestovat přímo na webu Azure portal. Syntaxe jazyka dotazů, najdete v části [dotazovací jazyk Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentaci.
- **Nasazení do produkčního prostředí dotaz** – dotaz můžete nasadit do produkčního prostředí tak, že vytvoření a spuštění úlohy Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Kompletního toku

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejděte do vaší **oboru názvů Event Hubs** a přejděte na **centra událostí**, který má příchozích dat. 
1. Vyberte **zpracování dat** na stránce centra událostí.  

    ![Proces data dlaždice](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Vyberte **prozkoumat** na **umožňují v reálném čase z událostí** dlaždici. 

    ![Vyberte Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Uvidíte stránku s dotazu s hodnotami již nastaven pro následující pole:
    1. Vaše **centra událostí** jako vstup pro dotaz.
    1. Ukázka **dotaz SQL** s příkazem SELECT. 
    1. **Výstup** alias jako reference k výsledkům testu. 

        ![Editor dotazů](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Při použití této funkce poprvé vyzve k zadání vašich oprávnění k vytvoření skupiny příjemců a zásady pro vaše Centrum událostí ve verzi preview příchozích dat na této stránce.
1. Vyberte **vytvořit** v **vstup ve verzi preview** podokně, jak je znázorněno na předchozím obrázku. 
1. Okamžitě uvidíte snímek nejnovější příchozích dat na této kartě.
    - Typ serializace ve vašich datech je automaticky zjištěno (JSON nebo CSV). Můžete ručně změnit ho i do formátu JSON, CSV nebo AVRO.
    - Můžete zobrazit náhled příchozích dat v nezpracovaném formátu nebo formátu tabulky. 
    - Pokud data zobrazovaná není aktuální, vyberte **aktualizovat** zobrazíte nejnovější události. 

        Tady je příklad dat **formátu tabulky**:   ![Výsledky ve formátu tabulky](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Tady je příklad dat **nezpracovaném formátu**: 

        ![Výsledky v nezpracovaném formátu](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Vyberte **testovat dotaz** zobrazíte snímek výsledky testu pro váš dotaz v **výsledky testů** kartu. Můžete také stáhnout výsledky.

    ![Výsledky testu dotazu](./media/process-data-azure-stream-analytics/test-results.png)
1. Napsat vlastní dotaz, který transformuje data. Zobrazit [dotazovací jazyk Stream Analytics odkaz](/stream-analytics-query/stream-analytics-query-language-reference).
1. Jakmile jste otestovali dotaz a chcete přesunout výroby, vyberte **nasadit dotazu**. Pokud chcete nasadit dotaz, vytvořte úlohu Azure Stream Analytics, ve kterém můžete nastavit výstup pro vaši úlohu a spustit úlohu. Chcete-li vytvořit úlohu Stream Analytics, zadejte název projektu a vyberte **vytvořit**.

      ![Vytvoření úlohy Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Doporučujeme vytvořit skupinu příjemců a zásadu pro každou novou úlohu Azure Stream Analytics, kterou vytvoříte na stránce služby Event Hubs. Skupiny příjemců povolit pouze pěti souběžných čtenářů, takže poskytuje vyhrazenou skupinu spotřebitelů pro každou úlohu se vyhnete případné chyby, které mohou vzniknout při překročení tohoto limitu. Zásady typu dedicated můžete svůj klíč nebo odvolat oprávnění bez dopadu na ostatní prostředky. 
1. Vaší úlohy Stream Analytics je vytvořený, kde váš dotaz je stejný, který jste otestovali a zadání se vašeho centra událostí. 

9.  Chcete-li dokončit kanálu, nastavte **výstup** dotazu a vyberte **Start** spustíte úlohu.

    > [!NOTE]
    > Před spuštěním úlohy, nezapomeňte nahradit outputalias výstupní název, který jste vytvořili ve službě Azure Stream Analytics.

      ![Nastavte výstup a spustit úlohu](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Známá omezení
Při testování se váš dotaz, výsledky testů trvat přibližně 6 sekund načíst. Pracujeme na vylepšení výkonu testování. Ale při nasazení v produkčním prostředí, Azure Stream Analytics bude mít subsecond latence.

## <a name="streaming-units"></a>Jednotky streamování
Váš Azure Stream Analytics úloh výchozí hodnota je tři jednotky streamování (su). Chcete-li toto nastavení upravit, vyberte **škálování** v nabídce vlevo v **úlohy Stream Analytics** stránky na webu Azure Portal. Další informace o jednotek streamování najdete v tématu [principy a úpravy jednotek streamování](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Škálování jednotek streamování](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Další postup
Další informace o dotazech Stream Analytics najdete v tématu [dotazovací jazyk Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)