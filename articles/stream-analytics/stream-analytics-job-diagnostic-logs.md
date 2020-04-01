---
title: Poradce při potížích s Azure Stream Analytics pomocí protokolů diagnostiky
description: Tento článek popisuje, jak analyzovat protokoly diagnostiky v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398427"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Poradce při potížích s Azure Stream Analytics pomocí protokolů diagnostiky

Zpracování úlohy Azure Stream Analytics se občas může neočekávaně zastavit. Je důležité mít možnost tento druh události vyřešit. Selhání můžou být způsobená neočekávaným výsledkem dotazu, připojením k zařízením nebo neočekávaným výpadkem služby. Diagnostické protokoly v Stream Analytics vám mohou pomoci určit příčinu problémů, když k nim dojde, a zkrátit dobu obnovení.

Důrazně doporučujeme povolit diagnostické protokoly pro všechny úlohy, protože to výrazně pomůže s laděním a monitorováním.

## <a name="log-types"></a>Typy protokolů

Stream Analytics nabízí dva typy protokolů:

* Protokoly aktivit (vždy [zapnuté),](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) které poskytují přehled o operacích prováděných na úlohách.

* [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurovatelné), které poskytují bohatší přehled o všem, co se děje s úlohou. Protokoly diagnostiky se spustí při vytvoření úlohy a ukončení při odstranění úlohy. Zahrnují události, když je úloha aktualizována a když je spuštěna.

> [!NOTE]
> Ke analýze nevyhovujících dat můžete použít služby, jako je Azure Storage, Azure Event Hubs a Azure Monitor. Poplatky vám budou účtovány na základě cenového modelu těchto služeb.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Ladění pomocí protokolů aktivit

Protokoly aktivit jsou ve výchozím nastavení zapnuté a poskytují přehled o operacích prováděných úlohou Stream Analytics na vysoké úrovni. Informace uvedené v protokolech aktivit mohou pomoci najít hlavní příčinu problémů, které mají vliv na vaši úlohu. Chcete-li v Stream Analytics používat protokoly aktivit, postupujte takto:

1. Přihlaste se k portálu Azure a v části **Přehled**vyberte **Protokol aktivit** .

   ![Protokol aktivit Služby Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Můžete zobrazit seznam operací, které byly provedeny. Každá operace, která způsobila selhání úlohy, má červenou informační bublinu.

3. Kliknutím na operaci zobrazíte její souhrnné zobrazení. Informace zde jsou často omezené. Další podrobnosti o operaci zobrazíte klepnutím na tlačítko **JSON**.

   ![Souhrn operace protokolu aktivit služby Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Přejděte dolů do části **Vlastnosti** JSON, který poskytuje podrobnosti o chybě, která způsobila neúspěšnou operaci. V tomto příkladu selhání bylo způsobeno chybou za běhu z hodnoty mimo vázané šířky. Nesoulad v datech zpracovávaných úlohou Stream Analytics způsobí chybu dat. Můžete se dozvědět o různých [vstupních a výstupních chybách dat a o tom, proč k nim dochází](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Podrobnosti o chybě JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Můžete provést nápravná opatření na základě chybové zprávy v JSON. V tomto příkladu kontroly zajistit, že hodnota zeměpisné šířky je mezi -90 stupňů a 90 stupňů je třeba přidat do dotazu.

6. Pokud chybová zpráva v protokolech aktivit není užitečné při identifikaci hlavní příčiny, povolit diagnostické protokoly a používat protokoly Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Odeslání diagnostiky do protokolů Azure Monitoru

Zapnutí diagnostické protokoly a jejich odeslání do protokolů Azure Monitor je vysoce doporučeno. Protokoly diagnostiky jsou ve výchozím nastavení **vypnuty.** Chcete-li zapnout protokoly diagnostiky, proveďte tyto kroky:

1.  Přihlaste se k portálu Azure a přejděte na úlohu Stream Analytics. V části **Sledování**vyberte **položku Diagnostické protokoly**. Pak vyberte **Zapnout diagnostiku**.

    ![Blade navigace do protokolů diagnostiky](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Vytvořte **název** v **nastavení diagnostiky** a zaškrtněte políčko vedle **odeslat do analýzy protokolů**. Potom přidejte existující nebo vytvořte nový **pracovní prostor analýzy protokolů**. Zaškrtněte políčka pro **provádění** a **vytváření** **v**log a **AllMetrics** v **metrická**. Klikněte na **Uložit**. Doporučujeme používat pracovní prostor Log Analytics ve stejné oblasti Azure jako úloha Stream Analytics, abyste zabránili dodatečným nákladům.

    ![Nastavení protokolů diagnostiky](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Po spuštění úlohy Stream Analytics jsou diagnostické protokoly směrovány do pracovního prostoru Analýzy protokolů. Chcete-li zobrazit diagnostické protokoly pro vaši úlohu, vyberte **protokoly** v části **Monitorování.**

   ![Diagnostické protokoly pod monitorováním](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics poskytuje předdefinované dotazy, které vám umožní snadno vyhledávat protokoly, které vás zajímají. 3 kategorie jsou **obecné**, **chyby vstupních dat** a **výstupní data chyby**. Chcete-li například zobrazit souhrn všech chyb úlohy za posledních 7 dní, můžete vybrat **možnost Spustit** příslušný předdefinovaný dotaz. 

   ![Diagnostické protokoly pod monitorováním](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Výsledky protokolů](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Kategorie protokolů diagnostiky

Azure Stream Analytics zachycuje dvě kategorie protokolů diagnostiky:

* **Vytváření**: Zachycuje události protokolu, které souvisejí s operacemi vytváření úloh, jako je například vytváření úloh, přidávání a odstraňování vstupů a výstupů, přidávání a aktualizace dotazu a spuštění nebo zastavení úlohy.

* **Spuštění**: Zachycuje události, ke kterým dochází během provádění úlohy.
    * Chyby připojení
    * Chyby zpracování dat, včetně:
        * Události, které neodpovídají definici dotazu (neodpovídající typy a hodnoty polí, chybějící pole a tak dále)
        * Chyby vyhodnocení výrazu
    * Další události a chyby

## <a name="diagnostics-logs-schema"></a>Schéma protokolů diagnostiky

Všechny protokoly jsou uloženy ve formátu JSON. Každá položka má následující společná pole řetězce:

Name (Název) | Popis
------- | -------
time | Časové razítko (v UTC) protokolu.
resourceId | ID prostředku, na který se operace uskutečnila, velkými písmeny. Zahrnuje ID předplatného, skupinu prostředků a název úlohy. Například **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategorie protokolu, **buď Spuštění** **nebo Vytváření**.
operationName | Název operace, která je protokolována. Například **Odeslat události: SQL Output selhání zápisu mysqloutput**.
status | Stav operace. Například **Neúspěšné** nebo **Úspěšné**.
level | Úroveň protokolu. Například **Chyba**, **Upozornění**nebo **Informační**.
properties | Podrobnosti specifické pro položku protokolu, serializované jako řetězec JSON. Další informace naleznete v následujících částech tohoto článku.

### <a name="execution-log-properties-schema"></a>Schéma vlastností protokolu spuštění

Protokoly spuštění mají informace o událostech, ke kterým došlo během spuštění úlohy Služby Streamování. Schéma vlastností se liší v závislosti na tom, zda je událost chyba dat nebo obecná událost.

### <a name="data-errors"></a>Chyby dat

Jakákoli chyba, ke které dojde při zpracování dat úlohy, je v této kategorii protokolů. Tyto protokoly jsou nejčastěji vytvářeny během operací čtení dat, serializace a zápisu. Tyto protokoly neobsahují chyby připojení. Chyby připojení jsou považovány za obecné události. Můžete se dozvědět více o příčině různých [chyb vstupních a výstupních dat](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Name (Název) | Popis
------- | -------
Zdroj | Název vstupu úlohy nebo výstupu, kde došlo k chybě.
Zpráva | Zpráva přidružená k chybě.
Typ | Typ chyby. Například **DataConversionError**, **CsvParserError**nebo **ServiceBusPropertyColumnMissingError**.
Data | Obsahuje data, která je užitečná pro přesné vyhledání zdroje chyby. V závislosti na velikosti se vztahuje zkrácení.

V závislosti na hodnotě **operationName** mají chyby dat následující schéma:

* **Serializovat události** dojít během operací čtení událostí. Dochází k nim, když data na vstupu nesplňuje schéma dotazu z jednoho z těchto důvodů:

   * *Neshoda typů během event (de)serialize*: Identifikuje pole, které chybu způsobuje.

   * *Nelze číst událost, neplatná serializace*: Uvádí informace o umístění ve vstupních datech, kde došlo k chybě. Zahrnuje název objektu blob pro vstup objektu blob, posun a ukázku dat.

* **Odeslat události** dojít během operací zápisu. Identifikují událost streamování, která způsobila chybu.

### <a name="generic-events"></a>Obecné události

Obecné události pokrývají vše ostatní.

Name (Název) | Popis
-------- | --------
Chyba | (nepovinné) Informace o chybě. Obvykle se jedná o informace o výjimce, pokud jsou k dispozici.
Zpráva| Protokolovat zprávu.
Typ | Typ zprávy. Mapuje na interní kategorizaci chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | [GUID,](https://en.wikipedia.org/wiki/Universally_unique_identifier) který jednoznačně identifikuje spuštění úlohy. Všechny položky protokolu spuštění od okamžiku spuštění úlohy až do zastavení úlohy mají stejnou hodnotu **ID korelace.**

## <a name="next-steps"></a>Další kroky

* [Úvod do streamové analýzy](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úloh Stream Analytics](stream-analytics-scale-jobs.md)
* [Odkaz na dotaz ového jazyka Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Chyby dat analýzy streamu](https://docs.microsoft.com/azure/stream-analytics/data-errors)
