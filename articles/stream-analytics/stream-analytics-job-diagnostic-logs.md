---
title: Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků
description: Tento článek popisuje, jak analyzovat protokoly prostředků v Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperfq1
ms.date: 06/18/2020
ms.openlocfilehash: 0e7777cba93706baea815521757b495209431ce6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006468"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Řešení potíží s Azure Stream Analytics pomocí protokolů prostředků

Zpracování úlohy Azure Stream Analytics se občas může neočekávaně zastavit. Je důležité mít možnost tento druh události vyřešit. Selhání můžou být způsobená neočekávaným výsledkem dotazu, připojením k zařízením nebo neočekávaným výpadkem služby. Protokoly prostředků v Stream Analytics vám můžou poznat příčinu problémů, když k nim dojde, a zkrátit tak dobu obnovení.

Důrazně doporučujeme povolit protokoly prostředků pro všechny úlohy, protože to významně pomůže s laděním a monitorováním.

## <a name="log-types"></a>Typy protokolů

Stream Analytics nabízí dva typy protokolů:

* [Protokoly aktivit](../azure-monitor/platform/platform-logs-overview.md) (Always On), které poskytují přehled o operacích provedených v úlohách.

* [Protokoly prostředků](../azure-monitor/platform/platform-logs-overview.md) (konfigurovatelné), které poskytují podrobné přehledy o všech úlohách, které se v rámci úlohy děje. Protokoly prostředků se spustí při vytvoření a ukončení úlohy, když se úloha odstraní. Pokrývají události při aktualizaci úlohy a při jejím spuštění.

> [!NOTE]
> K analýze nevyhovujících dat můžete použít služby, jako jsou Azure Storage, Azure Event Hubs a protokoly Azure Monitor. Účtují se vám poplatky podle cenového modelu pro tyto služby.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Ladění pomocí protokolů aktivit

Protokoly aktivit jsou ve výchozím nastavení zapnuté a poskytují podrobné přehledy o operacích, které provádí vaše úloha Stream Analytics. Informace, které jsou k dispozici v protokolech aktivit, mohou pomáhat najít hlavní příčinu problémů, které mají vliv na vaši úlohu. K použití protokolů aktivit v Stream Analytics postupujte podle následujících kroků:

1. Přihlaste se k Azure Portal a v části **Přehled** vyberte **Protokol aktivit** .

   ![Protokol aktivit Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Můžete zobrazit seznam operací, které byly provedeny. Jakákoli operace, která způsobila selhání úlohy, má bublinu červené informace.

3. Kliknutím na operaci zobrazíte její souhrnné zobrazení. Informace zde jsou často omezené. Chcete-li získat další informace o operaci, klikněte na tlačítko **JSON**.

   ![Souhrn operací protokolu aktivit Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Přejděte dolů k části **vlastnosti** ve formátu JSON, která poskytuje podrobné informace o chybě, která způsobila neúspěšnou operaci. V tomto příkladu došlo k chybě kvůli chybě za běhu z nevázaných hodnot zeměpisné šířky. Nesoulad v datech, která jsou zpracována úlohou Stream Analytics, způsobuje chybu dat. Můžete se seznámit s různými [chybami vstupních a výstupních dat a proč k nim dojde](./data-errors.md).

   ![Podrobnosti o chybě JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Můžete provést opravné akce na základě chybové zprávy ve formátu JSON. V tomto příkladu zkontroluje, jestli je hodnota zeměpisné šířky mezi-90 stupňů a 90m stupně je potřeba přidat do dotazu.

6. Pokud chybová zpráva v protokolech aktivit není užitečná při identifikaci hlavní příčiny, povolte protokoly prostředků a použijte protokoly Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Odeslání diagnostiky do protokolů Azure Monitor

Důrazně doporučujeme zapnout protokoly prostředků a odesílat je do protokolů Azure Monitor. Ve výchozím nastavení jsou **vypnuté** . Pokud je chcete zapnout, proveďte tyto kroky:

1.  Vytvořte pracovní prostor Log Analytics, pokud ho ještě nemáte. Doporučuje se, aby byl váš pracovní prostor Log Analytics ve stejné oblasti jako vaše úloha Stream Analytics.

2.  Přihlaste se k Azure Portal a přejděte do Stream Analytics úlohy. V části **monitorování** vyberte **diagnostické protokoly**. Pak vyberte **zapnout diagnostiku**.

    ![Navigační okno s protokoly prostředků](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Zadejte **název** v **nastavení diagnostiky název** a zaškrtněte políčka pro **spouštění** a **vytváření** v části **log** a **AllMetrics** v části **metrika**. Pak vyberte **Odeslat do Log Analytics** a zvolte pracovní prostor. Klikněte na **Uložit**.

    ![Nastavení pro protokoly prostředků](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. Po spuštění úlohy Stream Analytics se protokoly prostředků směrují do vašeho pracovního prostoru Log Analytics. Chcete-li zobrazit protokoly prostředků pro vaši úlohu, vyberte v části **monitorování** možnost **protokoly** .

   ![Snímek obrazovky s vybranými protokoly zobrazuje nabídku Obecné.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics poskytuje předdefinované dotazy, které vám umožní snadno vyhledat protokoly, které vás zajímají. V levém podokně můžete vybrat všechny předem definované dotazy a pak vybrat **Spustit**. Výsledky dotazu se zobrazí v dolním podokně. 

   ![Snímek obrazovky se zobrazí protokoly Stream Analytics úlohy.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Kategorie protokolu prostředků

Azure Stream Analytics zachycuje dvě kategorie protokolů prostředků:

* **Vytváření obsahu**: zachycuje události protokolu, které souvisejí s operacemi vytváření úloh, jako je vytváření úloh, přidávání a odstraňování vstupů a výstupů, přidávání a aktualizace dotazu a spuštění nebo zastavení úlohy.

* **Spuštění**: zachycuje události, ke kterým dojde během provádění úlohy.
    * Chyby připojení
    * Chyby zpracování dat, včetně:
        * Události, které neodpovídají definici dotazu (neshodné typy polí a hodnoty, chybějící pole atd.)
        * Chyby vyhodnocení výrazu
    * Další události a chyby

## <a name="resource-logs-schema"></a>Schéma protokolů prostředků

Všechny protokoly jsou uložené ve formátu JSON. Každá položka má následující obecná pole řetězců:

Název | Description
------- | -------
time | Časové razítko (v UTC) protokolu
resourceId | ID prostředku, na kterém byla operace provedena, v horním případě. Obsahuje ID předplatného, skupinu prostředků a název úlohy. Například **/Subscriptions/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/my-Resource-Group/Providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Kategorie protokolu, buď **spuštění** , nebo **vytváření obsahu**.
operationName | Název operace, která se zaznamená do protokolu. Například **odeslání událostí: Chyba zápisu výstupu SQL do mysqloutput**.
status | Stav operace. Například **Chyba** nebo **úspěch**.
úroveň | Úroveň protokolování. Například **Chyba**, **Upozornění** nebo **informativní**.
properties | Podrobnosti specifické pro záznam v protokolu, serializované jako řetězec JSON. Další informace najdete v následujících částech tohoto článku.

### <a name="execution-log-properties-schema"></a>Schéma vlastností protokolu spuštění

Protokoly spouštění obsahují informace o událostech, ke kterým došlo během Stream Analytics provádění úlohy. Schéma vlastností se liší v závislosti na tom, zda se jedná o chybu dat nebo o obecnou událost.

### <a name="data-errors"></a>Chyby dat

Všechny chyby, ke kterým dojde během zpracování úlohy, jsou v této kategorii protokolů. Tyto protokoly se nejčastěji vytvářejí během operací čtení, serializace a zápisu dat. Tyto protokoly nezahrnují chyby připojení. Chyby připojení se považují za obecné události. Můžete si přečíst další informace o příčině různých [vstupních a výstupních chybových dat](./data-errors.md).

Název | Popis
------- | -------
Zdroj | Název vstupu nebo výstupu úlohy, kde došlo k chybě
Zpráva | Zpráva přidružená k chybě
Typ | Typ chyby Například **DataConversionError**, **CsvParserError** nebo **ServiceBusPropertyColumnMissingError**.
Data | Obsahuje data, která jsou užitečná k přesnému nalezení zdroje chyby. Může se zkrátit v závislosti na velikosti.

V závislosti na hodnotě **OperationName** mají chyby dat následující schéma:

* **Události serializace** dojde během operací čtení událostí. K tomu dochází, když data na vstupu nevyhovují schématu dotazu v jednom z těchto důvodů:

   * *Neshoda typů během události (de) serializace*: identifikuje pole, které způsobuje chybu.

   * *Událost se nedá přečíst, neplatná serializace*: obsahuje informace o umístění ve vstupních datech, kde došlo k chybě. Zahrnuje název objektu BLOB pro vstup, posun a ukázku dat objektu BLOB.

* **Posílání událostí** během operací zápisu. Identifikují událost streamování, která způsobila chybu.

### <a name="generic-events"></a>Obecné události

Obecné události se týkají všech ostatních.

Název | Description
-------- | --------
Chyba | volitelné Informace o chybě. Obvykle se jedná o výjimku, pokud je k dispozici.
Zpráva| Zpráva protokolu
Typ | Typ zprávy Provede mapování na interní kategorizaci chyb. Například **JobValidationError** nebo **BlobOutputAdapterInitializationFailure**.
ID korelace | Identifikátor GUID, který jedinečně identifikuje provádění úlohy. Všechny položky protokolu spuštění od okamžiku spuštění úlohy, dokud úloha přestane mít stejnou hodnotu **ID korelace** .

## <a name="next-steps"></a>Další kroky

* [Chyby Stream Analytics dat](./data-errors.md)
* [Referenční dokumentace jazyka Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference)