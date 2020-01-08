---
title: Řešení potíží s Azure Stream Analytics a využívat protokoly diagnostiky
description: Tento článek popisuje, jak analyzovat diagnostické protokoly v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: f318b373f6a6f46ee3a85703c6099c76568580ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426161"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Řešení potíží s Azure Stream Analytics s využitím diagnostických protokolů

Zpracování úlohy Azure Stream Analytics se občas může neočekávaně zastavit. Je důležité mít možnost tento druh události vyřešit. Selhání můžou být způsobená neočekávaným výsledkem dotazu, připojením k zařízením nebo neočekávaným výpadkem služby. Diagnostické protokoly v Stream Analytics vám můžou poznat příčinu problémů, když k nim dojde, a zkrátit tak dobu obnovení.

Důrazně doporučujeme povolit diagnostické protokoly pro všechny úlohy, protože to významně pomůže s laděním a monitorováním.

## <a name="log-types"></a>Typy protokolů

Stream Analytics nabízí dva typy protokolů:

* [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Always On), které poskytují přehled o operacích provedených v úlohách.

* [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (konfigurovatelné), které poskytují podrobné přehledy o všech úlohách, které se v rámci úlohy děje. Diagnostické protokoly jsou spouštěny při vytvoření úlohy a končí při jejím odstranění. Při aktualizaci úlohy a během jejího běhu pokrývají události.

> [!NOTE]
> K analýze nevyhovujících dat můžete použít služby, jako jsou Azure Storage, Azure Event Hubs a protokoly Azure Monitor. Budou se vám účtovat podle cenového modelu pro služby.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Ladění pomocí protokolů aktivit

Protokoly aktivit jsou ve výchozím nastavení zapnuté a poskytují podrobné přehledy o operacích, které provádí vaše úloha Stream Analytics. Informace, které jsou k dispozici v protokolech aktivit, mohou pomáhat najít hlavní příčinu problémů, které mají vliv na vaši úlohu. K použití protokolů aktivit v Stream Analytics postupujte podle následujících kroků:

1. Přihlaste se k Azure Portal a v části **Přehled**vyberte **Protokol aktivit** .

   ![Protokol aktivit Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Můžete zobrazit seznam operací, které byly provedeny. Jakákoli operace, která způsobila selhání úlohy, má bublinu červené informace.

3. Kliknutím na operaci zobrazíte její souhrnné zobrazení. Informace zde jsou často omezené. Chcete-li získat další informace o operaci, klikněte na tlačítko **JSON**.

   ![Souhrn operací protokolu aktivit Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Přejděte dolů k části **vlastnosti** ve formátu JSON, která poskytuje podrobné informace o chybě, která způsobila neúspěšnou operaci. V tomto příkladu došlo k chybě kvůli chybě za běhu z nevázaných hodnot zeměpisné šířky. Nesoulad v datech, která jsou zpracována úlohou Stream Analytics, způsobuje chybu dat. Můžete se seznámit s různými [chybami vstupních a výstupních dat a proč k nim dojde](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Podrobnosti o chybě JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Můžete provést opravné akce na základě chybové zprávy ve formátu JSON. V tomto příkladu zkontroluje, jestli je hodnota zeměpisné šířky mezi-90 stupňů a 90m stupně je potřeba přidat do dotazu.

6. Pokud chybová zpráva v protokolech aktivit není užitečná při identifikaci hlavní příčiny, povolte diagnostické protokoly a použijte protokoly Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Odeslání diagnostiky do protokolů Azure Monitor

Důrazně se doporučuje zapnout diagnostické protokoly a jejich odesílání do protokolů Azure Monitor. Diagnostické protokoly jsou **vypnout** ve výchozím nastavení. Chcete-li zapnout diagnostické protokoly, postupujte takto:

1.  Přihlaste se k Azure Portal a přejděte do Stream Analytics úlohy. V části **monitorování**vyberte **diagnostické protokoly**. Pak vyberte **zapnout diagnostiku**.

    ![Navigace v okně diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  V **nastavení diagnostiky** vytvořte **název** a zaškrtněte políčko u možnosti **Odeslat do Log Analytics**. Pak přidejte existující **pracovní prostor Log Analytics**, nebo vytvořte nový. Zaškrtněte políčka pro **spouštění** a **vytváření** v části **log**a **AllMetrics** v části **metrika**. Klikněte na možnost **Uložit**. Doporučuje se použít Log Analytics pracovní prostor ve stejné oblasti Azure jako vaše úloha Stream Analytics a zabránit tak dalším nákladům.

    ![Nastavení pro diagnostické protokoly](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Po spuštění úlohy Stream Analytics se protokoly diagnostiky směrují do vašeho pracovního prostoru Log Analytics. Chcete-li zobrazit protokoly diagnostiky pro vaši úlohu, vyberte v části **monitorování** možnost **protokoly** .

   ![Diagnostické protokoly v části monitorování](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics poskytuje předdefinované dotazy, které vám umožní snadno vyhledat protokoly, které vás zajímají. 3 kategorie jsou **Obecné**, **chyby vstupních dat** a **chyby výstupních dat**. Pokud například chcete zobrazit souhrn všech chyb vaší úlohy za posledních 7 dní, můžete vybrat **spuštění** příslušného předem definovaného dotazu. 

   ![Diagnostické protokoly v části monitorování](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Výsledky protokolů](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Kategorie protokolu diagnostiky

Azure Stream Analytics zachycuje dvě kategorie diagnostických protokolů:

* **Vytváření obsahu**: zachycuje události protokolu, které souvisejí s operacemi vytváření úloh, jako je vytváření úloh, přidávání a odstraňování vstupů a výstupů, přidávání a aktualizace dotazu a spuštění nebo zastavení úlohy.

* **Spuštění**: zachycuje události, ke kterým dojde během provádění úlohy.
    * Chyby připojení
    * Chyby zpracování dat, včetně:
        * Události, které nejsou v souladu s definicí dotazu (pole neodpovídající typy a hodnoty, chybějící pole a tak dále)
        * Chyby vyhodnocení výrazů
    * Další události a chyby

## <a name="diagnostics-logs-schema"></a>Schéma protokoly diagnostiky

Všechny protokoly se ukládají ve formátu JSON. Každý záznam obsahuje následující společných polí řetězec:

Name (Název) | Popis
------- | -------
time | Časové razítko (ve standardu UTC) v protokolu.
resourceId | ID prostředku, že operace konal úplně, velkými písmeny. Obsahuje ID předplatného, skupinu prostředků a název úlohy. Například   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategorie, buď protokolu **provádění** nebo **Authoring**.
operationName | Název operace, která je zaznamenána. Například **odesílat události: Chyba zápisu výstupu SQL do mysqloutput**.
status | Stav operace. Například **neúspěšné** nebo **Succeeded**.
úroveň | Úroveň protokolu. Například **chyba**, **upozornění**, nebo **informativní**.
properties | Podrobnosti konkrétní položky protokolu serializován jako řetězec formátu JSON. Další informace najdete v následujících částech tohoto článku.

### <a name="execution-log-properties-schema"></a>Schéma vlastnosti protokolu spuštění

Protokoly spuštění obsahují informace o události, ke kterým došlo během provádění úlohy Stream Analytics. Schéma vlastností se liší v závislosti na tom, zda se jedná o chybu dat nebo o obecnou událost.

### <a name="data-errors"></a>Chyby dat

Všechny chyby, ke které dojde během úlohy zpracování dat je v této kategorii protokolů. Nejčastěji tyto protokoly se vytvoří během data načtená, serializaci a operace zápisu. Tyto protokoly neobsahují chyby připojení. K chybám připojení jsou považovány za obecné události. Můžete si přečíst další informace o příčině různých [vstupních a výstupních chybových dat](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Name (Název) | Popis
------- | -------
Zdroj | Název úlohy vstup nebo výstup, kde došlo k chybě.
Zpráva | Zpráva přidružená k chybě.
Typ | Typ chyby. Například **DataConversionError**, **CsvParserError**, nebo **ServiceBusPropertyColumnMissingError**.
Data | Obsahuje data, která slouží k vytvoření přesně najít zdroje chyby. Pro zkrácení, v závislosti na velikosti na základě práv subjektů.

V závislosti na tom **operationName** hodnota, data chyby mají následující schéma:

* **Události serializace** dojde během operací čtení událostí. K nim dojde, když data na vstupu nevyhovuje schématu dotaz pro některého z těchto důvodů:

   * *Neshoda typů při události (de) serializovat*: Určuje pole, která je příčinou chyby.

   * *Nelze přečíst událost, neplatná Serializační*: obsahuje informace o umístění vstupních dat, kde došlo k chybě. Obsahuje název objektu blob pro vstupní objekt blob, posun a ukázková data.

* **Posílání událostí** během operací zápisu. Identifikují streamování událostí, která způsobila chybu.

### <a name="generic-events"></a>Obecné události

Obecné události pokrývají všechno ostatní.

Name (Název) | Popis
-------- | --------
Chyba | (volitelné) Informace o chybě. Obvykle se jedná o výjimku, pokud je k dispozici.
Zpráva| Zpráva protokolu.
Typ | Typ zprávy. Mapuje na interní kategorizace chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | [Identifikátor GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) , který jednoznačně identifikuje provádění úlohy. Zahájení úlohy všechny položky protokolu spuštění od okamžiku, dokud je úloha pozastavena mají stejné **ID korelace** hodnotu.

## <a name="next-steps"></a>Další kroky

* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Chyby Stream Analytics dat](https://docs.microsoft.com/azure/stream-analytics/data-errors)
